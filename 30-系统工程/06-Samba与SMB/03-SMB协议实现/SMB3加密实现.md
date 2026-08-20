---
title: SMB3 加密实现
created: 2026-06-23
updated: 2026-06-23
type: concept
domain: SMB协议实现
tags:
  - OS
  - Samba
  - SMB3
  - 加密
  - AES-CCM
  - AES-GCM
---

# SMB3 加密实现

> 属于 [[Samba学习索引]] | 相关：[[SMB认证机制]]、[[SMB2实现路线图]]

---

## 加密能力协商

NEGOTIATE 阶段声明加密能力：

```
客户端 → 服务端：
  Capabilities 中设置 SMB2_GLOBAL_CAP_ENCRYPTION 位

服务端 → 客户端：
  如果也支持加密，响应中也设置该位

加密策略：
  server smb encrypt = disabled     # 不加密（默认）
  server smb encrypt = desired      # 鼓励加密，允许不加密
  server smb encrypt = required     # 强制加密，拒绝明文
  server smb encrypt = mandatory    # 同 required
```

---

## 算法选择

| SMB 版本 | 加密算法 | 认证标签 | 密钥长度 |
|---------|---------|---------|---------|
| SMB 3.0 | AES-128-CCM | CCM MAC | 128 位 |
| SMB 3.0.2 | AES-128-CCM | CCM MAC | 128 位 |
| SMB 3.1.1 | AES-128-CCM | CCM MAC | 128 位 |
| SMB 3.1.1 | AES-128-GCM | GCM MAC | 128 位 |

SMB 3.1.1 在 NEGOTIATE 中增加 `NegotiateContext` 来协商具体算法：

```
NegotiateContextType:
  0x0001 = SMB2_PREAUTH_INTEGRITY_CAPS（预认证完整性）
  0x0002 = SMB2_ENCRYPTION_CAPS（加密能力）

加密能力列表：
  0x0001 = AES-128-CCM
  0x0002 = AES-128-GCM
```

---

## 密钥派生

### SMB 3.0 / 3.0.2

```
SessionKey = SessionBaseKey (来自 NTLM 或 Kerberos)

EncryptionKey = HMAC_SHA256(SessionKey, "SMB2AESCCM" + "\0"×11)
DecryptionKey = EncryptionKey  ← 相同
```

### SMB 3.1.1

```
PreAuthHash = SHA-512(NEGOTIATE_REQUEST || NEGOTIATE_RESPONSE)
  （累计哈希：第一条哈希，第二条追加后重新哈希）

EncryptionKey = KDF(SessionKey, "SMBC2SCipherKey" + PreAuthHash)
DecryptionKey = KDF(SessionKey, "SMBS2CCipherKey" + PreAuthHash)
  KDF = HMAC_SHA256(key, label + context)
```

> SMB 3.1.1 的加解密密钥**不同**（方向区分）。

---

## Transform Header 报文格式

加密后，原始 SMB2 报文被替换：

```
偏移  大小   字段
0     4      ProtocolId: 0xFD 0x53 0x4D 0x42 ("\xFDSMB")
4     2      OriginalMessageSize（明文报文总长度，含 SMB2 头部）
6     2      Reserved
8     16     EncryptionNonce（随机值）
24    16     OriginalMessageSignature（认证标签）
40    n      EncryptedPayload
```

> Magic 不同：普通 SMB2 是 `\xFESMB`，加密报文是 `\xFDSMB`。

---

## AES-128-CCM 加密过程

```
输入：
  Key    = EncryptionKey (16字节)
  Nonce  = EncryptionNonce (11字节，实际使用)
  AAD    = Transform Header 前 20 字节（ProtocolId + OriginalMessageSize + Reserved）
  Data   = 原始 SMB2 报文（头部 + 请求体，含签名位置先填0）

输出：
  EncryptedPayload = AES-CCM-Encrypt(Key, Nonce, AAD, Data)
  Signature = AES-CCM-MAC(Key, Nonce, AAD, Data)

组装 Transform Header：
  EncryptionNonce = 16字节（前11字节=Nonce，后5字节=0）
  OriginalMessageSignature = Signature
  EncryptedPayload = 加密后的数据
```

### AES-128-GCM（SMB 3.1.1 可选）

流程与 CCM 相同，仅算法替换为 GCM。Nonce 长度通常为 12 字节。

---

## 解密过程

```
收到 Transform Header：

1. 读取 Nonce（前 11 字节）
2. 读取 Signature（16 字节）
3. 读取 EncryptedPayload
4. 构造 AAD = Transform Header 前 20 字节
5. Plaintext = AES-CCM-Decrypt(DecryptionKey, Nonce, AAD, EncryptedPayload, Signature)
6. 验证 Signature
7. 解析明文 SMB2 报文
```

---

## 发送加密报文

```
构造明文 SMB2 报文 → 加密 → 替换为 Transform Header → Direct TCP 帧发送
```

### 代码骨架（Go）

```go
func (c *Session) SendEncrypted(msg *SMB2Message) error {
    // 1. 序列化明文
    plaintext, err := EncodeMessage(msg)

    // 2. 生成 Nonce
    nonce := make([]byte, 16)
    rand.Read(nonce)

    // 3. 构造 AAD（Transform Header 前20字节）
    aad := make([]byte, 20)
    copy(aad[0:4], []byte{0xFD, 0x53, 0x4D, 0x42})
    binary.LittleEndian.PutUint16(aad[4:6], uint16(len(plaintext)))
    // aad[6:20] = 0 (reserved)

    // 4. AES-CCM 加密 + 生成 MAC
    encrypted, mac := aesCCMEncrypt(c.EncryptionKey, nonce[:11], aad, plaintext)

    // 5. 组装 Transform Header
    transform := make([]byte, 40+len(encrypted))
    copy(transform[0:4], []byte{0xFD, 0x53, 0x4D, 0x42})
    binary.LittleEndian.PutUint16(transform[4:6], uint16(len(plaintext)))
    copy(transform[8:24], nonce)
    copy(transform[24:40], mac)
    copy(transform[40:], encrypted)

    // 6. Direct TCP 发送
    return SendFrame(c.conn, transform)
}
```

---

## 实现检查清单

- [ ] NEGOTIATE 中声明加密能力
- [ ] 加密算法协商（CCM 优先，3.1.1 可选 GCM）
- [ ] 密钥派生（区分 3.0 和 3.1.1）
- [ ] AES-128-CCM 加密/解密（可调用 crypto/aes + cipher）
- [ ] Transform Header 组装（Magic `\xFDSMB`）
- [ ] Nonce 生成（随机 + 不重复）
- [ ] AAD 构造（前 20 字节）
- [ ] Signature 验证
- [ ] 测试：用 Wireshark + `debug encryption = yes` 对照
- [ ] 性能：AES-GCM 比 CCM 快（如有硬件加速）

---

> 回到 [[Samba学习索引]]
