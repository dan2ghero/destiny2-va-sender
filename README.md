# destiny2-va-sender v1.1.4

**Supported OS: Windows 10 22H2 / Windows 11 (x64).**


Send Destiny 2 video and process-tree audio over SRT or RTMP.

## Use cases

Send a player's game video and audio to a video server operated by an event production team, for events such as Destiny 2 raid races and PvP tournaments. The team can use the incoming streams for monitoring, switching between player perspectives, and event broadcasts.

## Quick start

1. Extract the entire ZIP to a writable folder.
2. Double-click destiny2-va-sender.exe.
3. Start Destiny 2 and keep its game window visible.
4. Open SETTING, choose your options and press SAVE.
5. Enter the destination URL in OUTPUT and press START. Press STOP to end the session.
6. Use TEST for a 20-second local preview in ffplay. Startup and buffering add a little time. TEST does not publish to the destination server.

On first launch, the app downloads the pinned official BtbN FFmpeg build (about 211 MB) and verifies its SHA-256.

## Audio / video sync

SETTING → AUDIO SYNC (ms) adjusts audio relative to video. Default: 0. Range: -2000 to +2000 ms.

- If audio is about 0.2 seconds late relative to video, try **-200 ms**.
- Negative values advance audio by trimming that duration from the beginning and rebasing its timestamps.
- Positive values delay audio by adding silence at the beginning.
- Use small changes and compare a visible action with its sound. Test the actual receiving player, too.

This corrects relative A/V alignment. It does not remove network, server or player latency when comparing the game PC's direct sound with the received stream. The raw audio input is already initialized before the video filter, so swapping the order alone is not a demonstrated fix for a 200 ms offset.

Audio sync is applied in normal and CUSTOM modes and is saved with your settings.

## Language

English (ENG) is the default. In SETTING, select LANGUAGE → ENG or JPN, then SAVE. The settings dialog changes language immediately; SAVE applies it to the main window and stores it in settings.json for the next launch. CANCEL leaves the saved language unchanged.

The main window, settings, CUSTOM dialog, app status messages, validation errors and initial setup progress support both languages. Protocol names, codec names, encoder arguments and raw external-tool / Windows diagnostics retain their original text. The app does not translate raw FFmpeg logs.

Existing settings without a Language field open in English. Existing encoding settings are preserved. The destination URL is not saved.

## Encoding settings

| Resolution | 30 fps | 60 fps |
|---|---:|---:|
| 960x540 | CBR 3 Mbps | CBR 4 Mbps |
| 1280x720 | CBR 4.5 Mbps | CBR 6 Mbps |
| 1920x1080 | CBR 6 Mbps | CBR 8 Mbps |

Keyframe interval: 1 second. B-frames: 0. Audio: 128 kbps.
Default: SRT / 1280x720 / 30 fps / NVEnc P3 / Opus.

- SRT: MPEG-TS, AAC or Opus. Use an srt:// URL.
- RTMP: FLV, AAC. Use an rtmp:// or rtmps:// URL. RTMP TEST also uses FLV.
- CROP: maintain aspect ratio, then crop the center.
- COMPRESS: stretch to the selected 16:9 resolution.
- LETTER-BOX: fit the whole image and fill unused space with black.
- CPU: libx264, veryfast, zerolatency.
- NVEnc: h264_nvenc, P3, ll, zerolatency.
- AMD: h264_amf, ultralowlatency, speed.

CUSTOM accepts output encoding options as -option value pairs. The app manages inputs, URL, mapping, filters, output format and preview duration. RTMP forces AAC / 128 kbps / 48 kHz / stereo without duplicate audio options and removes Opus-only settings.

## Requirements and limitations

- Windows 11 x64 with .NET Framework 4.8 (included with Windows 11). No .NET 8 Runtime installation is required.
- GPU encoding requires compatible NVIDIA or AMD hardware and drivers.
- Pinned FFmpeg: BtbN N-124714-g49a77d37be-20260531.
- Captures destiny2.exe and its descendant processes. It does not mix the whole desktop or microphone audio.
- STOP closes only the helper processes started by this app.
- End-to-end latency under 0.5 seconds is not guaranteed or measured for this release.

## License

App license: The Unlicense (see LICENSE). Attribution is not required for the app. Use, modification, reverse engineering, forks, commercial use and redistribution are permitted. Third-party programs retain their own licenses; see licenses/THIRD-PARTY.md.

## Source and build

All application source is under src. To rebuild on Windows with .NET SDK 8 and .NET Framework 4.8, run:

    powershell -ExecutionPolicy Bypass -File .\build.ps1

No NuGet package restore is required. The execution policy option applies only to that PowerShell process.

See README.ja.md for Japanese instructions.

## Confirmed environments

User-reported successful operation:

| OS | GPU |
|---|---|
| Windows 10 22H2 / 19045.6466 | RTX 2060 6GB |
| Windows 11 Home 25H2 / 26200.9711 | RTX 3060 12GB |

These are confirmed examples, not a guarantee for every hardware, driver, or application combination. No additional virtual audio driver is required.

## v1.1.4

Automatic audio/video startup synchronization is enabled. SESSION LOG is displayed in memory only; no diagnostic logs or timing CSV files are saved. A small temporary synchronization timestamp file is removed when the session is disposed. Settings are still saved locally.

The bundled synchronization FFmpeg engine has corresponding source in **ffmpeg-autosync1-source.zip**, provided with this release. Publish that archive alongside the public ZIP. The first-launch download provides the additional FFmpeg/ffplay tools.
