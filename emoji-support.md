# Emoji Support Setup on Raspberry Pi OS

This guide explains how to enable emoji support on Raspberry Pi OS by installing the Noto Color Emoji font.

## Prerequisites

Ensure your Raspberry Pi is updated to the latest version of its operating system and has internet access.

This guide was written as tested with:

- Raspberry Pi 4 or later (tested with Raspberry Pi 4 Model B)
- Raspberry Pi OS (Bookworm or later) installed

## Installation

Follow these steps to install the Noto Color Emoji font on your Raspberry Pi:

1. **Update your package list**:

   ```bash
   sudo apt update
   ```

2. **Install the Noto Color Emoji font**:

   ```bash
   sudo apt install fonts-noto-color-emoji
   ```

3. **Update the font cache** (optional, as this usually happens automatically):

   ```bash
   fc-cache -f -v
   ```

4. **Restart your application or Raspberry Pi** to ensure the new fonts are used.

## Usage

After installing the font, you should be able to see emojis in applications that support them, such as web browsers or text editors that are capable of rendering them.

## Troubleshooting

If emojis are not displaying properly, make sure the application you are using supports color emojis and has been restarted after the font installation.
