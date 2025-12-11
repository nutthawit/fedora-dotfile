1. Partition with `Blivet-gui`

| Device | Type | Format | Size | Label | Mountpoint |
|--------|------|--------|------|-------|------------|
| nvme0n1 | partition | efi | 1024MiB | ESP | /boot/efi |
| FEDORA | btrfs | btrfs | -1 | FEDORA ||

2. Btrfs subvolume design

| Name | Mount path |
|------|------------|
| @    | / |
| @home| /home |
| @var | /var |
| @mozilla | ${HOME}/.mozilla |
| @thunderbird | ${HOME}/.thunderbird |
