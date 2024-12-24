# First Boot

## Assumptions

- You have a Raspberry Pi Model B or later
- You have an appropriate SD card with fresh install of Raspberry Pi OS Bookworm or later
- You haven't booted the OS yet
- (Optional) You have connected your Pi to a touchscreen display

Most steps in these guides will assume you are doing them via SSH (i.e., you are connecting remotely from another computer, like a Mac). If you are using a keyboard connected directly to the Pi, some details may differ but everything within can absolutely be done with that setup.

## Basic System Updates and Security

Update the package list and upgrade all packages:

```bash
sudo apt update
sudo apt full-upgrade
```

## Admin Experience Improvements

Install common utilities:

```bash
sudo apt install htop # for system monitoring
sudo apt install neofetch # for system info
```

## Basic OS Configuration

Configure timezone and locale settings:

1. Run raspi-config:

   ```bash
   sudo raspi-config
   ```

2. Go to "Localisation Options" (or "Internationalisation Options" depending on your version)

3. Select "Locale"

4. In the locale list:

   - Deselect any undesired locales (e.g. en_GB.UTF-8, which is usually the default) by pressing Space
   - Select the desired locales (e.g. "en_US.UTF-8") by pressing Space
   - Press Tab to get to OK
   - Press Enter

5. When prompted for default locale, select "en_US.UTF-8" (or your desired locale)

6. Let it finish generating locales

7. Exit raspi-config and reboot
