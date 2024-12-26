# Raspberry Pi Display Sleep and Wake Automation

This guide provides step-by-step instructions for setting up a Raspberry Pi with automated screen sleep and wake functionality using xscreensaver.

## Features

- Automatically blanks and powers down the screen after a period of inactivity
- Wakes the screen upon detecting touch input (handled by hardware and drivers, no extra scripts required)
- Configurable timeout and power management settings

## Prerequisites

### Hardware:

- Raspberry Pi 4 or later (tested with Raspberry Pi 4 Model B)
- ROADOM 7" Raspberry Pi Touch Screen or a compatible display

### Software:

- Raspberry Pi OS (Bookworm or later) installed
- Network access for updates and package installation

## Setup Instructions

### 0. Set Window Manager to Openbox with X11 Backend

1. Open the Raspberry Pi Configuration tool:

   ```bash
   sudo raspi-config
   ```

2. Navigate to _Advanced Options > Wayland_
3. Select "Openbox window manager with X11 backend"
4. Exit the configuration tool and confirm rebooting when prompted

### 1. Install xscreensaver

xscreensaver is used to manage screen blanking and power-saving features.

```bash
sudo apt-get update
sudo apt-get install xscreensaver
```

### 2. Configure xscreensaver

1. Open the .xscreensaver configuration file:

   ```bash
   nano ~/.xscreensaver
   ```

2. Update the file with the following settings:

   ```bash
   # Screen will blank (turn off visually) after 1 minute of inactivity
   timeout: 1:00
   # Cycle is for changing screensaver modes. Using mode: blank, this has no real effect
   cycle: 1:00
   # Ensures the screen goes completely blank rather than showing an animated or image-based screensaver
   mode: blank
   # Enables Display Power Management Signaling (DPMS), allowing the display to enter power-saving states
   dpmsEnabled: True
   # Standby after 2 minutes
   dpmsStandby: 2:00
   # Suspend (deeper power-saving state) after 3 minutes
   dpmsSuspend: 3:00
   # Turn off the screen after 4 minutes
   dpmsOff: 4:00
   ```

3. Save and exit the editor (press CTRL+X, then Y, and Enter)

### 3. Configure Startup Services

Ensure xscreensaver starts automatically on boot.

1. Edit the user's crontab:

   ```bash
   crontab -e
   ```

   Note: do not use `sudo` for this command, which would instead edit the root user's crontab. The screensaver should run as the user, not root. For more information, see the the Auto-Reboot guide.

2. Add the following line:

   ```
   @reboot xscreensaver -no-splash
   ```

3. Save and exit

### 4. Test the Setup

1. Reboot the Raspberry Pi:

   ```bash
   sudo reboot
   ```

2. Verify functionality:

   - The screen should blank and power down after 1 minute of inactivity (as configured in .xscreensaver)
   - The screen should wake up upon touch input

## Troubleshooting

### Screen Does Not Blank:

- Verify xscreensaver is running:

  ```bash
  ps aux | grep xscreensaver
  ```

- Start it manually if not running:

  ```bash
  xscreensaver -no-splash &
  ```

### Screen Does Not Wake:

- Check DPMS settings:

  - Verify settings with:

    ```bash
    xset q
    ```

- Manually force DPMS states:

  ```bash
  xset dpms force off
  ```

## Customization

- Adjust .xscreensaver settings for different timeouts or modes to suit your needs

## Support

If you encounter issues:

- Verify all hardware connections are secure
- Check for updates:

  ```bash
  sudo apt-get update && sudo apt-get full-upgrade
  ```

- Refer to the Raspberry Pi documentation or relevant forums for additional help
