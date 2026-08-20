# Phase 02 - LicheeRV Nano Software Bootstrap (M0 / M1)

Covers the Nano software bootstrap and resolution-agnostic rendering foundation. Tasks are independent of display hardware after the Nano and microSD are available.

---

### AF-025 - Flash LicheeRV Nano-W microSD card

**Milestone:** M1
**Depends on:** -
**Labels:** software nano docs
**Context:** Download the official Sipeed SG2002 lightweight headless image, verify its SHA256, write it to the Lenovo 64 GB microSD, and verify the resulting partitions.

#### Do
1. Download the selected official image and published checksum; record both filenames and the checksum result.
2. Verify the download, write the image with Etcher or `dd`, and safely eject the card.
3. Inspect the written card with host tools and record the partitions/filesystem types actually present.

#### Done when
- The checksum verification passes for the exact image written.
- The write completes without an I/O error and the card is safely ejected.
- The resulting partition layout is recorded; any difference from image documentation is marked for follow-up.

#### If it fails
Do not boot from a card with a failed checksum or write. Re-download a failed image; if writing fails, re-identify the media/device path, preserve the command error, and repeat only after that check.

---

### AF-026 - Verify Nano first boot, serial console, kernel log, and OS resources

**Milestone:** M1
**Depends on:** AF-025
**Labels:** software nano safety-review docs validation
**Safety:** CH340
**Stop condition:** Do not connect CH340 VCC or 3.3 V; disconnect the adapter before changing target power.
**Context:** Establish a trustworthy Nano baseline through the strict CH340 3-pin serial connection, then capture the boot/kernel evidence and the actual OS, CPU, memory, storage, and Python environment.

#### Do
1. Power the Nano only from its dedicated 5 V USB-C source; connect CH340 TX, RX, and GND only, record or photograph the unused VCC/3.3 V lead, and open serial at 115200 8N1.
2. Capture the complete visible boot sequence through a login prompt, save the raw serial capture and `dmesg`, and identify successful, absent, or failed initialization messages without treating absence as hardware failure.
3. Run and save unedited output from `cat /etc/os-release`, `free -m`, `cat /proc/cpuinfo`, storage inspection, and `python3 --version`; compare observations with the Nano-W/SG2002 authority and record discrepancies.
4. Disconnect CH340 before any Nano power-source change and store the board/image identifiers with the environment-audit record.

#### Done when
- The serial log shows boot output and a usable login prompt at 115200 8N1, with the CH340 3-pin rule evidenced.
- Raw serial and kernel logs identify the image and distinguish observed initialization from unavailable or unknown devices.
- The OS, CPU, memory, storage, and Python baseline is recorded from measured command output, including any mismatch with expectations.

#### If it fails
Remove CH340 and power down before inspecting wiring. Correct TX/RX or serial settings one variable at a time; preserve partial logs and command output. Return to AF-025 if the boot media or console cannot be trusted.

---

### AF-181 - SUPERSEDED

Replaced by: AF-026

(Do not export to Jira)

---

### AF-182 - SUPERSEDED

Replaced by: AF-026

(Do not export to Jira)

---

### AF-027 - Configure Nano Wi-Fi, SSH, and Python virtual environment

**Milestone:** M1
**Depends on:** AF-026
**Labels:** software nano
**Context:** Make the Nano reachable and capable of running reproducible Python software using the OS/network tooling discovered by AF-026.

#### Do
1. Use the discovered network manager/tooling to configure Wi-Fi with project credentials, record the active interface and assigned address, and apply the agreed DHCP reservation or supported static setup.
2. Verify `ai-frame.local` when mDNS is available; otherwise record the supported name/address-discovery result. Install the host public key and prove a non-interactive SSH login using the recorded hostname/address.
3. Create `~/ai-frame-venv` with the available `python3 -m venv`, upgrade available packaging tools, install only OS-supported build dependencies, and freeze the resulting baseline to `requirements-base.txt` with Python/package versions.

#### Done when
- Wi-Fi is active with an observed address; name resolution or its documented alternative reaches the Nano.
- Key-based SSH succeeds without a password prompt and is restricted to the intended account.
- The venv activates, `python --version` and `pip --version` run from it, and `requirements-base.txt` records reproducible available packages or explicit blockers.

#### If it fails
Revert only the changed network profile if the Nano becomes unreachable, then diagnose through serial. Test the recorded IP before changing Wi-Fi for a name-resolution failure. Keep password access while repairing SSH permissions; do not substitute an untracked Python environment for a missing dependency.

---

### AF-028 - SUPERSEDED

Replaced by: AF-027

(Do not export to Jira)

---

### AF-029 - Install Pillow and implement standard test pattern generator library

**Milestone:** M1
**Depends on:** AF-027
**Labels:** software nano
**Context:** Confirm the Nano can generate reusable 128x64 RGB image content for controller validation.

#### Do
1. Activate the Nano venv, install an environment-compatible Pillow version, and record the installed version and selection basis.
2. Generate a 128x64 RGB `#FF0000` PNG; assert dimensions, mode, and sampled pixels programmatically, retaining the artifact and assertion output.
3. Implement six named generators: solid fills, 8x8 checkerboard, diagonal lines, horizontal gradient, vertical gradient, and coordinate grid. Generate all six at 128x64 and assert dimensions, RGB mode, and a pattern-specific sentinel for each.

#### Done when
- Pillow imports from the venv and the solid-red smoke image is exactly 128x64 RGB with the expected pixels.
- All six generators return 128x64 RGB images and pass their documented sentinel assertions; one artifact per pattern is retained.
- Font availability and any coordinate-label limitation are recorded for later rendering work.

#### If it fails
Check venv activation and import paths first. Keep the failing pattern isolated, compare its expected sentinel to generated pixels, fix that generator, then rerun the complete six-pattern suite.

---

### AF-031 - SUPERSEDED

Replaced by: AF-029

(Do not export to Jira)

---

### AF-030 - Render dynamic text onto 128x64 RGB canvas

**Milestone:** M1
**Depends on:** AF-029
**Labels:** software nano
**Context:** Prove arbitrary runtime text can be rendered onto the target canvas with an explicit clipping/wrapping policy.

#### Do
1. Implement a renderer accepting a runtime string and producing a 128x64 RGB image using an available Pillow font.
2. Run short and long inputs that exercise the documented clipping/wrapping policy; save both outputs.
3. Inspect rendered bounds programmatically and record font, placement, input, and bounding-box results.

#### Done when
- The renderer accepts runtime input and emits a 128x64 RGB image without exception.
- Every requested character that fits the documented policy appears, and no pixel is written outside the canvas.
- The test record identifies the font and measured bounds.

#### If it fails
Preserve the failing input/output. Check font availability, image mode, and text bounds independently; correct placement or the policy, then rerun both short and long cases.

---

### AF-032 - Implement canonical framebuffer, pluggable transport interface, and resolution-agnostic scaling validation

**Milestone:** M1
**Depends on:** AF-030
**Labels:** software nano validation
**Context:** Provide the controller-neutral RGB24 framebuffer and transport boundary used by every candidate controller path.

#### Do
1. Implement `Framebuffer` with `new`, `set_pixel`, `get_region`, `export_raw_bytes`, and `size`, using RGB24 row-major bytes; test construction, mutation/readback, region extraction, byte order, and bounds with deterministic fixtures.
2. Define abstract `DisplayTransport.send_frame(buffer, width, height)`, the `TransportError` hierarchy, and a `StubTransport` that logs width, height, and payload length. Test the exact signature, abstractness, error behavior, and known-frame stub record; placeholder candidate transports must raise rather than pretend to send hardware frames.
3. Generate the same patterns through the unchanged API at 128x64, 256x64, 256x128, and 256x192; assert `width x height x 3` byte lengths and verify a row-crop region for each applicable fixture.

#### Done when
- All framebuffer, interface, and stub tests pass; exported RGB24 length and first-pixel byte order are correct.
- A concrete test transport receives the exact documented method arguments, while unimplemented candidates cannot be mistaken for live transports.
- The four-resolution assertion table and row-crop results show no resolution-specific renderer branch.

#### If it fails
Use the smallest failing fixture to separate coordinate, bounds, channel-order, and interface errors. Do not change transport code to hide a framebuffer defect; correct the owning component and rerun the full test set.

---

### AF-033 - SUPERSEDED

Replaced by: AF-032

(Do not export to Jira)

---

### AF-034 - SUPERSEDED

Replaced by: AF-032

(Do not export to Jira)

---

### AF-036 - SUPERSEDED

Replaced by: AF-032

(Do not export to Jira)

---

### AF-035 - Execute end-to-end software pipeline smoke test and commit Nano bootstrap codebase

**Milestone:** M1
**Depends on:** AF-032
**Labels:** software nano validation docs
**Context:** Prove the Nano application -> renderer -> framebuffer -> stub transport path works end-to-end and leave a reproducible code/evidence baseline for controller integrations.

#### Do
1. Invoke the CLI with arbitrary text, then with empty or short input; render through Pillow, load the `Framebuffer`, dispatch to `StubTransport`, and retain both exit statuses, rendered artifacts, and stub records.
2. Verify each stub record's dimensions and payload length against the RGB24 framebuffer output. Review source, tests, setup notes, and artifacts for consistent paths, commands, and API names.
3. Run the complete software suite from a clean shell, inspect the staged-file list, commit only the Nano bootstrap source/tests/docs, and record the commit hash in the evidence index.

#### Done when
- Both CLI runs exit 0 and reach the stub without vendor UI or hardware-controller involvement.
- Stub dimensions/payload length match the requested framebuffer, and a clean-shell complete suite result is recorded.
- The bootstrap files and evidence index are committed with their hash and no required artifact is left untracked.

#### If it fails
Run renderer, framebuffer, and stub separately with the same input; fix the first failing stage and preserve its output. Do not commit a red suite; correct the first missing path, dependency, or documentation mismatch and inspect staging before retrying.

---

### AF-037 - SUPERSEDED

Replaced by: AF-035

(Do not export to Jira)
