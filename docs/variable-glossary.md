# Variable Glossary

This page summarizes the most commonly used BitBake variables that are
defined or consumed by the `meta-tegra` layer. It is intended as a quick
reference for anyone reading or writing machine configurations, distro
configurations, or recipes for NVIDIA Jetson targets.

Variables that are part of standard OpenEmbedded / BitBake (for example
`MACHINE`, `DISTRO`, `KERNEL_DEVICETREE`, `IMAGE_FSTYPES`,
`PREFERRED_PROVIDER_*`, `MACHINE_FEATURES`, …) are listed only when
`meta-tegra` uses them in a layer-specific way. For their generic
meaning, refer to the
[Yocto Project Reference Manual](https://docs.yoctoproject.org/ref-manual/variables.html).

The variables are grouped by purpose. Within each group they are listed
alphabetically.

---

## Layer metadata

| Variable | Purpose |
| --- | --- |
| `LAYERDEPENDS_tegra` | Lists the other BitBake layer collections that `meta-tegra` requires (currently `core`). |
| `LAYERSERIES_COMPAT_tegra` | Yocto release codenames this branch of `meta-tegra` is compatible with. |
| `LAYERVERSION_tegra` | Internal version number of the layer, used by other layers to express compatibility. |

## Machine / SoC identity

| Variable | Purpose |
| --- | --- |
| `L4T_BSP_ARCH` | Package architecture name used for L4T BSP packages (`tegra`). |
| `L4T_BSP_PKGARCH` | Full package architecture for L4T BSP binaries; usually `${TEGRA_PKGARCH}`. |
| `L4T_BSP_PREFIX` | Marketing prefix used in L4T BSP filenames (for example `Jetson`). |
| `L4T_DEB_SOCNAME` | Short SoC name used in NVIDIA's Debian package feed (`t234`, `t210`, …). |
| `NVIDIA_BOARD` | Free-form board identifier passed to NVIDIA's flashing helpers; defaults to `generic`. |
| `NVIDIA_CHIP` | Tegra chip ID in hexadecimal (for example `0x23` for Orin/T234). Used by flashing logic. |
| `SOC_FAMILY` | Name of the SoC family (`tegra234`, `tegra210`, …). Drives the SoC-family include and overrides. |
| `SOC_FAMILY_PKGARCH` | Package architecture string suffixed with the SoC family, used for SoC-specific packages. |
| `TEGRA_BOARDID` | Numeric board ID reported by NVIDIA flashing tools (for example `3701`, `3767`). |
| `TEGRA_BOARDREV` | Board revision (FAB letter and number, e.g. `C.0`). |
| `TEGRA_BOARDSKU` | Module SKU as four digits (for example `0000`, `0005`). |
| `TEGRA_CHIPREV` | Chip revision number used by the flashing helpers. |
| `TEGRA_CUDA_ARCHITECTURE` | CUDA SM/compute capability for the SoC's GPU (for example `87` for Orin). |
| `TEGRA_PKGARCH` | Package architecture for generic tegra binaries (ARM tune flags suffixed with `_tegra`). |
| `TNSPEC_BOOTDEV` | Boot device the running system expects (for example `mmcblk0p1`, `nvme0n1p1`). |
| `TNSPEC_BOOTDEV_DEFAULT` | Default boot device for the platform; used to detect when the rootfs has moved off-module. |
| `TNSPEC_COMPAT_MACHINE` | Optional compatibility name used by UEFI to accept BUP payloads built for related MACHINEs. |
| `TNSPEC_MACHINE` | Machine name embedded in TNSPEC strings and passed to NVIDIA flashing helpers. |

## Kernel and boot arguments

| Variable | Purpose |
| --- | --- |
| `KERNEL_ARGS` | Command-line arguments appended to the kernel by U-Boot / extlinux. |
| `KERNEL_DEVICETREE` | Device-tree blob(s) to deploy for the MACHINE (standard Yocto var; required by tegra recipes). |
| `KERNEL_IMAGETYPE` | Primary kernel image type used at runtime (defaults to `Image`). |
| `KERNEL_IMAGETYPES` | List of kernel image types to build (`Image.gz Image` by default). |
| `KERNEL_MODULE_AUTOLOAD` | Modules auto-loaded at boot (tegra-specific defaults such as `nvmap`, `nvgpu`, …). |
| `L4T_EXTLINUX_BASEDIR` | Directory on the rootfs where extlinux looks for kernel/initrd (default `/boot`). |
| `UBOOT_EXTLINUX` | When `1`, the extlinux-based boot path (l4t-launcher-extlinux) is used. |
| `UBOOT_EXTLINUX_INITRD` | Initrd path written into the generated `extlinux.conf`. |
| `UBOOT_EXTLINUX_KERNEL_ARGS` | Kernel command line written into `extlinux.conf` (defaults to `${KERNEL_ARGS}`). |
| `UBOOT_EXTLINUX_KERNEL_IMAGE` | Kernel image path written into `extlinux.conf`. |

## Initramfs and image generation

| Variable | Purpose |
| --- | --- |
| `DTBFILE` | Basename of the primary devicetree blob used by the flashing scripts. |
| `ESP_FILE` | Filename of the EFI System Partition image (`esp.img` when `TEGRA_ESP_IMAGE` is set). |
| `IMAGE_TEGRAFLASH_ESPIMG` | Full path to the generated ESP image. |
| `IMAGE_TEGRAFLASH_FS_TYPE` | Filesystem image type embedded in the tegraflash bundle (for example `ext4`). |
| `IMAGE_TEGRAFLASH_INITRD_FLASHER` | Path to the initrd-flash bootable image. |
| `IMAGE_TEGRAFLASH_KERNEL` | Path to the kernel image (or boot image) embedded in the tegraflash bundle. |
| `IMAGE_TEGRAFLASH_ROOTFS` | Path to the rootfs image embedded in the tegraflash bundle. |
| `INITRAMFS_IMAGE` | Image recipe used to build the initramfs (default `tegra-minimal-initramfs`). |
| `INITRAMFS_IMAGE_BUNDLE` | Set to `1` to bundle the initramfs into the kernel; `0` keeps it as a separate file. |
| `INITRD_IMAGE` | Computed name of the initramfs image to ship alongside the kernel. |
| `LNXFILE` | Boot image filename used by tegraflash (`boot.img`). |
| `LNXSIZE` | Maximum size of the `LNXFILE` boot image partition. |
| `RECROOTFSSIZE` | Size of the recovery rootfs partition. |
| `TEGRA_ESP_IMAGE` | Recipe that builds the EFI System Partition image; clear to disable ESP generation. |
| `TEGRA_EXT4_OPTIONS` | Extra options passed to `mkfs.ext4` when generating the tegra ext4 image. |
| `TEGRA_INITRAMFS_FSTYPES` | Additional initramfs filesystem types appended to `INITRAMFS_FSTYPES` (e.g. `cpio.gz.cboot`). |
| `TEGRA_INITRD_FLASH_INITRAMFS_FSTYPES` | Filesystem types built for the initrd-flash initramfs. |
| `TEGRAFLASH_INITRD_FLASH_IMAGE` | Image recipe that builds the initrd-flash initramfs (default `tegra-initrd-flash-initramfs`). |

## Flashing variables (NVIDIA flashing tools)

`meta-tegra` generates the `flashvars` file consumed by NVIDIA's
flashing helpers from a per-board list of `TEGRA_FLASHVAR_*` settings.

| Variable | Purpose |
| --- | --- |
| `EMMC_BCT` | Primary SDRAM/BCT source file (DTS) used when building the boot configuration table. |
| `EMMC_BCT_OVERRIDE` | Optional secondary BCT appended to `EMMC_BCTS`. |
| `EMMC_BCTS` | Comma-separated list of BCT files built from `EMMC_BCT` (+ override). |
| `EMMC_DEVSECT_SIZE` | Sector size used by the eMMC controller (typically `512`). |
| `ODMDATA` | NVIDIA ODM data string written to the boot ROM (configures UPHY, PCIe lanes, etc.). |
| `TEGRA_BLBLOCKSIZE` | Block size used when computing bootloader partition alignment. |
| `TEGRA_BOOT_FIRMWARE_FILES` | List of firmware binaries from the L4T BSP that must be staged for flashing. |
| `TEGRA_BPMP_SERIAL_LOGGING` | Controls whether BPMP serial logging remains enabled in the BPMP DTB (`0` removes `/serial`, `1` keeps it). |
| `TEGRA_FLASH_CHECK_BOARDID` | Expected BoardID used by the flasher to refuse flashing onto the wrong hardware. |
| `TEGRA_FLASH_CHECK_BOARDSKU` | Expected BoardSKU used by the flasher's safety check. |
| `TEGRA_FLASH_CHECK_VARS` | Names of identifiers to verify before flashing (`BOARDID BOARDSKU` by default). |
| `TEGRA_FLASHVAR_*` | Per-variable overrides (one for every entry in `TEGRA_FLASHVARS`). |
| `TEGRA_FLASHVARS` | Ordered list of variable names whose `TEGRA_FLASHVAR_<name>` values are written into `flashvars`. |
| `TEGRA_MB1_LOG_LEVEL` | MB1 boot-stage debug log level injected into the MB1 misc config DTS fragment by `tegra-bootfiles`. |
| `TEGRA_MB1_MISC_CONFIG_SECTION` | Node name in the MB1 misc config DTS where `TEGRA_MB1_LOG_LEVEL` is written (default `misc`). |
| `TEGRA_SIGNING_ENV` | Environment string (`CHIPREV=... BOARDID=...`) passed to the BUP/signing tools. |
| `TEGRA_STAGED_BOOT_FIRMWARE` | All boot firmware staged for flashing (includes `TEGRA_BOOT_FIRMWARE_FILES` plus eks/badpage). |

## Partition layout

| Variable | Purpose |
| --- | --- |
| `BOOTPART_LIMIT` | Maximum allowed boot partition size. |
| `BOOTPART_SIZE` | Default boot partition size in bytes. |
| `HAS_REDUNDANT_PARTITION_LAYOUT_EXTERNAL` | `1` when the external storage layout supports A/B redundancy. |
| `PARTITION_LAYOUT_EXTERNAL` | Effective partition layout used for the external (e.g. NVMe/SD) device. |
| `PARTITION_LAYOUT_EXTERNAL_DEFAULT` | Default external layout XML file for the MACHINE. |
| `PARTITION_LAYOUT_EXTERNAL_REDUNDANT` | A/B variant of the external layout XML. |
| `PARTITION_LAYOUT_TEMPLATE` | Effective partition layout XML used for the on-module storage. |
| `PARTITION_LAYOUT_TEMPLATE_DEFAULT` | Default on-module partition layout XML for the MACHINE. |
| `PARTITION_LAYOUT_TEMPLATE_DEFAULT_SUPPORTS_REDUNDANT` | `1` if the default template already includes A/B partitioning. |
| `PARTITION_LAYOUT_TEMPLATE_REDUNDANT` | A/B variant of the on-module partition layout XML. |
| `ROOTFSPART_SIZE` | Effective rootfs partition size in bytes (chosen between default and redundant variants). |
| `ROOTFSPART_SIZE_DEFAULT` | Default rootfs partition size for the MACHINE. |
| `ROOTFSPART_SIZE_REDUNDANT` | Rootfs size used when A/B redundancy is enabled (half of the default by default). |
| `TEGRAFLASH_NO_INTERNAL_STORAGE` | `1` for modules without on-module eMMC (e.g. Orin NX/Nano without SDCard). |
| `TEGRAFLASH_SDCARD_SIZE` | Total size to use when building an SDCard image. |
| `USE_REDUNDANT_FLASH_LAYOUT` | `1` to build A/B rootfs / redundant-bootloader partitioning. |
| `USE_REDUNDANT_FLASH_LAYOUT_DEFAULT` | Default value of `USE_REDUNDANT_FLASH_LAYOUT` for the MACHINE. |

## Over-the-air (OTA) updates and BUP

| Variable | Purpose |
| --- | --- |
| `BUP_PAYLOAD_DIR` | Directory name inside `tegra-flash/` where Bootloader Update Payload (BUP) artifacts are staged. |
| `OTABOOTDEV` | Block device the OTA tooling treats as the bootloader storage (for example `/dev/mtdblock0`). |
| `OTAGPTDEV` | Block device that holds the GPT used by OTA tooling. |
| `TEGRA_BUPGEN_SPECS` | Specifications passed to the BUP generator listing the board/chip combinations to build for. |

## Devicetree overlays and TBC

| Variable | Purpose |
| --- | --- |
| `OVERLAY_DTB_FILE` | Deprecated single-overlay variable; new overlays should be added to one of the lists below. |
| `TEGRA_BOOTCONTROL_OVERLAYS` | Devicetree overlays applied unconditionally by the boot-control loader. |
| `TEGRA_PLUGIN_MANAGER_OVERLAYS` | Overlays applied by NVIDIA's plugin manager based on board EEPROM data. |

## UEFI / EDK2

| Variable | Purpose |
| --- | --- |
| `EFI_PROVIDER` | Recipe providing the EFI loader installed on the ESP (default `l4t-launcher`). |
| `OPTEE_MM_PROVIDER` | Recipe providing OP-TEE's Management Mode firmware (prebuilt vs. source build). |
| `TEGRA_EDK2_CONFIGURATION` | EDK2 build configuration selected from the `edk2-nvidia` source tree (`general`, `kernel`, …). |
| `TEGRA_EDK2_PLATFORM` | EDK2 platform name for the SoC (for example `t23x`). |
| `TEGRA_RCM_EDK2_CONFIGURATION` | EDK2 configuration used for the RCM-boot variant of UEFI. |
| `TEGRA_RCM_EDK2_DEPENDS` | Build dependencies needed to produce the RCM UEFI image. |
| `TEGRA_UEFI_SIGNING_CLASS` | bbclass that implements signing of the UEFI image (override for custom signers). |

## OP-TEE and secure OS

| Variable | Purpose |
| --- | --- |
| `TEGRA_OPTEE_VERSION` | `PV` constraint applied to OP-TEE recipes (empty when using the prebuilt OP-TEE). |
| `USE_PREBUILT_OPTEE` | `1` to use NVIDIA's prebuilt OP-TEE binaries instead of building from source. |

## NVIDIA runtime services

| Variable | Purpose |
| --- | --- |
| `NVFANCONTROL` | nvfancontrol profile installed for the MACHINE (chosen per module). |
| `NVPMODEL` | nvpmodel configuration installed (selects the default power model). |
| `NVPOWER` | jetsonpower profile installed for the MACHINE. |
| `TEGRA_AUDIO_DEVICE` | tegra-configs-udev audio profile selected for the MACHINE. |

## Graphics and providers

| Variable | Purpose |
| --- | --- |
| `TEGRA_LIBGLVND_PROVIDER` | Recipe providing libGLVND (default `libglvnd`); also drives the GL/EGL/GLES virtual providers. |
| `XSERVER` | Tegra-specific X.Org server package set (includes `xserver-xorg-video-nvidia`). |

## CUDA

| Variable | Purpose |
| --- | --- |
| `CUDA_ARCHITECTURES` | CUDA architecture(s) to build for; defaults to `${TEGRA_CUDA_ARCHITECTURE}`. |
| `CUDA_NVCC_ARCH_FLAGS` | `nvcc` flags derived from the architecture (`--gpu-architecture=…`, `--gpu-code=…`). |
| `CUDA_VERSION` | CUDA toolkit version associated with the L4T BSP (for example `12.6`). |

## Wi-Fi and Bluetooth

| Variable | Purpose |
| --- | --- |
| `LINUX_WIFI` | Wi-Fi/Bluetooth packages used when the build uses the mainline `linux-tegra` kernel. |
| `NVIDIA_WIFI` | Wi-Fi/Bluetooth packages used when the build uses NVIDIA's `linux-jammy-nvidia-tegra` kernel. |
| `TEGRA_BT_SUPPORT_PACKAGE` | Optional extra Bluetooth support package added to the image. |

---

## Where these variables are defined

Most of these variables are introduced or have defaults in:

- `conf/layer.conf` — layer metadata.
- `conf/machine/include/tegra-common.inc` — defaults common to every Tegra MACHINE.
- `conf/machine/include/tegra234.inc`, `agx-orin.inc`, `orin-nx.inc`, `orin-nano.inc`, … — SoC- and module-specific defaults.
- `classes-recipe/image_types_tegra.bbclass` — flashing/image-generation helpers.
- `recipes-bsp/tegra-binaries/tegra-bootfiles_*.bb` — boot firmware staging and MB1/BPMP logging controls.
- `classes-recipe/l4t_bsp.bbclass`, `classes/l4t_version.bbclass`,
  `classes-recipe/l4t-extlinux-config.bbclass`,
  `classes-recipe/l4t_deb_pkgfeed.bbclass` — L4T BSP plumbing.

If you need a variable that is not listed here, the best starting point
is to `git grep` for it under `conf/` and `classes*/` in this layer, or
check the relevant `recipes-bsp/tegra-binaries/*` recipe.
