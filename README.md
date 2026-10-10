# AniTube Player

AniTube is an offline video player for Android, built for anime and local media. It is built on Media3 / ExoPlayer, and uses libass for full ASS/SSA subtitle support. A library screen (folders, search, favourites) sits in front of the player.

| | |
| :-- | :-- |
| Min SDK | 26 (Android 8.0) |
| Target / Compile SDK | 34 / 35 |
| Language | Kotlin 2.2.0, Java 17 |
| Playback engine | Media3 / ExoPlayer 1.8.0 |
| ASS/SSA rendering | libass via `io.github.peerless2012:ass-media` 0.5.1 |
| Build | Android Gradle Plugin 8.5.2, Gradle 8.7 |

---

## Features at a glance

- Plays local videos with hardware or software decoding
- Gesture controls: seek, volume, brightness, double-tap skip, press-and-hold 2x speed, pinch to zoom
- Full subtitle support: SRT, VTT, ASS, SSA, TTML, with subtitle delay, folder picker, and styling
- Animated ASS rendering (karaoke, moving text) through libass
- Audio track selection and audio delay
- Custom fonts with a live preview, including whole font folders
- Display tuning (brightness, contrast, saturation) with presets
- Playlist queue, auto-play next, A-B repeat, sleep timer, picture-in-picture, screenshot
- Resume where you left off, with per-video saved speed and subtitles
- In-app updates from GitHub Releases

---

## Player controls

### Gestures

| Gesture | Action |
| :-- | :-- |
| Double-tap in the middle | Play / pause (with an animated symbol) |
| Double-tap left / right | Seek -10 s / +10 s. Repeated taps accumulate (+10, +20, +30), and single taps keep skipping afterwards |
| Press and hold | Temporary 2x speed, released on lift |
| Swipe horizontally | Seek, with the target time shown |
| Swipe vertically | Brightness (left side) and volume (right side), with a level bar |
| Pinch | Zoom in to fill the screen, pinch out to fit |
| Seekbar drag | Thumbnail preview of the position |

The last brightness level is remembered across videos and app restarts.

### On-screen controls

- Play / pause, previous / next, -10 s / +10 s buttons
- Skip button (default +85 s, adjustable from 10 to 180 s in Playback settings)
- Lock button hides all controls and ignores touches until unlocked
- CC button opens the subtitle panel
- Back button closes an open side panel first, then exits

### Left shortcut rail

A vertical rail on the left side with: Mute, Sleep timer, Audio, Rotate, Lock, Picture-in-picture. It can be collapsed with the chevron, or hidden completely with the "Shortcuts" switch in the 3-dot menu.

### 3-dot menu (right side panel)

An icon grid with:

| | | | |
| :-- | :-- | :-- | :-- |
| Playing Queue | Aspect Ratio | Display Settings | Subtitle |
| Audio | Speed | Playback | Information |
| Share | Screenshot | Picture in Picture | Quit |

Plus switches for **Subtitles**, **Shortcuts**, **Auto-play next**, and **Animated ASS (libass)**.

### Orientation

Orientation follows the video: vertical videos open in portrait, horizontal videos in landscape, including on the next episode. Pressing Rotate switches to manual rotation for that video.

### Aspect ratio

Cycles through Fit, Fill, and Zoom.

### Speed

0.5x, 0.75x, 1x, 1.25x, 1.5x, 2x. The speed is saved per video.

### Playback settings

- Auto-play next on / off
- Sleep timer
- A-B repeat (Set A, Set B, Clear)
- Skip button length
- Playing queue / playlist

### Display settings

Brightness, contrast, and saturation sliders, with presets: Normal, Vivid, Anime, Cinema.

### Other

- Screenshot saves to Pictures/AniTube
- Picture-in-picture
- Information shows size, duration, resolution, codec, fps, bitrate, and audio tracks
- Share sends the current video to other apps
- Playback position is saved every 10 seconds, so crashes or app kills resume cleanly
- Friendly error messages when a video cannot be decoded

---

## Audio

- **Audio track** selection for videos with multiple tracks. The chosen language is remembered and applied automatically on the next episodes.
- **Audio delay** in both directions (+ makes audio later, - makes it earlier), set per video for the current session. It works on decoded PCM audio. It does not work on passthrough audio (direct AC3 / DTS output).
- **Decoder switch** between hardware and software decoding. If hardware decoding fails, the player falls back to software automatically.

---

## Subtitles

### Loading subtitles

- **Open**: pick a single subtitle file.
- **Subtitle folder**: pick a whole folder once, and the app remembers it. Its subtitle files are then listed so you can pick the one for the current video. A file whose name matches the video appears first, marked with a star. "Change Folder" selects a different folder.
- **Track**: switch between embedded tracks and the added external subtitle, or choose None.
- An added subtitle is saved for that video, so it does not have to be added again. "Remove added subtitle" clears it.
- Supported formats: SRT, VTT, ASS, SSA, TTML. Invalid or unreadable files show a clear message instead of failing silently.

### Controls

- **Subtitles on / off** from the CC button, the 3-dot menu, or the subtitle settings.
- **Subtitle delay**, per video. On an external file it works in both directions, because the file's timestamps are re-timed. On an embedded track it works as a positive (later) delay only.
- The preferred subtitle language is remembered across episodes.

### Styling (SRT / VTT)

- Presets: Classic, Yellow, Box, Big
- Size, position, and opacity sliders
- Colour swatches and a background box switch
- Font selection (see below)

### Fonts

- Default, system fonts (Roboto Medium, Roboto Condensed, Serif, Monospace, Casual, Cursive)
- Any `.ttf` / `.otf` placed in `app/src/main/assets/fonts/` appears in the list automatically
- **Load Font File** for a single TTF / OTF / TTC
- **Select Font Folder** for a whole folder. Every font is listed with a live preview, rendered in that font, so you can see how it looks before choosing it.

A small corner badge shows the active subtitle and font when they change.

---

## ASS / SSA subtitles with libass

ASS/SSA subtitles, both embedded in MKV files and external `.ass` / `.ssa` files, are rendered by libass.

| Mode | How it works | Best for |
| :-- | :-- | :-- |
| **CUES** (default) | libass renders static frames that flow through the normal subtitle pipeline | Everyday use. Delay, on / off, and track selection all work as usual |
| **Animated ASS** | Overlay rendering on an OpenGL thread | Karaoke, moving text, and other animated typesetting |

Switch with the "Animated ASS (libass)" switch in the 3-dot menu. The player restarts at the same position when the mode changes.

**Limits**

- ASS tracks use their own style, font, and position, so the size, colour, opacity, and font settings apply to SRT / VTT only.
- In Animated mode, subtitle delay does not apply to embedded ASS tracks. It still works on external files.
- How fonts that are not embedded in the MKV are displayed (especially Devanagari and CJK) depends on the device, so test on your phone.

---

## Library screen

- Folders and videos in list or grid view, with Dark, Light, or system theme
- Pull to refresh to rescan new or deleted files
- Folder sorting (Name, Most videos, Newest), and hide / unhide folders with a long-press
- Continue Watching, Favourites (pinned), NEW badge, duration and size labels, fast thumbnails
- Search across all videos by name
- Multiselect with long-press: Share, Delete (system confirmation on Android 11+), More
- More menu: Rename, Favourite, Mark as watched, Reset progress, Properties

---

## Building

### Android Studio

1. File > Open > the `AniTube` folder, and let Gradle sync.
2. Connect a phone and press Run.
3. Release APK: Build > Generate Signed Bundle / APK.

### GitHub Actions (build from a phone)

1. Create `.github/workflows/build.yml` in the repository.
2. Upload `source.zip` to the repository (replace it when the code changes).
3. Add the secrets `KS_PASS` and `KS_B64` under Settings > Secrets and variables > Actions.
4. Run "Build and Release APK" from the Actions tab with a version and a changelog.
5. The APK and `version.json` appear in Releases after a few minutes.

Keep a backup of `anitube-release.jks` and its password. The first install over an older debug-signed build requires uninstalling the old app.

### Version requirements

- Keep `ass-media` and Media3 on matching versions. `ass-media` 0.5.1 is built against Media3 1.8.0.
- libass is compiled with Kotlin 2.2, so the project uses the Kotlin 2.2 plugin.
- `android.suppressUnsupportedCompileSdk=35` in `gradle.properties` allows compileSdk 35 with AGP 8.5.2.
- Release builds use R8. libass keep rules are in `app/proguard-rules.pro`.

---

## Auto-update

On launch, the app checks `version.json` from the latest GitHub Release and verifies the APK's SHA-256 before installing. Generate the file with:

```
tools/make-version-json.sh <versionCode> <versionName> <apkFile> "<changelog>"
```

`tools/make-keystore.sh` creates the release keystore. `keystore.properties` and `*.jks` are excluded from git.

---

## Project structure

```
AniTube/
├─ app/src/main/java/com/anitube/player/
│  ├─ MainActivity.kt           Library screen
│  ├─ PlayerActivity.kt         Player, gestures, panels, subtitles, fonts, libass
│  ├─ AudioDelayProcessor.kt    Audio delay processor and renderers factory
│  ├─ History.kt                Resume and watched state
│  ├─ Updater.kt                In-app update
│  └─ Util.kt
├─ app/src/main/assets/fonts/   Bundled fonts (Poppins, SIL Open Font License)
├─ app/proguard-rules.pro       R8 keep rules for libass
└─ tools/                       version.json and keystore scripts
```

---

## Credits

- [Media3 / ExoPlayer](https://github.com/androidx/media)
- [libass](https://github.com/libass/libass) and [libass-android](https://github.com/peerless2012/libass-android) (MIT)
- Poppins font, SIL Open Font License
