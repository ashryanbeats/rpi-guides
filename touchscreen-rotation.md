# Raspberry Pi Touchscreen Rotation Setup

## Overview

Note: This guide uses X11 to configure the touchscreen rotation. If you know how to do this with Wayland, please let me know!

This guide explains how to rotate both the HDMI display and touchscreen input on a Raspberry Pi. In this example, both are rotated 90° clockwise (right).

Below you will see the steps to enable:

- **HDMI Display Rotation**: Configured using `xrandr`
- **Touchscreen Input Rotation**: Configured using `xinput` and a transformation matrix
- **Persistence**: Managed via a combined rotation script and `systemd`

By following these steps, you'll have a Raspberry Pi with a rotated display and touchscreen input that persists across reboots.

## Prerequisites

- Raspberry Pi 4 Model B or later
- Raspberry Pi OS (Bookworm or later)

## Steps

### 0. Set Window Manager to Openbox with X11 Backend

1. Open the Raspberry Pi Configuration tool:

   ```bash
   sudo raspi-config
   ```

2. Navigate to _Advanced Options > Wayland_
3. Select "Openbox window manager with X11 backend"

4. Exit the configuration tool and confirm rebooting when prompted

### 1. Rotate the HDMI Display

1. Identify the Active Display Output:

   ```bash
   DISPLAY=:0 xrandr --query
   ```

   (Sidebar: If you're curious what `DISPLAY=:0` does, see the explanation at the end of this guide.)

   Example output:

   ```
   HDMI-2 connected 1024x600+0+0 (normal left inverted right x axis y axis) 0mm x 0mm
   ```

   In this example, `HDMI-2` is the active HDMI display. We'll use this string in the next step.

2. Rotate the Display:

   Replace `HDMI-2` with your display name in the following command:

   ```bash
   DISPLAY=:0 xrandr --output HDMI-2 --rotate right
   ```

3. Verify the Display Rotation:

   You can verify the display rotation by looking at the screen or running the `xrandr --query` command again.

   ```bash
   DISPLAY=:0 xrandr --query
   ```

   The output should show the display as rotated (e.g. instead of `1024x600`, you'll see `600x1024`).

### 2. Rotate the Touchscreen Input

1. Identify the Touchscreen Device:

   ```bash
   DISPLAY=:0 xinput list
   ```

   Example output:

   ```
   ⎡ Virtual core pointer    id=2    [master pointer  (3)]
   ⎜   ↳ yldzkj USB2IIC_CTP_CONTROL   id=6    [slave  pointer  (2)]
   ```

   `yldzkj USB2IIC_CTP_CONTROL` is the touchscreen device name in this example, which we'll use in the next step.

2. Rotate the Touchscreen Input:

   ```bash
   DISPLAY=:0 xinput set-prop "yldzkj USB2IIC_CTP_CONTROL" "Coordinate Transformation Matrix" 0 1 0 -1 0 1 0 0 1
   ```

   This command sets the transformation matrix to rotate the touchscreen input 90° clockwise. Other options include:

   - `0 -1 1 1 0 0 0 0 1`: 90° counterclockwise
   - `-1 0 1 0 -1 1 0 0 1`: 180°

   Just make sure you're matching the display rotation with the touchscreen input rotation.

3. Test the Touchscreen:

   Verify that the touch input aligns with the rotated display. If not, you may need to adjust the transformation matrix or the display rotation, or both.

### 3. Create a Combined Rotation Script for Persistence

1. Create the Script:

   ```bash
   nano ~/rotate_display.sh
   ```

2. Add the Commands:

   ```bash
   #!/bin/bash
   DISPLAY=:0 xrandr --output HDMI-2 --rotate right
   DISPLAY=:0 xinput set-prop "yldzkj USB2IIC_CTP_CONTROL" "Coordinate Transformation Matrix" 0 1 0 -1 0 1 0 0 1
   ```

3. Make the Script Executable:

   ```bash
   chmod +x ~/rotate_display.sh
   ```

### 4. Automate the Rotation Script with systemd

1. Create a systemd Service:

   ```bash
   sudo nano /etc/systemd/system/rotation.service
   ```

2. Add the Following to the File:

   Note: Be sure to replace the following with your own values:

   1. `/home/pi/rotate_display.sh`: the full path to your script (no shortcuts)
   2. `User=pi` and `Group=pi`: the user and group that will run the script

   ```ini
   [Unit]
   Description=Rotate HDMI Display and Touchscreen
   After=graphical.target

   [Service]
   ExecStart=/home/pi/rotate_display.sh
   Environment=DISPLAY=:0
   User=pi
   Group=pi

   [Install]
   WantedBy=graphical.target
   ```

3. Enable and Start the Service:

   ```bash
   sudo systemctl enable rotation.service
   sudo systemctl start rotation.service
   ```

4. Reboot to Verify:

   ```bash
   sudo reboot
   ```

   After rebooting, the display and touchscreen should be rotated as expected.

## What is DISPLAY=:0?

The DISPLAY environment variable tells X11 which display server session to use:

- `=:0`: Refers to the first graphical display session (the default on most setups)
- This is required when running xrandr or xinput commands over SSH or outside the graphical session

If you are running the commands directly on the Raspberry Pi, you can omit the DISPLAY=:0 prefix. If you are running the commands over SSH, you need to include it.

If you are addressing a different display session, you can change the number accordingly (e.g., DISPLAY=:1).

## Troubleshooting

1. Verify Touchscreen and Display Configurations:

```bash
# List touchscreen devices
DISPLAY=:0 xinput list

# Check connected displays
DISPLAY=:0 xrandr --query
```

2. Check systemd Logs:

```bash
sudo journalctl -u rotation.service
```

3. Manually Test the Script:

```bash
~/rotate_display.sh
```
