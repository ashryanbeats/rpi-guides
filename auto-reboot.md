# Raspberry Pi Automatic Reboot Configuration

This guide explains how to set up an automatic daily reboot schedule for your Raspberry Pi using cron.

## Overview

Automatic reboots can help maintain system stability and performance by clearing memory, terminating stuck processes, and ensuring all services start fresh. This setup uses the system crontab to schedule a safe reboot process.

## Prerequisites

- Raspberry Pi running Raspberry Pi OS
- Root/sudo access
- Basic knowledge of terminal commands

## Important Note About Crontabs

Different commands should go in different crontabs based on their permission needs:

- `crontab -e`: Edits the current user's crontab

  - GUI applications like xscreensaver
  - User scripts
  - Applications that don't need elevated privileges

- `sudo crontab -e`: Edits the root user's crontab for commands that require root privileges

  - System commands like shutdown, reboot
  - System service management
  - Network configuration changes

For example:

```bash
# In user's crontab (crontab -e):
@reboot xscreensaver -no-splash

# In root's crontab (sudo crontab -e):
30 14 * * * /usr/sbin/shutdown -r now "System is rebooting now"
```

For this guide, we use the root crontab (`sudo crontab -e`) because the shutdown/reboot command requires root privileges. Contrast this with user-level commands like starting a screensaver, which would go in the user's crontab (`crontab -e`).

## Setup Instructions

### 1. Access the Root Crontab

```bash
sudo crontab -e
```

If prompted to select an editor, nano is recommended for beginners (usually option 1).

### 2. Add the Reboot Schedule

Add the following line to schedule a reboot at 2:30 PM daily:

```bash
30 14 * * * /usr/sbin/shutdown -r now "System is rebooting now"
```

Breaking down the cron expression:

- `30`: Minutes (30)
- `14`: Hour (2 PM in 24-hour format)
- `*`: Day of month (every day)
- `*`: Month (every month)
- `*`: Day of week (every day)

### 3. Save and Exit

1. Press `Ctrl + X` to exit
2. Press `Y` to save changes
3. Press `Enter` to confirm

## Verification

### Check if Cron Job is Installed

View your current crontab entries:

```bash
sudo crontab -l
```

You should see your reboot command listed.

### Monitor Reboots

Check when the last reboot occurred:

```bash
last reboot
```

## Customization

### Adjusting the Reboot Time

To change the reboot time, modify the first two numbers in the cron expression:

```bash
# Format: minute hour * * *
0 3 * * *     # Reboot at 3:00 AM
15 18 * * *   # Reboot at 6:15 PM
45 9 * * *    # Reboot at 9:45 AM
```

### Adding a Delay

To add a warning delay before reboot:

```bash
30 14 * * * /usr/sbin/shutdown -r +5 "System will reboot in 5 minutes"
```

The `+5` adds a 5-minute delay before the reboot begins.

Note that if you're following the instructions in the kiosk mode guide, the warning message won't be visible since Chromium is running in fullscreen kiosk mode with `--noerrdialogs` and `--disable-infobars` flags.

## Removing the Automatic Reboot

To remove the automatic reboot:

1. Open the crontab:

   ```bash
   sudo crontab -e
   ```

2. Delete or comment out (#) the reboot line
3. Save and exit

## Troubleshooting

### Cron Not Running

1. Check if cron service is running:

   ```bash
   sudo systemctl status cron
   ```

2. Start cron if needed:
   ```bash
   sudo systemctl start cron
   sudo systemctl enable cron
   ```

### Verifying Time Settings

Ensure your system time is correct:

```bash
date
```

If needed, update your timezone:

```bash
sudo raspi-config
# Navigate to Localisation Options > Timezone
```

## Notes

- The reboot will occur even if users are logged in
- Any unsaved data will be lost during reboot
- The system will be unavailable during the reboot process (typically 1-2 minutes)
- If running in kiosk mode, reboot warning messages will not be visible

## Support

For additional help:

- Check system logs: `sudo journalctl -xe`
- Review cron logs: `grep CRON /var/log/syslog`
- Consult Raspberry Pi documentation
