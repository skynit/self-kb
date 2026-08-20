---
title: NTLMSSP 认证实现
created: 2026-06-23
updated: 2026-06-23
type: concept
domain: SMB协议实现
tags:
  - OS
  - Samba
  - SMB2
  - NTLMSSP
  - 认证
---

# NTLMSSP 认证实现

> 属于 [[Samba学习索引]] | 相关：[[SMB认证机制]]、[[SMB2实现路线图]]

---

## NTLMSSP 状态机

```
         ┌──────────────┐
         │    START     │
         └──────┬───────┘
                │
                ▼
    ┌───────────────────────┐
    │ 生成 Type1 (Negotiate) │
    │ 放入 SESSION_SETUP    │
    │ SecurityBuffer        │
    └───────────┬───────────┘
                │
                ▼
    ┌───────────────────────┐
    │ 收到 Type2 (Challenge)│◀── STATUS_MORE_PROCESSING_REQUIRED
    │ 提取 ServerChallenge │
    │ 提取 TargetInfo      │
    └───────────┬───────────┘
                │
                ▼
    ┌───────────────────────┐
    │ 计算 NTLMv2 Response  │
    │ 生成 Type3 (Auth)     │
    │ 放入 SESSION_SETUP    │
    │ SecurityBuffer        │
    └───────────┬───────────┘
                │
                ▼
         ┌──────────────┐
         │ STATUS_SUCCESS│──▶ 认证完成，获得 SessionId
         └──────────────┘
```

---

## Type1 — Negotiate

```
客户端告诉服务端：我支持哪些能力

偏移  大小  字段
0     8     Signature: "NTLMSSP\0"
8     4     MessageType: 0x00000001 (Type1)
12    4     NegotiateFlags
16    2     DomainNameFieldsLen
18    2     DomainNameFieldsMaxLen
20    4     DomainNameFieldsOffset
24    2     WorkstationFieldsLen
26    2     WorkstationFieldsMaxLen
28    4     WorkstationFieldsOffset
32    变长  DomainName (UTF-16LE)
      变长  Workstation (UTF-16LE)
```

### 关键 NegotiateFlags

| 位 | 名称 | 说明 |
|---|------|------|
| 0x00020000 | NTLMSSP_NEGOTIATE_NTLM | 支持 NTLM |
| 0x00080000 | NTLMSSP_NEGOTIATE_ALWAYS_SIGN | 始终签名 |
| 0x00100000 | NTLMSSP_TARGET_TYPE_SERVER | 目标是服务器 |
| 0x00200000 | NTLMSSP_TARGET_TYPE_DOMAIN | 目标是域 |
| 0x00400000 | NTLMSSP_NEGOTIATE_EXTENDED_SESSIONSECURITY | 扩展会话安全（必选） |
| 0x02000000 | NTLMSSP_NEGOTIATE_UNICODE | Unicode 支持 |
| 0x04000000 | NTLMSSP_NEGOTIATE_128 | 128 位加密 |
| 0x08000000 | NTLMSSP_NEGOTIATE_KEY_EXCH | 密钥交换 |

---

## Type2 — Challenge

```
服务端返回：Challenge + 目标信息

偏移  大小  字段
0     8     Signature: "NTLMSSP\0"
8     4     MessageType: 0x00000002 (Type2)
12    2     TargetNameFieldsLen
14    2     TargetNameFieldsMaxLen
16    4     TargetNameFieldsOffset
20    4     NegotiateFlags
24    8     ServerChallenge ← 核心！用于计算 Response
32    8     Reserved
40    2     TargetInfoFieldsLen
42    2     TargetInfoFieldsMaxLen
44    4     TargetInfoFieldsOffset
48    变长  TargetName
      变长  TargetInfo (AV_PAIR 列表)
```

### AV_PAIR（TargetInfo 中的键值对）

| Id | 名称 | 值 |
|---|------|----|
| 0x0001 | NbComputerName | 服务端计算机名 |
| 0x0002 | NbDomainName | 域名 |
| 0x0003 | DnsComputerName | DNS 计算机名 |
| 0x0004 | DnsDomainName | DNS 域名 |
| 0x0005 | DnsTreeName | DNS 树名 |
| 0x0006 | Flags | 额外标志 |
| 0x0007 | Timestamp | 64 位 FILETIME |
| 0x0008 | Restriction | 限制编码 |
| 0x0009 | TargetName | SPN |
| 0x000A | ChannelBindings | 通道绑定 |

---

## Type3 — Auth

```
客户端发送：用 ServerChallenge 计算的 Response

偏移  大小  字段
0     8     Signature: "NTLMSSP\0"
8     4     MessageType: 0x00000003 (Type3)
12    2     LanManagerResponseFieldsLen
14    2     LanManagerResponseFieldsMaxLen
16    4     LanManagerResponseFieldsOffset
20    2     NtChallengeResponseFieldsLen  ← 核心字段
22    2     NtChallengeResponseFieldsMaxLen
24    4     NtChallengeResponseFieldsOffset
28    2     DomainNameFieldsLen
30    2     DomainNameFieldsMaxLen
32    4     DomainNameFieldsOffset
36    2     UserNameFieldsLen
38    2     UserNameFieldsMaxLen
40    4     UserNameFieldsOffset
44    2     WorkstationFieldsLen
46    2     WorkstationFieldsMaxLen
48    4     WorkstationFieldsOffset
52    2     EncryptedRandomSessionKeyFieldsLen
54    2     EncryptedRandomSessionKeyFieldsMaxLen
56    4     EncryptedRandomSessionKeyFieldsOffset
60    4     NegotiateFlags
64    变长  各字段数据
```

---

## NTLMv2 Response 计算

```
输入：
  - Password（用户密码）
  - User（用户名）
  - Domain（域名，区分大小写）
  - ServerChallenge（Type2 中的 8 字节）
  - TargetInfo（Type2 中的 AV_PAIR 列表）

步骤：

1. 计算 NT Hash
   NTHash = MD4(UTF-16LE(Password))

2. 计算 ResponseKeyNT
   ResponseKeyNT = HMAC_MD5(NTHash, UTF-16LE(UPPER(User) + Domain))

3. 构造 Temp（NTLMv2 Client Challenge）
   Temp = BlobSignature(0x0101)
        + Reserved(0x00000000)
        + Timestamp(FILETIME, 8字节)
        + ClientChallenge(随机8字节)
        + 0x00000000
        + TargetInfo(Type2中的AV_PAIR)
        + 0x00000000

4. 计算 NTProofStr
   NTProofStr = HMAC_MD5(ResponseKeyNT, ServerChallenge + Temp)

5. NtChallengeResponse = NTProofStr + Temp

6. 计算 SessionBaseKey
   SessionBaseKey = HMAC_MD5(ResponseKeyNT, NTProofStr)
```

---

## 密钥派生

认证完成后，SessionBaseKey 用于派生后续所有安全密钥：

```
SMB2 签名密钥（SMB 3.0+）：
  SigningKey = HMAC_SHA256(SessionBaseKey, "SMB2AESCMAC" + "0"×11)

SMB3 加密密钥（SMB 3.0）：
  EncryptionKey = HMAC_SHA256(SessionBaseKey, "SMB2AESCCM" + "0"×11)
  DecryptionKey = HMAC_SHA256(SessionBaseKey, "SMB2AESCCM" + "0"×11)

SMB3 加密密钥（SMB 3.1.1，不同 label）：
  EncryptionKey = HMAC_SHA256(SessionBaseKey, "SMBSigningKey" + PreAuthHash)
  （PreAuthHash = NEGOTIATE 报文的 SHA-512）
```

---

## 在 SESSION_SETUP 中嵌入 NTLMSSP

```
1. 构造 NTLMSSP Type1 字节流
2. 放入 SESSION_SETUP 请求的 SecurityBuffer：
   - SecurityBufferOffset = 从 SMB2 头部算的偏移
   - SecurityBufferLength = NTLMSSP 字节流长度
3. 发送 SESSION_SETUP
4. 检查响应 Status：
   - STATUS_MORE_PROCESSING_REQUIRED → 提取 Type2，继续
   - STATUS_SUCCESS → 认证完成
5. 构造 Type3，放入下一个 SESSION_SETUP 的 SecurityBuffer
6. 保存响应中的 SessionId
```

---

## 实现检查清单

- [ ] NTLMSSP Type1 构造（NegotiateFlags + Domain + Workstation）
- [ ] NTLMSSP Type2 解析（提取 ServerChallenge + TargetInfo）
- [ ] NT Hash 计算（MD4，注意 MD4 不是 MD5）
- [ ] ResponseKeyNT 计算（HMAC-MD5）
- [ ] Temp 构造（BlobSignature + Timestamp + ClientChallenge + AV_PAIR）
- [ ] NTProofStr 计算（HMAC-MD5）
- [ ] SessionBaseKey 计算
- [ ] SMB2 签名密钥派生
- [ ] SESSION_SETUP 多轮状态机
- [ ] 测试：用 Samba 服务端验证完整认证流程

---

> 回到 [[Samba学习索引]]
