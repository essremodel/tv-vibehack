<div align="center">

<img src="assets/tv-vibehack-hero.jpg" alt="tv-vibehack — conceptual television and remote artwork in charcoal, amber and violet" width="100%">

# tv-vibehack

### Your TV. Your home screen.

A lightweight Android TV launcher and a practical toolkit for disabling unwanted packages over Wi-Fi.

<p>
<a href="LICENSE"><img alt="License: MIT" src="https://img.shields.io/badge/License-MIT-8b5cf6?style=flat-square"></a>
<a href="launcher/app/build.gradle"><img alt="Launcher: Android 8 and newer" src="https://img.shields.io/badge/Launcher-Android%208%2B-3ddc84?style=flat-square"></a>
<a href="launcher/app/src/main/java/com/gathin/tvhome/"><img alt="Launcher written in Java" src="https://img.shields.io/badge/Launcher-Java-f59e0b?style=flat-square"></a>
<a href="tools/requirements.txt"><img alt="Tools use Python 3" src="https://img.shields.io/badge/Tools-Python%203-3776ab?style=flat-square"></a>
</p>

**[Get started](#quick-start) · [Find your TV](#supported-tvs) · [Customize](#the-launcher) · [Restore](#restore-your-tv)**

</div>

> **ESS fork of [Gathin23/tv-vibehack](https://github.com/Gathin23/tv-vibehack).**
> Original launcher, tooling, screenshots and community model lists come from upstream.
> This fork's README describes the code in this checkout; upstream may have newer fixes.

## See the difference

![Before: stock Google TV home with a sponsored ad. After: the tv-vibehack launcher](docs/stock-vs-new-side.jpg)

*Existing upstream before-and-after photo. The banner is illustrative artwork; this photo shows the actual project.*

**No root. No cable. Packages are disabled, not uninstalled.** Replace the home screen,
choose which manufacturer packages to disable, and keep a record of those changes.
This does not remove ads inside streaming apps or change their subscriptions.

## Choose your path

| You want to… | Start with |
|---|---|
| Have a coding assistant guide the changes | [Quick start](#quick-start), then [`PROMPT.md`](PROMPT.md) |
| Run the tools yourself | [Manual setup](#doing-it-by-hand) or [Windows](#windows) |
| Change the dock, layout or language | [Launcher configuration](#the-launcher) |
| Check your model before changing it | [Compatibility and contributed lists](#supported-tvs) |
| Get the original home screen back | [Restore your TV](#restore-your-tv) |
| Share what worked on your device | [Contribution guide](CONTRIBUTING.md) |

Device behavior varies by firmware. Start with the model reports and preserve your
original package/settings inventory before making changes.

---

<details>
<summary><strong>Browse the full guide</strong></summary>

- [Before you start](#before-you-start)
- [How it works](#how-it-works)
- [Quick start](#quick-start)
- [What you get](#what-you-get)
- [Supported TVs](#supported-tvs)
- [Doing it by hand](#doing-it-by-hand) · [Windows](#windows)
- [The launcher](#the-launcher)
- [Restore your TV](#restore-your-tv)
- [Troubleshooting](#troubleshooting)
- [Questions people ask](#questions-people-ask)
- [Project story](#project-story)
- [Contributing](#contributing) · [Licence](#licence)

</details>

---

## Before you start

| Requirement | Details |
|---|---|
| TV and network | Compatible Android TV / Google TV with ADB debugging available; computer and TV on the same local network |
| Device access | Access to the TV's settings and remote to approve your computer |
| Manual tooling | Python 3 plus the packages in [`tools/requirements.txt`](tools/requirements.txt), installed in a virtual environment |
| Building the launcher | JDK 17, Android SDK platform 34 and build-tools 34.0.0; [setup below](#the-launcher) |
| Recovery information | Save the original package/settings inventory, exact list of changes and original launcher package before changing the TV |

Run commands from a local checkout of this repository. If you use Git:

```bash
git clone https://github.com/essremodel/tv-vibehack.git
cd tv-vibehack
```

The guided prompt asks the assistant to create backups. The standalone disable and
install scripts do **not** create that complete backup for you. Read a contributed
list before applying it: firmware, apps and the features you want to keep can differ.

## How it works

Android TVs have the same debug bridge as Android phones. Once you switch on USB debugging in the
TV's developer options, the TV listens on your Wi-Fi and asks you once to allow your computer.
After that, everything is standard Android tooling:

1. **Back up** the list of installed packages and settings.
2. **Disable** the manufacturer's telemetry, ad services, content rows and preloaded junk with
   `pm disable-user`. Nothing is uninstalled; the restore helper re-enables listed packages.
   See [restore scope](#restore-your-tv) for changes it does not roll back.
3. **Install** a small native launcher and make it the default home screen.

The bloat differs by brand, which is why the prompt asks your coding assistant to discover it
on your TV and ask you before touching anything you might use.

## Quick start

You need a computer on the same Wi-Fi as the TV and a coding assistant with shell access
(Claude Code, or anything similar).

**1. On the TV**
Settings → Device Preferences → About → press OK on **Build** seven times → go back →
Developer options → turn on **USB debugging**. Some TVs also show a Network or ADB debugging
toggle; turn it on too. Keep the TV switched on.

**2. On your computer**
Open your assistant, paste the prompt from [`PROMPT.md`](PROMPT.md), and fill in your TV model.

**3. Follow along**
Accept the "Allow USB debugging?" prompt when it appears on the TV, then answer the checklist
questions: which streaming apps you use, which four go in the dock, whether to keep the voice
assistant, Chromecast and firmware updates.

That's it. The assistant backs up, debloats, builds and installs the launcher, and checks every
step with screenshots pulled from the TV. On a 1 GB Mi TV the whole thing took one afternoon the
first time; with this repo as context, people report about fifteen minutes.

## What you get

| Experience | What you get |
| --- | --- |
| **A quieter home** | Pitch-black background with four favorite apps in a rounded dock at the bottom, inspired by Apple TV |
| **Focus that follows you** | A top shelf with the focused app's name and artwork-derived ambient color; a floating glass highlight springs between tiles, lifting the selected tile and showing its name |
| **Your full app library** | A grid that slides up when you press Down |
| **Everyday controls** | Frosted Inputs, Wi-Fi and Settings buttons; Inputs lists HDMI, AV and Live TV, and Wi-Fi shows the connected network |
| **Clock and greeting** | Large thin clock, date and greeting in [Outfit](https://fonts.google.com/specimen/Outfit) |
| **Optional screensaver** | A matching Clock display that moves around while idle to reduce static positioning; [setup below](#the-launcher) |
| **Remote input support** | Optional accessibility service maps `KEYCODE_TV` to the system input switcher from any app; install with `INPUT_KEY_SERVICE=1` |

The new home screen has no sponsored recommendation rows or "Discover" feed.
Which manufacturer services stop running depends on the package list you apply;
this is not a guarantee that every installed app stops collecting analytics.

## Supported TVs

Designed for **Android TV** and **Google TV**: Xiaomi and Redmi, TCL, Hisense, Sony, OnePlus,
Realme, Nvidia Shield and others. The included launcher requires **Android 8.0 / API 26
or newer** (`minSdk 26`). ADB access and launcher replacement depend on the device firmware;
the model-specific reports below are a better starting point than a brand-wide guarantee.

Not supported: Samsung (Tizen) and LG (webOS), which run different systems. Amazon Fire TV
(Fire OS) takes the debloat half only: Fire OS won't let a third-party launcher take over Home,
so the Fire TV lists below are about removing Amazon's ads and telemetry, not the home screen.

Lists that people have tested and contributed live in [`examples/`](examples/):

| TV | OS |
|---|---|
| BenQ E55-735 (Changhong AI PONT, DVB96BQ) | Google TV 14 |
| CHiQ AI PONT (DVB93CQ) | Android TV 11 |
| Hisense SmartTV 4K FFM (huangshan, MediaTek) | Android TV 10 |
| Insignia 55" F30 4K Fire TV (NS-55F301NA25, AFTDCT31, duckie) | Fire OS 7 / Android 9 |
| NVIDIA SHIELD TV Pro 2019 (P2897, mdarcy) | Android TV 11 |
| OnePlus TV (D00, Dubai) | Google TV 14 |
| RCA AI PONT SA (Changhong, redwood, ISDB92RC) | Android 14 |
| SANYO ATV R2 (SCBC, Realtek RTD285O) | Android TV 9 |
| Sony BRAVIA 4K UR2 (BRAVIA_UR2_4K, MediaTek) | Android TV 10 |
| Sony BRAVIA XBR-65X900E (2017, BRAVIA_ATV2) | Android TV 9 |
| TCL 55" 4K Google TV, 2023 (G10, G10_4K_US_NF) | Google TV 12 |
| TCL 65C725 (Smart TV Pro, BeyondTV4) | Android TV 11 |
| TCL BeyondTV (India) | Android TV 9 |
| TCL QM6K Pro, 2025 (G08, MediaTek tcl9618) | Google TV 14 |
| Xiaomi Mi Box S (MIBOX4, oneday), streaming box | Android TV 9 |
| Xiaomi Mi TV (MiTV-MOOQ0, croods) | Android TV 10 |
| Xiaomi Mi TV 4A (MiTV-MOOQ1) | Android TV 10 |
| Xiaomi MiTV-AESP0 stick (aquaman, 1 GB) | Android TV 9 |
| Xiaomi MiTV-AXSO0 (dangal) | Android TV 9 |
| Xiaomi MiTV-MOOR2 (venom, MediaTek) | Google TV 11 |
| Xiaomi TV A Pro 75" 2026 (MiTV-MZTU0, river, MediaTek) | Google TV 14 |

Your TV not here? The prompt guides discovery of its packages; compatibility still needs to be checked on that device. If you add your
list afterwards, the next person with your model skips that step. See
[Contributing](#contributing).

## Doing it by hand

Everything the assistant does, you can run yourself. The Python tools need Python 3 and the dependencies below. Building the launcher also needs the [Android toolchain](#the-launcher).

```bash
python3 -m venv tools/venv && tools/venv/bin/pip install -r tools/requirements.txt
export TV_HOST=192.168.1.50                                   # your TV's IP address

tools/venv/bin/python -I tools/tv.py --probe                 # model, Android version, firmware
tools/venv/bin/python -I tools/tv.py 'pm list packages'      # run one shell command on the TV
printf 'cmd1\ncmd2\n' | tools/venv/bin/python -I tools/tv.py --batch   # several, one connection

tools/disable.sh examples/xiaomi-mi-tv-4a/debloat.txt        # apply a debloat list
tools/restore.sh examples/xiaomi-mi-tv-4a/debloat.txt        # undo it and restore the stock launcher
```

| Tool | What it does |
|---|---|
| [`tools/tv.py`](tools/tv.py) | A pure-Python ADB client: shell, batch, push, pull. Works where the stock `adb` binary is blocked. |
| [`tools/b64push.py`](tools/b64push.py) | Uploads larger files such as APKs as base64 chunks and verifies the checksum. |
| [`tools/disable.sh`](tools/disable.sh) / [`tools/restore.sh`](tools/restore.sh) | Apply or undo a debloat list. PowerShell versions sit next to them. |

### Windows

The PowerShell scripts work without WSL or Git Bash. With Android Studio installed nothing else
is needed: the install script uses Android Studio's bundled JDK and the default SDK location.

```powershell
python -m venv tools\venv; tools\venv\Scripts\python.exe -m pip install -r tools\requirements.txt
powershell -ExecutionPolicy Bypass -File launcher\build-and-install.ps1 -TvHost 192.168.1.50
powershell -ExecutionPolicy Bypass -File tools\disable.ps1 -TvHost 192.168.1.50 examples\xiaomi-mi-tv-4a\debloat.txt
powershell -ExecutionPolicy Bypass -File tools\restore.ps1 -TvHost 192.168.1.50 examples\xiaomi-mi-tv-4a\debloat.txt
```

The install script stops before touching the stock launcher if the install or the home-screen
switch fails. Git Bash also works; the `.sh` scripts detect a Windows venv and disable MSYS path
rewriting themselves.

## The launcher

A small native Android TV app (`com.gathin.tvhome`, about 250 KB installed), written in plain Java with
AndroidX RecyclerView and nothing heavier. It is built for sets with 1 GB of RAM: no blur, no
video backgrounds or Internet permission. Optional accessibility services handle remote
buttons, and a dream service provides the screensaver.

**Configure** it in [`launcher/app/src/main/res/values/config.xml`](launcher/app/src/main/res/values/config.xml):
the dock order, apps to hide, your TV's Live TV package, the number of columns, and how many grid
rows stay visible under the dock (`rows_below_dock`). The Inputs button hides itself on sticks and
boxes that have no HDMI or tuner.

**Language** follows the TV: English, German, Spanish, Catalan and Simplified Chinese so far. If your
TV doesn't offer yours system-wide, Android 13 and later can set it for the launcher alone, e.g. Catalan:
`tools/tv.py 'cmd locale set-app-locales com.gathin.tvhome --locales ca'`.

**Build and install** with one command:

```bash
TV_HOST=192.168.1.50 launcher/build-and-install.sh
```

It needs JDK 17 and the Android SDK (platform 34, build-tools 34.0.0). On macOS:

```bash
brew install openjdk@17 android-commandlinetools
sdkmanager "platforms;android-34" "build-tools;34.0.0"
```

Without Homebrew, the script also finds a JDK 17 known to `/usr/libexec/java_home` and an SDK in
`~/Library/Android/sdk`, Android Studio's default. The script installs a shrunk release build
(about 250 KB instead of 1.6 MB, signed with the debug key so it installs over an older build)
and asks the TV to precompile it, so the first launch doesn't wait for the JIT.

**Navigation** is deliberate: Up and Down move exactly one row, Up from the dock goes to the top
buttons, the ends of the button row don't fall through to tiles, and the last focused app is
remembered when you come back, even after the app list changes.

<details>
<summary><strong>Optional: the Clock screensaver</strong></summary>

A large thin clock and the date over an ambient glow, in the launcher's style. Every minute the
clock drifts to a new spot and the glow shifts colour. The system runs it only while the TV is
idle. Turn it on over ADB, or pick "Clock" in the TV's screensaver settings:

```bash
tools/venv/bin/python -I tools/tv.py 'settings put secure screensaver_components com.gathin.tvhome/.ClockDream; settings put secure screensaver_enabled 1'
```

It starts after the TV's "screen off" idle time (`settings get system screen_off_timeout`).
</details>

### Changing the default home from the TV

Optional: set `show_home_settings_shortcut` to `true` in `config.xml` and a **Default home** tile
appears at the end of the grid, opening Android's own home-screen picker so you can switch
launchers from the sofa. Most Android TV firmware has no such page, and then the tile stays
hidden. The stock launcher has to be enabled before it can be picked, see the restore instructions.

## Restore your TV

Keep the **same debloat list** you applied and the **original launcher package name**.
For Bash, the helper's optional second argument is the stock launcher package; if
omitted, it defaults to `com.google.android.tvlauncher`:

```bash
# Example for a Google TV device; use the stock package recorded for YOUR TV.
TV_HOST=192.168.1.50 tools/restore.sh examples/xiaomi-mi-tv-4a/debloat.txt com.google.android.apps.tv.launcherx
```

The example list is only appropriate if it is the list you actually applied.
For Windows, use the existing [PowerShell restore example](#windows) and pass
`-Stock` with your original package if it differs from the default.

| The helper does | It does not |
|---|---|
| Re-enable packages in the supplied list | Reconstruct their original enabled/disabled state |
| Re-enable and select the supplied stock launcher | Automatically discover the original launcher for restore |
| Set three animation scales to `1.0` | Restore an arbitrary settings backup or original custom animation values |

It leaves the custom launcher installed and does not undo optional accessibility
or screensaver configuration. Review those settings separately if you enabled them.
If the TV remains reachable, verify Home, inputs and the apps you use after restoring.
The [troubleshooting notes](#troubleshooting) cover known launcher and connectivity issues.

## Troubleshooting

Things that bit us or contributors, so they don't bite you.

**Connecting**

- **macOS: `adb connect` says "No route to host"** although the port is open. Recent macOS treats
  the Google-signed `adb` as its own app under Local Network privacy and silently blocks it.
  `tools/tv.py` avoids the binary entirely.
- **No "wireless debugging" toggle** on some TVs, Xiaomi among them. USB debugging alone opens
  network ADB on port 5555.
- **USB devices go dead while USB debugging is on**, seen on the Xiaomi Mi Box S: its firmware
  switches the USB port to ADB device mode, which cuts the power to a USB speaker or drive. Switch
  USB debugging off when you're done; the launcher doesn't need it.
- **Windows PowerShell 5.1 adds a byte-order mark** to text piped into a program, which turned the
  first batch command into gibberish. `tv.py` now strips it, and the `.ps1` scripts pipe clean UTF-8.
- **Git Bash rewrites paths** like `/data/local/tmp` into Windows paths. The scripts set
  `MSYS_NO_PATHCONV=1` for you. Set `JAVA_HOME` and `ANDROID_HOME` before building there.

**Working on the TV**

- **Chained `pm` commands** (`pm disable a; pm disable b`) may silently stop after the first. Run
  one per shell call; `--batch` does exactly that.
- **Large pushes hang** through the Python client. Use `tools/b64push.py` for APKs.
- **Screenshots come out empty** while Live TV or an HDMI input is on screen, seen on TCL. Press
  Home before `screencap`.
- **Disable the stock launcher only after the new one is installed**, or the Home button lands on
  a factory test app.
- **The new launcher still doesn't own Home** after the stock one is disabled, seen on Google TV
  (Xiaomi MiTV-MOOR2). Another system app answers HOME: `com.google.android.tungsten.setupwraith`,
  with `.MainActivity` at intent-filter priority 4 and `.RecoveryActivity` at priority 1. You
  cannot outrank it, and the obvious workarounds don't help: Android ignores `android:priority` on
  HOME filters for apps that aren't on the system image, `cmd package set-home-activity` prints
  `Success` without changing anything (not even after a reboot), and `pm disable-user` on one of
  setupwraith's components is refused with a SecurityException. Disabling the whole
  `com.google.android.tungsten.setupwraith` package is what frees the slot. It is the first-run
  setup wizard, so everyday use is unaffected, but re-enable it before a factory reset - list it in
  your `debloat.txt` and `tools/restore.sh` does that for you.
- **The TV drops off Wi-Fi every minute right after the install**, seen on Google TV 14 (Xiaomi
  MiTV-MZTU0): the same setupwraith `.RecoveryActivity` took Home and system_server crash-looped
  on it. The install script now starts the new launcher before disabling the stock one, sets the
  home preference last (Android drops it whenever the set of HOME apps changes), then checks where
  Home resolves and re-enables the stock launcher if it is not the new one. Disable setupwraith as
  above. If you're already in the loop:
  `tools/tv.py 'pm enable --user 0 com.google.android.apps.tv.launcherx'`.
- **No on-screen keyboard** after switching launchers, seen on Xiaomi: the default input method
  pointed at a Baidu keyboard that isn't installed. Fix:
  `tools/tv.py 'ime set com.google.android.inputmethod.latin/com.android.inputmethod.latin.LatinIME'`
- **Third-party launchers** often charge for basics like a custom wallpaper. That's why this repo
  has its own.
- **Wrong time zone, clock off by 30 or 60 minutes**, seen on Xiaomi in Iran: the TV picked a
  neighbouring zone, and older firmware ships time zone data from before Iran dropped daylight
  saving in 2022 (on Android 10 and newer, check with
  `tools/tv.py 'cat /apex/com.android.tzdata/etc/tz/tz_version'`). Set the zone yourself:
  `tools/tv.py 'settings put global auto_time_zone 0; cmd alarm set-timezone Asia/Tehran'`.
  If the clock then jumps an hour ahead in spring, a fixed offset keeps it right:
  `tools/tv.py 'cmd alarm set-timezone GMT+03:30'`. The same applies to any country whose rules
  changed after the firmware was built.

## Questions people ask

**Is this safe? Can it brick the TV?**
The tools do not flash firmware, root the TV or uninstall system packages. They disable
packages for a user profile, but disabling the wrong service can still interrupt Home,
inputs or connectivity. Keep the original inventory and a device-specific recovery path.
[`tools/restore.sh`](tools/restore.sh) re-enables the supplied list and stock launcher;
its [scope is described above](#restore-your-tv). Where the firmware exposes the option,
you can also re-enable apps from the TV's settings.

**What about firmware updates?**
Most lists disable the manufacturer's update service, so updates only arrive when you re-enable
it on purpose. After an update, re-check the package list and Home behavior before reapplying changes;
firmware can re-enable packages or change which components the TV needs.

**Won't the apps break as their APIs change?**
The streaming apps still update through the Play Store, which is left alone. The launcher
doesn't talk to them; it lists what's installed and launches it, using Android launcher intents
that have been stable for over a decade.

**How did it get access? Isn't that a security hole?**
Through the Android Debug Bridge, with one explicit "allow this computer" tap on the TV, the same
as developing an Android app. The TV only trusts the key you approved. You can switch USB
debugging off again afterwards and the launcher keeps working.

**Can I just download the launcher APK?**
Build it yourself with the one-line script above; it takes about a minute and you control what
goes on your TV. We don't publish prebuilt binaries.

## Project story

The original author describes the project's beginning:

> It started as an afternoon fixing my parents' Mi TV, which showed them an advert before they
> could reach Netflix. The whole thing was done with Claude Code talking to the real TV, and the
> prompt that did it is in this repo so you can do the same with yours. The replies to the
> [tweet below](https://x.com/gathint/status/2111379569030939102) are where most of the TV lists in
> this repo came from.

<p align="center">
  <a href="https://x.com/gathint/status/2111379569030939102"><img src="docs/tweet.png" alt="The tweet: Vibe coded my parents' TV this afternoon. Before and after screenshots." width="380"></a>
</p>


## Contributing

For fixes intended for everyone, check [upstream's issues](https://github.com/Gathin23/tv-vibehack/issues)
and follow the existing [contribution guide](CONTRIBUTING.md). For changes specific
to this fork, use [ESS issues](https://github.com/essremodel/tv-vibehack/issues) or a
pull request here. Keep upstream authorship and licensing intact.

The most useful contribution is your TV's debloat list, so the next person with that model
doesn't have to rediscover it. Launcher improvements and sharper prompt wording are welcome too.
See [CONTRIBUTING.md](CONTRIBUTING.md) for the format and the testing we ask for.

## Licence

MIT, see [`LICENSE`](LICENSE). The Outfit typeface is under the SIL Open Font License, see
[`launcher/FONT-LICENSE-OFL.txt`](launcher/FONT-LICENSE-OFL.txt).

<sub>Original project: [Gathin23/tv-vibehack](https://github.com/Gathin23/tv-vibehack) · [Artwork provenance](assets/README.md)</sub>
