# cnc_trackexport

***English** · [Deutsch](README.de.md)*

The full soundtrack of the **Command & Conquer Remastered Collection** as
tagged files — with filenames that match, character for character, the track
titles the game shows in its own jukebox.

```
Act On Instinct (Classic).mp3
Act On Instinct – Tiberian Sons (Bonus).mp3
Grinder 1 & 2 Medley – Tiberian Sons (Bonus).mp3
Hell March Original Demo (Bonus, Unlocked).mp3
We Will Stop Them ∕ Deception (Remastered).mp3
```

203 tracks, 12 hours 6 minutes, about 1.6 GiB.

> **You need your own copy of the game.** This repository ships no audio, no
> artwork, no extracted data and no game code — only a script that reads the
> files already on your disk. Linux only.

---

## Why the names are the point

Inside `MUSIC.MEG` the tracks are called `TDR_MUS_ACT_ON_INSTINCT.WAV` and
`RAB_MUS_HM_2_3_MEDLEY_FKTS.WAV`. Nothing in that archive says those are "Act
On Instinct (Remastered)" and "Hell March 2 & 3 Medley – Tiberian Sons
(Bonus)". Most extractors therefore leave you with the raw internal names, or
guess at prettier ones.

This one reads the titles where the game itself reads them: the jukebox's own
track list in `CONFIG.MEG`, and the string table it points into. The mapping is
complete and exact — **203 entries ↔ 203 files ↔ 203 titles**, nothing left
over on either side. No guessing, and no lookup table to maintain.

## Requirements

- Command & Conquer Remastered Collection, installed (Steam, Origin/EA, GOG)
- Linux, `python3` 3.6 or newer
- `ffmpeg` with `libmp3lame` — only for MP3 and FLAC output

```bash
sudo apt install python3 ffmpeg      # Debian / Ubuntu
sudo dnf install python3 ffmpeg      # Fedora (ffmpeg via RPM Fusion)
sudo pacman -S python ffmpeg         # Arch
```

## Using it

```bash
git clone -b export https://github.com/SoulInfernoDE/cnc-remastered-jukebox.git
cd cnc-remastered-jukebox
./cnc_trackexport
```

That is all — there is nothing to build. The game directory is found
automatically: standard Steam, Flatpak, Snap, and any extra library listed in
`libraryfolders.vdf`. `CNC_REMASTERED_DIR` overrides the search if that fails.

```
  -o, --out DIR      output directory        (default: the game's own folder)
  -g, --game DIR     game directory          (default: autodetected)
  -f, --format FMT   mp3 | flac | wav        (default: mp3)
  -l, --lang LANG    EN-US, DE-DE, ...       (default: EN-US)
  -j, --jobs N       parallel encoders       (default: CPU cores, max 16)
      --no-cover     do not embed artwork
      --portable     restrict filenames to plain ASCII (FAT32 etc.)
      --force        re-encode instead of skipping existing files
      --list         show the mapping, write nothing
  -h, --help         help
```

The tracks go to the game's own user folder by default —
`Documents/CnCRemastered/Soundtrack` inside the Proton prefix, next to `Mods`,
`Save` and `Replays`, which Steam leaves alone on updates. If that cannot be
found, the music directory is used instead; the destination is always printed
before encoding starts.

Ctrl+C is safe: partial files are removed, and the next run resumes where it
stopped.

## The three formats

| | |
| --- | --- |
| **mp3** | 44.1 kHz at 320 kbps, tagged, with cover art. The Classic tracks are resampled on purpose — [it is the difference between keeping the top of the band and losing 84 % of it](docs/SOUNDTRACK-EXPORT.md#why-everything-is-resampled-to-441-khz). |
| **flac** | Bit-exact against the source, original sample rate, same tags. About 2.5× the space. |
| **wav** | The untouched ADPCM straight out of the archive, byte for byte. Smallest, but awkward for most players. |

## How it works

The interesting parts are written down rather than left in the code:

| | |
| --- | --- |
| [`docs/SOUNDTRACK-EXPORT.md`](docs/SOUNDTRACK-EXPORT.md) | where the titles come from, the audio-quality measurements, tags and filenames |
| [`docs/FILE-FORMATS.md`](docs/FILE-FORMATS.md) | the MEG container, the `.LOC` string table, the jukebox track list |

Both are also available in German; the link sits at the top of each.

## Also in this repository

The [`gui`](../../tree/gui) branch carries a rebuild of the game's own music
jukebox as a standalone Linux window — the same layout, skin and typefaces,
with every caption in your system's language, read from your installation at
runtime the same way this exporter reads the track names.

It is a separate program with its own requirements (PyQt5 on top of ffmpeg),
so it lives on its own branch. This one stays what it is: one script, no
install, no extra dependencies.

## Legal

This is an unofficial fan project. It is **not affiliated with, endorsed by, or
sponsored by** Electronic Arts, Petroglyph Games or Valve. "Command & Conquer",
"Red Alert" and "Tiberian Dawn" are trademarks of Electronic Arts Inc.

The repository ships no game content whatsoever — no audio, no artwork, no
extracted data, and no code from the game. It reads only files that a licensed
installation already put on your own machine, and is intended for personal use
with a copy you own. Do not redistribute what it produces.

By default the script fetches the game's public Steam store image at runtime to
embed as cover art for your personal files. That image is EA's; it is never
stored in this repository. Use `--no-cover` to skip it.

The music is by Frank Klepacki, and for the Bonus arrangements by Frank
Klepacki & The Tiberian Sons.

## License

[MIT](LICENSE) — applies to this script only, not to anything it reads or
produces.
