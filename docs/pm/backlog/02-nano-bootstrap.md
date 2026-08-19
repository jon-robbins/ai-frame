# Phase 02 — LicheeRV Nano Software Bootstrap (M0 / M1)

Covers the Nano software bootstrap and resolution-agnostic rendering foundation. Tasks are independent of display hardware after the Nano and microSD are available.

---

### AF-025 — Flash LicheeRV Nano-W microSD card

**Milestone:** M1
**Depends on:** —
**Labels:** software nano docs
**Context:** Download official Sipeed SG2002 Linux image (Debian/Buildroot lightweight headless), verify SHA256, write to Lenovo 64GB microSD via Etcher/dd, and verify dual partitions.

#### Do
1. Download the selected official Sipeed SG2002 lightweight headless image and its published checksum; record both filenames and the checksum result.
2. Verify the downloaded file before writing it to the Lenovo 64GB microSD, write the image with Etcher or `dd`, and safely eject the card.
3. Inspect the written card with the host tools and record the partitions/filesystem types that are actually present.

#### Done when
- The checksum verification passes for the exact image written.
- The write completes without an I/O error and the card is safely ejected.
- The resulting partition layout is recorded; any difference from the image documentation is explicitly marked for follow-up.

#### If it fails
Do not boot from a card with a failed checksum or write. Re-download the image if verification fails; if writing fails, inspect the card/device path, repeat only after the media is re-identified, and preserve the command error.

### AF-026 — Verify Nano first boot and serial console connection

**Milestone:** M1
**Depends on:** AF-025
**Labels:** software nano safety-review
**Safety:** CH340
**Stop condition:** Do not connect CH340 VCC or 3.3 V; disconnect the adapter before changing target power.
**Context:** Connect CH340 to Nano following the strict 3-pin rule (TX, RX, GND only; zero VCC connection); power Nano via dedicated 5V USB-C; verify kernel boot output and reach login prompt over 115200 8N1 serial.

#### Do
1. Power the Nano through its dedicated 5V USB-C source and connect only CH340 TX, RX, and GND; photograph or record that VCC/3.3V is unused.
2. Open the serial device at 115200 8N1, reset or boot the Nano, and capture the complete visible boot sequence.
3. Confirm a login prompt, then disconnect the CH340 before changing the Nano power source.

#### Done when
- The serial log contains boot output and a usable login prompt at 115200 8N1.
- The connection record shows exactly TX, RX, and GND; CH340 power output is not connected.
- The adapter is disconnected before any power-source change.

#### If it fails
Remove the CH340 from the Nano and power down before inspecting wiring. Correct TX/RX or serial settings one variable at a time; if no output remains, preserve the log and escalate to AF-025 media/boot verification.

### AF-181 — Capture and document first boot kernel log

**Milestone:** M1
**Depends on:** AF-026
**Labels:** software nano docs
**Context:** Capture full dmesg kernel ring buffer and serial console boot log; document hardware initialization status and peripheral device enumeration.

#### Do
1. From the working console, save the complete `dmesg` output and the serial boot capture without filtering out initialization messages.
2. Mark the timestamps or boot boundary, then list successful, failed, and absent device/peripheral initialization messages.
3. Store the raw logs with the observed board/image identifiers and a short interpretation.

#### Done when
- Raw `dmesg` and serial boot logs are both present and readable.
- The report distinguishes observed initialization from unavailable or unrecognized devices.
- The report identifies the image and board used for the capture.

#### If it fails
Keep the original partial capture, reboot once to determine whether the omission is repeatable, and check console permissions or log-storage space. Do not infer hardware failure from a missing message; record it as unresolved for follow-up.

### AF-182 — Verify OS environment and hardware resources

**Milestone:** M1
**Depends on:** AF-026
**Labels:** software nano validation
**Context:** Execute system resource audit (cat /etc/os-release, free -m confirming 256 MB DDR3 RAM baseline, /proc/cpuinfo confirming SG2002 RISC-V/ARM core).

#### Do
1. Run `cat /etc/os-release`, `free -m`, and `cat /proc/cpuinfo` from the Nano console and save their unedited output.
2. Compare reported board, kernel, CPU, and memory information with the SG2002/Nano-W project authority; record discrepancies rather than correcting them in prose.
3. Record available storage and the Python executable/version needed by later tasks.

#### Done when
- All three command outputs are saved and identify the running image.
- The observed memory, CPU architecture, and OS values are explicitly recorded, including any mismatch with expected hardware evidence.
- The result identifies whether the Nano can proceed to network/bootstrap work.

#### If it fails
Preserve the command output. If a command is absent or fails, check the running image and permissions; do not substitute the BOM baseline for measured output, and return to AF-026 if the console or boot image is not trustworthy.

### AF-027 — Configure Nano Wi-Fi and passwordless SSH access

**Milestone:** M1
**Depends on:** AF-182
**Labels:** software nano
**Context:** After AF-182 identifies the delivered OS and available network services, configure the supported Wi-Fi/network tooling, establish name resolution if the environment provides mDNS, and configure passwordless SSH public key authentication.

#### Do
1. Use the network manager and commands available in the AF-182 environment audit to configure Wi-Fi with the project network credentials, then record the interface state and address actually assigned.
2. Apply the agreed DHCP reservation or static configuration using the discovered environment's supported mechanism; if mDNS is available, verify that `ai-frame.local` resolves from the development host, otherwise record the supported name/address-discovery result.
3. Install the host public key, test a non-interactive SSH login, and record the exact hostname/address used.

#### Done when
- The discovered network tooling reports the intended Wi-Fi connection as active and the observed address is recorded.
- Name resolution reaches the Nano using the environment-supported method, with mDNS verified when available or its absence recorded.
- Key-based SSH succeeds without a password prompt and the key remains restricted to the intended account.

#### If it fails
Revert only the changed network profile if the Nano becomes unreachable, then use the serial console to inspect the environment-specific network state. If name resolution alone fails, test the recorded IP before changing Wi-Fi settings; if key login fails, retain password access and repair authorized-key permissions.

### AF-028 — Create minimal Python 3 virtual environment

**Milestone:** M1
**Depends on:** AF-027
**Labels:** software nano
**Context:** Provision a Python 3 environment using the venv/package/build tooling discovered by AF-182, install only dependencies supported by that OS, and generate requirements-base.txt.

#### Do
1. Create `/home/sipeed/ai-frame-venv` with the Nano's available `python3 -m venv` implementation.
2. Upgrade the available packaging tools inside the venv and install image-library build headers only if the discovered OS/package manager provides them.
3. Freeze the resulting baseline to `requirements-base.txt`, recording Python and package versions.

#### Done when
- The venv activates and `python --version` and `pip --version` run from it.
- The dependency installation exits successfully, or each unavailable package is recorded with its blocker.
- `requirements-base.txt` is reproducible from the recorded package list and versions.

#### If it fails
Leave the system package state unchanged beyond the recorded command. Check Python venv support and package-manager errors separately; if a native header is unavailable, record it and stop before attempting an untracked substitute.

### AF-029 — Install Pillow and execute solid color smoke test

**Milestone:** M1
**Depends on:** AF-028
**Labels:** software nano
**Context:** Install a Pillow version supported by the AF-182 OS/Python environment inside the venv and generate a 128×64 pure red (#FF0000) test image; verify dimensions and pixel values programmatically.

#### Do
1. Activate the Nano venv and install the selected environment-compatible Pillow version; record the installed version and selection basis.
2. Generate a 128×64 RGB PNG filled with `#FF0000` and inspect it with a short Python assertion script.
3. Save the image and assertion output with the software test record.

#### Done when
- Pillow imports from the venv and reports the installed version.
- The PNG dimensions are exactly 128×64, mode RGB, and every sampled pixel is `(255, 0, 0)`.
- The assertion command exits 0 and the artifact is retained.

#### If it fails
Check venv activation and Pillow import paths first. If dimensions or channels differ, delete only the failed test artifact, inspect the image-construction code, and rerun the assertions before proceeding to AF-030.

### AF-030 — Render dynamic text onto 128×64 RGB canvas

**Milestone:** M1
**Depends on:** AF-029
**Labels:** software nano
**Context:** Render arbitrary runtime text string onto a 128×64 RGB canvas using Pillow; export PNG and verify character rendering without clipping.

#### Do
1. Implement a renderer accepting a runtime string and producing a 128×64 RGB image using the available Pillow font.
2. Run it with at least one short string and one string long enough to exercise the chosen clipping/wrapping behavior; save both outputs.
3. Inspect the rendered bounds programmatically and record the font and placement inputs.

#### Done when
- The renderer accepts runtime input and emits a 128×64 RGB image without an exception.
- The saved output shows each requested character that fits the documented policy and no pixel is written outside the canvas.
- The test command records the selected font and bounding-box result.

#### If it fails
Preserve the failing input and output. Check font availability, image mode, and measured text bounds independently; adjust placement or clipping policy in code, then rerun both short and long-string cases.

### AF-031 — Implement 6 standard test pattern generator functions

**Milestone:** M1
**Depends on:** AF-030
**Labels:** software nano
**Context:** Implement Python test pattern library generating 6 standard patterns (solid fills, 8×8 checkerboard, diagonal lines, horizontal/vertical gradients, coordinate grid) for 128×64.

#### Do
1. Implement the six named generators: solid fills, 8×8 checkerboard, diagonal lines, horizontal gradient, vertical gradient, and coordinate grid.
2. Generate each at 128×64 and verify mode, dimensions, and a pattern-specific sentinel (for example, corner colors or grid labels).
3. Save the six outputs and the test results under the Nano software test record.

#### Done when
- All six generator functions exist, return 128×64 RGB images, and pass their sentinel assertions.
- The output set contains one artifact per named pattern and records any coordinate-label limitation.
- Re-running the generator test produces the same dimensions and sentinel values.

#### If it fails
Keep the failing pattern isolated and compare its expected sentinel with the generated pixels. Fix only that generator, rerun the complete six-pattern test, and retain the failed output if the defect persists.

### AF-032 — Implement canonical Framebuffer abstraction class

**Milestone:** M1
**Depends on:** AF-031
**Labels:** software nano
**Context:** Implement canonical Framebuffer class with exact API (new, set_pixel, get_region, export_raw_bytes exporting RGB24 row-major byte arrays, size); verify unit test suite.

#### Do
1. Implement `Framebuffer` with `new`, `set_pixel`, `get_region`, `export_raw_bytes`, and `size` using RGB24 row-major ordering.
2. Test construction, pixel mutation/readback, region extraction, byte ordering, and reported size with small deterministic fixtures.
3. Run the framebuffer unit tests and save the result.

#### Done when
- Every named API is callable with the documented arguments and the unit test command exits 0.
- Exported length equals `width × height × 3` for each fixture and the first pixel's bytes match RGB order.
- `get_region` returns the requested bounds without mutating the source framebuffer.

#### If it fails
Use the smallest failing fixture to distinguish coordinate, bounds, and channel-order errors. Do not alter transport code to mask a framebuffer failure; correct the class and rerun all unit tests.

### AF-033 — Define pluggable display transport abstract base interface

**Milestone:** M1
**Depends on:** AF-032
**Labels:** software nano
**Context:** Implement abstract base class DisplayTransport.send_frame(buffer, width, height) and custom TransportError exception hierarchy.

#### Do
1. Define the abstract `DisplayTransport.send_frame(buffer, width, height)` contract and the `TransportError` hierarchy.
2. Add tests for the required signature, abstractness, and error subclass behavior without binding the interface to a controller protocol.
3. Run the interface tests and record the public API surface.

#### Done when
- The interface cannot be instantiated without an implementation of `send_frame`.
- A concrete test implementation can receive the buffer and dimensions through the exact method signature.
- The exception tests pass and no controller pin/protocol assumption is embedded.

#### If it fails
Read the failing test traceback and correct the interface or exception hierarchy only. Keep candidate-specific behavior out of the ABC; rerun interface tests before adding the stub.

### AF-034 — Implement candidate transport skeleton and stub

**Milestone:** M1
**Depends on:** AF-033
**Labels:** software nano
**Context:** Implement StubTransport class logging frame payload length and dimensions, plus stubbed candidate transport subclasses.

#### Do
1. Implement `StubTransport` to log frame dimensions and payload length, plus placeholder candidate classes that satisfy the transport interface.
2. Send a known framebuffer through the stub and inspect the structured log for dimensions and RGB24 byte count.
3. Run the transport skeleton tests and record which candidate classes remain intentionally unimplemented.

#### Done when
- Stub output records the supplied width, height, and exact payload length.
- Placeholder classes import and expose the interface without pretending to transmit hardware frames.
- The skeleton test command exits 0.

#### If it fails
Compare the logged length with the framebuffer export before changing the stub. If a placeholder is accidentally used as a live transport, make it raise the documented error and record that boundary.

### AF-035 — Execute end-to-end software pipeline smoke test

**Milestone:** M1
**Depends on:** AF-034
**Labels:** software nano validation
**Context:** Execute CLI smoke test passing arbitrary command-line text into Pillow renderer, loading into Framebuffer, and dispatching to StubTransport.

#### Do
1. Invoke the CLI with arbitrary text, render it through Pillow, load the result into `Framebuffer`, and dispatch it to `StubTransport`.
2. Capture the CLI exit status and stub record for the supplied text, dimensions, and payload length.
3. Repeat with an empty or short input to verify the command path handles runtime input deterministically.

#### Done when
- Both CLI runs exit 0 and reach the stub without a vendor UI or hardware controller.
- The stub record identifies the requested dimensions and payload length equal to the RGB24 framebuffer output.
- The rendered artifact and command output are retained.

#### If it fails
Run the renderer, framebuffer, and stub stages separately using the same input. Fix the first failing stage, preserve its traceback/output, and rerun the full CLI smoke test only after that stage passes.

### AF-036 — Validate framebuffer scaling across 4 resolutions

**Milestone:** M1
**Depends on:** AF-035
**Labels:** software nano validation
**Context:** Generate test patterns and verify byte lengths across 128×64, 256×64, 256×128, and 256×192 without renderer code modifications; verify sub-region row cropping.

#### Do
1. Generate the same test pattern at 128×64, 256×64, 256×128, and 256×192 through the unchanged renderer/framebuffer API.
2. Assert each exported byte length and extract a sub-region representing a row; record its dimensions and bytes.
3. Compare outputs for deterministic dimensions and document any implementation limitation instead of changing the renderer per resolution.

#### Done when
- All four resolutions pass byte-length checks of `width × height × 3`.
- Sub-region extraction returns the requested row bounds and expected byte length.
- No resolution-specific renderer branch was added for the test.

#### If it fails
Use the failing resolution to isolate dimension arithmetic versus region coordinates. Correct the framebuffer API or test fixture, rerun all four sizes, and preserve any unsupported-size result as an explicit limitation.

### AF-037 — Collate and commit Nano software bootstrap codebase

**Milestone:** M1
**Depends on:** AF-036
**Labels:** software nano docs
**Context:** Commit complete Nano application software framework, venv setup documentation, and test scripts to repository under software/nano/.

#### Do
1. Review the Nano source, venv setup notes, tests, and generated artifacts for consistent paths, commands, and API names.
2. Run the complete software test suite from a clean shell and record the commit candidate, test command, and exit status.
3. Commit only the Nano bootstrap files and link the evidence record to the resulting commit.

#### Done when
- The expected Nano software files, setup documentation, and tests are present under `software/nano/` or the documented project locations.
- The clean-shell test run exits 0 and its command is recorded.
- The commit contains no untracked required bootstrap artifact and its hash is recorded in the subtrack evidence.

#### If it fails
Do not commit a red test run. Preserve the failure output, fix the first missing path/dependency or documentation mismatch, rerun from a clean shell, and inspect the staged file list before retrying the commit.
