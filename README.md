# Teams Downloader

A Chrome extension that downloads Microsoft Teams, SharePoint and Stream meeting recordings and transcripts, including from pages where the built-in download button is disabled. Everything runs in the browser.

![Manifest V3](https://img.shields.io/badge/Manifest-V3-4285F4?logo=googlechrome&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-Vanilla-F7DF1E?logo=javascript&logoColor=black)
![Version](https://img.shields.io/badge/Version-1.6.1-informational)
![License](https://img.shields.io/badge/License-MIT-blue)

---

## Features

**Transcripts**
- Three formats: **Grouped** (`.txt`, speaker turns merged for easy reading), **VTT** (`.vtt`, standard subtitles) and **JSON** (`.json`, raw data).
- **Portion picker:** export only part of a meeting with a start/end range slider and a live preview.
- **Custom file names** with a per-format suffix; your preferred format is remembered for one-click downloads.

**Video and audio**
- Downloads the meeting recording as a single MP4, rebuilt in the browser from the streamed video and audio segments.
- Audio-only (`.m4a`) option.
- Parallel segment downloads with adjustable concurrency, live progress and a cancel button.
- Supports both the classic `videomanifest` delivery and the newer SharePoint `oneDrive.transcode` delivery (AES-128 encrypted segments, decrypted locally).
- DRM-protected recordings are detected and reported, not bypassed.

**Where it works**
- `teams.microsoft.com`, `teams.cloud.microsoft`, `*.sharepoint.com`, plus their Defender for Cloud Apps (`*.mcas.ms`) proxied variants.

---

## Installation

### From the release zip

1. Download the latest `teams-downloader-v*.zip` from the [Releases](../../releases) page and unzip it.
2. Open `chrome://extensions` in Chrome.
3. Turn on **Developer mode** (top right).
4. Click **Load unpacked** and select the unzipped folder.

### From source

```bash
git clone https://github.com/aamer2411/chrome-extension-teams-downloader.git
```

Then follow steps 2 to 4 above, selecting the cloned folder.

---

## Usage

1. Open a meeting recording in Teams, SharePoint or Stream and let the player load.
2. Use the download button the extension adds next to the transcript controls:
   - **Click** to download the transcript in your saved format.
   - **Open the options** to pick a format, a portion, the file name, or to download the video/audio.
3. Files are saved to your Downloads folder.

You can only download recordings and transcripts your account already has access to.

---

## How It Works

| File | Role |
|---|---|
| `intercept.js` | Runs in the page context at load. Hooks the player's own network requests to capture the video manifest, transcript address and the access tokens Microsoft's CDN requires, then relays them to the content script. |
| `content.js` | Adds the download button and options dialog, fetches and converts transcripts, and downloads video/audio segments in parallel. |
| `mux-worker.js` | Background worker that joins the fragmented MP4 video and audio segments into one standard MP4 file, off the main thread so the page stays responsive. |
| `modal.css` | Styling for the button and options dialog. |

No data leaves your browser except the requests to Microsoft that the player itself would make.

---

## Permissions

| Permission | Why |
|---|---|
| `storage` | Remembers your preferred format, file name suffixes and download concurrency |
| Host access: Teams, SharePoint, `*.svc.ms` | Reads the meeting page and downloads the recording and transcript from Microsoft's servers |

---

## Project Structure

```
.
├── manifest.json     Extension manifest (Manifest V3)
├── intercept.js      Page-context request hook (tokens, manifests)
├── content.js        Button, options dialog, transcript and video download
├── mux-worker.js     MP4 muxer (runs in a Web Worker)
├── modal.css         UI styling
└── icons/            Extension icon
```

---

## Disclaimer

Use this tool only for recordings you are permitted to download, and follow your organisation's policies and Microsoft's terms of use.

---

## License

[MIT](LICENSE)
