# AI Frame — Bill of Materials

**Last updated:** 18 August 2026

## Status Values

`ALREADY OWNED | ORDERED | RECEIVED | TESTED | FINAL | OPTIONAL | REJECTED`

A component marked `ORDERED` does **not** mean it has been selected for the final product.

## Component Inventory

### Core Display Hardware

| Item (EN) | Item (ZH) | Model / Spec | Qty | Unit ¥ | Status | Notes | Link |
|-----------|-----------|--------------|-----|--------|--------|-------|------|
| P2 indoor RGB HUB75E LED matrix module | P2室内全彩LED显示屏模组 / HUB75E LED显示屏模组 | P2, 128×64, 2 mm pitch, 256×128 mm, 1/32 scan, 5 V, ~23 W (seller-rated max), HUB75E, SMD1515 | 6 | — | ALREADY OWNED | Seller/model ref: P2-32S (Generic). Main display. | [淘宝](https://item.taobao.com/item.htm?id=1040063420578) |
| 16-pin HUB75 IDC ribbon cable | HUB75排线 / LED显示屏16P排线 / 16针灰排线 | 16-pin, ~40 cm | 6 | — | ORDERED | Controller → first panel; panel OUT → next panel IN. 6 cables fit the 3-row topology; 2 spares optional. | [淘宝](https://item.taobao.com/item.htm?id=38333799075) |

### Application Computer

| Item (EN) | Item (ZH) | Model / Spec | Qty | Unit ¥ | Status | Notes | Link |
|-----------|-----------|--------------|-----|--------|--------|-------|------|
| Sipeed LicheeRV Nano-W | 矽速科技 LicheeRV Nano-W / 荔枝派 Nano开发板 | SG2002 (算能 SG2002), 256 MB DDR3, microSD, Wi-Fi, USB 2 OTG Type-C, Linux | 1 | — | ORDERED | Linux/Python app host: API access, Wi-Fi, image rendering, application state, scheduling, display transport. Not for real-time HUB75 GPIO refresh. | [淘宝](https://item.taobao.com/item.htm?id=765832577675) |
| Lenovo microSD / TF card | 联想 TF存储卡 / microSD卡 | 64 GB | 1 | — | ORDERED | Linux system storage for the Nano. | [淘宝](https://item.taobao.com/item.htm?id=944218533288) |
| USB-C data cable | Type-C数据线 / USB-C数据线 | — | — | — | ALREADY OWNED | Nano setup, ESP32 programming, USB communication. No additional cable required. | — |
| Raspberry Pi | — | — | 0 | — | REJECTED | Unnecessary cost/capability for V1; workload fits a cheaper Linux SBC. | — |

### Controller Candidates

| Item (EN) | Item (ZH) | Model / Spec | Qty | Unit ¥ | Status | Notes | Link |
|-----------|-----------|--------------|-----|--------|--------|-------|------|
| Huidu HD-WF4 Wi-Fi LED controller card | 灰度 HD-WF4 WiFi控制卡 / LED显示屏控制卡 | HD-WF4, 4× HUB75 outputs | 1 | ¥57 | ORDERED | Huidu / 灰度科技 (深圳市灰度科技有限公司). Primary stock-controller candidate. Open Q: can the Nano push dynamic content to it? | [淘宝](https://item.taobao.com/item.htm?id=978382694524) |
| Huidu HD-WF2 Wi-Fi LED controller card | 灰度 HD-WF2 WiFi控制卡 / LED显示屏控制卡 | HD-WF2, 2× HUB75 outputs | 1 | ~¥35 | ORDERED | Experimental/reference; community reverse-engineering, WLED-MM support; useful comparison vs WF4. | [淘宝](https://item.taobao.com/item.htm?id=978382694524) |
| ESP32-S3 DevKitC-style development board | ESP32-S3-N16R8开发板 / ESP32-S3 DevKitC-1开发板 | N16R8: ESP32-S3, 16 MB flash, 8 MB octal PSRAM, USB Type-C | 1 | ¥29.54 | ORDERED | Chip: Espressif / 乐鑫科技 (board Generic). Tests the open path Nano → ESP32-S3 → HCT245 → HUB75E. Reference design: PatternFlow (physical layout/pinout). Buy more only after it drives real panels. | [淘宝](https://item.taobao.com/item.htm?id=896606984020) |
| Integrated ESP32-HUB75 controller (e.g. Waveshare ESP32-S3-RGB-Matrix) | — | ¥130–160 class | 0 | — | REJECTED | Poor unit economics: Huidu boards cheaper, generic ESP32-S3 ~¥30, custom buffer PCB very cheap. | — |

### ESP32 Prototype Components

| Item (EN) | Item (ZH) | Model / Spec | Qty | Unit ¥ | Status | Notes | Link |
|-----------|-----------|--------------|-----|--------|--------|-------|------|
| SN74HCT245N octal bus transceiver / logic buffer | SN74HCT245N 八路总线收发器 / 八路缓冲器 / 电平转换芯片 | DIP-20 (双列直插20脚) | 2 | ¥5.20 (total) | ORDERED | Mfr: Texas Instruments / 德州仪器 (subject to delivered part). Buffers 3.3 V ESP32 GPIO → 5 V HCT for ~14 HUB75E signals. Reference design: Seengreat RGB Matrix Adapter V3.9. | [淘宝](https://item.taobao.com/item.htm?id=705441896558) |
| 100 nF / 0.1 µF ceramic capacitor | 104瓷片电容 / 0.1uF陶瓷电容 | marking 104, ~50 V | 2 (1 pack) | ¥1 | ORDERED | One decoupling cap beside each HCT245. | [淘宝](https://item.taobao.com/item.htm?id=43410587709) |
| 1000 µF 16 V electrolytic capacitor | 1000UF 16V电解电容 | 1000 µF, 16 V | 0–1 (1 pack) | ¥1.94 | ORDERED | Optional bulk cap on the 5 V rail; footprint may go on the custom PCB. | [淘宝](https://item.taobao.com/item.htm?id=788897776544) |
| 2×8 16-pin 2.54 mm keyed IDC box header | 2×8P 2.54mm牛角座 / 简牛座 / IDC排线插座 | 2×8, 2.54 mm pitch | 1 (1 pack) | ¥2.10 | ORDERED | Standard keyed 16-pin HUB75E cable connection. | [淘宝](https://item.taobao.com/item.htm?id=628665828342) |
| 5×7 cm double-sided prototype PCB / perfboard | 5×7cm双面洞洞板 / 万用板 / 实验板 | ~5×7 cm, 2.54 mm pitch | 1 pack | ¥3 | ORDERED | Fallback for hand-assembling the HCT245 adapter; may be unnecessary if the custom PCB is ready first. | [淘宝](https://item.taobao.com/item.htm?id=663881840604) |
| Bidirectional level shifters (TXS0108E, MOSFET I²C modules) | — | — | 0 | — | REJECTED | Not suitable for the high-speed unidirectional HUB75 signal path. | — |

### Power Infrastructure

| Item (EN) | Item (ZH) | Model / Spec | Qty | Unit ¥ | Status | Notes | Link |
|-----------|-----------|--------------|-----|--------|--------|-------|------|
| 5 V 40 A 200 W switching power supply | 5V40A 200W开关电源 / LED显示屏电源 | A-200-5, 5 V / 40 A / 200 W (nominal), ~199×110×50 mm | 1 | ¥43 | ORDERED | Centralized 5 V supply; nominal headroom but needs load + thermal testing before FINAL. | [淘宝](https://item.taobao.com/item.htm?id=521178544848) |
| 1-to-4 LED panel power harness | LED显示屏一拖四电源线 / 一拖四全彩模组电源线 | Pure copper (纯铜) | 2 | ¥3.10 | ORDERED | 2 harnesses = 8 branches for 6 panels. Panels receive parallel power, never daisy-chained. | [淘宝](https://item.taobao.com/item.htm?id=564279996011) |
| 18 AWG red/black silicone power wire | 18AWG红黑并线 / 硅胶电源线 / 红黑电源线 | ~0.75 mm² | 5 × 1 m | — | ORDERED | 5 V distribution, panel branches, low-voltage controller power. Not for AC mains. | [淘宝](https://item.taobao.com/item.htm?id=597211562418) |
| IEC C14 fused/switched power inlet | C14品字插座 / 带保险丝带开关电源插座 / 三合一电源插座 | IEC C14 inlet, illuminated rocker switch, fuse holder | 1 | ¥4.54 | ORDERED | Fused + switched mains inlet for the PSU. | [淘宝](https://item.taobao.com/item.htm?id=1057972413606) |
| T2A 5×20 mm slow-blow glass fuse | T2A 5×20慢熔玻璃保险丝 / 延时保险管 | 2 A time-delay, 5×20 mm | 1 pack | ¥7.50 | ORDERED | Intended for the C14 inlet; verify fit and rating on receipt. | [淘宝](https://item.taobao.com/item.htm?id=624051469133) |
| 3-core 0.75 mm² AC power cable | 三芯0.75平方电源线 / 国标三芯电源线 | 3 × 0.75 mm² | ~1 m | — | ORDERED | Internal AC wiring (C14 inlet → PSU). | [淘宝](https://item.taobao.com/item.htm?id=936130512179) |
| 6.3 mm insulated female spade / quick-disconnect terminal | 6.3mm插簧端子 / 母插簧 / 冷压接线端子 | 6.3 mm | 1 pack | ¥2.10 | ORDERED | Connects to the rear tabs of the C14 inlet/switch assembly. | [淘宝](https://item.taobao.com/item.htm?id=843030934300) |

### Wiring & Connectors

| Item (EN) | Item (ZH) | Model / Spec | Qty | Unit ¥ | Status | Notes | Link |
|-----------|-----------|--------------|-----|--------|--------|-------|------|
| Bootlace ferrule assortment | 管型冷压端子套装 / 欧式端子 | ~200-piece assortment | 1 kit | — | ORDERED | Clean termination of stranded conductors into PSU screw terminals. | [淘宝](https://item.taobao.com/item.htm?id=848037946178) |
| HS-202D ferrule crimping tool | HS-202D管型端子压线钳 | HS-202D | 1 | ¥16.90 (kit) | ORDERED | Purchased together with the ferrule assortment. | [淘宝](https://item.taobao.com/item.htm?id=848037946178) |
| 1×40 2.54 mm male pin header strip | 1×40P 2.54mm排针 / 单排排针 | 1×40, 2.54 mm | 1 pack (~5 strips) | ¥2.66 | ORDERED | Prototyping, controller connections, custom adapter PCB, removable signal interfaces. | [淘宝](https://item.taobao.com/item.htm?id=1052841742987) |
| 26 AWG Dupont jumper wires | 杜邦线 / 2.54mm杜邦线 | ~26 AWG | confirm on receipt | — | ORDERED | Low-current GPIO, UART, temporary prototyping. Not for panel power, high-current 5 V, or AC mains. | [淘宝](https://item.taobao.com/item.htm?id=626699339752) |

### Development & Debugging Tools

| Item (EN) | Item (ZH) | Model / Spec | Qty | Unit ¥ | Status | Notes | Link |
|-----------|-----------|--------------|-----|--------|--------|-------|------|
| CH340 USB-to-TTL serial adapter | CH340 USB转TTL模块 / USB转串口模块 | CH340 | 1 | ¥6 | ORDERED | Chip: WCH / 南京沁恒微电子 (module Generic). Serial debugging of boards such as the Nano. Do not feed 5 V to an already-powered target. | [淘宝](https://item.taobao.com/item.htm?id=37946005623) |
| CHINT digital multimeter | 正泰数字万用表 | ZTW0138A | 1 | ¥35 | ORDERED | DC/AC voltage, continuity, polarity, resistance, basic troubleshooting. Do not measure full display current in series. | [淘宝](https://item.taobao.com/item.htm?id=674233137778) |
| 60 W digital temperature-controlled soldering iron kit | 60W数显调温电烙铁套装 | 60 W | 1 | ¥34.80 | ORDERED | Headers, perfboard, HCT245 adapter, custom PCB, low-voltage wiring. Verify kit includes solder/flux/stand/tips. | [淘宝](https://item.taobao.com/item.htm?id=950417733333) |

### Consumables

| Item (EN) | Item (ZH) | Model / Spec | Qty | Unit ¥ | Status | Notes | Link |
|-----------|-----------|--------------|-----|--------|--------|-------|------|
| Heat-shrink tubing assortment | 热缩管套装 / 绝缘热缩套管 | ~560-piece assortment | 1 kit | ¥13 | ORDERED | Insulating and protecting electrical joints. | [淘宝](https://item.taobao.com/item.htm?id=596563754516) |

### Deferred / Future Items

| Item (EN) | Item (ZH) | Model / Spec | Qty | Unit ¥ | Status | Notes | Link |
|-----------|-----------|--------------|-----|--------|--------|-------|------|
| Custom ESP32-HUB75 adapter PCB | — | 5 boards, 2-layer FR-4, 1.6 mm, 1 oz Cu, green mask / white silk, HASL, <100 × 100 mm | 5 | ¥0–30 (5 boards) | OPTIONAL | Contents: ESP32-S3 socket, 2× SN74HCT245N, HUB75E 2×8 header, 5 V/GND, optional bulk cap. Fabricate only after the ESP32 board arrives and is measured. | — |

Fabrication services researched (PCB prototyping):

| English Name | Chinese Name | Role |
|--------------|--------------|------|
| JLCPCB | 嘉立创 | PCB prototyping |
| Jiepei | 捷配 | PCB prototyping |
| JDBPCB / related services | 捷多邦 / 聚多邦 | PCB prototyping |

## Safety Notes

- Do not measure full display current in series — the 27.6 A maximum exceeds the typical multimeter 10 A shunt.
- Do not connect the CH340 5 V output to a board already receiving external 5 V power.

## Receipt Inspection Checklist

When the shipment arrives, cross-check each item against this BOM:

1. [ ] Received; quantity correct
2. [ ] Model / spec correct; photograph taken if relevant
3. [ ] Visible damage checked
4. [ ] HD-WF4 and HD-WF2 PCB revisions recorded
5. [ ] ESP32-S3 exact board dimensions measured; N16R8 module marking confirmed
6. [ ] LicheeRV Nano-W confirmed as the Wi-Fi (W) version
7. [ ] PSU model/rating (A-200-5) and C14 fuse-holder dimensions checked
8. [ ] HUB75 cable orientation and LED panel HUB75E pin labels checked
9. [ ] SN74HCT245N markings recorded
10. [ ] Basic power-on / function test completed

## Lifecycle Rules

- ORDERED → RECEIVED on physical delivery and inspection.
- RECEIVED → TESTED after the relevant experiment in [EXPERIMENTS.md](EXPERIMENTS.md) passes.
- TESTED → FINAL after the architecture decision in [DECISIONS.md](DECISIONS.md) is ACCEPTED.
- Components not selected for V1 are marked REJECTED with rationale.

## Related Documentation

- [PROJECT.md](PROJECT.md) — product requirements and architecture
- [STATUS.md](STATUS.md) — current project state
- [EXPERIMENTS.md](EXPERIMENTS.md) — hardware validation plans and results
- [DECISIONS.md](DECISIONS.md) — accepted architectural decisions