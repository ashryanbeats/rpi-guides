Basic System Updates and Security

Update the package list and upgrade all packages:

```bash
sudo apt update
sudo apt full-upgrade
```

User Experience Improvements

Install common utilities:

```bash
sudo apt install htop # for system monitoring
sudo apt install neofetch # for system info
```

Set up automatic updates if desired

Configure timezone and locale settings

```bash
sudo raspi-config # Navigate to Localisation Options
```

Looking at the problem again - since you want to use raspi-config, let's try this specific sequence:

1. Run raspi-config:

```bash
sudo raspi-config
```

2. Go to "Localisation Options" (or "Internationalisation Options" depending on your version)

3. Select "Locale"

4. In the locale list:

   - Deselect any selected GB locales (en_GB.UTF-8) by pressing Space
   - Select ONLY "en_US.UTF-8" by pressing Space
   - Press Tab to get to OK
   - Press Enter

5. When prompted for default locale, select "en_US.UTF-8"

6. Let it finish generating locales

7. Exit raspi-config and reboot

The key difference from our earlier attempt is making sure to deselect any GB locales before setting US as the default. This should give you a clean US-only setup without the locale errors we saw before.

en_US.UTF-8 UTF-8

default locale

en_US.UTF-8

timezone

America/New_York

(Move to kiosk.md if using as a kiosk)
If using as a display:

Configure auto-login
