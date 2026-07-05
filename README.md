# DietPi TV Box Builder

Dự án này build **DietPi arm64 images** cho các TV box đang có trong database của Ophub `amlogic-s9xxx-armbian`.

Mục tiêu: giữ pipeline đơn giản để ra image DietPi. Không cố giữ đầy đủ bề mặt/phức tạp như upstream Ophub.

## Credit / upstream

Project này dùng tài nguyên, script, device database, u-boot/kernel layout từ:

- [ophub/amlogic-s9xxx-armbian](https://github.com/ophub/amlogic-s9xxx-armbian)
- [ophub/kernel](https://github.com/ophub/kernel)
- [ophub/u-boot](https://github.com/ophub/u-boot)
- [MichaIng/build](https://github.com/MichaIng/build) branch `dietpi`

License upstream giữ nguyên theo `LICENSE`.

## Support

Giữ toàn bộ board/platform từ Ophub database:

- Amlogic
- Rockchip
- Allwinner

`S905W` chỉ là board test mặc định, không phải board duy nhất.

Board list nằm ở:

```text
build-armbian/armbian-files/common-files/etc/model_database.conf
```

## GitHub Actions

Chỉ dùng workflow này:

```text
.github/workflows/build-armbian-arm64-server-image.yml
```

Default:

- source: `MichaIng/build:dietpi`
- board: `s905w`
- kernel repo: `ophub/kernel` releases by default
- bundled `./kernel`: config/patch resources only, not default package source
- action: local `uses: ./`

Chạy Actions → chọn release/board/kernel → build DietPi base → repack cho target board.

## Kernel

Mặc định workflow dùng release packages từ:

```text
ophub/kernel
```

Folder vendored này vẫn giữ để tham chiếu config/patch từ Ophub:

```text
kernel/
```

Nó không phải Linux source tree và hiện không phải package source mặc định.

Nếu muốn dùng `kernel_repo: kernel`, folder đó phải có package kernel local dạng:

```text
kernel/<tag>/<version>/boot-*.tar.gz
kernel/<tag>/<version>/dtb-<platform>-*.tar.gz
kernel/<tag>/<version>/modules-*.tar.gz
kernel/<tag>/<version>/header-*.tar.gz
```

## Local smoke test

```bash
cd amlogic-s9xxx-armbian
bash -n rebuild
find kernel -name 'boot-*.tar.gz'
```

Rebuild local khi đã có input image + kernel package:

```bash
sudo ./rebuild -b s905w -r kernel -k 6.12.y
```

## Notes

- Internal names vẫn còn `armbian_*` để giữ compatibility với script Ophub.
- Runtime commands như `armbian-install`, `armbian-update` vẫn giữ vì overlay hiện phụ thuộc chúng.
- Đây là DietPi-focused fork, không phải upstream Ophub đầy đủ.
