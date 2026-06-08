# Hackintosh — AMD Ryzen 3 1200 + GTX 760

macOS Big Sur (11.x) on AMD Ryzen 3 1200 with NVIDIA GTX 760. OpenCore bootloader.

---

## Hardware

| Component | Details |
|-----------|---------|
| CPU | AMD Ryzen 3 1200 (Summit Ridge) |
| GPU | NVIDIA GeForce GTX 760 (Kepler) |
| RAM | 8 GB DDR4 |
| Bootloader | OpenCore |
| macOS | Big Sur (11.x) |

---

## What Works

| Feature | Status |
|---------|--------|
| Boot | Working |
| GPU acceleration (GTX 760) | Working — Kepler native support in Big Sur |
| Display output | Working |
| USB ports | Working (after USB mapping) |
| Ethernet | Working |
| Audio | Working |
| CPU power management | Working (AMD patches) |

## What Does Not Work

| Feature | Status |
|---------|--------|
| iMessage / iCloud | Requires valid SMBIOS — not configured in this build |
| Sleep/Wake | Unstable on AMD platform |
| Hardware video acceleration | Limited on AMD pre-Polaris |

---

## Important: GTX 760 (Kepler) — Big Sur is the Ceiling

> GTX 760 (Kepler) is natively supported through Big Sur. Monterey and later dropped Kepler support entirely. Big Sur is the ceiling for this GPU.

NVIDIA Kepler GPUs (GTX 600/700 series) are supported natively in macOS up to and including Big Sur (11.x). Starting with Monterey (12.x), Apple removed the Kepler driver. There is no workaround — the GPU simply has no driver on Monterey and later.

If you have a GTX 760 or similar Kepler GPU, Big Sur is the highest macOS version you can run with GPU acceleration.

---

## AMD Notes

AMD CPUs require custom kernel patches to run macOS. See [docs/amd-ryzen-opencore-guide.md](docs/amd-ryzen-opencore-guide.md) for the full setup process and why AMD is harder than Intel.

---

## Docs

- [docs/amd-ryzen-opencore-guide.md](docs/amd-ryzen-opencore-guide.md) — AMD vanilla patches, BIOS settings, USB mapping
- [docs/gtx760-kepler-notes.md](docs/gtx760-kepler-notes.md) — Kepler macOS support timeline
- [docs/troubleshooting.md](docs/troubleshooting.md) — common failure patterns and fixes
