1. Install

```sh
sudo dnf install btrbk
```

2. Create systemd service

```sh
cat << 'EOF' > /etc/systemd/system/create-machine-startup-snapshot.service
[Unit]
Description=Create Btrfs machine startup snapshots
DefaultDependencies=no
After=local-fs.target
Before=multi-user.target

[Service]
Type=oneshot
ExecStart=/usr/local/bin/create-machine-startup-snapshot
RemainAfterExit=no

[Install]
WantedBy=multi-user.target
EOF
systemctl enable create-machine-startup-snapshot
```

3. Create a startup script

```sh
cat << 'EOF' > /usr/local/bin/create-machine-startup-snapshot 
#!/usr/bin/bash

set -eu

TARGET_DIR="${1:-/mnt/btr_pool/btrbk_snapshots}"

# Delete old machine start snapshots
find "$TARGET_DIR" -maxdepth 1 -name "*machine_start*" -type d -print -exec btrfs subvolume delete {} \;

# Create new snapshots
snap @ machine_start
snap @home machine_start
EOF
chmod +x /usr/local/bin/create-machine-startup-snapshot 
```

4. Create the `snap` command (a wrapper script)

```sh
cat << 'EOF' > /usr/local/bin/snap 
#!/usr/bin/bash
#
# snap

show_example() {
        echo "Usage: $0 <subvol_name> <description>"
    echo "Example: $0 @ firefox_installed"
}

if [ $# -eq 0 ]; then
    show_example
        exit 1
fi

if [ "$(id -u)" -ne 0 ]; then
  echo "This script must be run with 'sudo' or as the 'root' user."
  exit 1
fi

subvol_name="$1"
description="$2"

if [ "$subvol_name" == "@" ]; then
        btrbk snapshot -c /etc/btrbk/btrbk-@.conf @ | rename-snapshot "$description"
elif [ "$subvol_name" == "@home" ]; then
        btrbk snapshot -c /etc/btrbk/btrbk-@home.conf @home | rename-snapshot "$description"
else
        show_example
    exit 1
fi
EOF
chmod +x /usr/local/bin/snap
```

5. Create the `rename-snapshot` command

```sh
cat << 'EOF' > /usr/local/bin/rename-snapshot
#!/usr/bin/bash
#
# rename-snapshot

# Check if description parameter is provided
if [ $# -eq 0 ]; then
    echo "Usage: $0 <description>"
    echo "Example: btrbk snapshot -c /etc/btrbk/btrbk-@.conf @ | $0 firefox_installed"
    exit 1
fi

description="$1"

# Read from stdin if piped, otherwise use first argument
if [ ! -t 0 ]; then
    # Reading from pipe
    input=$(cat)
fi

# Extract just the snapshot name (home.20251006T0809 or @.20251006T0809)
snapshot_name=$(echo "$input" | grep -oE '(@home|@)\.[0-9]{8}T[0-9]{4}')

if [ -z "$snapshot_name" ]; then
    echo "Error: Could not extract snapshot name from input"
    exit 1
fi

subvol="${snapshot_name%.*}"      # Everything before the last dot
timestamp="${snapshot_name#*.}"   # Everything after the first dot

# New name format with custom description
new_name=${subvol}.${description}.${timestamp}

# Rename the snapshot
mv "/mnt/btr_pool/btrbk_snapshots/$snapshot_name" "/mnt/btr_pool/btrbk_snapshots/$new_name"
echo "Renamed: $snapshot_name -> $new_name"
EOF
chmod +x /usr/local/bin/rename-snapshot
```

6. Create btrfs configurations

```sh
cat << EOF > /etc/btrbk/btrbk-@.conf
timestamp_format long

snapshot_dir /mnt/btr_pool/btrbk_snapshots
subvolume /mnt/btr_pool/@

EOF

cat << EOF > /etc/btrbk/btrbk-@home.conf
timestamp_format long

snapshot_dir /mnt/btr_pool/btrbk_snapshots
subvolume /mnt/btr_pool/@home

EOF
```

7. Mount root partition to /mnt/btr_pool and add entry to /etc/fstab

```sh
ROOT=/dev/nvme0n1p2
BTR_POOL=/mnt/btr_pool

echo "${ROOT} ${BTR_POOL} btrfs subvolid=5,noatime 0 0" | tee -a /etc/fstab
mkdir ${BTR_POOL}
mount -a
mount
```

8. Create directory for store snapshots

```sh
mkdir /mnt/btr_pool/btrbk_snapshots
```

Then, restart a machine and snapshot will automatically create when the machine startup.
fresh-kde

9. Create first snapshot

```sh
# run as root
DE="cosmic"
snap @ "fresh_${DE}"
snap @home "fresh_${DE}"
```
