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

The **Possible values / examples** column lists the default shipped by
`meta-tegra` (where one exists) together with concrete values seen across
the in-tree machine configurations, or a description of the expected
value format. Values shown as `${...}` are computed/derived defaults;
the examples in parentheses show what they typically expand to. The
concrete examples are drawn from the Tegra234 (Orin) machines that this
branch supports, so other SoC families would use analogous values.

---

## Layer metadata

| Variable | Purpose | Possible values / examples |
| --- | --- | --- |
| `LAYERDEPENDS_tegra` | Lists the other BitBake layer collections that `meta-tegra` requires. | `core` (the only current dependency). |
| `LAYERSERIES_COMPAT_tegra` | Yocto release codenames this branch of `meta-tegra` is compatible with. | A space-separated list of codenames (for example `wrynose`). |
| `LAYERVERSION_tegra` | Internal version number of the layer, used by other layers to express compatibility. | Integer; currently `1`. |

## Machine / SoC identity

| Variable | Purpose | Possible values / examples |
| --- | --- | --- |
| `L4T_BSP_ARCH` | Package architecture name used for L4T BSP packages. | `tegra`. |
| `L4T_BSP_PKGARCH` | Full package architecture for L4T BSP binaries. | `${TEGRA_PKGARCH}` (for example `aarch64_tegra`). |
| `L4T_BSP_PREFIX` | Marketing prefix used in L4T BSP filenames. | `Jetson`. |
| `L4T_DEB_SOCNAME` | Short SoC name used in NVIDIA's Debian package feed. | `t234` (Orin); historically `t210`, `t186`, `t194` for older SoCs. |
| `NVIDIA_BOARD` | Free-form board identifier passed to NVIDIA's flashing helpers. | Defaults to `generic`; set per board as needed. |
| `NVIDIA_CHIP` | Tegra chip ID in hexadecimal. Used by flashing logic. | `0x23` (Orin/T234); `0x19` (Xavier/T194). |
| `SOC_FAMILY` | Name of the SoC family. Drives the SoC-family include and overrides. | `tegra234`; historically `tegra194`, `tegra210`. |
| `SOC_FAMILY_PKGARCH` | Package architecture string suffixed with the SoC family. | `${ARMPKGARCH}…_${SOC_FAMILY}` (for example `aarch64_tegra234`). |
| `TEGRA_BOARDID` | Numeric board ID reported by NVIDIA flashing tools. | `3701` (AGX Orin), `3767` (Orin NX / Nano). |
| `TEGRA_BOARDREV` | Board revision (FAB letter and number). | For example `C.0`, `A.3`, `B.4`, `P.1`. |
| `TEGRA_BOARDSKU` | Module SKU as four digits. | For example `0000`, `0001`, `0003`, `0004`, `0005`, `0008`. |
| `TEGRA_CHIPREV` | Chip revision number used by the flashing helpers. | Small integer; `0` (AGX Orin), `1` (Orin NX). |
| `TEGRA_CUDA_ARCHITECTURE` | CUDA SM/compute capability for the SoC's GPU. | `87` (Orin); `72` (Xavier). |
| `TEGRA_FAB` | Board FAB (fabrication/manufacturing) revision passed to the flashing and BUP tools. | For example `TS4` (AGX Orin), `ES1` (Orin NX), `RC1` (Orin Nano), `300`. |
| `TEGRA_PKGARCH` | Package architecture for generic tegra binaries. | `${ARMPKGARCH}…_tegra` (for example `aarch64_tegra`). |
| `TNSPEC_BOOTDEV` | Boot device the running system expects. | `mmcblk0p1`, `mmcblk1p1`, `nvme0n1p1`; defaults to `${TNSPEC_BOOTDEV_DEFAULT}`. |
| `TNSPEC_BOOTDEV_DEFAULT` | Default boot device for the platform; used to detect when the rootfs has moved off-module. | `mmcblk0p1` (default); `mmcblk1p1` on some devkits. |
| `TNSPEC_COMPAT_MACHINE` | Optional compatibility name used by UEFI to accept BUP payloads built for related MACHINEs. | Unset by default; a MACHINE name string when used. |
| `TNSPEC_MACHINE` | Machine name embedded in TNSPEC strings and passed to NVIDIA flashing helpers. | Defaults to `${MACHINE}`. |

## Kernel and boot arguments

| Variable | Purpose | Possible values / examples |
| --- | --- | --- |
| `KERNEL_ARGS` | Command-line arguments appended to the kernel by U-Boot / extlinux. | Empty by default; space-separated args such as `console=ttyTCU0,115200 fbcon=map:0 video=efifb:off`. |
| `KERNEL_DEVICETREE` | Device-tree blob(s) to deploy for the MACHINE (standard Yocto var; required by tegra recipes). | Space-separated list of `.dtb` paths for the MACHINE. |
| `KERNEL_IMAGETYPE` | Primary kernel image type used at runtime. | `Image` (default). |
| `KERNEL_IMAGETYPES` | List of kernel image types to build. | `Image.gz Image` (default). |
| `KERNEL_MODULE_AUTOLOAD` | Modules auto-loaded at boot (tegra-specific defaults). | For example `nvmap nvgpu pwm-fan ina3221`. |
| `L4T_EXTLINUX_BASEDIR` | Directory on the rootfs where extlinux looks for kernel/initrd. | `/boot` (default). |
| `UBOOT_EXTLINUX` | When `1`, the extlinux-based boot path (l4t-launcher-extlinux) is used. | `1` (default) or `0`. |
| `UBOOT_EXTLINUX_INITRD` | Initrd path written into the generated `extlinux.conf`. | `${L4T_EXTLINUX_BASEDIR}/initrd` (for example `/boot/initrd`) or empty when the initrd is bundled. |
| `UBOOT_EXTLINUX_KERNEL_ARGS` | Kernel command line written into `extlinux.conf`. | Defaults to `${KERNEL_ARGS}`. |
| `UBOOT_EXTLINUX_KERNEL_IMAGE` | Kernel image path written into `extlinux.conf`. | Defaults to `${L4T_EXTLINUX_BASEDIR}/${KERNEL_IMAGETYPE}` (for example `/boot/Image`). |

## Initramfs and image generation

| Variable | Purpose | Possible values / examples |
| --- | --- | --- |
| `DTBFILE` | Basename of the primary devicetree blob used by the flashing scripts. | Defaults to the basename of the first `KERNEL_DEVICETREE` entry (for example `tegra234-p3737-0000+p3701-0000.dtb`). |
| `ESP_FILE` | Filename of the EFI System Partition image. | `esp.img` when `TEGRA_ESP_IMAGE` is set; empty otherwise. |
| `IMAGE_TEGRAFLASH_ESPIMG` | Full path to the generated ESP image. | `${DEPLOY_DIR_IMAGE}/${TEGRA_ESP_IMAGE}-${MACHINE}.esp`. |
| `IMAGE_TEGRAFLASH_FS_TYPE` | Filesystem image type embedded in the tegraflash bundle. | `ext4` (default). |
| `IMAGE_TEGRAFLASH_INITRD_FLASHER` | Path to the initrd-flash bootable image. | A `.cboot` path, or empty when `TEGRAFLASH_INITRD_FLASH_IMAGE` is cleared. |
| `IMAGE_TEGRAFLASH_KERNEL` | Path to the kernel image (or boot image) embedded in the tegraflash bundle. | A computed `.cboot` path that depends on the initramfs bundling mode. |
| `IMAGE_TEGRAFLASH_ROOTFS` | Path to the rootfs image embedded in the tegraflash bundle. | `${IMGDEPLOYDIR}/${IMAGE_LINK_NAME}.${IMAGE_TEGRAFLASH_FS_TYPE}`. |
| `INITRAMFS_IMAGE` | Image recipe used to build the initramfs. | `tegra-minimal-initramfs` (default); any initramfs image recipe. |
| `INITRAMFS_IMAGE_BUNDLE` | Set to `1` to bundle the initramfs into the kernel; `0` keeps it as a separate file. | `0` (default) or `1`. |
| `INITRD_IMAGE` | Computed name of the initramfs image to ship alongside the kernel. | `${INITRAMFS_IMAGE}` when separate; empty when bundled. |
| `LNXFILE` | Boot image filename used by tegraflash. | `boot.img` (default). |
| `LNXSIZE` | Maximum size of the `LNXFILE` boot image partition, in bytes. | `83886080` (80 MiB) by default. |
| `RECROOTFSSIZE` | Size of the recovery rootfs partition, in bytes. | `314572800` (300 MiB) by default. |
| `TEGRA_ESP_IMAGE` | Recipe that builds the EFI System Partition image; clear to disable ESP generation. | `tegra-espimage` (default); empty to disable. |
| `TEGRA_EXT4_OPTIONS` | Extra options passed to `mkfs.ext4` when generating the tegra ext4 image. | Empty by default; for example `-O ^metadata_csum`. |
| `TEGRA_INITRAMFS_FSTYPES` | Additional initramfs filesystem types appended to `INITRAMFS_FSTYPES`. | ` cpio.gz.cboot` when not bundled; empty when bundled. |
| `TEGRA_INITRD_FLASH_INITRAMFS_FSTYPES` | Filesystem types built for the initrd-flash initramfs. | ` cpio.gz.cboot` when not bundled; empty when bundled. |
| `TEGRAFLASH_INITRD_FLASH_IMAGE` | Image recipe that builds the initrd-flash initramfs. | `tegra-initrd-flash-initramfs` (default); empty to disable. |

## Flashing variables (NVIDIA flashing tools)

`meta-tegra` generates the `flashvars` file consumed by NVIDIA's
flashing helpers from a per-board list of `TEGRA_FLASHVAR_*` settings.

| Variable | Purpose | Possible values / examples |
| --- | --- | --- |
| `EMMC_BCT` | Primary SDRAM/BCT source file (DTS) used when building the boot configuration table. | A board-specific DTS name, for example `tegra234-p3701-0000-sdram-l4t.dts`. |
| `EMMC_BCT_OVERRIDE` | Optional secondary BCT appended to `EMMC_BCTS`. | Unset by default; an additional DTS name when used. |
| `EMMC_BCTS` | Comma-separated list of BCT files built from `EMMC_BCT` (+ override). | `${EMMC_BCT}` alone, or `file1.dts,file2.dts` when an override is set. |
| `EMMC_DEVSECT_SIZE` | Sector size used by the eMMC controller. | `512` (typical). |
| `ODMDATA` | NVIDIA ODM data string written to the boot ROM (configures UPHY, PCIe lanes, etc.). | A comma-separated config string, for example `gbe-uphy-config-8,hsstp-lane-map-3,hsio-uphy-config-0`. |
| `TEGRA_BLBLOCKSIZE` | Block size used when computing bootloader partition alignment. | Defaults to `IMAGE_ROOTFS_ALIGNMENT × 1024` (for example `4096`). |
| `TEGRA_BOOT_FIRMWARE_FILES` | List of firmware binaries from the L4T BSP that must be staged for flashing. | A space-separated SoC-specific list (for example `mb1_t234_prod.bin mb2_t234.bin …`). |
| `TEGRA_BPMP_SERIAL_LOGGING` | Controls whether BPMP serial logging remains enabled in the BPMP DTB (`0` removes `/serial`, `1` keeps it). | `1` (default) or `0`. |
| `TEGRA_FLASH_CHECK_BOARDID` | Expected BoardID used by the flasher to refuse flashing onto the wrong hardware. | Defaults to `${TEGRA_BOARDID}` (for example `3701`). |
| `TEGRA_FLASH_CHECK_BOARDSKU` | Expected BoardSKU used by the flasher's safety check. | Defaults to `${TEGRA_BOARDSKU}` (for example `0000`). |
| `TEGRA_FLASH_CHECK_VARS` | Names of identifiers to verify before flashing. | `BOARDID BOARDSKU` (default). |
| `TEGRA_FLASHVAR_*` | Per-variable overrides (one for every entry in `TEGRA_FLASHVARS`). | A filename or token per variable, for example `TEGRA_FLASHVAR_TBCDTB_FILE = "@DTBFILE@"`. |
| `TEGRA_FLASHVARS` | Ordered list of variable names whose `TEGRA_FLASHVAR_<name>` values are written into `flashvars`. | A space-separated list (for example `BPFDTB_FILE BPF_FILE … TBCDTB_FILE UEFI_IMAGE`). |
| `TEGRA_MB1_LOG_LEVEL` | MB1 boot-stage debug log level injected into the MB1 misc config DTS fragment by `tegra-bootfiles`. | Integer; `4` (default). |
| `TEGRA_MB1_MISC_CONFIG_SECTION` | Node name in the MB1 misc config DTS where `TEGRA_MB1_LOG_LEVEL` is written. | `misc` (default). |
| `TEGRA_SIGNING_ENV` | Environment string (`CHIPREV=... BOARDID=...`) passed to the BUP/signing tools. | Empty by default; for example `CHIPREV=0 BOARDID=3701 FAB=TS4 BOARDSKU=0000 BOARDREV=C.0`. |
| `TEGRA_SIGNING_PKC` | Path to the RSA Public Key (PKC) `.pem` file used to sign boot binaries for secure boot. | Empty by default (unsigned); an absolute path to a `.pem` key when secure boot is enabled. |
| `TEGRA_SIGNING_SBK` | Path to the Secure Boot Key (SBK) file used to encrypt boot binaries. | Empty by default (unencrypted); an absolute path to an SBK key file when used. |
| `TEGRA_STAGED_BOOT_FIRMWARE` | All boot firmware staged for flashing (includes `TEGRA_BOOT_FIRMWARE_FILES` plus eks/badpage). | `${TEGRA_BOOT_FIRMWARE_FILES} eks.img badpage.bin`. |

## Partition layout

| Variable | Purpose | Possible values / examples |
| --- | --- | --- |
| `BOOTPART_LIMIT` | Maximum allowed boot partition size, in bytes. | A byte count (for example `10485760` on AGX Orin); unset on some modules. |
| `BOOTPART_SIZE` | Default boot partition size in bytes. | `8388608` (8 MiB) by default. |
| `HAS_REDUNDANT_PARTITION_LAYOUT_EXTERNAL` | `1` when the external storage layout supports A/B redundancy. | `1` (default) or `0`. |
| `PARTITION_LAYOUT_EXTERNAL` | Effective partition layout used for the external (e.g. NVMe/SD) device. | The default or redundant external XML depending on `USE_REDUNDANT_FLASH_LAYOUT`. |
| `PARTITION_LAYOUT_EXTERNAL_DEFAULT` | Default external layout XML file for the MACHINE. | For example `flash_l4t_t234_nvme.xml`. |
| `PARTITION_LAYOUT_EXTERNAL_REDUNDANT` | A/B variant of the external layout XML. | Defaults to the `_rootfs_ab.xml` variant (for example `flash_l4t_t234_nvme_rootfs_ab.xml`). |
| `PARTITION_LAYOUT_TEMPLATE` | Effective partition layout XML used for the on-module storage. | The default or redundant template depending on `USE_REDUNDANT_FLASH_LAYOUT`. |
| `PARTITION_LAYOUT_TEMPLATE_DEFAULT` | Default on-module partition layout XML for the MACHINE. | For example `flash_t234_qspi.xml`, `flash_t234_qspi_sdmmc.xml`. |
| `PARTITION_LAYOUT_TEMPLATE_DEFAULT_SUPPORTS_REDUNDANT` | `1` if the default template already includes A/B partitioning. | `0` (default) or `1`. |
| `PARTITION_LAYOUT_TEMPLATE_REDUNDANT` | A/B variant of the on-module partition layout XML. | The default template or its `_rootfs_ab.xml` variant. |
| `ROOTFSPART_SIZE` | Effective rootfs partition size in bytes (chosen between default and redundant variants). | For example `30064771072` (28 GiB) or its half in A/B mode. |
| `ROOTFSPART_SIZE_DEFAULT` | Default rootfs partition size for the MACHINE, in bytes. | For example `30064771072` (~28 GiB), `59055800320` (~55 GiB). |
| `ROOTFSPART_SIZE_REDUNDANT` | Rootfs size used when A/B redundancy is enabled (half of the default by default). | `${ROOTFSPART_SIZE_DEFAULT} / 2` (for example `15032385536`). |
| `TEGRAFLASH_NO_INTERNAL_STORAGE` | `1` for modules without on-module eMMC (e.g. Orin NX/Nano without SDCard). | `0` (default) or `1`. |
| `TEGRAFLASH_SDCARD_SIZE` | Total size to use when building an SDCard image. | `16G` (default); for example `32G`. |
| `USE_REDUNDANT_FLASH_LAYOUT` | `1` to build A/B rootfs / redundant-bootloader partitioning. | `0` or `1`; defaults to `USE_REDUNDANT_FLASH_LAYOUT_DEFAULT` when external redundancy is supported. |
| `USE_REDUNDANT_FLASH_LAYOUT_DEFAULT` | Default value of `USE_REDUNDANT_FLASH_LAYOUT` for the MACHINE. | `0` (default) or `1`. |

## Over-the-air (OTA) updates and BUP

| Variable | Purpose | Possible values / examples |
| --- | --- | --- |
| `BUP_PAYLOAD_DIR` | Directory name inside `tegra-flash/` where Bootloader Update Payload (BUP) artifacts are staged. | Computed as `payloads_t<chip>x` (for example `payloads_t23x` on Orin). |
| `OTABOOTDEV` | Block device the OTA tooling treats as the bootloader storage. | For example `/dev/mtdblock0` (QSPI) or `/dev/mmcblk0boot0` (eMMC). |
| `OTAGPTDEV` | Block device that holds the GPT used by OTA tooling. | For example `/dev/mtdblock0` (QSPI) or `/dev/mmcblk0boot1` (eMMC). |
| `TEGRA_BUPGEN_SPECS` | Specifications passed to the BUP generator listing the board/chip combinations to build for. | Defaults to `boardid=${TEGRA_BOARDID};fab=${TEGRA_FAB};boardrev=${TEGRA_BOARDREV};chiprev=${TEGRA_CHIPREV}`; may list several `;`-separated specs. |

## Devicetree overlays and TBC

| Variable | Purpose | Possible values / examples |
| --- | --- | --- |
| `OVERLAY_DTB_FILE` | Deprecated single-overlay variable; new overlays should be added to one of the lists below. | Empty by default; a `.dtbo` filename when used. |
| `TEGRA_BOOTCONTROL_OVERLAYS` | Devicetree overlays applied unconditionally by the boot-control loader. | Defaults to `L4TConfiguration.dtbo` (plus `L4TConfiguration-RootfsRedundancyLevelABEnable.dtbo` when A/B is enabled). |
| `TEGRA_PLUGIN_MANAGER_OVERLAYS` | Overlays applied by NVIDIA's plugin manager based on board EEPROM data. | Empty by default; a space-separated list of `.dtbo` files (for example `tegra234-carveouts.dtbo tegra-optee.dtbo`). |

## UEFI / EDK2

| Variable | Purpose | Possible values / examples |
| --- | --- | --- |
| `EFI_PROVIDER` | Recipe providing the EFI loader installed on the ESP. | `l4t-launcher` (default). |
| `OPTEE_MM_PROVIDER` | Recipe providing OP-TEE's Management Mode firmware (prebuilt vs. source build). | `standalone-mm-optee-tegra` (source build) or `tegra-uefi-prebuilt` (prebuilt). |
| `TEGRA_EDK2_CONFIGURATION` | EDK2 build configuration selected from the `edk2-nvidia` source tree. | For example `general`, `kernel`. |
| `TEGRA_EDK2_PLATFORM` | EDK2 platform name for the SoC. | `t23x` (Orin). |
| `TEGRA_RCM_EDK2_CONFIGURATION` | EDK2 configuration used for the RCM-boot variant of UEFI. | For example `embedded`. |
| `TEGRA_RCM_EDK2_DEPENDS` | Build dependencies needed to produce the RCM UEFI image. | A task dependency string (for example `edk2-firmware-tegra-rcmboot:do_deploy`). |
| `TEGRA_UEFI_SIGNING_CLASS` | bbclass that implements signing of the UEFI image (override for custom signers). | A bbclass name (for example `tegra-uefi-signing`). |

## OP-TEE and secure OS

| Variable | Purpose | Possible values / examples |
| --- | --- | --- |
| `TEGRA_OPTEE_VERSION` | `PV` constraint applied to OP-TEE recipes (empty when using the prebuilt OP-TEE). | `4.2-l4t%` (source build) or empty (prebuilt). |
| `USE_PREBUILT_OPTEE` | `1` to use NVIDIA's prebuilt OP-TEE binaries instead of building from source. | `0` (default) or `1`. |

## NVIDIA runtime services

| Variable | Purpose | Possible values / examples |
| --- | --- | --- |
| `NVFANCONTROL` | nvfancontrol profile installed for the MACHINE (chosen per module). | For example `nvfancontrol_p3701_0000`, `nvfancontrol_p3767_0000`. |
| `NVPMODEL` | nvpmodel configuration installed (selects the default power model). | For example `nvpmodel_p3701_0000`, `nvpmodel_p3767_0000_super`. |
| `NVPOWER` | jetsonpower profile installed for the MACHINE. | For example `jetsonpower_t234`. |
| `TEGRA_AUDIO_DEVICE` | tegra-configs-udev audio profile selected for the MACHINE. | For example `tegra-hda-jetson-agx`, `tegra-hda-p3767-p3509`. |

## Graphics and providers

| Variable | Purpose | Possible values / examples |
| --- | --- | --- |
| `TEGRA_LIBGLVND_PROVIDER` | Recipe providing libGLVND (also drives the GL/EGL/GLES virtual providers). | `libglvnd` (default). |
| `XSERVER` | Tegra-specific X.Org server package set (includes `xserver-xorg-video-nvidia`). | A space-separated package list (for example `xserver-xorg xf86-input-evdev xserver-xorg-video-nvidia xserver-xorg-module-libwfb`). |

## CUDA

| Variable | Purpose | Possible values / examples |
| --- | --- | --- |
| `CUDA_ARCHITECTURES` | CUDA architecture(s) to build for. | Defaults to `${TEGRA_CUDA_ARCHITECTURE}` (for example `87`). |
| `CUDA_NVCC_ARCH_FLAGS` | `nvcc` flags derived from the architecture. | `--gpu-architecture=compute_<arch> --gpu-code=sm_<arch>` (for example `…compute_87 …sm_87`). |
| `CUDA_VERSION` | CUDA toolkit version associated with the L4T BSP. | For example `12.6`. |

## Wi-Fi and Bluetooth

| Variable | Purpose | Possible values / examples |
| --- | --- | --- |
| `LINUX_WIFI` | Wi-Fi/Bluetooth packages used when the build uses the mainline `linux-tegra` kernel. | A space-separated package list (for example `kernel-module-rtk-btusb kernel-module-rtw88-8822ce tegra-firmware-rtl8822`). |
| `NVIDIA_WIFI` | Wi-Fi/Bluetooth packages used when the build uses NVIDIA's `linux-jammy-nvidia-tegra` kernel. | A space-separated package list (for example `kernel-module-rtk-btusb kernel-module-rtl8822ce tegra-firmware-rtl8822`). |
| `TEGRA_BT_SUPPORT_PACKAGE` | Optional extra Bluetooth support package added to the image. | Empty by default; for example `tegra-brcm-patchram`. |

---

## Where these variables are defined

Most of these variables are introduced or have defaults in:

- `conf/layer.conf` — layer metadata.
- `conf/machine/include/tegra-common.inc` — defaults common to every Tegra MACHINE.
- `conf/machine/include/tegra234.inc`, `agx-orin.inc`, `orin-nx.inc`, `orin-nano.inc`, … — SoC- and module-specific defaults.
- `classes-recipe/image_types_tegra.bbclass` — flashing/image-generation helpers (including the signing variables).
- `recipes-bsp/tegra-binaries/tegra-bootfiles_*.bb` — boot firmware staging and MB1/BPMP logging controls.
- `classes-recipe/l4t_bsp.bbclass`, `classes/l4t_version.bbclass`,
  `classes-recipe/l4t-extlinux-config.bbclass`,
  `classes-recipe/l4t_deb_pkgfeed.bbclass` — L4T BSP plumbing.

If you need a variable that is not listed here, the best starting point
is to `git grep` for it under `conf/` and `classes*/` in this layer, or
check the relevant `recipes-bsp/tegra-binaries/*` recipe.
