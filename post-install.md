1. Sudo without password

```sh
# run as root
EDITOR=vi visudo
```

2. Permanent mount mozilla and thunderbird

<!--```sh
mv "${HOME}/.mozilla" "${HOME}/.mozilla-old"
```-->

```sh
# run as root
BTRFS_UUID=$(blkid -s UUID -o value /dev/nvme0n1p2)
bash -c 'cat >> /etc/fstab' << EOF
UUID=$BTRFS_UUID /home/tie/.mozilla      btrfs   subvol=@mozilla,compress=zstd:1 0 0
UUID=$BTRFS_UUID /home/tie/.thunderbird      btrfs   subvol=@thunderbird,compress=zstd:1 0 0
EOF

systemctl daemon-reload
mount -a
mount
lsblk
```

3. Copy ssh, mozilla and thunderbird from thumb drive

```sh
# Pluged the thumb drive
DRIVE=/run/media/tie/SanDisk32G
cp -r ${DRIVE}/ssh ${HOME}/.ssh
cp -r ${DRIVE}/mozilla/* ${HOME}/.mozilla/
cp -r ${DRIVE}/thunderbird/* ${HOME}/.thunderbird/
cp ${DRIVE}/Passwords.kdbx ${HOME}/
```

Enable rpmfusion

```sh
sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
sudo dnf config-manager setopt fedora-cisco-openh264.enabled=1
```

4. System upgrade

```sh
sudo dnf update -y
reboot
```

5. Clone this repository 

```sh
git clone git@github.com:nutthawit/fedora-dotfile.git ~/.dotfile
```

5. Install tools

```sh
# Make firefox play all videos on the enternet
sudo dnf install gstreamer1-plugin-openh264 mozilla-openh264

# Necessary tools
sudo dnf install stow tmux keepassxc telegram-desktop btop

# Terminal tools
sudo dnf copr enable atim/starship
sudo dnf install starship zoxide

# Install tmux-resurrect
git clone https://github.com/tmux-plugins/tmux-resurrect.git ~/tmux-resurrect

# Install zed
curl -f https://zed.dev/install.sh | sh

cd ~/.dotfile
mv ~/.bashrc ~/.bashrc-old
stow -v bash
stow -v tmux
stow -v git
source ~/.bashrc
```

6. Snapshot post-install

```sh
sudo snap @ post_install
sudo snap @home post_install
```
