# AERA Recovery for Redmi Note 8 Pro (`begonia` / `begoniain`)

Official-grade, self-contained **AERA Recovery** project and automated **GitHub Actions** CI builder for the **Xiaomi Redmi Note 8 Pro** (Helio G90T, `mt6785`).

---

## Highlights & Features

AERA Recovery combines an LVGL-based touch interface with modern recovery capabilities:

- **LVGL Native UI**: Crisp, hardware-accelerated user interface running natively at 1080x2340.
- **AERA PC Connection (Web Workspace)**:
  - Access `https://aera.local` (or phone IP) from any modern desktop browser.
  - Flash ROMs, stream live backups, manage files, and execute commands in a real recovery terminal without installing desktop apps.
  - Works over both **Wi-Fi** (MediaTek WMT/WLAN stack) and **USB** (`adb forward tcp:8000 tcp:8000`).
- **Root Manager**:
  - Direct inspection and flashing of KernelSU, KernelSU Next, and SukiSU Ultra.
- **Unified Partition Support**:
  - Auto-adapts at runtime to either **physical partitions** (legacy non-dynamic ROMs) or **retrofitted dynamic partitions** (`super` block devices `system` + `vendor`).
  - Supports **EROFS**, **EXT4**, and **F2FS** filesystems.
  - Android 10 to Android 15/16 decryption support (FBE v1 & FBE v2 with metadata encryption).
- **Fastbootd**: Userspace fastboot for managing dynamic logical partitions directly.
- **Safe Compression**: Tailored gzip ramdisk packaging preventing black-screen bootloops caused by MediaTek 4.14 kernel decompressor limitations.

---

## Repository Layout

```
.
├── .github/
│   └── workflows/
│       └── build.yml               # Automated GitHub Actions workflow (AOSP 16 / AERA manifest)
├── device/
│   └── xiaomi/
│       └── begonia/                # Full Begonia recovery device tree
│           ├── aera_begonia.mk     # AERA UI, storage, and platform flags
│           ├── Android.bp / .mk    # Android build system hooks
│           ├── AndroidProducts.mk  # Lunch target definitions
│           ├── BoardConfig.mk      # Board, AVB, kernel offsets, display, Wi-Fi configs
│           ├── device.mk           # Packages, health HAL, dynamic partition setup
│           ├── fox_begonia.mk      # Baseline partition & recovery settings
│           ├── libshim_beanpod/    # Crypto shim for beanpod keymaster
│           ├── prebuilt/           # Prebuilt 4.14 kernel (Image.gz), dtb, and dtbo.img
│           ├── recovery/root/      # Unified recovery.fstab, init scripts, Wi-Fi firmware
│           ├── system.prop / vendor.prop
│           ├── twrp_begonia.mk     # Product definition
│           ├── vendorsetup.sh      # AERA envsetup hook & build environment definitions
│           └── WIFI-BROWSER-TUTORIAL.md
├── .gitignore
└── README.md
```

---

## Build via GitHub Actions

This repository is pre-configured with a streamlined, self-cleaning GitHub Actions workflow (`.github/workflows/build.yml`) that syncs the AERA Android 16 manifest and compiles `recovery.img` and a flashable zip.

### Step 1: Authenticate with GitHub

In your terminal, log in to GitHub CLI:

```bash
gh auth login
```
Follow the interactive prompt (choose GitHub.com, HTTPS, and authorize in your web browser or with a Personal Access Token with `repo` and `workflow` scopes).

### Step 2: Initialize & Push Repository to Your GitHub

```bash
cd /media/dev/3A3E549FFDB4573D/aera
git init -b main
git add .
git commit -m "feat: initial AERA Recovery tree and CI builder for begonia"

# Create a new repository on your GitHub account and push
gh repo create aera-begonia --public --source=. --remote=origin --push
```

### Step 3: Trigger the Build Workflow

You can trigger the workflow from the terminal:

```bash
gh workflow run "AERA Recovery Build (begonia)"
```

Or trigger it from your browser:
1. Navigate to your repository on GitHub.
2. Click **Actions** > **AERA Recovery Build (begonia)**.
3. Click **Run workflow** (leave default lunch target `twrp_begonia-bp2a-eng`).
4. Click **Run workflow**.

When the build completes, download `aera-begonia-recovery` from the **Artifacts** section at the bottom of the run page.

---

## Flashing Instructions (Redmi Note 8 Pro)

1. Reboot your phone into Fastboot Mode (Hold **Power + Volume Down** until the FASTBOOT logo appears).
2. Connect your phone to your PC via USB.
3. Flash the recovery image:

```bash
fastboot flash recovery recovery.img
```

4. Reboot directly into AERA Recovery (Hold **Power + Volume Up** until the AERA splash screen appears):

```bash
fastboot reboot
```

---

## Credits & Upstream Projects

- **[AERA Recovery Project](https://aera-recovery.com/)**: Core recovery & native LVGL interface
- **[OrangeFox Recovery Project](https://gitlab.com/OrangeFox)** & **[TWRP](https://twrp.me)**: Foundational recovery engines
- **[Saikrishna1504](https://github.com/Saikrishna1504)**: Begonia device tree & PBRP maintainer
- **[methylphetamine](https://github.com/methylphetamine)**: Begonia AERA/OrangeFox Wi-Fi and unified partition bring-up
