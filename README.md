# COSMIC Tips

This gist gather various tips on COSMIC DE.

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
