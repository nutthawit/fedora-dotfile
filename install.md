1. Boot from *Fedora-Everything-netinst-x86_64-43-1.6.iso*

2. Partition with *Blivet-gui*

| Device | Type | Format | Size | Label | Mountpoint |
|--------|------|--------|------|-------|------------|
| nvme0n1 | partition | efi | 1024MiB | ESP | /boot/efi |
| nvme0n2 | swap | swap | 32768MiB | SWAP ||
| FEDORA | btrfs | btrfs | 100% | FEDORA ||

3. Btrfs subvolume design

| Name | Mount path |
|------|------------|
| @    | / |
| @home| /home |
| @var | /var |
| @mozilla | ${HOME}/.mozilla |
| @thunderbird | ${HOME}/.thunderbird |

4. Select packages as follow

```toml
Base_Environment = "COSMIC Desktop"
Additional_softwares = [
  "C Development Tools and Libraries",
  "COSMIC Desktop",
  "COSMIC Desktop Supplementary Applications",
  "Development Tools",
  "RPM Development Tools",
]
```
