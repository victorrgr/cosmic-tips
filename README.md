# COSMIC Tips

This repository gathers various tips for COSMIC DE.

## Table of Contents

- [Make an App Open Floating](#make-an-app-open-floating)
- [Install Chromium as a Native Package](#install-chromium-as-a-native-package)
- [Raise Volume Beyond 100% with Custom Keyboard Shortcuts](#raise-volume-beyond-100-with-custom-keyboard-shortcuts)

---

# Make an App Open Floating

In **COSMIC**, whether a window is *tiled* or *floating* is controlled by COSMIC window rules.  
So to force an App to open **floating**, add it as a **tiling exception**.

This is particularly useful for clipboard managers, some of them, like **copyq** currently does not have support to open floating.

## 1) Confirm the app id

Check the desktop entry name:

```bash
ls /usr/share/applications | grep -i copyq
```

Example output:

```text
com.github.hluk.copyq.desktop
```

The **appid** is the filename **without** `.desktop`:

- `com.github.hluk.copyq`

## 2) Add a tiling exception rule

Create the rules directory and edit the custom tiling exception file:

```bash
mkdir -p ~/.config/cosmic/com.system76.CosmicSettings.WindowRules/v1
nano ~/.config/cosmic/com.system76.CosmicSettings.WindowRules/v1/tiling_exception_custom
```

Paste this rule:

```text
[
  (
    enabled: true,
    appid: "com.github.hluk.copyq",
    title: "CopyQ",
  ),
]
```

Save the file, then open CopyQ again (e.g., using your shortcut). It should now open **floating**.

---

# Install Chromium as a Native Package

Chromium offers much better performance when installed as a native package instead of Flatpak.

#### First, install Chromium using this tutorial:
https://ubuntuhandbook.org/index.php/2024/05/install-chromium-ubuntu-2404/

#### Then enable this flag in Chromium for better compatibility and performance with the Wayland protocol:

chrome://flags/#wayland-session-management

---

# Raise Volume Beyond 100% with Custom Keyboard Shortcuts

By default, COSMIC's native volume keys are capped at 100%. You can override them with custom shortcuts that use `pactl` directly, allowing you to raise the volume up to 155% (and lower it with a safe lower bound of 5%).

## Steps

1. Open **Settings** → **Input Devices** → **Keyboard** → **View and Customize Shortcuts** → **Custom** → **Add Shortcut**.

2. Add the **lower volume** shortcut:

   - **Name:** `volumedown`
   - **Shortcut:** `XF86AudioLowerVolume`
   - **Command:**
     ```bash
     sh -c 'VOLUME=$(pactl get-sink-volume @DEFAULT_SINK@ | grep -oP '"'"'\d+(?=%)'"'"' | head -1); LIMIT=5; [ "$VOLUME" -gt "$LIMIT" ] && pactl set-sink-volume @DEFAULT_SINK@ -5%'
     ```

3. Add the **raise volume** shortcut:

   - **Name:** `volumeup`
   - **Shortcut:** `XF86AudioRaiseVolume`
   - **Command:**
     ```bash
     sh -c 'VOLUME=$(pactl get-sink-volume @DEFAULT_SINK@ | grep -oP '"'"'\d+(?=%)'"'"' | head -1); LIMIT=155; [ "$VOLUME" -lt "$LIMIT" ] && pactl set-sink-volume @DEFAULT_SINK@ +5%'
     ```

> **Note:** The lower-volume command prevents the volume from going below 5%, and the raise-volume command caps it at 155%.
