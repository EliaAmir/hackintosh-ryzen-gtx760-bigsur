# Troubleshooting — AMD Ryzen + GTX 760 Hackintosh

## "Still waiting for root device"

**Symptom:** Boot stalls at `Still waiting for root device` and never progresses. The macOS installer or installed macOS volume is not found.

**Cause:** USB port mapping issue. The boot drive (USB installer or internal SATA/NVMe) is connected via a USB controller that macOS cannot see because it hasn't been mapped.

**Fix:**
1. Try all available USB ports — some may work even without a map
2. For NVMe/SATA drives: verify the SATA mode is AHCI (not RAID/IDE)
3. Build a proper USB map using USBToolBox (see `docs/amd-ryzen-opencore-guide.md`)
4. If using a USB installer and the installer itself isn't loading: add `GenericUSBXHCI.kext` temporarily to enable all USB controllers during install, then remove it after and add a proper map

---

## Kernel Panic on AMD — "unable to find driver"

**Symptom:** Immediate kernel panic on boot. Log shows `unable to find driver for this platform: "ACPI"` or similar. Or panic references `_mach_msg_trap`.

**Cause:** AMD Vanilla kernel patches are not applied, are the wrong version, or have incorrect core count.

**Fix:**
1. Verify the patches in `Kernel > Patch` in config.plist match the macOS version (Big Sur = `macOS 11`)
2. Verify the CPU architecture matches (Zen/Zen+ for Ryzen 1200)
3. Verify the core count in `algrey - Force cpuid_cores_per_package` matches your CPU (4 for Ryzen 3 1200)
4. Verify `Kernel > Quirks > ProvideCurrentCpuInfo` = True
5. Boot with `-v` (verbose) to see the exact panic message

---

## No GPU Acceleration — Kepler Not Detected

**Symptom:** Display works, but System Information shows the GTX 760 without VRAM, or GPU History in Activity Monitor shows no GPU usage.

**Cause:** macOS version is Monterey or later (Kepler support was dropped), OR the GPU is not being detected as Kepler.

**Fix:**
1. Confirm macOS version is Big Sur (11.x) — check Apple menu → About This Mac
2. If on Big Sur, reboot and check OpenCore logs for GPU detection errors
3. Verify the GPU is properly seated in the PCIe slot
4. If using an AMD + NVIDIA system: ensure WhateverGreen.kext is present (it handles framebuffer patches for multiple GPU configurations)

---

## Verbose Boot Shows AMD Panic Immediately

**Symptom:** With `-v` in boot-args, boot fails immediately after the Apple logo with a text kernel panic referencing `_cpu_*` or `_xcpm_*`.

**Cause:** These are Intel-specific CPU functions that AMD doesn't implement. AMD Vanilla patches patch these callsites, but if the patches aren't loading, these panics occur.

**Fix:**
1. Check that all AMD Vanilla patches have `Enabled` = True in config.plist
2. Check for typos in the patch binary data (Find/Replace fields must be exact hex)
3. Verify OpenCore version compatibility — patches from AMD_Vanilla are version-specific
