---
title: Linux WiFi 协议栈与无线驱动开发
created: 2026-07-05
updated: 2026-07-05
type: concept
domain: Linux无线驱动
tags:
  - Linux
  - wifi
  - mac80211
  - cfg80211
  - wireless
  - 驱动开发
source: "Alexis Lothoré (Bootlin), 'Unpacking the Linux WiFi Stack: Writing and Integrating Wireless Drivers', OSSEU 2025 — 基于完整演讲记录整理"
---

# Linux WiFi 协议栈与无线驱动开发

> 来源: Alexis Lothoré (Bootlin), OSSEU 2025
> 幻灯片: [bootlin.com/pub/conferences/2025/elce/lothore-80211.pdf](https://bootlin.com/pub/conferences/2025/elce/lothore-80211.pdf)

---

## 前置知识速补

**WiFi 到底是什么？** 没有你想的那么神秘——WiFi 是品牌名，背后是 **802.11 标准**（IEEE 制定）。这个标准只定义了 OSI 七层模型中的最下两层：

```
┌──────────────────────────────┐
│  IP / TCP / UDP ...          │  ← 上层协议，WiFi 驱动不管这些
├──────────────────────────────┤
│  802.3 (Ethernet 帧)         │  ← 转换后变成这个（给网络协议栈）
├────── 802.11 标准负责 ───────┤
│  MAC 层 (802.11 帧格式)      │  ← 管理帧/控制帧/数据帧，关联、认证
│  PHY 层 (无线电调制)         │  ← 频率、信道、调制方式、发射功率
└──────────────────────────────┘
```

**驱动的任务**：把 WiFi 芯片收到的 802.11 无线电帧 → 转成 Ethernet 帧 → 交给 Linux 网络栈。反过来同理。

**关键认知**："WiFi 7" 不是全新标准，而是 802.11 标准的第 7 个修订版。每个修订版都在之前的基础上改进（更快、更远、更稳）。你的芯片可能只支持到 802.11ac（WiFi 5），所以驱动里的能力标志要如实告知内核。

---

## 一、Linux WiFi 协议栈全景架构

```                                                                              用户空间
┌──────────────────────────────────────────────────────────────┐
│                        用户空间                              │
│                                                              │
│   iw          wpa_supplicant       hostapd                  │
│   ""          ""                   ""                        │
│   命令行       客户端认证守护进程     AP模式守护进程             │
│   配置工具      (连WiFi用它)         (开热点用它)              │
│     │              │                  │                       │
│     └──────────────┼──────────────────┘                       │
│                    │  nl80211 (Netlink 协议族)                 │
│                    │  ""                                     │
│                    │  用户空间↔内核空间的通信方式              │
│                    │  所有 WiFi 命令和事件都走这条路            │
├────────────────────┼──────────────────────────────────────────┤
│                    ▼             内核空间                     │
│   ┌────────────────────────────────────┐                     │
│   │           cfg80211                 │  ← 配置/管理层       │
│   │           ""                       │                     │
│   │   WiFi 驱动的"总前台"，负责：       │                     │
│   │   · 监管域 (regulatory)            │  不同国家允许的信道不同│
│   │   · 扫描/认证/关联 状态机          │  管理连接全流程      │
│   │   · 虚拟接口 (wdev) 管理           │  一个芯片可建多个 wlan│
│   │   · 密钥管理                       │  WPA2/WPA3 密钥分发 │
│   └──────────────┬─────────────────────┘                     │
│                  │                                           │
│       ┌──────────┴──────────┐                                │
│       │                     │                                │
│  ┌────▼────────┐   ┌───────▼──────────┐                     │
│  │  mac80211   │   │   FullMAC 驱动    │                     │
│  │  (softMAC)  │   │   (直连 cfg80211) │                     │
│  │  ""         │   │   ""              │                     │
│  │  软件的MAC层│   │   MAC层在硬件里   │                     │
│  │  内核替你实 │   │   驱动直接和      │                     │
│  │  现了复杂部 │   │   cfg80211 对话   │                     │
│  │  分          │   │                   │                     │
│  │             │   │                   │                     │
│  │ · 帧转换      │   │ · 硬件/固件做 MLME│                    │
│  │ · 加密        │   │ · 实现 cfg80211_ops│                   │
│  │ · 省电        │   │ · 约128 个回调      │                    │
│  │ · 速率控制    │   │                    │                     │
│  └──────┬───────┘   └───────────────────┘                    │
│         │                                                    │
│  ┌──────▼──────────────────────────┐                         │
│  │     SoftMAC 驱动 (你写的代码)    │                         │
│  │     ""                          │                         │
│  │     实现 ieee80211_ops          │                         │
│  │     ~102 个回调中挑 8 个必选     │                         │
│  └────────────┬─────────────────────┘                         │
│               │ 你的驱动通过 PCI / USB / SDIO 和芯片通信        │
│               ▼                                               │
│  ┌─────────────────────────────────┐                         │
│  │     硬件 (WiFi 芯片)             │                         │
│  │     内部有固件 (firmware)        │                         │
│  └─────────────────────────────────┘                         │
└──────────────────────────────────────────────────────────────┘
```

**理解每一层的关系**（类比餐厅）：

| 层                   | 类比         | 说明                 |
| ------------------- | ---------- | ------------------ |
| hardware            | 厨房（锅、灶、食材） | 芯片实物               |
| driver (你的代码)       | 厨师助手       | 洗菜切菜 → 告诉主厨 "料备好了" |
| mac80211            | 主厨         | 做菜、调味、装盘 —— 最复杂的活  |
| cfg80211            | 服务员        | 接单、传菜、收钱 —— 外部接口   |
| nl80211             | 点菜系统       | 食客 ↔ 服务员之间的通信方式    |
| iw / wpa_supplicant | 食客         | 提需求 "来份牛排"         |

**你写的驱动 = 厨师助手**：不需要懂怎么做菜（那是 mac80211 的事），只需要知道如何把材料的生的变成熟的半成品（从芯片取数据 / 把数据送进芯片）。

---

## 二、SoftMAC vs FullMAC — 两种驱动模型

**你的 WiFi 芯片是哪种？决定驱动架构。**

```
SoftMAC 驱动                           FullMAC 驱动
═══════════                           ════════════

MAC 层在哪 → 内核 mac80211              MAC 层在哪 → 芯片固件
              │                                    │
        实现 ieee80211_ops                 实现 cfg80211_ops
        只需填 8 个必选项                  需要自己处理更多
              │                                    │
        调用 ieee80211_alloc_hw()         调用 wiphy_new()
        调用 ieee80211_register_hw()       调用 wiphy_register()
              │                             还需自己创建 net_device
              ▼                                    │
        内核帮你做:                               ▼
        · 帧格式转换 (802.11→802.3)            需自己做:
        · 加密解密                             · 帧格式转换
        · 速率控制 (Minstrel 算法)             · 扫描
        · 省电管理                             · 连接/断开
        · 注册网络接口 wlan0                   · 密钥管理
```

| 维度       | SoftMAC                   | FullMAC                      |
| -------- | ------------------------- | ---------------------------- |
| 驱动回调     | `struct ieee80211_ops`    | `struct cfg80211_ops`        |
| MLME 位置  | 内核 mac80211               | 硬件/固件                        |
| 你写代码的工作量 | 回调多 (~102) 但可选填，实际只写 ~8 个 | 回调少但每个都要自己实现                 |
| 灵活性      | 高（内核控制一切，bug 可以更新内核修复）    | 低（硬件怎么做就怎么做，bug 靠厂商推固件）      |
| 典型芯片     | ath9k, mt76, rtw88        | brcmfmac (博通), mwifiex (马威尔) |
| CPU 消耗   | MAC 层在 CPU 上跑（低端芯片可能吃力）   | 硬件处理 MAC（省 CPU）              |
| 当前主流     | ⭐ 大多数新驱动选 SoftMAC         | 少数                           |

> **结论**：如果你拿到的芯片支持 SoftMAC（vendor 提供的 SDK 里有 `ieee80211_ops` 结构体）→ 用它。内核社区也更欢迎 SoftMAC 驱动，因为 MAC 层逻辑在内核中统一维护，不依赖闭源固件。

---

## 三、重要概念：物理设备 ≠ 网络接口

```
┌───────────────────────────────────────────────┐
│  iw phy     → 看到的 "物理芯片"                │
│               一块 WiFi 芯片 = 一个 phy         │
│               如 wiphy0, wiphy1               │
│                                               │
│  ip link    → 看到的 "网络接口" = 虚拟接口      │
│               一个 phy 上可以有 0 个或多个 vif   │
│               如 wlan0, wlan1, mon0            │
│                                               │
│  类比: phy = 打印机, vif = 打印任务             │
│        一台打印机可以同时处理多个打印任务         │
└───────────────────────────────────────────────┘
```

**代码中的体现**：

- `struct ieee80211_hw` = 代表一个 phy（硬件）
- `struct ieee80211_vif` = 代表一个虚拟接口（上面跑的 wlan0）
- `struct wiphy` = cfg80211 层的物理设备描述符
- `struct wireless_dev` = cfg80211 层的虚拟接口描述符

**软 MAC 驱动不需要自己创建 net_device**：`ieee80211_register_hw()` 会自动为你注册第一个虚拟接口（wlan0）。用户可以在上面添加更多接口（如 monitor 模式抓包用）。

---

## 四、SoftMAC 驱动核心数据结构（逐字段详解）

### 4.1 struct ieee80211_hw — 描述你的硬件能力

> 这是你的驱动和 mac80211 之间的"合同"：你告诉内核你的芯片**能做什么、不能做什么**。

```c
struct ieee80211_hw {
    // ══ 能力标志集 ══
    // 这是最关键的部分 —— 每个 flag 告诉 mac80211 "这个活你做还是我做"：
    //
    //   设了这个 flag  → "内核你帮我做这个"
    //   没设这个 flag  → "我的硬件自己搞定"
    //
    u32 flags;
    // 常用 flag 详解:
    //   IEEE80211_HW_SIGNAL_DBM
    //     → 你汇报的 RSSI（信号强度）单位是 dBm
    //       如果没设这个 flag，内核假设你给的是百分比
    //
    //   IEEE80211_HW_SUPPORTS_HT_CCK_RATES
    //     → 你的硬件在使用 HT (802.11n) 时，仍然支持老式的 CCK 速率 (1/2/5.5/11 Mbps)
    //       这个要设，大部分芯片都支持
    //
    //   IEEE80211_HW_QUEUE_CONTROL
    //     → "我自己管队列"，不要 mac80211 帮我调度
    //       新手别设 —— 让内核管队列，省事
    //
    //   IEEE80211_HW_SUPPORTS_VHT
    //     → 支持 Very High Throughput = 802.11ac (WiFi 5)
    //
    //   IEEE80211_HW_SUPPORTS_HE
    //     → 支持 High Efficiency = 802.11ax (WiFi 6)
    //
    //   IEEE80211_HW_SUPPORTS_PS
    //     → 支持 Power Save（省电模式）
    //
    //   IEEE80211_HW_MFP_CAPABLE
    //     → 支持 Management Frame Protection（管理帧保护）

    // ══ 速率和重试 ══
    u8 max_rates;              // 一次传输能用的最大速率数
                               // 填 4 差不多够用
                               // 为什么是 4？Minstrel 算法会给每个数据包选
                               // 几个备选速率，硬件按顺序试，直到成功

    u8 max_rate_tries;         // 每个速率最多重试几次
                               // 填 7 比较常见
                               // 总共可重试 max_rates × max_rate_tries 次

    // ══ AMPDU 聚合 (802.11n/ac/ax 的关键性能特性) ══
    // 就是把多个小包打包成一个大包一起发，减少无线空口开销
    // 但你的硬件缓冲区大小有限定，所以设个上限
    u8 max_rx_aggregation_subframes;  // RX 方向最多聚合多少子帧
    u8 max_tx_aggregation_subframes;  // TX 方向最多聚合多少子帧

    // ══ 硬件队列 ══
    u16 queues;                // 你的硬件有几个 TX 队列
                               // 至少填 1（单队列）
                               // 4 是典型值（对应 WiFi 的 4 个 AC 类）

    u8 max_tx_fragments;       // TX 方向最多拆分几片
                               // 填 1 = 不支持分片

    // ══ 额外通道和天线 ══
    // 通过 max_rates/max_rate_tries 没法设置的更细粒度的限制
    // 在这个结构体里用 u16 vht_capabilities[];
    //             u16 he_capabilities[]; ...
};
```

### 4.2 关键概念：struct wiphy — 无线物理设备的"能力清单"

`hw->wiphy` 已经由 `ieee80211_alloc_hw()` 分配好了，你只需要填属性：

```c
// interface_modes: 你的芯片能当什么角色？
//   本质是位掩码 (bitmask)，用 BIT(宏) 组合:
//      BIT(NL80211_IFTYPE_STATION)    → 能当客户端 (连接 WiFi)
//      BIT(NL80211_IFTYPE_AP)         → 能当热点 (开 WiFi)
//      BIT(NL80211_IFTYPE_MONITOR)    → 能抓包 (监控模式)
//      BIT(NL80211_IFTYPE_P2P_CLIENT) → 能 P2P 直连
//      BIT(NL80211_IFTYPE_MESH_POINT) → 能组 Mesh 网

// bands: 你的芯片支持哪些频段？
//   每个 band 包含:
//     channels[]  → 这个频段有哪些信道可用
//     bitrates[]  → 支持的速率列表 (Mbps)
//     ht_cap      → 802.11n 能力
//     vht_cap     → 802.11ac 能力
```

### 4.3 struct ieee80211_ops — 你真正要实现的函数

> 这是 mac80211 调用你的驱动的方式。约 102 个回调，**新手只需实现 8 个必选**。

```c
struct ieee80211_ops {
    // ════════════════════════════════════════════════════
    // 8 个必须实现的最小集（少了注册会失败）
    // ════════════════════════════════════════════════════

    // ── 1/2. 生命周期 ──
    //   核心在"第一个虚拟接口上来时"调 start → 你有机会初始化硬件
    //   核心在"最后一个虚拟接口下去时"调 stop  → 你有机会关闭硬件
    int  (*start)(struct ieee80211_hw *hw);
    void (*stop)(struct ieee80211_hw *hw);
    //   为什么不在 probe 里初始化？
    //   → probe 时设备刚被检测到，可能永远没人用
    //   → 节能：如果没人联网，就不加载固件、不开启无线电
    //   → 维护者审查时如果看到 probe 里初始化固件，会被要求改掉

    // ── 3/4. 虚拟接口管理 ──
    //   核心说"我要加一个 wlan1" → add_interface 被调
    //   核心说"我要删 wlan1"    → remove_interface 被调
    //   你通常只在这里维护一个列表 + 通知固件（如果固件需要知道接口变化）
    int  (*add_interface)(struct ieee80211_hw *hw,
                          struct ieee80211_vif *vif);
    //     参数 vif:
    //       vif->type  → NL80211_IFTYPE_STATION / _AP / _MONITOR
    //       vif->addr  → MAC 地址
    //       vif->drv_priv → 驱动私有数据（可以存你自己的东西）
    void (*remove_interface)(struct ieee80211_hw *hw,
                             struct ieee80211_vif *vif);

    // ── 5. 核心的发送回调 ──
    //   这是 PUSH 模型：mac80211 把装好 802.11 帧的 skb "推"给你
    //   你的工作: 把这个 skb 数据通过 DMA 发给硬件
    //   发完之后: 异步通知结果 → ieee80211_tx_status() （在 TX 完成中断里调）
    void (*tx)(struct ieee80211_hw *hw,
               struct ieee80211_tx_control *control,
               struct sk_buff *skb);
    //   skb 里面是什么？
    //     skb->data  → 完整的 802.11 帧（不需要你构造 MAC 头）
    //     skb->cb    → 控制信息 struct ieee80211_tx_info
    //       - tx_info->control.rates[]: 发送速率
    //       - tx_info->control.hw_queue: 用哪个硬件队列
    //       - tx_info->flags: 各种标志位

    // ── 6. 配置更改 ──
    //   核心说"信道 / 功率 / HT模式变了" → config 被调
    //   changed 参数是位掩码，看哪个变就处理哪个:
    //      IEEE80211_CONF_CHANGE_CHANNEL → 切信道
    //      IEEE80211_CONF_CHANGE_POWER   → 调功率
    //      IEEE80211_CONF_CHANGE_PS      → 省电模式变化
    int  (*config)(struct ieee80211_hw *hw, u32 changed);

    // ── 7. 帧过滤 ──
    //   核心说"我不需要某类帧了，你帮我直接丢掉别上报"
    //   你的工作: 在硬件上设置 RX 过滤寄存器（如果芯片支持）
    //   为什么？节省 CPU —— 丢弃不需要的帧比收上来再丢掉更快
    void (*configure_filter)(struct ieee80211_hw *hw,
                             unsigned int changed_flags,
                             unsigned int *total_flags,
                             u64 multicast);

    // ── 8. TX/RX 补充 ──
    //   还有一个必须的 TX 相关操作: wake_tx_queue
    //   但通常用 mac80211 提供的默认实现就好，新手不用自己写
    void (*wake_tx_queue)(struct ieee80211_hw *hw,
                          struct ieee80211_txq *txq);

    // ════════════════════════════════════════════════════
    // 以下是有"8个必选"之外的常用回调（按需实现）
    // ════════════════════════════════════════════════════

    // ── BSS 信息变化 ──
    //   关联成功 / 断开关联时调用
    //   你会收到: BSSID (AP的MAC地址)、是否启用加密、信标间隔等
    void (*bss_info_changed)(struct ieee80211_hw *hw,
                             struct ieee80211_vif *vif,
                             struct ieee80211_bss_conf *info,
                             u64 changed);

    // ── 密钥 ──
    //   wpa_supplicant 完成 4-way handshake 后，通过 nl80211 下发密钥
    //   cmd = SET_KEY  → 把密钥写到硬件密钥表
    //   cmd = DISABLE_KEY → 删除密钥
    int  (*set_key)(struct ieee80211_hw *hw,
                    enum set_key_cmd cmd,
                    struct ieee80211_vif *vif,
                    struct ieee80211_sta *sta,
                    struct ieee80211_key_conf *key);

    // ── 硬件扫描（如果你的芯片能自己扫描，可以实现这个）──
    //   如果留空，mac80211 会用软件方式扫描（一条一条信道手动切）
    int  (*hw_scan)(struct ieee80211_hw *hw,
                    struct ieee80211_vif *vif,
                    struct ieee80211_scan_request *req);
    void (*cancel_hw_scan)(struct ieee80211_hw *hw,
                           struct ieee80211_vif *vif);

    // ── AMPDU 聚合 ──
    //   创建 / 销毁 AMPDU 聚合会话
    int  (*ampdu_action)(struct ieee80211_hw *hw,
                         struct ieee80211_vif *vif,
                         struct ieee80211_ampdu_params *params);

    // ── 更多回调（新手暂时不需要关心）──
    //   get_txpower, get_survey, set_tim, flush,
    //   channel_switch, remain_on_channel, ...
};
```

---

## 五、初始化流程 — 一步步带解释

```c
// ═══════════════════════════════════════════════════════
// 步骤概览 (记忆版):
//   1. alloc_hw  → 分配内存 (ieee80211_hw + 你的私有结构体)
//   2. 设置 flags → 告诉内核你的芯片能做什么
//   3. 设置 wiphy → 接口类型、频段、信道
//   4. 设置 bands → 详细能力 (支持的速率列表、HT/VHT 参数)
//   5. register   → 正式让内核认识你的设备。此后回调随时可能被调。
// ═══════════════════════════════════════════════════════

#include <linux/module.h>       // module_init / module_exit 宏
#include <linux/pci.h>          // PCI 总线函数 (你的芯片可能通过 USB/SDIO 连接)
#include <net/mac80211.h>       // ieee80211_alloc_hw / ieee80211_register_hw

// ══ 第一步: 定义驱动私有数据结构 ══
//   这个结构体里放"你自己需要的数据" — 不归 mac80211 管
//   比如: 硬件寄存器的内存映射地址、固件状态、DMA 缓冲区、锁...
struct my_priv {
    void __iomem *mmio;        // 设备 MMIO 基址 (通过 ioremap 映射)
    struct tasklet_struct rx_tasklet; // RX 下半部 (中断后延迟处理)
    struct sk_buff_head tx_queue;     // TX 发送队列
    struct mutex lock;          // 保护私有数据的互斥锁
    bool hw_running;            // 硬件是否在工作
};

// ══ 第二步: 实现最小回调集 ══
//   这里只写出函数声明，为了清晰。实际代码每个都有几十到几百行。

static int  my_start(struct ieee80211_hw *hw);
//   → 加载固件、注册中断、启动硬件
//   → 在此之后 iw dev wlan0 up 才能成功
static void my_stop(struct ieee80211_hw *hw);
//   → 卸载固件、停止硬件、注销中断
//   → 在此之后可以安全卸载模块
static int  my_add_interface(struct ieee80211_hw *hw,
                             struct ieee80211_vif *vif);
//   → 记录 vif 到私有列表；如固件需要，告知固件
static void my_remove_interface(struct ieee80211_hw *hw,
                                struct ieee80211_vif *vif);
//   → 从私有列表移除；如固件需要，告知固件
static void my_tx(struct ieee80211_hw *hw,
                  struct ieee80211_tx_control *ctrl,
                  struct sk_buff *skb);
//   → 把 skb 数据通过 DMA 发给硬件
//   → 异步等待 TX 完成中断 → ieee80211_tx_status(hw, skb)
static int  my_config(struct ieee80211_hw *hw, u32 changed);
//   → 根据 changed 掩码更新硬件设置 (信道/功率/ht模式...)
static void my_configure_filter(struct ieee80211_hw *hw,
                                unsigned int changed,
                                unsigned int *total, u64 mcast);
//   → 更新 RX 过滤规则

// ══ 第三步: 组装 ops 结构体 ══
//   这是一个"函数指针表"——内核通过这张表调用你的驱动
static const struct ieee80211_ops my_ops = {
    .start             = my_start,
    .stop              = my_stop,
    .add_interface     = my_add_interface,
    .remove_interface  = my_remove_interface,
    .tx                = my_tx,
    .config            = my_config,
    .configure_filter  = my_configure_filter,
    // wake_tx_queue 不填 → mac80211 用默认实现
};

// ══ 第四步: probe 函数 (PCI/USB 设备被发现时调用) ══
static int my_probe(struct pci_dev *pdev, const struct pci_device_id *id)
{
    struct ieee80211_hw *hw;
    struct my_priv *priv;
    int err;

    // --- 4.1 分配硬件结构体 ---
    //     第一个参数 sizeof(*priv): "给我额外的内存放我自己的数据结构"
    //     第二个参数 &my_ops:      "这是我的驱动函数表"
    //     返回的 hw 中: hw->priv = 驱动私有数据的起始地址
    hw = ieee80211_alloc_hw(sizeof(*priv), &my_ops);
    if (!hw)
        return -ENOMEM;    // 内存分配失败，probe 失败

    priv = hw->priv;       // 拿到私有数据指针

    // --- 4.2 设置父子关系 (用于 sysfs 和电源管理) ---
    SET_IEEE80211_DEV(hw, &pdev->dev);
    // 等价于: hw->parent = &pdev->dev
    // 为什么需要？设备管理器 (udev) 需要知道 WiFi 设备挂在哪个 PCI 设备下

    // --- 4.3 设置设备能力 ---
    hw->flags = IEEE80211_HW_SIGNAL_DBM |          // RSSI 用 dBm 报告
                IEEE80211_HW_SUPPORTS_HT_CCK_RATES; // 支持 HT+老速率
    hw->max_rates      = 4;   // 最多 4 个速率备选
    hw->max_rate_tries = 7;   // 每个速率重试最多 7 次
    hw->queues          = 4;   // 4 个硬件 TX 队列 (对应 WMM 的 4 个 AC)

    // --- 4.4 设置 wiphy ---
    //     hw->wiphy 已经由 alloc_hw 分配好了，直接填属性
    hw->wiphy->interface_modes =
        BIT(NL80211_IFTYPE_STATION) |   // 能当 WiFi 客户端
        BIT(NL80211_IFTYPE_AP);         // 能当 WiFi 热点
    // BIT(值) = 1 << 值 — 用于位掩码
    // BIT(0) = 1, BIT(1) = 2, BIT(2) = 4...

    // --- 4.5 填充频段信息 ---
    //     以 2.4GHz 为例 (5GHz 同理)
    hw->wiphy->bands[NL80211_BAND_2GHZ] = &my_band_2ghz;

    // 信道列表: 告诉内核你的硬件能在这些频率上工作
    //   例: 2.4GHz 频段信道 1-11 (给 FCC/美国)
    //       每个信道 = 中心频率 + 标志(是否可用/是否被动扫描...)
    my_band_2ghz.channels   = my_channels_2ghz;
    my_band_2ghz.n_channels = ARRAY_SIZE(my_channels_2ghz);

    // 基础速率列表: 802.11b/g/n 都支持的"最低共同速率"
    //   例: {1, 2, 5.5, 11} Mbps (802.11b) +
    //       {6, 9, 12, 18, 24, 36, 48, 54} Mbps (802.11g)
    my_band_2ghz.bitrates   = my_rates_2ghz;
    my_band_2ghz.n_bitrates = ARRAY_SIZE(my_rates_2ghz);

    // --- 4.6 (可选) 设置 HT/VHT 能力 ---
    //     在 my_band_2ghz.ht_cap 和 .vht_cap 中填能力
    // 	 如果不支持 HT/VHT → 留空即可

    // --- 4.7 准备 RX 路径 ---
    //     不是在这里初始化，而是注册中断 + workqueue
    //     (probe = 只是分配内存、设置字段，不启动硬件)

    // --- 4.8 注册到内核 ---
    //     从这行开始, kernel 随时可能调用你的 ops 回调! 确保之前所有设置已完成。
    err = ieee80211_register_hw(hw);
    if (err)
        goto err_free;
    // 注册成功后 → kernel 自动创建 wlan0 (第一个虚拟接口)
    // 用户可以 ip link set wlan0 up → start() 回调被调

    return 0;

err_free:
    ieee80211_free_hw(hw);   // 释放 alloc_hw 分配的内存
    return err;
}

// ══ 第五步: remove 函数 (设备被拔出 / 模块被卸载时调用) ══
static void my_remove(struct pci_dev *pdev)
{
    struct ieee80211_hw *hw = pci_get_drvdata(pdev);
    // pci_set_drvdata 在 probe 最后调，remove 中读回来

    ieee80211_unregister_hw(hw);  // 向内核注销 — 先删 wlan0
    ieee80211_free_hw(hw);        // 释放内存
}
```

---

## 六、数据收发路径 — 深入理解

### RX 路径（收包）

```
硬件中断触发 (WiFi 芯片说 "有数据到了!")
  │
  ▼
驱动 ISR (中断服务程序) — 必须执行快、不能阻塞
  │
  │  通常做法: ISR 中只是关中断 + 调度 tasklet/workqueue
  │  真正处理数据的代码在 tasklet/workqueue 中 (下半部)
  │
  ▼
下半部: 读 DMA 缓冲区 → 取出帧 → 构造 skb → 调用 ieee80211_rx()
  │
  │  ⚠️ 关键: 你的 skb->data 必须以 **802.11 帧头** 开始
  │     不是你构造的 — 芯片收到的就是 802.11 帧，直接发给 mac80211 即可
  │
  ▼
ieee80211_rx(hw, skb)  ← 驱动唯一需要调用的 RX API
  │
  ▼
mac80211 接管:
  ├── 管理帧 (Beacon/Probe/Auth/Assoc...)
  │   → mac80211 MLME 状态机自己消费 (内核自己处理，你不需要管)
  ├── 控制帧 (ACK/RTS/CTS/BlockAck...)
  │   → mac80211 内部处理 (你不需要管)
  └── 数据帧 (真正要上网的包)
       ├── 解密 (如果密钥已设置 → 用 set_key 下发的那把密钥)
       ├── 去掉 802.11 头，加上 Ethernet 头 (802.11 → 802.3 转换)
       ├── AMPDU 重组 (把聚合的小包拆开)
       └── netif_receive_skb(skb) → 送入 Linux 网络协议栈
                                    → IP 层 → TCP/UDP → 用户空间

┌─────────────────────────────────────────────────────────┐
│  你作为驱动开发者，RX 路径的任务只有一个:                │
│    ieee80211_rx(hw, skb)                               │
│  其余全由 mac80211 处理。这是 softMAC 最大的优势。       │
└─────────────────────────────────────────────────────────┘
```

### TX 路径（发包）

```
用户空间发数据 (比如你在浏览器里打开一个网页)
  │
  ▼
网络协议栈 → 产生 Ethernet 帧 (skb) → 进入 mac80211
  │
  ▼
mac80211 处理:
  ├── 找到这个包要发往的目标 station (根据目的 MAC 地址)
  ├── Minstrel 算法选择最佳 TX 速率 (基于历史成功率)
  ├── Ethernet → 802.11 转换 (加 802.11 MAC 头)
  ├── 加密 (如果需要)
  └── 填充 ieee80211_tx_info (控制信息，放在 skb->cb 中)
      │
      ▼  你在这里收到 skb
ieee80211_ops->tx(hw, control, skb) ← 你的驱动被回调
  │
  │  你的工作:
  │    1. 从 skb->cb 里读 ieee80211_tx_info (速率、队列号、标志)
  │    2. 编程 DMA 描述符 → 把 skb 数据映射到你要发给芯片的内存区域
  │    3. 通知硬件 "这里有包，发它"
  │    4. 异步等待 (tx 回调不能阻塞! 必须立即返回)
  │
  ▼
... 若干时间后 ...
  │
TX 完成中断 → 你的 ISR 被调
  │
  │  调用 ieee80211_tx_status(hw, skb) 或 ieee80211_tx_status_irqsafe(hw, skb)
  │    → 告诉 mac80211 "包发送成功了" (或失败)
  │    → mac80211 更新速率统计、重试逻辑
```

**两种 TX 模型对比**：

```
Push 模型 (.tx 回调):               Pull 模型 (.wake_tx_queue 回调):
  mac80211 主动推包给你              mac80211 把包放在队列里
  → 你的 tx() 被调用                 → 你的 wake_tx_queue() 被调用
  → 你立即处理                       → 你去队列里取包
  ─────────────────────             ─────────────────────────
  ⭐ 新手推荐: 实现 tx() 即可        性能更好但更复杂
  wake_tx_queue 留空 = mac80211     新手不要碰
  自动帮你从队列取包→调 tx()        用默认实现
```

---

## 七、固件加载 — 为什么不在 probe 里加载

```
关键原则: 硬件在不需要时必须保持关闭状态

probe 阶段 (设备驱动被加载):
  → 只做内存分配、字段设置 (不需要硬件真正跑起来)
  → ❌ 不要加载固件
  → ❌ 不要启动硬件
  → ❌ 不要注册中断

start() 阶段 (用户执行 ip link set wlan0 up):
  → 硬件真正需要工作了
  → ✅ 在这里加载固件
  → ✅ 在这里注册中断
  → ✅ 在这里启动硬件

stop() 阶段 (用户执行 ip link set wlan0 down 或最后一个接口被删):
  → 硬件不需要工作了
  → ✅ 在这里卸载固件
  → ✅ 在这里注销中断
  → ✅ 在这里关闭硬件

为什么? 节能 + 热插拔 + 维护者要求
```

### request_firmware 示例

```c
#include <linux/firmware.h>

static int my_load_firmware(struct my_priv *priv)
{
    const struct firmware *fw;
    int ret;

    // 参数: &fw        ← 输出指针, 成功后 fw->data 和 fw->size 就有效了
    //       "mywifi/fw.bin" ← 固件路径 (相对 /lib/firmware/)
    //       &dev->dev   ← 设备指针 (用于 dev_err 日志)
    ret = request_firmware(&fw, "mywifi/fw.bin", &priv->dev->dev);
    if (ret) {
        dev_err(&priv->dev->dev,
                "固件加载失败: mywifi/fw.bin (错误码 %d)\n", ret);
        return ret;
    }

    // 把固件二进制数据写入芯片内存 (这里假设通过 MMIO)
    memcpy_toio(priv->fw_ram_base, fw->data, fw->size);

    // 启动固件 (比如写一个 "GO" 命令到芯片寄存器)
    writel(1, priv->mmio + FW_START_REG);

    // ⚠️ 释放固件缓冲区 —— 固件数据已经写入硬件，不需要再保留了
    release_firmware(fw);
    return 0;
}
```

**固件查找路径（按顺序）**：
```
1. fw_path 模块参数                 # insmod mywifi.ko fw_path=/custom
2. /lib/firmware/updates/6.x.y/    # 特定内核版本的更新
3. /lib/firmware/updates/          # 通用更新
4. /lib/firmware/6.x.y/            # 特定内核版本固件
5. /lib/firmware/                  # 标准位置 ← 绝大多数固件放这里
```

---

## 八、安全 — WPA2/WPA3 的连接流程

> **核心认知**：内核**不处理** WPA2/WPA3 的复杂认证逻辑，这件事由用户空间的 wpa_supplicant 做。

```
时间线: 用户点击 "连接 WiFi" →

用户空间 (wpa_supplicant)              内核 (cfg80211 → mac80211 → 你的驱动)
─────────────────────────              ────────────────────────────────

1. 触发扫描
   NL80211_CMD_TRIGGER_SCAN ──────────→ hw_scan() 或切信道发 ProbeReq
                                       收到 ProbeResp ← ieee80211_rx()
   NEW_SCAN_RESULTS ←──────────────────  扫描结果上报用户空间

2. 用户选择网络，发起认证
   NL80211_CMD_AUTHENTICATE ──────────→ 发 Auth 帧
                                       收到 Auth 响应 ← 芯片 RX → ieee80211_rx()

3. 发起关联
   NL80211_CMD_ASSOCIATE ─────────────→ 发 AssocReq 帧
                                       收到 AssocResp ← 芯片 RX → ieee80211_rx()

4. ★ 4-way handshake (WPA 核心) ★
   wpa_supplicant 自行完成             内核只是转发 EAPOL 帧
   PMK → PTK 推导                      (不参与密钥计算)
   验证 AP 的合法性                   

5. 下放密钥
   NL80211_CMD_NEW_KEY ───────────────→ set_key() ← 你的驱动收到密钥
                                        写入硬件密钥表
                                        之后的加密/解密由 mac80211 或你的硬件做
```

### 硬件密钥卸载 — "这个活你帮内核做"

如果你的芯片固件能自己处理 4-way handshake（不需要 wpa_supplicant 参与），设置 `wiphy` 标志即可：

```c
// 你的芯片固件能处理客户端 PSK 的 4-way handshake
hw->wiphy->ext_features[NL80211_EXT_FEATURE_4WAY_HANDSHAKE_STA_PSK / 8]
    |= BIT(NL80211_EXT_FEATURE_4WAY_HANDSHAKE_STA_PSK % 8);
```

---

## 九、监管域 — 为什么你的 WiFi 在某些信道搜不到信号

```bash
iw reg get    # 查看当前监管域规则
# 输出: country CN: ...  ← 这说明你的设备遵循中国法规
#       具体限制: 2.4GHz 只能用 1-13 信道，最大 20dBm
#                5GHz 可用信道: 36,40,44,48,149,153,157,161,165...

iw reg set US  # 把监管域设为美国 (信道变多)
# 注意: 你的硬件可能不认这个设置 (芯片可能有内置限制)
```

**监管域数据库不在内核内**——它是独立文件 `/lib/firmware/regulatory.db`：
```
wireless-regdb 包 → /lib/firmware/regulatory.db       (二进制规则库)
                    /lib/firmware/regulatory.db.p7s   (RSA 签名，防篡改)
```

**你的驱动如何参与**：
1. 不需要做任何事 —— cfg80211 通过 `config()` 回调自动下发限制
2. 如果需要知道监管域何时变化：在 `wiphy` 上设置 `reg_notifier()` 回调
3. 如果你的芯片能从固件/beacon 猜到当前区域：可以调 `regulatory_hint()` 告诉 cfg80211

---

## 十、省电 (Power Save) — 调试时的头号陷阱

```
省电机制 (简要):
  1. 你的STA说"我困了" → 发PS帧给AP
  2. AP帮你把包暂存起来
  3. AP在周期性 Beacon 里设 TIM bit = "你有包裹"
  4. 你的STA定期醒来 → 看Beacon → TIM有你的bit → 发PS-Poll取包裹
  5. 取完 → 继续睡

⚠️ 开发早期必须关掉省电！
  原因: 固件在休眠时可能有bug → 醒来后状态混乱
        → 包收不到、连不上、莫名断线
        → 你会花几小时去找"难道是 DMA 配置错了?"
        → 结果只是因为芯片在睡觉!
```

```bash
# 关掉省电 — 开发早期第一件事
iw dev wlan0 set power_save off
# 确认关了
iw dev wlan0 get power_save
# 输出应该是: Power save: off
```

---

## 十一、FullMAC 驱动 — 当 MAC 层在芯片里

> 你的芯片是 SoftMAC 还是 FullMAC? → 如果 vendor 给的 SDK 里你看到 `cfg80211_ops` (不是 `ieee80211_ops`)，你的芯片就是 FullMAC。

```
FullMAC 注册流程 (比 SoftMAC 复杂):
  1. wiphy = wiphy_new(&my_cfg80211_ops, sizeof(*priv))
  2. 设置 wiphy 属性 (同 SoftMAC)
  3. wiphy_register(wiphy)
  4. 自己创建 net_device (alloc_netdev)
  5. 自己实现 net_device_ops (.ndo_open, .ndo_stop, .ndo_start_xmit)
  6. 在 net_device 上设置 ieee80211_ptr = 指向 wireless_dev 的指针
     ⚠️ 这个指针必须正确！内核靠它把 net_device 和 wiphy 关联
  7. register_netdev(ndev)
```

| 关键区别   | FullMAC                                                  | SoftMAC                                            |
| ------ | -------------------------------------------------------- | -------------------------------------------------- |
| 注册函数   | `wiphy_new()` + `wiphy_register()`                       | `ieee80211_alloc_hw()` + `ieee80211_register_hw()` |
| 网络设备   | **你自己创建** `net_device`                                   | **内核自动创建** wlan0                                   |
| RX API | `netif_rx(skb)` / `napi_gro_receive()` (标准 Linux 网卡 API) | `ieee80211_rx(hw, skb)` (mac80211 专用)              |
| TX 路径  | `ndo_start_xmit()` (标准 Linux 网卡 API)                     | `ieee80211_ops->tx()`                              |

---

## 十二、测试与调试 — 四阶段验证法

### 阶段 1: iw 基础检查（5 分钟）

```bash
# ① 设备注册成功了吗？
iw phy
# 应该能看到你的设备: phy#0 / phy#1 ...

# ② 能力清单对吗？
iw phy phy0 info | head -30
# 检查: Wiphy phy0
#       Band 1: (2.4GHz 或 5GHz)
#       Supported interface modes: * managed * AP
#       → 之前填的 interface_modes 对不对

# ③ 虚拟接口自动创建了吗？
iw dev
# 应该能看到 wlan0 (mac80211 自动创建的)

# ④ 扫描能看到周围网络吗？
iw dev wlan0 scan | grep SSID
# 如果啥也没有 → TX/RX 路径可能有问题 → 去阶段 4 抓包
```

### 阶段 2: wpa_supplicant 连接测试（10 分钟）

```bash
# 新建一个配置 (连接你的测试路由器)
cat > /tmp/test.conf << 'EOF'
network={
    ssid="MyTestWiFi"           # ← 改成你的路由器名字
    psk="password123"           # ← 改成你的 WiFi 密码
    key_mgmt=WPA-PSK            # 认证方式 (WPA2 个人版)
    proto=WPA2                  # 协议版本
    pairwise=CCMP               # 加密套件 (AES)
}
EOF

# 后台启动 wpa_supplicant
wpa_supplicant -B -i wlan0 -c /tmp/test.conf -D nl80211
# -B = 后台运行  -D nl80211 = 使用 nl80211 驱动接口

# 查看状态
wpa_cli status

# 测试 ping (连上后获取 DHCP 地址)
dhclient wlan0 &      # 或 udhcpc -i wlan0
ping -c 4 8.8.8.8     # 能通 = 驱动基本正常
```

### 阶段 3: 内核调试工具

```bash
# ① 动态调试 — 开启 mac80211 的日志
echo "file net/mac80211/* +p" \
    > /sys/kernel/debug/dynamic_debug/control

# ② ftrace — 追踪 mac80211→你的驱动 的调用
echo 1 > /sys/kernel/debug/tracing/events/mac80211/enable
cat /sys/kernel/debug/tracing/trace
# 重点看 DRV_* 事件 = 内核在调你的驱动哪个回调
# 例: DRV_START, DRV_TX, DRV_CONFIG...

# ③ nl80211 事件监听 — 看用户空间和内核在聊什么
iw event -t &
# 然后在另一个终端操作: iw dev wlan0 scan
# iw event 会打印收到的所有 nl80211 事件

# ④ debugfs — 看 mac80211 维护的 station 和 速率控制状态
cat /sys/kernel/debug/ieee80211/phy0/stations/
cat /sys/kernel/debug/ieee80211/phy0/rc/
```

### 阶段 4: 抓包（终极手段）

```bash
# 在**另一台设备**上开 Monitor 模式抓包
iw dev wlan0 interface add mon0 type monitor
ip link set mon0 up
iw dev mon0 set channel 6         # ← 设成你的测试信道

# 抓包
tcpdump -i mon0 -w test.pcap -s 0
# 或者在设备上用 Wireshark:
# wireshark -i mon0 -k

# 抓到的帧序列应该是:
#   Probe Request (你的设备发给 AP)
#   Probe Response (AP 回复)
#   Authentication (认证 — 无加密时用 open system)
#   Association Request / Response (关联)
#   EAPOL (如果有加密 — 这是 4-way handshake 的帧)
#   Data (真正上网的数据)

# ★ 如果看不到任何 Probe Request:
#   → 你的 TX 回调根本没发数据
# ★ 如果只有 Probe Request 没有 Probe Response:
#   → 可能 RX 路径有问题 (中断没触发 / DMA 地址配错 / ieee80211_rx 没调)
```

### 抓 nl80211 消息（高级 — 看内核和用户空间的对话）

```bash
# 创建 nlmon 接口 (抓 Netlink 消息)
ip link add nlmon0 type nlmon
ip link set nlmon0 up
tcpdump -i nlmon0 -w nl.pcap

# 在另一个终端操作: iw dev wlan0 scan
# Wireshark 打开 nl.pcap → 选择 "Netlink" 协议 → 能看到 NL80211_CMD_* 的消息
```

---

## 十三、驱动开发最佳实践

### 1. probe 中不要初始化硬件

```c
// ❌ 错:
static int my_probe(...) {
    request_firmware(...);    // 不该在这
    my_start_chip(...);       // 不该在这
    // → 维护者审查时一定被打回
}

// ✅ 对:
static int my_start(struct ieee80211_hw *hw) {
    request_firmware(...);    // 在 start 中加载
    my_start_chip(...);       // 在 start 中启动
}
static void my_stop(struct ieee80211_hw *hw) {
    my_stop_chip(...);        // 在 stop 中关闭
}
```

### 2. 注册前确保所有字段准备好

```
⚠️ ieee80211_register_hw() 一调，内核可能立刻回调你的驱动
→ 确保在此之前所有 flags / wiphy 属性 / bands 已经填好
→ 不要先 register 再填属性!
```

### 3. 分离总线代码

```
mywifi/
  bus_pci.c    ← PCI 读/写寄存器、DMA 设置
  bus_sdio.c   ← SDIO 读/写寄存器 (如果同芯片有不同连接方式)
  main.c       ← 共享逻辑 (ops 实现、数据结构)
  避免重复代码
```

### 4. 不要一口气实现 102 个回调

```
起步: 8 个最小集 → 能注册、能扫描、能连 open/WEP 网络
第二步: 加 set_key / bss_info_changed → 能连 WPA2 网络
第三步: 加 hw_scan / ampdu_action → 优化性能
```

---

## 十四、无线 vs 有线驱动差异

| 维度 | 以太网驱动 | WiFi 驱动 |
|------|----------|----------|
| 框架 | 直接实现 net_device_ops | 实现 ieee80211_ops (softMAC) 或 cfg80211_ops (fullMAC) |
| 帧格式 | Ethernet 帧 | 802.11 帧 (mac80211 帮你做 802.11↔802.3 转换) |
| 连接管理 | 无 (插线通电即可) | 扫描/认证/关联/密钥管理 (用户空间辅助) |
| 测试难度 | 较低 (插网线 → ping) | 高 (丢包/射频性能/环境干扰/固件bug) |
| 框架复用度 | 各驱动独立 | **mac80211 高度复用** 避免功能重复 |

> mac80211 的最大价值：你不需要知道 802.11 协议的每一个细节。内核替你处理了帧格式、加密、速率控制、省电。你只需要做"数据搬运工"——把数据从芯片搬到内核、从内核搬到芯片。

---

## 推荐学习顺序

```
1. 读 include/net/mac80211.h       (理解每个回调的含义)
2. 读 include/net/cfg80211.h       (理解 wiphy 属性)
3. 读 drivers/net/wireless/ath/ath9k/init.c  (看真实的 softMAC probe 怎么写)
4. 读 drivers/net/wireless/mediatek/mt76/   (看更现代的 softMAC 驱动)
5. 读 wireless.wiki.kernel.org              (维护者写的官方笔记)
```

---

## 参考资料

| 资源 | 链接 |
|------|------|
| 原演讲幻灯片 | [bootlin.com/pub/conferences/2025/elce/lothore-80211.pdf](https://bootlin.com/pub/conferences/2025/elce/lothore-80211.pdf) |
| 原演讲视频 | [YouTube](https://www.youtube.com/watch?v=kvyLE4esjPE) |
| Linux Wireless Wiki | [wireless.wiki.kernel.org](https://wireless.wiki.kernel.org/) |
| mac80211 头文件 | `include/net/mac80211.h` |
| cfg80211 头文件 | `include/net/cfg80211.h` |
| nl80211 头文件 | `include/uapi/linux/nl80211.h` |
| wireless-regdb | [kernel.org](https://git.kernel.org/pub/scm/linux/kernel/git/wens/wireless-regdb.git/) |
| 参考驱动 ath9k | `drivers/net/wireless/ath/ath9k/` |
| 参考驱动 mt76 | `drivers/net/wireless/mediatek/mt76/` |

---

> 本文基于 Alexis Lothoré (Bootlin) OSSEU 2025 演讲整理，所有代码示例已添加详细中文标注。
