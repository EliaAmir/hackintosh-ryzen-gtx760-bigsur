# GTX 760 (Kepler) — macOS Support Notes

## Kepler Architecture

NVIDIA Kepler GPUs include the GTX 600 series (GTX 650, 660, 670, 680) and GTX 700 series (GTX 750 Ti excluded — Maxwell; GTX 760, 770, 780).

The GTX 760 is a Kepler GPU (GK104 chip).

---

## macOS Support Timeline

| macOS Version | GTX 760 Support |
|--------------|----------------|
| OS X 10.8 Mountain Lion | Supported (NVIDIA Web Driver) |
| OS X 10.9 Mavericks | Supported |
| OS X 10.10 Yosemite | Supported |
| OS X 10.11 El Capitan | Supported |
| macOS 10.12 Sierra | Supported |
| macOS 10.13 High Sierra | Supported (last version with NVIDIA Web Driver) |
| macOS 10.14 Mojave | Supported (native Kepler driver) |
| macOS 10.15 Catalina | Supported (native Kepler driver) |
| macOS 11 Big Sur | **Supported (native Kepler driver) — MAXIMUM VERSION** |
| macOS 12 Monterey | **Not supported — Kepler driver removed** |
| macOS 13 Ventura | Not supported |
| macOS 14 Sonoma | Not supported |

---

## Why Big Sur is the Ceiling

Apple removed the legacy NVIDIA Kepler driver when they dropped support for it in Monterey. This was part of the same deprecation sweep that removed support for Metal-incompatible GPUs.

There is no NVIDIA Web Driver for Monterey or later (NVIDIA stopped updating the Web Driver after High Sierra due to the Apple-NVIDIA driver dispute). There is no third-party workaround that restores GPU acceleration for Kepler on Monterey+.

**If you want GPU acceleration with a GTX 760, Big Sur is your maximum macOS version.**

---

## Boot Args for Kepler Detection

For the GTX 760 in Big Sur, no special boot args are required — Kepler is natively detected. Standard graphics boot args:

```
agdpmod=pikera
```
Note: `agdpmod=pikera` is for AMD/Intel GPU framebuffer fixes and is not needed for NVIDIA. Include it only if you have a secondary Intel GPU on the same system.

For NVRAM:
```
nvda_drv=1
```
This was required for the NVIDIA Web Driver on older macOS versions. It is not needed for native Kepler support in Big Sur, but harmless if left in.

---

## Testing GPU Acceleration

Verify Kepler acceleration is active:
1. System Information → Graphics → confirm the GTX 760 shows metal support and VRAM
2. Open Activity Monitor → GPU History — should show GPU utilization on any graphical task
3. Run Geekbench GPU benchmark to confirm hardware acceleration

If the display is working but GPU History shows no activity or System Information shows no VRAM, the Kepler native driver is not loading.
