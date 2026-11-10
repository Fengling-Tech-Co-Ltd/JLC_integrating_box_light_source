# JLC_integrating_box_light_source

胶片翻拍用「积分盒」四色 LED 光源 —— 立创EDA专业版工程。

同一块板的 KiCad 工程见 [kicad_integrating_box_light_source](https://github.com/Fengling-Tech-Co-Ltd/kicad_integrating_box_light_source)，两版对应同一套电路，改动需两边同步。

## 功能

- **R / G / B / IR 四个独立通道**，每通道 4 颗 3535 灯珠串联，恒流 333 mA
- 恒流驱动 **AL8862SP-13**（滞环降压，`I_OUT = 0.1 V / R_SET`），CTRL 端 0.4–2.5 V **DC（模拟）调光**
- 调光由 **MCP4728**（I2C 四路 12 位 DAC）给出，CH1–CH4 对应 R/G/B/IR
- 光强闭环：**BPW34 + OPA333 跨阻 + ADS1110**（16 位 ΔΣ ADC，同一 I2C 总线）
- 电源：24 V 输入 → 2 A 保险丝 + SMBJ30CA TVS + SS54 防反接 → **LM317 线性**产生 3V3（模拟电源用线性，避开开关噪声）

## 工程结构

`JLC_Integrating_Sphere_Light_Source_2026-11-10.epro` 是立创EDA专业版的工程包，文件名里的日期即版本号。包内是自包含的，不依赖外部库：

| 目录 | 内容 | 数量 |
|---|---|---|
| `SHEET/` | 原理图图纸 | 8 张 |
| `PCB/` | PCB 工程 | 1 块 |
| `SYMBOL/` | 器件符号 | 56 个 |
| `FOOTPRINT/` | 封装 | 27 个 |
| `INSTANCE/` | 板级实例 | 9 个 |
| `BLOB/` | 图纸正文（加密存储） | 7 个 |

## 关键计算

| 项目 | 值 | 依据 |
|---|---|---|
| LED 电流（100%） | 333 mA | `I_OUT = 0.1 V / R_SET`，R_SET = R30 |
| 电感 | 100 µH | `t_OFF = L·ΔI/(V_LED+V_D+I·(R_SET+R_L))`；满载 570–700 kHz（手册推荐 33–100 µH，高压取大） |
| 输入电容 | 10 µF/50V | 手册推荐 4.7–10 µF，X7R |
| 3V3 | 3.28 V | `1.25×(1+390/240)`，TO-252，耗散 0.19 W |
| 跨阻增益 | 47 kΩ ∥ 10 pF | 带宽 339 kHz，噪声 ≈20 µV rms，折算 1.33 nA/LSB |

以上参数取自同板的 KiCad 工程，两版应保持一致。

## 灯珠与立创编号

| 通道 | 位号 | 型号 | 立创编号 |
|---|---|---|---|
| 红 | LED_R1–R4 | XL-HD3535SURC-A2 | C3646952 |
| 绿 | LED_G1–G4 | JNJ-LEHG0113W120 | C19185875 |
| 蓝 | LED_B1–B4 | XL-HD3535UBC-A2 | C3646953 |
| 红外 | LED_IR1–IR4 | XL-HD3535HIR-T4A2 | C5221244 |
| 驱动 IC | U4–U7 | AL8862SP-13 | C526360 |

## 打开方式

立创EDA专业版 → 文件 → 导入 → 选择 `JLC_Integrating_Sphere_Light_Source_2026-11-10.epro`。工程包内已含全部符号与封装，无需另配库。

## 状态

- 工程文件以本仓库的 `.epro` 为准，仓库里不再放外层压缩包
- PCB 已建板
- 本版与 KiCad 版之间的差异未在仓库内记录，以 `.epro` 实际内容为准
- 疑似待办：其余阻容/连接器的立创编号核对（同 KiCad 版未项）
