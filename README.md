<div align="center">

<img src="macos/JenkinsStatus/Assets.xcassets/AppIcon.appiconset/Icon256.png" width="120" alt="EKJenkins Status">

# EKJenkins Status

**Your Jenkins builds, in the macOS menu bar.**

A tiny, native, **read-only** Jenkins monitor. See what's running, what broke, and
which pipeline stage is grinding — without keeping a browser tab open.

[![platform](https://img.shields.io/badge/platform-macOS%2012%2B-000?logo=apple)](#install)
[![arch](https://img.shields.io/badge/arch-Apple%20Silicon-000?logo=apple)](#install)
[![built with](https://img.shields.io/badge/built%20with-C%23%20%2B%20AppKit-512BD4?logo=dotnet)](#why-its-native)
[![license](https://img.shields.io/badge/license-PolyForm%20Noncommercial-blue)](LICENSE)

<img src="docs/screenshots/watched.png" width="470" alt="Watched builds">

</div>

---

## What it does

- **Watches your builds.** Every running build on the instance that's *yours* —
  you started it, you opened the PR, or you authored a commit in it.
- **Groups projects into tabs.** Point it at a folder and get a tab listing every
  branch and PR in it, with the latest build status for each.
- **Expands pipeline stages** inline, so you can see it's stuck on *Integration*
  without opening Jenkins.
- **Pins what matters** to the top of the list, and drives the menu bar icon from it.
- **Notifies you** when a job you rang the bell on finishes — nothing else nags you.
  Uses Notification Center where macOS allows it, and falls back to its own
  floating panel on unsigned builds (see [Notifications](#notifications)).
- **Read-only by design.** It only ever issues GETs. It cannot trigger, abort, or
  change anything on your Jenkins.

<table>
<tr>
<td width="50%" align="center">
  <img src="docs/screenshots/stages.png" alt="Pipeline stages expanded">
  <br><em>Pipeline stages, inline</em>
</td>
<td width="50%" align="center">
  <img src="docs/screenshots/project.png" alt="A project tab">
  <br><em>A project tab: every branch and PR</em>
</td>
</tr>
</table>

> Screenshots use synthetic demo data (`--demo`), not a real server.

---

## Install

Grab the latest **`.dmg`** from [Releases](https://github.com/enizvk/EkJenkinsStatus/releases), drag the app to
`/Applications`, then run this once — the build isn't notarised by Apple, so macOS
quarantines it:

```bash
xattr -dr com.apple.quarantine "/Applications/EKJenkins Status.app"
```

Launch it. There's **no Dock icon** — look for the pipeline glyph in the menu bar.
Open **Settings** and fill in:

| Field | Example |
|---|---|
| Jenkins URL | `https://jenkins.example.com/jenkins` |
| Username / email | `jdoe@example.com` |
| API token | from *Jenkins → your profile → Security → API Token* |

Anonymous-read instances work too — leave the username and token empty.
**Test connection** tells you who you're authenticated as.

Requires macOS 12+ on Apple Silicon. Nothing else: the .NET runtime is bundled, so
there's no SDK to install and no dependencies to manage.

---

## Using it

| Action | How |
|---|---|
| Open the list | Click the menu bar icon |
| About / Refresh / Clear notifications / Settings / Quit | **Right-click** the icon |
| Open a build in the browser | Double-click a row |
| Show pipeline stages | The circled chevron on a row |
| Notify me when this finishes | The 🔔 bell on a row |
| Watch a job (keeps it on top) | The 👁 eye on a row |
| Copy links, go to PR, jump to test results | **Right-click** a row |
| Only my builds | The **Mine** switch |
| Resize | Drag the bottom-right corner |

Your API token is stored in the **macOS Keychain**, never on disk. Everything else
lives in `~/Library/Application Support/JenkinsStatus/settings.json`.

---

## Notifications

Click the 🔔 on a row and you get told when that build finishes — click the
notification to open the build.

macOS only grants Notification Center access to apps with a stable code-signing
identity. This build is ad-hoc signed, so the system refuses with *"Notifications
are not allowed for this application"* and the app falls back to drawing its own
floating panel in the top-right corner. Same information, no permission needed.
How long it stays up is configurable in Settings (3–30 seconds, or until clicked).
Sign with a Developer ID and it uses real Notification Center banners instead —
no code change required.

---

## Why it's native

No Electron, no webview, no browser extension: a single self-contained ~24 MB app
written in **C# against AppKit** (`net10.0-macos`). Real `NSStatusItem`,
`NSPopover`, and `NSTableView`, so it behaves the way a Mac app should — the menu
bar glyph is a template image that macOS tints for light and dark mode, and the
whole thing idles at essentially zero CPU between polls.

It also ships an in-app updater that checks this repo's GitHub Releases, so you
get new versions without hunting for a download.

---

## Contributing & building

See **[docs/DEVELOPING.md](docs/DEVELOPING.md)** for build commands, the release
workflow, the headless verification flags, and notes on the AppKit gotchas this
codebase has already hit.

```bash
git clone https://github.com/enizvk/EkJenkinsStatus.git
cd EkJenkinsStatus
dotnet workload install macos     # one-time, needs Xcode
cd macos/JenkinsStatus && dotnet build
```

---

## License

[PolyForm Noncommercial 1.0.0](LICENSE) — free to use, modify, and share for
**noncommercial** purposes (personal, research, teaching, and charitable,
educational, or government organisations).

**Commercial use requires a separate licence — contact the author.** This app is
not distributed or monetised through the Apple App Store.

Provided "as is", without warranty of any kind. Not affiliated with or endorsed by
the Jenkins project or Apple.
