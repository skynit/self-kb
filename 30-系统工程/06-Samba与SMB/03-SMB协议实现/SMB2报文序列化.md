---
title: SMB2 报文序列化
created: 2026-06-23
updated: 2026-06-23
type: concept
domain: SMB协议实现
tags:
  - OS
  - Samba
  - SMB2
  - 序列化
  - 编解码
---

# SMB2 报文序列化

> 属于 [[Samba学习索引]] | 相关：[[SMB报文格式]]、[[SMB2实现路线图]]

---

## 字节序规则

**所有 SMB2 字段均为小端序（little-endian）**，与网络字节序（大端）相反。

```go
// Go 示例
func putUint16(b []byte, v uint16) { binary.LittleEndian.PutUint16(b, v) }
func putUint32(b []byte, v uint32) { binary.LittleEndian.PutUint32(b, v) }
func putUint64(b []byte, v uint64) { binary.LittleEndian.PutUint64(b, v) }

func readUint16(b []byte) uint16 { return binary.LittleEndian.Uint16(b) }
func readUint32(b []byte) uint32 { return binary.LittleEndian.Uint32(b) }
func readUint64(b []byte) uint64 { return binary.LittleEndian.Uint64(b) }
```

> Direct TCP 传输层的 4 字节 Length 前缀是**大端序**（例外）。

---

## SMB2 头部编解码

### 编码

```go
func EncodeSMB2Header(h *SMB2Header) []byte {
    buf := make([]byte, 64)

    // 0-3: ProtocolId
    copy(buf[0:4], []byte{0xFE, 0x53, 0x4D, 0x42})

    // 4-5: StructureSize
    putUint16(buf[4:6], 64)

    // 6-7: CreditCharge
    putUint16(buf[6:8], h.CreditCharge)

    // 8-11: Status (请求中为0)
    putUint32(buf[8:12], h.Status)

    // 12-13: Command
    putUint16(buf[12:14], h.Command)

    // 14-15: CreditRequest
    putUint16(buf[14:16], h.CreditRequest)

    // 16-19: Flags
    putUint32(buf[16:20], h.Flags)

    // 20-23: NextCommand
    putUint32(buf[20:24], h.NextCommand)

    // 24-31: MessageId
    putUint64(buf[24:32], h.MessageId)

    // 32-35: Reserved / ProcessId
    putUint32(buf[32:36], 0)

    // 36-43: SessionId
    putUint64(buf[36:44], h.SessionId)

    // 44-59: Signature (先填0，签名后覆盖)
    // ...

    return buf
}
```

### 解码

```go
func DecodeSMB2Header(data []byte) (*SMB2Header, error) {
    if len(data) < 64 {
        return nil, errors.New("header too short")
    }
    // 检查 Magic
    if !bytes.Equal(data[0:4], []byte{0xFE, 0x53, 0x4D, 0x42}) {
        return nil, errors.New("invalid SMB2 magic")
    }
    h := &SMB2Header{
        StructureSize: readUint16(data[4:6]),
        CreditCharge:  readUint16(data[6:8]),
        Status:        readUint32(data[8:12]),
        Command:       readUint16(data[12:14]),
        CreditRequest: readUint16(data[14:16]),
        Flags:         readUint32(data[16:20]),
        NextCommand:   readUint32(data[20:24]),
        MessageId:     readUint64(data[24:32]),
        SessionId:     readUint64(data[36:44]),
    }
    copy(h.Signature[:], data[44:60])
    return h, nil
}
```

---

## 变长字段处理

SMB2 请求/响应体中的变长字段用 **Offset + Length** 对引用：

```
规则：
  - Offset 从 SMB2 头部（第0字节）开始计算，不是从请求体开始
  - Offset 必须 >= 64（头部之后）
  - 对齐：通常 8 字节对齐
```

### 示例：NEGOTIATE 请求

```
偏移(从头部算)  大小   字段
64              2      StructureSize (=36)
66              2      DialectCount
68              2      SecurityMode
70              2      Reserved
72              4      Capabilities
76              16     ClientGuid
92              4      NegotiateContextOffset（SMB 3.1.1）
96              2      NegotiateContextCount
98              2      Reserved2
100             2      Dialects Offset (=100+4=104? 不，从头部算)
102             2      Dialects Length
104+            变长   Dialects 数组
```

> 注意：不同命令的变长字段引用方式可能不同，需逐个对照 MS-SMB2。

---

## Direct TCP 帧编解码

### 发送

```go
func SendSMB2(conn net.Conn, payload []byte) error {
    frame := make([]byte, 4+len(payload))
    // Length: 大端序！
    binary.BigEndian.PutUint32(frame[0:4], uint32(len(payload)))
    copy(frame[4:], payload)
    _, err := conn.Write(frame)
    return err
}
```

### 接收

```go
func RecvSMB2(conn net.Conn) ([]byte, error) {
    // 读 4 字节长度
    lenBuf := make([]byte, 4)
    if _, err := io.ReadFull(conn, lenBuf); err != nil {
        return nil, err
    }
    length := binary.BigEndian.Uint32(lenBuf)

    // 验证长度合理
    if length > 8*1024*1024 { // 8MB 上限
        return nil, errors.New("frame too large")
    }

    // 读报文体
    payload := make([]byte, length)
    if _, err := io.ReadFull(conn, payload); err != nil {
        return nil, err
    }
    return payload, nil
}
```

---

## 字符串编码

SMB2 中的字符串一律为 **UTF-16LE**。

```go
func EncodeSMB2String(s string) []byte {
    // Go 的 utf16.Encode 返回 []uint16，需转 LE 字节
    u16s := utf16.Encode([]rune(s))
    buf := make([]byte, len(u16s)*2)
    for i, v := range u16s {
        putUint16(buf[i*2:], v)
    }
    return buf
}

func DecodeSMB2String(data []byte) string {
    if len(data)%2 != 0 {
        data = data[:len(data)-1]
    }
    u16s := make([]uint16, len(data)/2)
    for i := range u16s {
        u16s[i] = readUint16(data[i*2:])
    }
    return string(utf16.Decode(u16s))
}
```

---

## 实现检查清单

- [ ] SMB2 头部编码（小端序、所有字段）
- [ ] SMB2 头部解码（Magic 校验、字段提取）
- [ ] Direct TCP 帧发送（4 字节大端长度 + payload）
- [ ] Direct TCP 帧接收（处理粘包/拆包）
- [ ] UTF-16LE 字符串编解码
- [ ] 变长字段 Offset/Length 正确计算（从头部起算）
- [ ] 对齐填充（8 字节对齐）
- [ ] 单元测试：编码→解码→原值一致
- [ ] Wireshark 对照：编码结果与 Windows 客户端报文对比

---

> 回到 [[Samba学习索引]]
