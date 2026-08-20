---
title: SMB 认证机制
created: 2026-06-23
updated: 2026-06-23
type: concept
domain: SMB协议
tags:
  - OS
  - Samba
  - SMB
  - NTLM
  - Kerberos
  - SPNEGO
---

# SMB 认证机制

> 属于 [[Samba学习索引]] | 相关：[[SMB会话建立流程]]

---

## 三种机制概览

| 机制 | 安全性 | 场景 | 轮次 |
|------|--------|------|------|
| **NTLMv2** | 中（已逐步弃用 NTLMv1） | 工作组环境、无域 | 3 轮 |
| **Kerberos** | 高（推荐） | AD 域环境 | 1-2 轮 |
| **SPNEGO** | — | 不是独立机制，是**协商包装器** | 1+ 轮 |

> SMB2/3 的 SESSION_SETUP 请求体中 `SecurityBuffer` 字段承载 SPNEGO token。

---

## NTLM 认证

### 流程

```
Client                                         Server
  │                                               │
  │─── SESSION_SETUP ────────────────────────────▶│
  │    SecurityBuffer = NTLMSSP_TYPE1             │
  │    {Domain, Workstation, Flags}               │
  │                                               │
  │◀── SESSION_SETUP (STATUS_MORE_PROCESSING) ───│
  │    SecurityBuffer = NTLMSSP_TYPE2             │
  │    {ServerChallenge, TargetInfo, Flags}       │
  │                                               │
  │    计算 NTLMv2 Response:                      │
  │    ResponseKeyNT = HMAC_MD5(PasswordHash,     │
  │                            User+Domain)       │
  │    NTProofStr  = HMAC_MD5(ResponseKeyNT,      │
  │                            ServerChallenge     │
  │                            + ClientChallenge   │
  │                            + Temp)            │
  │    SessionBaseKey = HMAC_MD5(ResponseKeyNT,   │
  │                               NTProofStr)     │
  │                                               │
  │─── SESSION_SETUP ────────────────────────────▶│
  │    SecurityBuffer = NTLMSSP_TYPE3             │
  │    {NTProofStr, User, Domain, Workstation}   │
  │                                               │
  │◀── SESSION_SETUP (STATUS_SUCCESS) ───────────│
  │    SessionId = S1                             │
  │    后续用 SessionBaseKey 派生签名/加密密钥    │
```

### NTLMv1 vs NTLMv2

| 维度 | NTLMv1 | NTLMv2 |
|------|--------|--------|
| ClientChallenge | 8 字节固定 | 128 位随机 + 时间戳 |
| Response | DES 加密 | HMAC-MD5 |
| 安全性 | ★（已破解） | ★★★ |
| 状态 | **已弃用** | 仍在用但不推荐 |

> **规则**：永远不要实现 NTLMv1。生产环境优先 Kerberos。

---

## Kerberos 认证

### 前置条件

- AD 域环境，客户端和服务端都在域中
- DNS 正确配置（SRV 记录）
- 时间同步（Kerberos 允许 ±5 分钟偏差）
- 服务端有 keytab 文件或使用 `machine account`

### 流程

```
Client                                    KDC            Server
  │                                         │               │
  │─── AS-REQ (User principal) ───────────▶│               │
  │◀── AS-REP (TGT) ──────────────────────│               │
  │                                         │               │
  │─── TGS-REQ (cifs/server.domain) ──────▶│               │
  │◀── TGS-REP (Service Ticket) ──────────│               │
  │                                                         │
  │─── SESSION_SETUP ──────────────────────────────────────▶│
  │    SecurityBuffer = SPNEGO(Kerberos AP-REQ)             │
  │◀── SESSION_SETUP (STATUS_SUCCESS) ────────────────────│
  │    SessionId + SPNEGO(AP-REP)                           │
```

> Kerberos 通常 1 轮 SESSION_SETUP 即完成，因为 AP-REQ 已含服务票据。

### Samba 中配置 Kerberos

```ini
[global]
    security = ADS
    realm = EXAMPLE.COM
    workgroup = EXAMPLE
    kerberos method = secrets and keytab
    # keytab 由 `net ads keytab create` 或 `ktutil` 生成
```

---

## SPNEGO — 协商包装器

SPNEGO（Simple and Protected GSS-API Negotiation Mechanism）不是认证机制，是**协商层**。

```
┌──────────────────────────────────────┐
│ SESSION_SETUP SecurityBuffer         │
│ ┌──────────────────────────────────┐ │
│ │ SPNEGO Token                    │ │
│ │ ┌────────────────────────────┐  │ │
│ │ │ MechTypeList:              │  │ │  ← 客户端支持哪些机制
│ │ │   [Kerberos, NTLMSSP]      │  │ │
│ │ │ MechToken:                 │  │ │  ← 实际认证数据
│ │ │   Kerberos AP-REQ          │  │ │     或 NTLMSSP_TYPE1
│ │ └────────────────────────────┘  │ │
│ └──────────────────────────────────┘ │
└──────────────────────────────────────┘
```

**协商过程**：
1. 客户端提议 MechTypeList（如 Kerberos 优先，NTLM 备选）
2. 服务端选择一种，返回对应 MechToken
3. 若选 Kerberos 且认证成功 → 结束
4. 若选 NTLM → 进入 NTLMSSP 多轮交换

---

## 密钥派生

认证成功后，`SessionBaseKey` 用于派生所有后续安全材料：

```
SessionBaseKey (来自 NTLM 或 Kerberos Session Key)
    │
    ├──► SigningKey  = HMAC-SHA256(SessionBaseKey, "SMB2AESCMAC")
    ├──► EncryptionKey = HMAC-SHA256(SessionBaseKey, "SMB2AESCCM")
    └──► DecryptionKey = HMAC-SHA256(SessionBaseKey, "SMB2AESCCM")
         （SMB 3.1.1 还会用不同的 label 派生 GCM 密钥）
```

---

## 实现建议

1. **先实现 NTLMSSP**（3 轮交互，逻辑清晰，适合理解流程）
2. **再实现 Kerberos**（依赖 GSSAPI 库，如 MIT/Heimdal）
3. **SPNEGO 解析用现成库**（如 GSSAPI 或 libspnego），不要手写 ASN.1
4. **永远不要存明文密码**，只存 NT Hash

---

> 回到 [[Samba学习索引]]
