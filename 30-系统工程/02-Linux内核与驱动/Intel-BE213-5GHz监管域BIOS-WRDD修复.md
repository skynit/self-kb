---
title: Intel BE213 5 GHz 监管域 BIOS WRDD 修复
created: 2026-08-18
updated: 2026-08-20
type: procedure
tags:
  - BIOS
  - UEFI
  - iwlwifi
  - BE213
  - regulatory
---

# Intel BE213 5 GHz 监管域 BIOS WRDD 修复

> [!danger] 风险边界
> 本文记录的是 BIOS 镜像静态修改与校验，不包含刷写步骤。生成的镜像已通过结构化重新提取和校验和检查，但尚未证明主板的刷写程序会接受它，也未进行实体启动验证。刷写前必须保留原始完整 BIOS，并准备硬件编程器恢复方案。

## 修复流程

### 1. 固定原始镜像并记录哈希

本次以 `1608改ap2` 的 32 MiB 镜像为基础：

```bash
ORIGINAL='/home/skynit/Downloads/bios2/1608改ap2/Y6808051220.bin'
```

```bash
sha256sum "$ORIGINAL"
```

已观测结果：

```text
fdd915b1be06a5c36ffe0cc8cbef639dcb2f28ce9ee3b42d493b3a633dde558a  Y6808051220.bin
```

### 2. 提取并定位 `CnvUefiConfigVariables`

使用 `UEFIExtract NE alpha 75` 完整提取镜像：

```bash
/tmp/uefitool-a75/uefiextract "$ORIGINAL" all
```

在报告和 GUID 数据库中定位目标模块：

```bash
rg -n -i 'A4578B9E-C666-4161-A168-159FF541ACD3|CnvUefiConfigVariables' "${ORIGINAL}.report.txt" "${ORIGINAL}.guids.csv"
```

目标为：

```text
Name: CnvUefiConfigVariables
GUID: A4578B9E-C666-4161-A168-159FF541ACD3
Section: PE32 image, type 0x10
```

从 `.dump` 目录取出它的 PE32 `body.bin`，本次原始 PE 哈希为：

```text
2ac0e403cfa2f3fe605fcfae20c8d3a51899915513222f4e35445293ed9dd894
```

### 3. 修改 WRDD MCC

反汇编后找到唯一的默认 WRDD 赋值：

```asm
mov DWORD PTR [rbp+0x111],0x4150
```

`0x4150` 表示 `AP`。将其改为 ISO 3166-1 alpha-2 国家码 `CN` 对应的 `0x434e`：

```asm
mov DWORD PTR [rbp+0x111],0x434e
```

PE 文件偏移为 `0xA90`，只修改两个字节：

```text
50 41 -> 4E 43
```

修改后的 PE 为：

```text
/tmp/CnvUefiConfigVariables-WRDD-CN.pe
SHA-256: 8a280c03ae39213535c8af22d45b69fd23cf45ca5ae2c450c0618194a36830ed
```

用反汇编再次确认语义：

```bash
objdump -d -Mintel /tmp/CnvUefiConfigVariables-WRDD-CN.pe | rg '\[rbp\+0x111\]|0x434e|0x4150'
```

### 4. 替换 PE32 section

使用 `UEFIReplace 0.28.0` 替换指定 GUID 内的 PE32 section：

```bash
/tmp/uefireplace-028/UEFIReplace "$ORIGINAL" A4578B9E-C666-4161-A168-159FF541ACD3 10 /tmp/CnvUefiConfigVariables-WRDD-CN.pe -o /tmp/Y6808051220-WRDD-CN.bin
```

`10` 是 PE32 section 类型 `0x10`。`UEFIReplace` 会重建相关的嵌套固件卷，所以整个 BIOS 镜像的差异不会只有两个字节。

### 5. 保留原 Descriptor/ME 区域

第一次生成的候选镜像在 BIOS 区域之外也出现了差异：

```text
changed_bytes=3,226,827
first difference=0x1000
last difference=0x15A70A3
BIOS region starts=0x1200000
```

这个候选文件不能直接交付。已解析的 32 MiB 镜像布局为：

| 区域 | 起始偏移 | 大小 |
| --- | ---: | ---: |
| Descriptor | `0x0000000` | `0x1000` |
| ME | `0x0004000` | `0x8F4000` |
| BIOS | `0x1200000` | `0xE00000` |

复制候选镜像，然后将前 `0x1200000` 字节（18 MiB）恢复为原镜像内容：

```bash
cp -- /tmp/Y6808051220-WRDD-CN.bin /tmp/Y6808051220-WRDD-CN-prefix-preserved.bin
```

```bash
dd if="$ORIGINAL" of=/tmp/Y6808051220-WRDD-CN-prefix-preserved.bin bs=1M count=18 conv=notrunc status=progress
```

这一步保证 Descriptor、ME 及 BIOS 前的其他原始前缀不受旧版 `UEFIReplace` 重建行为影响。

## 校验

### 1. 大小和区域边界

最终文件必须仍为 32 MiB：

```bash
stat -c '%s' /tmp/Y6808051220-WRDD-CN-prefix-preserved.bin
```

已观测输出：

```text
33554432
```

前 `0x1200000` 字节必须与原镜像完全一致：

```bash
cmp -n 18874368 "$ORIGINAL" /tmp/Y6808051220-WRDD-CN-prefix-preserved.bin
```

命令无输出且退出码为 `0` 时才算通过。最终差异统计为：

```text
changed=3,226,223
first=0x12900A0
last=0x15A70A3
```

所有差异都已限定在 `0x1200000` 之后的 BIOS 区域。

### 2. 重新提取和 FFS 校验和

对最终镜像重新执行完整提取：

```bash
/tmp/uefitool-a75/uefiextract /tmp/Y6808051220-WRDD-CN-prefix-preserved.bin all
```

`CnvUefiConfigVariables` 已观测的关键结果：

```text
File GUID: A4578B9E-C666-4161-A168-159FF541ACD3
Header checksum: valid
Data checksum: valid
```

重新提取的 PE 哈希与目标补丁 PE 完全一致：

```text
8a280c03ae39213535c8af22d45b69fd23cf45ca5ae2c450c0618194a36830ed
```

将原 PE 与补丁 PE 执行 `cmp -l`，只有两个差异位置：

```text
2705 120 116
2706 101 103
```

`cmp -l` 的位置从 1 开始，字节值以八进制显示，对应 PE 偏移 `0xA90–0xA91` 的 `50 41 -> 4E 43`。

### 3. 保留 `CnvDxe`

`CnvDxe` 的 Raw section 与 `1608改ap2` 完全一致：

```text
SHA-256: 721305b43d651547815561acf937065bef7534a3fe1b0bb024283877fc2a01a6
```

因此 `1608改ap2` 中已有的 `CnvDxe` Function 8=`0x1F` 修改被保留，本次只改变了 `CnvUefiConfigVariables` 中的 WRDD MCC 语义。

### 4. 最终交付物

```text
/home/skynit/Downloads/bios3/Y6808051220.bin
Size: 33,554,432 bytes
SHA-256: 064559d017778c014c71e3dd00a42e0ab56ce1f184f86e34215116e61b9d3be6
```

## 修复原理

实机中的 Intel 无线 PHY 使用 self-managed 监管域。刷入 `1608改ap2` 后，`iw reg get` 已能显示 `phy#0 country CN`，但信道 36–48 仍带有 `no IR`，因此仅看到 `CN` 不能证明 BIOS 的 WRDD 已正确设置，也不能证明 5 GHz AP 已获准发射。

进一步检查发现，实机 UEFI 变量和原始 BIOS 镜像中的 `CnvUefiConfigVariables` 都写入 `AP`；`AP` 在这里是 MCC 字段的两个字符，不是 Access Point 模式。镜像中的 `CnvDxe` 已包含 Function 8=`0x1F`，所以本次修复保留 Function 8，只把 WRDD MCC 从 `AP` 改为法定国家码 `CN`，使平台提供的数据内部一致。

> [!important] 证据边界
> 这是针对 BIOS 数据不一致的静态修复候选，不是“解除任意频率限制”的补丁。最终是否清除 5 GHz `no IR`，仍取决于 BIOS、`iwlwifi`、固件和 op mode 的组合。当前镜像尚未经过实体刷写、启动和 AP 发射验证。

这与 `c102/c103/c106` 固件 API 版本是两个层面：

- `c102/c103/c106` 决定驱动与无线固件是否匹配并成功加载。
- WRDD MCC 是驱动从平台获得监管信息的输入之一；动态 LAR 仍可能让 `iw reg get` 显示另一个国家码。

因此，仅切换 `c102` 不是 BIOS WRDD 修复。此前的 `c102` 测试也不构成有效结论，因为 `/lib/firmware/intel/iwlwifi/` 和 `/lib/firmware/iwlwifi/` 中仍存在可被优先加载的压缩 `c103.ucode.zst`。

## 相关笔记

- [[Intel-BE213-WiFi7-驱动安装与测试手册]]
- [[Ubuntu内核补丁]]
