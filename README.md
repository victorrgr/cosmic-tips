# COSMIC Tips

This repository gathers various tips for COSMIC DE.

## Table of Contents

- [Make an App Open Floating](#make-an-app-open-floating)
- [Install Chromium as a Native Package](#install-chromium-as-a-native-package)
- [Customize COSMIC with COSMIC Tweaks](#customize-cosmic-with-cosmic-tweaks)
- [Improve Audio Quality with Easy Effects and framework-dsp](#improve-audio-quality-with-easy-effects-and-framework-dsp)

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

# Customize COSMIC with COSMIC Tweaks

[COSMIC Tweaks](https://github.com/cosmic-utils/tweaks) is a third-party settings app that unlocks extra customization options not available in the default COSMIC Settings.

It offers:

- **Color schemes** — apply community color themes to the whole desktop
- **Dock** — fine-tune dock behavior and appearance
- **Panel** — adjust the top bar settings beyond what COSMIC Settings exposes
- **Layouts** — one-click presets that rearrange the dock and panel to mimic Mac, Ubuntu, Windows, or the default Cosmic layout, making it easier to transition from another OS
- **Shortcuts** — manage additional keyboard shortcuts
- **Snapshots** — save and restore complete desktop configuration snapshots

## 1) Install COSMIC Tweaks

Open the **COSMIC App Store**, search for **tweaks**, and install **COSMIC Tweaks**.

Alternatively, install it from the terminal:

```bash
sudo apt install cosmic-tweaks
```

## 2) Apply a Layout (optional)

If you are coming from another operating system, open COSMIC Tweaks, go to the **Layouts** section, and pick the preset that matches your previous environment (Mac, Ubuntu, or Windows). This repositions the dock and panel to a familiar arrangement with a single click.
# Improve Audio Quality with Easy Effects and framework-dsp

[Easy Effects](https://github.com/wwmm/easyeffects) is a powerful audio processing application for PipeWire. Combined with the community presets from [framework-dsp](https://github.com/cab404/framework-dsp), you can significantly improve the sound quality of your speakers or headphones — including the **EEGuide+Exciter** preset shown below.

## 1) Install Easy Effects

Install Easy Effects via Flatpak (recommended):

```bash
flatpak install flathub com.github.wwmm.easyeffects
```

## 2) Install the framework-dsp Presets

Clone the [framework-dsp](https://github.com/cab404/framework-dsp) repository and copy the preset files to the Easy Effects output presets directory.

**For a Flatpak installation:**

```bash
git clone https://github.com/cab404/framework-dsp.git /tmp/framework-dsp
mkdir -p ~/.var/app/com.github.wwmm.easyeffects/config/easyeffects/output/
cp /tmp/framework-dsp/*.json ~/.var/app/com.github.wwmm.easyeffects/config/easyeffects/output/
```

**For a native (non-Flatpak) installation:**

```bash
git clone https://github.com/cab404/framework-dsp.git /tmp/framework-dsp
mkdir -p ~/.config/easyeffects/output/
cp /tmp/framework-dsp/*.json ~/.config/easyeffects/output/
```

## 3) Configure Preset Autoloading

This step tells Easy Effects to apply the **EEGuide+Exciter** preset automatically whenever your audio device is detected.

1. Open Easy Effects.
2. Click the **PipeWire** tab at the top.
3. In the left sidebar, click **Presets Autoloading**.
4. In the **Device** dropdown, select your audio output device (e.g. *Family 17h/19h HD Audio Controller Analog Stereo*).
5. In the **Preset** dropdown, select **EEGuide+Exciter**.
6. Click the **+** button to save the autoloading rule.

The configuration will look like this:

![Presets Autoloading configuration showing EEGuide+Exciter preset applied to the audio device](https://github.com/user-attachments/assets/a54910f3-f0e6-4927-913c-ee5d3b0d6d93)

## 4) Run Easy Effects as a System Service

To have Easy Effects apply audio processing automatically at login (even without opening the UI), enable it as a systemd user service.

**Option A — Use the built-in toggle (easiest):**

Open Easy Effects → click the hamburger menu (top-right) → enable **"Launch Service at System Startup"**.

**Option B — Enable manually via the terminal:**

```bash
systemctl --user enable --now easyeffects.service
```

> **Note:** The `easyeffects.service` file is created automatically by Easy Effects on first launch. If the command above fails, launch Easy Effects once so it can register the service, then run the command again.

Verify the service is running:

```bash
systemctl --user status easyeffects.service
```

After this, Easy Effects will start silently in the background on every login and apply your configured presets automatically.

