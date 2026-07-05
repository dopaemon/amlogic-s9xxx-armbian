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

Primary workflow:

```text
.github/workflows/build-armbian-arm64-server-image.yml
```

Default flow:

```text
DietPi base image
→ Ophub kernel source + local kernel config
→ TV box repack
→ DietPi release artifacts
```

Important defaults:

- DietPi source: `MichaIng/build:dietpi`
- Kernel source: Ophub kernel sources
- Kernel config: `kernel/kernel-config/release/<kernel_usage>/config-*`
- Fallback kernel package repo: `ophub/kernel`
- Default board: `s905w`
- Local action: `uses: ./`

## Kernel

The repository includes Ophub-derived kernel config and patch resources:

```text
kernel/
```

This directory is **not** a Linux source tree.

The workflow can compile kernel packages from Ophub kernel sources while using the local config templates in this repository. These templates include CAN and USB-CAN support, including `CONFIG_CAN_GS_USB=m`.

If local kernel compilation is disabled, the workflow can fall back to prebuilt packages from:

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

## Runtime compatibility

Some internal names, paths, and commands still use `armbian` naming for compatibility with the inherited Ophub scripts and overlays.

Examples:

```text
armbian-install
armbian-update
armbian_* variables
build-armbian/ paths
```

These are compatibility shims, not project branding.

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
