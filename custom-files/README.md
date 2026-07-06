# Custom files overlay

Files in this directory are copied into the rebuilt DietPi root filesystem during the repack step.

Examples:

```text
custom-files/etc/sysctl.d/99-custom.conf
custom-files/etc/modules-load.d/custom.conf
custom-files/usr/local/bin/my-tool
custom-files/etc/custom_service/start_service.sh
```

Keep paths relative to the target root filesystem.
