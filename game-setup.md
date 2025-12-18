Install

```sh
# lm_sensor for cpu temperature measurement
sudo dnf install lm_sensors steam
```

Check GPU driver is loaded

```sh
lspci -k | grep amd
>>>
Kernel driver in use: amdgpu
Kernel modules: amdgpu

lsmod | grep amdgpu
>>>
amdgpu              20701184  59
amdxcp                 12288  1 amdgpu
gpu_sched              69632  1 amdgpu
drm_panel_backlight_quirks    12288  1 amdgpu
drm_buddy              32768  1 amdgpu
drm_ttm_helper         16384  1 amdgpu
ttm                   135168  2 amdgpu,drm_ttm_helper
drm_exec               12288  1 amdgpu
i2c_algo_bit           20480  1 amdgpu
drm_suballoc_helper    20480  1 amdgpu
drm_display_helper    331776  1 amdgpu
cec                   106496  2 drm_display_helper,amdgpu
video                  81920  1 amdgpu
```

CPU temperature measurement

```sh
watch -n 2 sensors
```

Joystick 

```sh
sudo dnf install  joystick-support jstest-gtk linuxconsoletools
```

Testing

Once the modules are loaded, you should be able to find a new device: /dev/input/js0 and a file ending with -event-joystick in /dev/input/by-id directory. You can simply cat those devices to see if the joystick works — move the stick around, press all the buttons - you should see mojibake printed when you move the sticks or press buttons.
