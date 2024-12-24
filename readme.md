# Raspberry Pi Kiosk Mode Setup

## Overview

This guide shows how to configure a Raspberry Pi to boot directly into Chromium browser in kiosk mode, creating a clean, fullscreen web display.

## Prerequisites

- Raspberry Pi 4 or later
- Raspberry Pi OS (Bookworm or later)
- Network access for updates and package installation

## Steps

### 1. Install Prerequisites

Install Chromium if not already present:

```bash
sudo apt update
sudo apt install -y chromium-browser
```

### 2. Create Kiosk Script

1. Create the script:

```bash
nano ~/kiosk.sh
```

2. Add the following content:

```bash
#!/bin/bash
# Optionally disable screen management features if you want the display to stay on
# xset s off      <- Disable screen saver
# xset -dpms      <- Disable power management
# xset s noblank  <- Disable blanking of the screen

# Launch Chromium in kiosk mode
chromium-browser \
  --kiosk \
  --noerrdialogs \
  --disable-infobars \
  --hide-scrollbars \
  --touch-events=enabled \
  --disable-pinch \
  --overscroll-history-navigation=0 \
  --force-device-scale-factor=1 \
  --no-first-run \
  --app="http://your-url-here"
```

3. Make it executable:

```bash
chmod +x ~/kiosk.sh
```

### 3. Automate Kiosk Startup

To run the kiosk script automatically when the graphical environment starts:

1. Edit the global LXDE autostart file:

```bash
sudo nano /etc/xdg/lxsession/LXDE-pi/autostart
```

2. Add the kiosk script to the file:

```bash
@/home/pi/kiosk.sh
```

Example file:

```bash
@lxpanel --profile LXDE-pi
@pcmanfm --desktop --profile LXDE-pi
@xscreensaver -no-splash
@/home/pi/kiosk.sh
```

3. Save and exit:
   • Press Ctrl + O, Enter to save.
   • Press Ctrl + X to exit.

4. Reboot to verify:

```bash
sudo reboot
```

After rebooting, Chromium should launch automatically in kiosk mode.

## Chromium Flags Explained

The kiosk script uses several Chromium flags to create a clean kiosk experience:

- `--kiosk`: Launches in fullscreen kiosk mode
- `--noerrdialogs`: Suppresses error dialogs
- `--disable-infobars`: Removes info bars from the top of the page
- `--hide-scrollbars`: Hides scrollbars for a cleaner look
- `--touch-events=enabled`: Ensures touch events work properly
- `--disable-pinch`: Disables pinch-to-zoom gestures
- `--overscroll-history-navigation=0`: Disables swipe navigation
- `--force-device-scale-factor=1`: Sets consistent scaling
- `--no-first-run`: Skips first-run setup screens
- `--app=<URL>`: Removes browser UI and loads only the specified page.

## Troubleshooting

### To exit kiosk mode:

- Close Chromium: Press Alt + F4 or Ctrl + Alt + F1
- Switch to a terminal: Ctrl + Alt + F1.

### Check Script or Autostart Issues

1. Run the Script Manually:

```bash
~/kiosk.sh
```

2. Check Autostart File:

```bash
cat /etc/xdg/lxsession/LXDE-pi/autostart
```

Ensure the file includes the line:

```txt
@/home/pi/kiosk.sh
```

### Debug logs

1. Check system logs:

```bash
sudo journalctl -xe
```

2. Check Chromium errors:

```bash
chromium-browser --no-sandbox
```

### Maintenance tips

1. Schedule a daily reboot:

```bash
sudo crontab -e
```

Add the following line to reboot at 3 am daily:

```txt
0 3 * * * /sbin/reboot
```

2. Monitor System Resources:

Use htop or similar tools to check CPU and memory usage periodically.

3. Keep the System Updated:

Run regular updates to keep the system and Chromium up to date.

```bash
sudo apt update
sudo apt upgrade -y
```

## Future Enhancements

- Add error recovery scripts
- Implement health monitoring
- Add custom CSS/JS for touch optimization
- Configure automatic updates
- Add remote management capabilities
