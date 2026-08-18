# AI Frame — Connection Diagrams

These diagrams describe the prototype's intended physical and logical connections.

They are deliberately **decision-aware**:

- **Accepted** connections reflect current architecture decisions.
- **Candidate** connections are under evaluation and must not be treated as final wiring.
- **TBD / verify on receipt** means the exact connector, pinout, voltage input, or transport still needs physical validation.

Do not infer exact board pin numbers from these diagrams. Board-specific pin mappings belong in separate schematics after hardware identification.

---

## 1. Whole-System Topology

```mermaid
flowchart LR
    NET[Internet / APIs]
    WIFI[Wi-Fi AP]
    NANO[LicheeRV Nano-W\nLinux + Python\n256×192 renderer]
    CTRL[Display controller\nADR-016 pending]
    DISP[6 × P2 HUB75E panels\n2 × 3 layout\n256 × 192 logical]
    AC[230 V AC]
    PSU[A-200-5\n5 V / 40 A PSU]

    NET --> WIFI
    WIFI -. Wi-Fi .-> NANO
    NANO -->|display data\ntransport TBD| CTRL
    CTRL -->|HUB75E refresh| DISP

    AC --> PSU
    PSU -->|5 V parallel branch| NANO
    PSU -->|5 V parallel branch| CTRL
    PSU -->|6 independent 5 V branches| DISP
```

**Status:** architecture accepted except final controller and Nano→controller transport.

---

## 2. Physical Panel Layout and HUB75 Signal Chaining

The six modules form three independent 256×64 signal rows. Within each row, HUB75 signals may chain from the left panel's `OUT` connector to the right panel's `IN` connector.

```mermaid
flowchart TB
    subgraph TOP[Top row — logical y = 0..63]
        TL[Top-left\n128×64]
        TR[Top-right\n128×64]
        TL -->|HUB75 OUT → IN| TR
    end

    subgraph MID[Middle row — logical y = 64..127]
        ML[Middle-left\n128×64]
        MR[Middle-right\n128×64]
        ML -->|HUB75 OUT → IN| MR
    end

    subgraph BOT[Bottom row — logical y = 128..191]
        BL[Bottom-left\n128×64]
        BR[Bottom-right\n128×64]
        BL -->|HUB75 OUT → IN| BR
    end
```

**Accepted rule:** signal chaining is allowed. Power chaining is not.

---

## 3. Power Distribution

Each panel receives its own 5 V branch. No panel carries downstream panel current.

```mermaid
flowchart LR
    WALL[Wall outlet\n230 V AC]
    C13[IEC C13 cable]
    INLET[C14 fused + switched inlet\nL / N / PE]
    PSU[A-200-5 PSU\n5 V / 40 A nominal]

    WALL --> C13 --> INLET --> PSU

    PSU -->|5 V + GND| P1[Panel 1]
    PSU -->|5 V + GND| P2[Panel 2]
    PSU -->|5 V + GND| P3[Panel 3]
    PSU -->|5 V + GND| P4[Panel 4]
    PSU -->|5 V + GND| P5[Panel 5]
    PSU -->|5 V + GND| P6[Panel 6]
    PSU -->|5 V branch — input method TBD| CTRL[Display controller]
    PSU -->|5 V branch — input method TBD| NANO[LicheeRV Nano-W]

    INLET -->|PE| EARTH[PSU FG / earth\nand exposed conductive enclosure]
```

### Power rules

- Panels are powered in parallel.
- Do not route total display current through a controller board, development board, breadboard, Dupont lead, or panel PCB trace.
- Controller and Nano power input details must be verified against the delivered boards before wiring.
- All low-voltage subsystems that exchange electrical signals must share an appropriate common ground.

---

## 4. Candidate A — Nano + HD-WF4

The WF4 is the primary stock-controller candidate because its four HUB75 outputs map naturally onto the three physical rows.

```mermaid
flowchart LR
    NANO[LicheeRV Nano-W\nPython renderer]
    WF4[Huidu HD-WF4\n4 × HUB75 outputs]

    TL[Top-left]
    TR[Top-right]
    ML[Middle-left]
    MR[Middle-right]
    BL[Bottom-left]
    BR[Bottom-right]

    NANO -->|programmatic frame transfer\nTBD: wired preferred; local network acceptable| WF4

    WF4 -->|X1 → HUB75 IN| TL
    TL -->|OUT → IN| TR

    WF4 -->|X2 → HUB75 IN| ML
    ML -->|OUT → IN| MR

    WF4 -->|X3 → HUB75 IN| BL
    BL -->|OUT → IN| BR

    WF4 -. X4 unused .-> UNUSED[Unused]
```

**Candidate, not final.** EXP-004 through EXP-007 determine whether this path is viable.

---

## 5. Candidate B — Nano + Three ESP32-S3 Controllers

If the ESP32 path wins, the Nano still renders one canonical 256×192 framebuffer. The transport layer crops it into three 256×64 row images.

```mermaid
flowchart LR
    NANO[LicheeRV Nano-W\n256×192 framebuffer]

    E1[ESP32-S3 #1\nTop row]
    E2[ESP32-S3 #2\nMiddle row]
    E3[ESP32-S3 #3\nBottom row]

    H1[2 × SN74HCT245\nbuffer stage]
    H2[2 × SN74HCT245\nbuffer stage]
    H3[2 × SN74HCT245\nbuffer stage]

    TL[Top-left]
    TR[Top-right]
    ML[Middle-left]
    MR[Middle-right]
    BL[Bottom-left]
    BR[Bottom-right]

    NANO -->|crop y=0..63\nUART / USB TBD| E1
    NANO -->|crop y=64..127\nUART / USB TBD| E2
    NANO -->|crop y=128..191\nUART / USB TBD| E3

    E1 -->|~14 HUB75 signals @ 3.3 V| H1
    E2 -->|~14 HUB75 signals @ 3.3 V| H2
    E3 -->|~14 HUB75 signals @ 3.3 V| H3

    H1 -->|buffered HUB75E| TL
    TL -->|OUT → IN| TR

    H2 -->|buffered HUB75E| ML
    ML -->|OUT → IN| MR

    H3 -->|buffered HUB75E| BL
    BL -->|OUT → IN| BR
```

**Candidate, not final.** EXP-010 through EXP-013 validate this path before any three-controller build.

---

## 6. ESP32 → HCT245 → HUB75 Electrical Boundary

This is a functional diagram only. Exact GPIO assignments and HCT245 pin numbers remain TBD until the actual ESP32 board is identified and measured.

```mermaid
flowchart LR
    ESP[ESP32-S3\n3.3 V GPIO]
    BUF1[SN74HCT245 #1\nVCC = 5 V]
    BUF2[SN74HCT245 #2\nVCC = 5 V]
    IDC[2×8 keyed HUB75E header]
    PANEL[P2 HUB75E panel]
    PSU[5 V PSU]

    ESP -->|R1 G1 B1 R2 G2 B2\nand selected control lines| BUF1
    ESP -->|A B C D E\nCLK LAT OE\nand remaining control lines| BUF2

    BUF1 --> IDC
    BUF2 --> IDC
    IDC --> PANEL

    PSU -->|5 V| BUF1
    PSU -->|5 V| BUF2
    PSU -->|5 V high-current branch| PANEL

    PSU --- GND[Common GND]
    GND --- ESP
    GND --- BUF1
    GND --- BUF2
    GND --- PANEL

    C1[100 nF decoupling\nclose to HCT245 #1] --- BUF1
    C2[100 nF decoupling\nclose to HCT245 #2] --- BUF2
```

### HUB75 functional signals

Expected logical signal set for the buffered path:

`R1 G1 B1 R2 G2 B2 A B C D E CLK LAT OE`

The final pin mapping must be validated against:

1. the delivered ESP32-S3 board,
2. the chosen firmware/library GPIO constraints,
3. the panel's actual HUB75E connector orientation/pinout,
4. the adapter PCB schematic.

---

## 7. Nano → Controller Transport Boundary

The application renderer must remain independent of the physical controller transport.

```mermaid
flowchart LR
    API[API clients / local data]
    STATE[Application state]
    RENDER[Renderer\nPillow]
    FB[Canonical\n256×192 RGB framebuffer]
    ADAPT[Transport adapter]

    WF4[WF4 adapter\nprotocol TBD]
    ESP[ESP32 adapter\nUART / USB TBD]

    API --> STATE --> RENDER --> FB --> ADAPT
    ADAPT --> WF4
    ADAPT --> ESP
```

**Accepted software contract:** controller-specific framing, cropping, encoding, retransmission, and reconnection belong below the framebuffer boundary.

---

## 8. Bring-Up Connection Sequence

These are the connection stages implied by the current experiment plan.

```mermaid
flowchart TD
    A[1. Inspect hardware\nand verify connector labels]
    B[2. Bring up PSU with no DC load]
    C[3. Power one panel only]
    D[4. Connect controller → one panel]
    E[5. Chain second panel\nfor one 256×64 row]
    F[6. Scale to all three rows]
    G[7. Add Nano → controller transport]
    H[8. Test full system reboot / recovery]

    A --> B --> C --> D --> E --> F --> G --> H
```

Do not skip directly to full-system wiring before polarity, board revisions, connector orientation, controller power input, and panel behavior have been confirmed.

---

## 9. Diagrams Still Needed After Hardware Arrival

The following should become separate board-specific schematics once the physical hardware is available:

- `nano-power-and-io.md` — exact Nano-W power input and controller-facing I/O.
- `wf4-connections.md` — WF4 power connector, output connector orientation, and tested Nano transport.
- `wf2-connections.md` — experimental/reference wiring only.
- `esp32-hub75-pinmap.md` — actual ESP32 GPIO → HCT245 → HUB75 pin mapping.
- `mains-wiring.md` — C14 inlet switch/fuse terminal identification → PSU L/N/FG, based on the received inlet.
- `panel-power-harness.md` — actual harness connector polarity and branch topology.
- custom PCB schematic under `hardware/pcb/` if ADR-016 selects the ESP32 path.

These should be created from measured hardware and photographs, not seller photos or assumed generic pinouts.
