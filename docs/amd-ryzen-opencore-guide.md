# AMD Ryzen OpenCore Guide

## Why AMD is Harder Than Intel

macOS is designed and tested exclusively on Intel CPUs. The XNU kernel contains low-level code that makes assumptions about Intel's x86 implementation — instruction timing, CPU topology reporting, and specific MSR registers that AMD implements differently or not at all.

The result: macOS panics on boot on unpatched AMD hardware.

The fix: AMD Vanilla kernel patches (from the AMD-OSX/AMD_Vanilla repository on GitHub) modify the kernel at the binary level to remove Intel-specific assumptions. These are boot-time binary patches applied by OpenCore via the `Kernel > Patch` section of config.plist.

---

## AMD Vanilla Kernel Patches

The AMD_Vanilla repo provides a JSON file with patches for each macOS version. For Big Sur on Ryzen 3 1200 (Zen architecture):

1. Download the patches JSON from the AMD-OSX/AMD_Vanilla GitHub repository
2. Select patches for `macOS 11 (Big Sur)` → `Zen/Zen+` (Ryzen 1000/2000 series)
3. Copy each patch entry into `Kernel > Patch` in config.plist
4. Set `Kernel > Quirks > ProvideCurrentCpuInfo` = True (required for AMD)

The number of cores must match your CPU. For Ryzen 3 1200 (4 cores, 4 threads), the patch `algrey - Force cpuid_cores_per_package` must specify 4 cores.

---

## BIOS Settings

These BIOS settings are required before macOS will boot on AMD:

| Setting | Required Value | Why |
|---------|---------------|-----|
| Secure Boot | Disabled | OpenCore cannot boot with Secure Boot |
| fTPM / TPM | Disabled | Causes random kernel panics on Ryzen |
| SATA Mode | AHCI | macOS does not support RAID mode |
| CSM (Compatibility Support Module) | Disabled | Must be off for UEFI-only boot |
| Above 4G Decoding | Enabled | Required for GPU memory mapping above 4GB |
| Resizable BAR / SAM | Disabled | Can cause issues with non-Apple GPU kexts |
| XHCI Handoff | Enabled | Required for USB to function in macOS |

---

## USB Mapping with USBToolBox

AMD platforms require explicit USB port mapping. macOS limits the number of active USB controllers. Without a USB map, some ports may not work or macOS may panic at the USB initialization step.

Process:
1. Boot Windows on the same machine
2. Download and run USBToolBox (Windows version)
3. Plug a USB device into every physical port one at a time and record which port ID lights up in USBToolBox
4. Configure the desired ports in USBToolBox and export UTBMap.kext
5. Add UTBMap.kext and USBToolBox.kext to the EFI
6. Remove any prior USB kexts (GenericUSBXHCI for AMD, if present) to avoid conflicts

---

## config.plist: AMD vs Intel Key Differences

| Setting | Intel | AMD Ryzen |
|---------|-------|-----------|
| `Kernel > Quirks > ProvideCurrentCpuInfo` | False | **True** |
| `Kernel > Quirks > PanicNoKextDump` | Optional | **True** (helps diagnose panics) |
| `Kernel > Patch` | Usually empty | **AMD Vanilla patches required** |
| `Kernel > Emulate > Cpuid1Data` | Usually not needed | **Required for Ryzen** |
| `NVRAM > boot-args` | Standard | Add `-v` during testing for verbose boot |
| `PlatformInfo > SMBIOS` | iMac or MacBook Pro models | Use `MacPro7,1` for AMD builds |

Use `MacPro7,1` as the SMBIOS for AMD Ryzen builds. It is the only modern Mac Pro with a non-Apple CPU architecture (AMD) and triggers the correct kernel paths.
