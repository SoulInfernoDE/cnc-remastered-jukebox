# cnc_trackexport

*[English](README.md) · **Deutsch***

Der komplette Soundtrack der **Command & Conquer Remastered Collection** als
getaggte Dateien — mit Dateinamen, die Zeichen für Zeichen den Titeln
entsprechen, die das Spiel in seiner eigenen Jukebox anzeigt.

```
Act On Instinct (Classic).mp3
Act On Instinct – Tiberian Sons (Bonus).mp3
Grinder 1 & 2 Medley – Tiberian Sons (Bonus).mp3
Hell March Original Demo (Bonus, Unlocked).mp3
We Will Stop Them ∕ Deception (Remastered).mp3
```

203 Titel, 12 Stunden 6 Minuten, rund 1,6 GiB.

> **Du brauchst dein eigenes Exemplar des Spiels.** Dieses Repository enthält
> keine Audiodaten, keine Grafiken, keine extrahierten Daten und keinen
> Spielcode — nur ein Skript, das die Dateien liest, die ohnehin auf deiner
> Platte liegen. Nur unter Linux.

---

## Warum es auf die Namen ankommt

In `MUSIC.MEG` heißen die Stücke `TDR_MUS_ACT_ON_INSTINCT.WAV` und
`RAB_MUS_HM_2_3_MEDLEY_FKTS.WAV`. Nichts in diesem Archiv sagt, dass das „Act
On Instinct (Remastered)" und „Hell March 2 & 3 Medley – Tiberian Sons
(Bonus)" sind. Die meisten Extraktoren lassen einen deshalb mit den rohen
internen Namen zurück oder raten hübschere.

Dieses liest die Titel dort, wo auch das Spiel sie liest: in der Titelliste der
Jukebox in `CONFIG.MEG` und in der Zeichenkettentabelle, auf die sie verweist.
Die Zuordnung ist vollständig und exakt — **203 Einträge ↔ 203 Dateien ↔ 203
Titel**, auf keiner Seite bleibt etwas übrig. Kein Raten, und keine
Nachschlagetabelle, die gepflegt werden müsste.

## Voraussetzungen

- Command & Conquer Remastered Collection, installiert (Steam, Origin/EA, GOG)
- Linux, `python3` 3.6 oder neuer
- `ffmpeg` mit `libmp3lame` — nur für die Ausgabe als MP3 und FLAC

```bash
sudo apt install python3 ffmpeg      # Debian / Ubuntu
sudo dnf install python3 ffmpeg      # Fedora (ffmpeg über RPM Fusion)
sudo pacman -S python ffmpeg         # Arch
```

## Benutzung

```bash
git clone -b export https://github.com/SoulInfernoDE/cnc-remastered-jukebox.git
cd cnc-remastered-jukebox
./cnc_trackexport
```

Das ist alles — es gibt nichts zu bauen. Das Spielverzeichnis wird automatisch
gefunden: gewöhnliches Steam, Flatpak, Snap und jede weitere Bibliothek, die in
`libraryfolders.vdf` steht. `CNC_REMASTERED_DIR` überschreibt die Suche, falls
sie fehlschlägt.

```
  -o, --out DIR      Zielverzeichnis        (Standard: der Ordner des Spiels)
  -g, --game DIR     Spielverzeichnis       (Standard: automatisch gesucht)
  -f, --format FMT   mp3 | flac | wav       (Standard: mp3)
  -l, --lang LANG    EN-US, DE-DE, ...      (Standard: EN-US)
  -j, --jobs N       parallele Encoder      (Standard: CPU-Kerne, max. 16)
      --no-cover     kein Artwork einbetten
      --portable     Dateinamen auf reines ASCII beschränken (FAT32 usw.)
      --force        neu erzeugen statt vorhandene Dateien überspringen
      --list         nur die Zuordnung anzeigen, nichts schreiben
  -h, --help         Hilfe
```

Die Titel landen standardmäßig im eigenen Benutzerordner des Spiels —
`Documents/CnCRemastered/Soundtrack` im Proton-Prefix, neben `Mods`, `Save` und
`Replays`, den Steam bei Updates in Ruhe lässt. Ist er nicht auffindbar, wird
der Musikordner benutzt; das Ziel wird vor dem Kodieren immer ausgegeben.

Strg+C ist unbedenklich: angefangene Dateien werden entfernt, und der nächste
Lauf macht dort weiter, wo er aufgehört hat.

## Die drei Formate

| | |
| --- | --- |
| **mp3** | 44,1 kHz bei 320 kbit/s, getaggt, mit Titelbild. Die Classic-Titel werden mit Absicht umgerechnet — [es ist der Unterschied zwischen dem Erhalt des oberen Frequenzbereichs und dem Verlust von 84 % davon](docs/SOUNDTRACK-EXPORT.de.md#warum-alles-auf-441-khz-umgerechnet-wird). |
| **flac** | Bitgenau zur Quelle, ursprüngliche Abtastrate, dieselben Tags. Etwa das 2,5-fache an Platz. |
| **wav** | Das unveränderte ADPCM direkt aus dem Archiv, Byte für Byte. Am kleinsten, für die meisten Abspielprogramme aber unhandlich. |

## Wie es funktioniert

Die interessanten Teile sind aufgeschrieben, statt nur im Code zu stehen:

| | |
| --- | --- |
| [`docs/SOUNDTRACK-EXPORT.de.md`](docs/SOUNDTRACK-EXPORT.de.md) | woher die Titel stammen, die Messungen zur Audioqualität, Tags und Dateinamen |
| [`docs/FILE-FORMATS.de.md`](docs/FILE-FORMATS.de.md) | der MEG-Container, die `.LOC`-Zeichenkettentabelle, die Titelliste der Jukebox |

Beide gibt es auch auf Englisch; oben in jedem steht der Verweis.

## Ebenfalls in diesem Repository

Der Branch [`gui`](../../tree/gui) trägt einen Nachbau der spieleigenen
Musik-Jukebox als eigenständiges Linux-Fenster — dasselbe Layout, derselbe
Skin, dieselben Schriften, jede Beschriftung in der Sprache deines Systems, zur
Laufzeit aus deiner Installation gelesen, genau so, wie dieser Exporter die
Titelnamen liest.

Es ist ein eigenes Programm mit eigenen Voraussetzungen (PyQt5 zusätzlich zu
ffmpeg) und liegt deshalb auf einem eigenen Branch. Dieser bleibt, was er ist:
ein Skript, keine Installation, keine weiteren Abhängigkeiten.

## Rechtliches

Dies ist ein inoffizielles Fan-Projekt. Es steht **in keiner Verbindung zu
Electronic Arts, Petroglyph Games oder Valve und wird von diesen weder
unterstützt noch gefördert.** „Command & Conquer", „Alarmstufe Rot" und
„Tiberiumkonflikt" sind Marken von Electronic Arts Inc.

Das Repository enthält keinerlei Spielinhalte — keine Audiodaten, keine
Grafiken, keine extrahierten Daten und keinen Code aus dem Spiel. Es liest
ausschließlich Dateien, die eine lizenzierte Installation ohnehin auf deinem
Rechner abgelegt hat, und ist für den persönlichen Gebrauch mit einem Exemplar
gedacht, das dir gehört. Gib nicht weiter, was dabei entsteht.

Standardmäßig lädt das Skript zur Laufzeit das öffentliche Steam-Store-Bild des
Spiels, um es als Cover in deine persönlichen Dateien einzubetten. Dieses Bild
gehört EA; es wird nie in diesem Repository gespeichert. Mit `--no-cover`
bleibt es außen vor.

Die Musik stammt von Frank Klepacki, bei den Bonus-Arrangements von Frank
Klepacki & The Tiberian Sons.

## Lizenz

[MIT](LICENSE) — gilt nur für dieses Skript, nicht für das, was es liest oder
erzeugt.
