# Fix Error "exclusive access to /dev/XXX"

Linux MD partitions and LVM PVs are automatically run at startup, which interferes with `coreos.inst`

- Boot the live image without any `coreos.inst` options
- If MD partitions where used, for each mdXXX listed in /proc/mdstat execute

```bash
sudo mdadm --stop /dev/mdXXX
```

- If VolumeGroups are listed `vgs`, for each one execute

```bash
sudo vgchange -an XXX
```

-  Fetch the Ignition `curl -o config.ign <ignition_config_url>`
```bash
sudo coreos-installer install <target-device> -i config.ign <additional-args>
```

