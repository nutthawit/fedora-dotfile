Rustup install

```sh
sudo dnf install rustup
rustup-init --profile default --no-modify-path -y

cd ~/.dotfile
stow -v cargo
cd ~
```

c-development and rpm-development-tools install

```sh
sudo dnf install @c-development @rpm-development-tools
```

Rustrover install

```sh
# Plugged the thumb drive
DRIVE=/run/media/tie/SanDisk32G
cp -a "${DRIVE}/RustRover-2025.3.tar.gz" "/var/RustRover-2025.3.tar.gz"
cd /var
sudo tar -xzf RustRover-*.tar.gz -C /opt
cd /opt/RustRover-2025.3/bin
bash rustrover.sh
```

Cosmic install

```sh
sudo dnf @cosmic-desktop @cosmic-desktop-apps
```

Clone forks

```sh
git clone git@github.com:nutthawit/cosmic-comp.git ~/cosmic-comp
git clone git@github.com:nutthawit/smithay.git ~/smithay
```
