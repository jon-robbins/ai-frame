# AI Frame — Bill of Materials

This document tracks the hardware, electrical components, tools, and prototype materials currently associated with the AI Frame project.

The BOM distinguishes between:

- **Core prototype hardware** — required to build the working display
- **Controller candidates** — alternative display-controller architectures currently being evaluated
- **Electrical infrastructure** — AC/DC power distribution and wiring
- **ESP32-HUB75 prototype components** — parts required to test the open ESP32 controller path
- **Development/debugging tools** — equipment used during bring-up
- **Consumables and prototyping materials**

Status values:

```text
ALREADY OWNED
ORDERED
RECEIVED
TESTED
FINAL
OPTIONAL
REJECTED
```

A component being marked `ORDERED` does **not** mean it has been selected for the final product.

---

# 1. Core Prototype Hardware

| Component            | English Name                           | Chinese Name / Search Term         | Brand         | Model / Specification                           | Qty | Status        | Purpose                           |
| -------------------- | -------------------------------------- | ---------------------------------- | ------------- | ----------------------------------------------- | --: | ------------- | --------------------------------- |
| LED module           | P2 Indoor RGB HUB75E LED Matrix Module | P2室内全彩LED显示屏模组 / HUB75E LED显示屏模组   | Generic       | P2, 128×64, 256×128 mm, 1/32 scan, 5 V, SMD1515 |   6 | ALREADY OWNED | Main display                      |
| Application computer | Sipeed LicheeRV Nano-W                 | 矽速科技 LicheeRV Nano-W / 荔枝派 Nano开发板 | Sipeed / 矽速科技 | SG2002, 256 MB DDR3, Wi-Fi model                |   1 | ORDERED       | Linux/Python application computer |
| Storage              | Lenovo microSD / TF Card               | 联想 TF存储卡 / microSD卡                | Lenovo / 联想   | 64 GB                                           |   1 | ORDERED       | Linux system storage for Nano     |
| Power supply         | 5 V 40 A 200 W Switching Power Supply  | 5V40A 200W开关电源 / LED显示屏电源          | Generic       | A-200-5                                         |   1 | ORDERED       | Centralized 5 V supply            |

---

# 2. Display Assembly

## 2.1 LED Modules

### P2 HUB75E modules

**English:** P2 Indoor RGB HUB75E LED Matrix Module
**Chinese:** P2室内全彩LED显示屏模组 / HUB75E LED显示屏模组
**Brand:** Generic
**Seller/model reference:** approximately `P2-32S`

Per-module specification:

```text
Resolution:       128 × 64
Pixel pitch:      2 mm
Physical size:    256 × 128 mm
Voltage:          5 V
Max power:        ~23 W
Scan:             1/32
Interface:        HUB75E
LED package:      SMD1515
```

Quantity:

```text
6 panels
```

Final physical arrangement:

```text
2 wide × 3 high
```

Final logical resolution:

```text
256 × 192
```

Total pixels:

```text
49,152 RGB pixels
```

Maximum nominal panel load:

```text
6 × 23 W = 138 W
```

---

## 2.2 HUB75 Ribbon Cables

**English:** 16-pin HUB75 IDC Ribbon Cable
**Chinese:** HUB75排线 / LED显示屏16P排线 / 16针灰排线
**Brand:** Generic
**Specification:** 16-pin, approximately 40 cm
**Quantity ordered:** 6
**Status:** ORDERED

Purpose:

* controller → first panel
* panel OUT → next panel IN

The six cables are enough for the intended three-row topology.

Two additional spare cables may be useful later but are not required for bring-up.

---

# 3. Application Computer

## Sipeed LicheeRV Nano-W

**English:** Sipeed LicheeRV Nano-W
**Chinese:** 矽速科技 LicheeRV Nano-W / 荔枝派 Nano开发板
**Brand:** Sipeed / 矽速科技
**Model:** LicheeRV Nano-W
**Quantity:** 1
**Status:** ORDERED

Key specification:

```text
SoC:       Sophgo SG2002 / 算能 SG2002
RAM:       256 MB DDR3
Storage:   microSD
Wireless:  Wi-Fi
USB:       USB 2 OTG Type-C
OS:        Linux-capable
```

Role:

* Linux
* Python
* API access
* Wi-Fi
* image rendering
* application state
* scheduling
* display transport

The Nano is **not** intended to perform real-time HUB75 GPIO refresh directly.

---

# 4. Display Controller Candidates

Three controller approaches are being purchased/tested.

These are alternatives, not cumulative layers.

---

## 4.1 Huidu HD-WF4

**English:** Huidu HD-WF4 Wi-Fi LED Controller Card
**Chinese:** 灰度 HD-WF4 WiFi控制卡 / LED显示屏控制卡
**Brand:** Huidu / 灰度科技
**Company:** 深圳市灰度科技有限公司
**Model:** HD-WF4
**Quantity:** 1
**Status:** ORDERED
**Approx. purchase price:** ¥57

Purpose:

Primary stock-controller candidate.

Expected physical topology:

```text
WF4 X1 → Top-L → Top-R
WF4 X2 → Mid-L → Mid-R
WF4 X3 → Bot-L → Bot-R
WF4 X4 → unused
```

Why it is being tested:

* four HUB75 outputs
* inexpensive
* designed specifically for LED panels
* clean match to three physical display rows

Main unresolved question:

> Can the LicheeRV Nano programmatically send dynamic content to it in a practical way?

---

## 4.2 Huidu HD-WF2

**English:** Huidu HD-WF2 Wi-Fi LED Controller Card
**Chinese:** 灰度 HD-WF2 WiFi控制卡 / LED显示屏控制卡
**Brand:** Huidu / 灰度科技
**Model:** HD-WF2
**Quantity:** 1
**Status:** ORDERED
**Approx. purchase price:** ~¥35

Purpose:

Experimental/reference controller.

Why it was purchased:

* inexpensive
* existing community reverse-engineering work
* experimental WLED-MM support
* useful comparison with WF4
* useful for understanding Huidu hardware

The WF2 is not currently expected to be the cleanest final six-panel controller because it provides only two HUB75 outputs.

---

## 4.3 Generic ESP32-S3 N16R8

**English:** ESP32-S3-N16R8 DevKitC-style Development Board
**Chinese:** ESP32-S3-N16R8开发板 / ESP32-S3 DevKitC-1开发板
**Chip manufacturer:** Espressif / 乐鑫科技
**Board brand:** Generic / seller-branded
**Configuration:** N16R8
**Quantity:** 1
**Status:** ORDERED
**Approx. purchase price:** ¥29.54

Specification:

```text
Flash:   16 MB
PSRAM:   8 MB octal PSRAM
USB:     Type-C
MCU:     ESP32-S3
```

Purpose:

Test the open-controller architecture:

```text
LicheeRV Nano-W
       │
       ▼
ESP32-S3 N16R8
       │
       ▼
HCT245 buffer board
       │
       ▼
HUB75E panels
```

Only one ESP32 has been ordered.

Do **not** purchase additional units until one controller has successfully driven the actual LED modules.

Reference design: PatternFlow (physical layout and pinout reference)

---

# 5. ESP32 → HUB75 Prototype Components

These components allow one generic ESP32-S3 controller to be tested with a HUB75E panel.

---

## 5.1 SN74HCT245N

**English:** SN74HCT245N Octal Bus Transceiver / Logic Buffer
**Chinese:** SN74HCT245N 八路总线收发器 / 八路缓冲器 / 电平转换芯片
**Typical manufacturer:** Texas Instruments / 德州仪器, subject to delivered component
**Model:** SN74HCT245N
**Package:** DIP-20 / 双列直插20脚
**Quantity:** 2
**Status:** ORDERED
**Approx. total price:** ¥5.20

Purpose:

Translate/buffer the ESP32's 3.3 V HUB75 GPIO signals using 5 V HCT logic.

Two 8-channel devices provide enough outputs for the approximately 14 HUB75E signals.

Reference design: Seengreat RGB Matrix Adapter V3.9 (electrical buffering reference)

---

## 5.2 100 nF ceramic capacitors

**English:** 100 nF / 0.1 µF Ceramic Capacitor
**Chinese:** 104瓷片电容 / 0.1uF陶瓷电容
**Marking:** `104`
**Voltage rating:** approximately 50 V
**Brand:** Generic
**Required:** 2
**Purchased:** one multi-piece pack
**Status:** ORDERED
**Approx. pack price:** ¥1

Purpose:

One decoupling capacitor beside each SN74HCT245N.

Connection:

```text
HCT245 VCC
    │
  100 nF
    │
   GND
```

---

## 5.3 1000 µF electrolytic capacitor

**English:** 1000 µF 16 V Electrolytic Capacitor
**Chinese:** 1000UF 16V电解电容
**Brand:** Generic
**Required:** 0–1
**Purchased:** one multi-piece pack
**Status:** ORDERED
**Approx. pack price:** ¥1.94

Purpose:

Optional local bulk capacitance on the 5 V rail.

The footprint may also be included on the eventual custom adapter PCB.

---

## 5.4 HUB75 keyed box header

**English:** 2×8 16-pin 2.54 mm Keyed IDC Box Header
**Chinese:** 2×8P 2.54mm牛角座 / 简牛座 / IDC排线插座
**Brand:** Generic
**Specification:** 2 × 8 pins, 2.54 mm pitch
**Required:** 1
**Purchased:** one small multi-piece pack
**Status:** ORDERED
**Approx. pack price:** ¥2.10

Purpose:

Provides the standard keyed 16-pin HUB75E cable connection.

---

## 5.5 Prototype perfboard

**English:** 5×7 cm Double-Sided Prototype PCB / Perfboard
**Chinese:** 5×7cm双面洞洞板 / 万用板 / 实验板
**Brand:** Generic
**Specification:** approximately 5 × 7 cm, 2.54 mm pitch
**Purchased:** one multi-board pack
**Status:** ORDERED
**Approx. pack price:** ¥3

Purpose:

Fallback method for manually assembling the HCT245 adapter before a custom PCB is fabricated.

This may become unnecessary if the custom PCB is ready first.

---

# 6. Planned Custom PCB

No PCB has been fabricated yet.

The eventual board is expected to contain approximately:

```text
┌─────────────────────────────────┐
│ ESP32-S3 N16R8 headers/socket   │
│                                 │
│ SN74HCT245N     SN74HCT245N     │
│      │                │         │
│      └───────┬────────┘         │
│              ▼                  │
│       HUB75E 2×8 header         │
│                                 │
│ 5V   GND                        │
│ optional bulk capacitor         │
└─────────────────────────────────┘
```

Likely fabrication options researched:

| English Name              | Chinese Name | Role            |
| ------------------------- | ------------ | --------------- |
| JLCPCB                    | 嘉立创          | PCB prototyping |
| Jiepei                    | 捷配           | PCB prototyping |
| JDBPCB / related services | 捷多邦 / 聚多邦    | PCB prototyping |

Typical prototype target:

```text
Quantity:         5
Layers:           2
Material:         FR-4
Thickness:        1.6 mm
Copper:           1 oz
Solder mask:      green
Silkscreen:       white
Finish:           HASL
Size:             <100 × 100 mm
```

Expected bare-PCB fabrication cost:

```text
approximately ¥0–30 for 5 boards
```

Fabrication is intentionally deferred until the physical ESP32 board arrives and its dimensions are measured.

---

# 7. DC Power System

## 7.1 A-200-5 PSU

**English:** 5 V 40 A 200 W Switching Power Supply
**Chinese:** 5V40A 200W开关电源 / LED显示屏电源
**Brand:** Generic
**Model:** A-200-5
**Quantity:** 1
**Status:** ORDERED
**Approx. purchase price:** ¥43

Specification:

```text
Output voltage: 5 V
Output current: 40 A
Nominal power:  200 W
Dimensions:     ~199 × 110 × 50 mm
```

Expected maximum LED load:

```text
~138 W
~27.6 A
```

The PSU provides nominal headroom but still requires load and thermal testing.

---

## 7.2 LED panel power harness

**English:** 1-to-4 LED Display Panel Power Harness
**Chinese:** LED显示屏一拖四电源线 / 一拖四全彩模组电源线
**Brand:** Generic
**Material selected:** Pure copper / 纯铜
**Quantity:** 2
**Status:** ORDERED
**Approx. unit price:** ¥3.10

Two harnesses provide eight branches for six panels.

The six panels should receive parallel power.

Correct:

```text
          PSU
       ┌───┼───┐
       ▼   ▼   ▼
     P1   P2   P3 ...
```

Do not use:

```text
PSU → P1 → P2 → P3
```

for high-current panel power.

---

## 7.3 18 AWG silicone DC wire

**English:** 18 AWG Red/Black Silicone Power Wire
**Chinese:** 18AWG红黑并线 / 硅胶电源线 / 红黑电源线
**Brand:** Generic
**Cross-section:** approximately 0.75 mm²
**Quantity:** 5 × 1 m
**Total:** 5 m
**Status:** ORDERED

Purpose:

* 5 V power distribution
* panel branches
* low-voltage controller power

Not for AC mains.

---

# 8. AC Power System

## 8.1 IEC C14 fused/switched inlet

**English:** IEC C14 Fused Switched Power Inlet
**Chinese:** C14品字插座 / 带保险丝带开关电源插座 / 三合一电源插座
**Brand:** Generic
**Quantity:** 1
**Status:** ORDERED
**Approx. purchase price:** ¥4.54

Features:

* IEC C14 inlet
* illuminated rocker switch
* fuse holder

Expected system path:

```text
Wall
 │
 ▼
IEC C13 cable
 │
 ▼
C14 fused/switched inlet
 │
 ▼
A-200-5 PSU
```

---

## 8.2 T2A fuse

**English:** T2A 5×20 mm Slow-Blow Glass Fuse
**Chinese:** T2A 5×20慢熔玻璃保险丝 / 延时保险管
**Brand:** Generic
**Rating:** 2 A time-delay
**Dimensions:** 5 × 20 mm
**Purchased:** 1 multi-piece pack
**Status:** ORDERED
**Approx. pack price:** ¥7.50

Purpose:

Fuse protection in the C14 inlet.

Physical fit and suitability must be verified before final use.

---

## 8.3 Internal mains cable

**English:** 3-core 0.75 mm² AC Power Cable
**Chinese:** 三芯0.75平方电源线 / 国标三芯电源线
**Brand:** Generic
**Specification:** 3 × 0.75 mm²
**Quantity:** approximately 1 m
**Status:** ORDERED

Expected conductor convention:

```text
Brown          Live / L / 火线
Blue           Neutral / N / 零线
Green-yellow   Protective Earth / PE / 地线
```

Purpose:

```text
C14 inlet → PSU L / N / FG
```

---

## 8.4 6.3 mm female spade terminals

**English:** 6.3 mm Insulated Female Spade / Quick-Disconnect Terminal
**Chinese:** 6.3mm插簧端子 / 母插簧 / 冷压接线端子
**Brand:** Generic
**Purchased:** one multi-piece pack
**Status:** ORDERED
**Approx. pack price:** ¥2.10

Purpose:

Connections to rear tabs of the C14 inlet/switch assembly.

---

# 9. Wiring and Connector Hardware

## 9.1 Ferrule assortment

**English:** Bootlace Ferrule Assortment
**Chinese:** 管型冷压端子套装 / 欧式端子
**Brand:** Generic
**Purchased:** approximately 200-piece assortment
**Status:** ORDERED

Purpose:

Clean termination of stranded conductors into PSU screw terminals.

---

## 9.2 HS-202D ferrule crimper

**English:** HS-202D Ferrule Crimping Tool
**Chinese:** HS-202D管型端子压线钳
**Brand:** Generic
**Model:** HS-202D
**Quantity:** 1
**Status:** ORDERED

Purchased together with the ferrule assortment.

Approximate kit price:

```text
¥16.90
```

---

## 9.3 Pin headers

**English:** 1×40 2.54 mm Male Pin Header Strip
**Chinese:** 1×40P 2.54mm排针 / 单排排针
**Brand:** Generic
**Purchased:** one pack, approximately 5 strips
**Status:** ORDERED
**Approx. price:** ¥2.66

Purpose:

* prototyping
* controller connections
* custom adapter PCB
* removable signal interfaces

---

## 9.4 Dupont jumper wires

**English:** 26 AWG Dupont Jumper Wires
**Chinese:** 杜邦线 / 2.54mm杜邦线
**Brand:** Generic
**Specification:** approximately 26 AWG
**Status:** ORDERED
**Final checkout quantity:** VERIFY ON RECEIPT

Purpose:

* low-current GPIO
* UART
* temporary prototyping

Do **not** use Dupont wires for:

* panel power
* high-current 5 V distribution
* AC mains

---

# 10. Development and Debugging Hardware

## 10.1 CH340 USB-UART adapter

**English:** CH340 USB-to-TTL Serial Adapter
**Chinese:** CH340 USB转TTL模块 / USB转串口模块
**Chip manufacturer:** WCH / 南京沁恒微电子
**Module brand:** Generic
**Quantity:** 1
**Status:** ORDERED
**Approx. price:** ¥6

Purpose:

Serial debugging of boards such as the LicheeRV Nano.

Typical connection:

```text
Adapter TX → target RX
Adapter RX → target TX
Adapter GND → target GND
```

Do not supply 5 V from the adapter to a target already powered separately.

---

## 10.2 CHINT digital multimeter

**English:** CHINT Digital Multimeter
**Chinese:** 正泰数字万用表
**Brand:** CHINT / 正泰
**Model:** ZTW0138A
**Quantity:** 1
**Status:** ORDERED
**Approx. price:** ¥35

Purpose:

* DC voltage
* AC voltage
* continuity
* polarity
* resistance
* basic electrical troubleshooting

Important limitation:

Do not place the meter in series with the entire LED display to measure its full current.

The complete display may draw far more current than the meter's current input is designed to tolerate.

---

# 11. Assembly Tools and Consumables

## 11.1 Soldering iron

**English:** 60 W Digital Temperature-Controlled Soldering Iron Kit
**Chinese:** 60W数显调温电烙铁套装
**Brand:** Generic / seller-branded
**Power:** 60 W
**Quantity:** 1
**Status:** ORDERED
**Approx. price:** ¥34.80

Purpose:

* headers
* prototype board
* HCT245 adapter
* custom PCB assembly
* low-voltage wiring

Verify on receipt whether the kit includes adequate:

* solder
* flux
* stand
* tips

---

## 11.2 Heat-shrink tubing

**English:** Heat-Shrink Tubing Assortment
**Chinese:** 热缩管套装 / 绝缘热缩套管
**Brand:** Generic
**Purchased:** approximately 560-piece assortment
**Quantity:** 1 kit
**Status:** ORDERED
**Approx. price:** ¥13

Purpose:

Insulating and protecting electrical joints.

---

## 11.3 Prototype PCB / Perfboard

See ESP32-HUB75 prototype section.

This is retained as a cheap fallback even though a custom PCB is likely to be fabricated later.

---

# 12. Items Already Available / Not Required to Purchase

## USB-C data cable

**English:** USB-C Data Cable
**Chinese:** Type-C数据线 / USB-C数据线
**Status:** ALREADY OWNED

Purpose:

* LicheeRV Nano setup
* ESP32 programming
* USB communication

No additional USB-C cable was required.

---

# 13. Items Deliberately Not Purchased

The following were considered but are currently unnecessary.

## Expensive integrated ESP32-HUB75 controllers

Examples:

* Waveshare ESP32-S3-RGB-Matrix
* ¥130–160 class integrated ESP32/HUB75 controllers

Reason:

The unit economics are unattractive while:

* Huidu boards cost much less
* generic ESP32-S3 costs ~¥30
* custom HUB75 buffer PCB fabrication is extremely inexpensive

---

## Generic bidirectional level shifters

Examples:

* TXS0108E
* MOSFET I²C level-shifter modules

Status:

```text
REJECTED
```

Reason:

Not appropriate for the high-speed unidirectional HUB75 signal path.

---

## Raspberry Pi

Status:

```text
NOT SELECTED
```

Reason:

Unnecessary cost and capability for V1.

The application workload should fit a cheaper Linux SBC.

---

## Custom production PCB

Status:

```text
DEFERRED
```

Reason:

The exact generic ESP32 board dimensions and revision should be measured first.

---

# 14. Parts That Are Experimental Rather Than Final

The following components were intentionally purchased for comparison:

```text
Huidu HD-WF4
Huidu HD-WF2
ESP32-S3 N16R8
SN74HCT245N adapter components
```

They should not all appear in the final product.

The controller experiments will determine which architecture survives.

Likely outcomes:

### Outcome A

```text
LicheeRV Nano-W
       │
       ▼
Huidu HD-WF4
       │
       ▼
6 LED modules
```

In this outcome:

* WF2 becomes test/spare hardware
* ESP32 adapter becomes test/spare hardware

### Outcome B

```text
LicheeRV Nano-W
       │
       ├────────┬────────┐
       ▼        ▼        ▼
     ESP32    ESP32    ESP32
       │        │        │
     Row 1    Row 2    Row 3
```

In this outcome:

* additional ESP32 controllers would be purchased later
* the custom HCT245 PCB would become part of the final architecture
* WF2/WF4 become test/spare hardware

---

# 15. Prototype Power Budget

## LED modules

```text
6 × 23 W
= 138 W
```

## Maximum panel current

```text
138 W ÷ 5 V
= 27.6 A
```

## PSU

```text
5 V × 40 A
= 200 W nominal
```

Nominal unused capacity before other electronics:

```text
200 W - 138 W
= 62 W
```

This is sufficient on paper.

The inexpensive PSU must still pass real-world:

* voltage-drop testing
* full-load testing
* thermal testing
* extended runtime testing

before being considered final.

---

# 16. Prototype Signal Topologies

## WF4 candidate

```text
                  Huidu HD-WF4
                       │
       ┌───────────────┼───────────────┐
       │               │               │
       ▼               ▼               ▼
      X1              X2              X3

       │               │               │
       ▼               ▼               ▼

    TOP-L            MID-L           BOT-L
       │               │               │
      OUT             OUT             OUT
       │               │               │
       ▼               ▼               ▼
    TOP-R            MID-R           BOT-R
```

---

## ESP32 candidate

```text
ESP32-S3 N16R8
      │
      │ 3.3 V GPIO
      ▼
2 × SN74HCT245N
      │
      ▼
HUB75E 16-pin header
      │
      ▼
Panel
```

Panel power is separate:

```text
5 V PSU ─────────────► Panel power input
```

The panel's high-current load must not pass through the ESP32 adapter.

---

# 17. Receipt Checklist

When the shipment arrives, update this BOM.

For each item:

```text
[ ] Received
[ ] Quantity correct
[ ] Model correct
[ ] Visible damage checked
[ ] Photograph taken if relevant
[ ] PCB revision recorded
[ ] Marking/model recorded
[ ] Basic test completed
```

Particularly important to inspect:

```text
[ ] HD-WF4 PCB revision
[ ] HD-WF2 PCB revision
[ ] ESP32-S3 exact board dimensions
[ ] ESP32 module marking confirms N16R8
[ ] LicheeRV Nano-W is W/Wi-Fi version
[ ] PSU model/rating
[ ] HUB75 cable orientation
[ ] LED panel HUB75E pin labels
[ ] SN74HCT245N markings
[ ] C14 fuse-holder dimensions
```

---

# 18. BOM Maintenance Rules

This document should distinguish between:

### Purchased

A component exists because it was useful for prototype testing.

### Tested

The component has been experimentally validated.

### Final

The component has been selected for the actual V1 architecture.

Do not automatically change:

```text
ORDERED → FINAL
```

after a successful delivery.

A controller should only become `FINAL` after the relevant experiments in [`EXPERIMENTS.md`](EXPERIMENTS.md) have passed and the corresponding architecture decision has been recorded in [`DECISIONS.md`](DECISIONS.md).

---

# 19. Related Documentation

* [`PROJECT.md`](PROJECT.md) — Product requirements and architecture
* [`STATUS.md`](STATUS.md) — Current project state
* [`EXPERIMENTS.md`](EXPERIMENTS.md) — Hardware validation plans and results
* [`DECISIONS.md`](DECISIONS.md) — Accepted architectural decisions

