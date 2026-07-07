# DietPi TV Box Builder

Build **DietPi arm64 images** for supported Amlogic, Rockchip, and Allwinner TV boxes using the proven device database, boot assets, kernel layout, and tooling from the Ophub ecosystem.

This project is DietPi-focused: the goal is to provide a smaller, simpler build pipeline for TV box images, not to mirror the full upstream Ophub project surface.

## Features

- Builds DietPi arm64 base images from `MichaIng/build` branch `dietpi`
- Rebuilds images for supported TV box targets
- Keeps the full Ophub board database
- Supports Amlogic, Rockchip, and Allwinner platforms
- Builds kernel packages from Ophub kernel sources with local config templates
- Includes CAN / USB-CAN kernel config support in local templates
- Publishes DietPi-named release artifacts

## Supported devices

The supported board list is inherited from Ophub and kept generic.

Database:

```text
build-armbian/armbian-files/common-files/etc/model_database.conf
```

Default board for workflow smoke builds:

```text
s905w
```

`S905W` is only the default test target, not the only supported device.

## GitHub Actions

Primary image workflow:

```text
.github/workflows/build-armbian-arm64-server-image.yml
```

Kernel package workflow:

```text
.github/workflows/compile-kernel.yml
```

Default flow:

```text
compile-kernel workflow
→ Ophub kernel source + local kernel config
→ kernel_* release packages
→ DietPi image workflow
→ TV box repack
→ DietPi release artifacts
```

Build kernel packages first when using this repository as `kernel_repo`; the image workflow downloads those release packages and does not compile TV box kernels inline.

Important defaults:

- DietPi source: `MichaIng/build:dietpi`
- Kernel source: Ophub kernel sources
- Kernel config: `kernel/kernel-config/release/<kernel_usage>/config-*`
- Kernel package repo: `dopaemon/amlogic-s9xxx-armbian`
- Fallback kernel package repo: `ophub/kernel`
- Custom rootfs overlay: `custom-files/`
- Default board: `s905w`
- Local action: `uses: ./`

## Kernel

The repository includes Ophub-derived kernel config and patch resources:

```text
kernel/
```

This directory is **not** a Linux source tree.

Kernel packages are built by `compile-kernel.yml` from Ophub kernel sources while using the local config templates in this repository. These templates include CAN and USB-CAN support, including `CONFIG_CAN_GS_USB=m`.

The DietPi image workflow consumes kernel packages from this repository's releases by default, with fallback packages available from:

```text
ophub/kernel
```

For a local package repository, the expected layout is:

```text
kernel/<tag>/<version>/boot-*.tar.gz
kernel/<tag>/<version>/dtb-<platform>-*.tar.gz
kernel/<tag>/<version>/modules-*.tar.gz
kernel/<tag>/<version>/header-*.tar.gz
```

## Custom files

The image workflow copies `custom-files/` into the rebuilt root filesystem during the repack step. Use it for small local additions without changing the inherited Ophub scripts.

Examples:

```text
custom-files/etc/sysctl.d/99-custom.conf
custom-files/etc/modules-load.d/custom.conf
custom-files/usr/local/bin/my-tool
custom-files/etc/custom_service/start_service.sh
```

Keep scripts idempotent and binaries executable.

## Runtime compatibility

Some internal names, paths, and commands still use `armbian` naming for compatibility with the inherited Ophub scripts and overlays.

Examples:

```text
dietpi-install
armbian-install
armbian-update
armbian_* variables
build-armbian/ paths
```

`dietpi-install` is a compatibility alias for the inherited `armbian-install` TV box installer. Other `armbian` names are compatibility shims, not project branding.

## Credits

This project uses resources and implementation work from:

- [ophub/amlogic-s9xxx-armbian](https://github.com/ophub/amlogic-s9xxx-armbian)
- [ophub/kernel](https://github.com/ophub/kernel)
- [ophub/u-boot](https://github.com/ophub/u-boot)
- [MichaIng/build](https://github.com/MichaIng/build)
- [DietPi](https://dietpi.com/)
- [Armbian](https://www.armbian.com/)

All upstream licenses and credits remain with their respective projects and contributors.

## License

This repository keeps the upstream license terms from the projects it derives from. See `LICENSE` and the linked upstream projects for details.
