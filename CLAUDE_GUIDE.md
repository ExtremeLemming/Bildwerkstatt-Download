**[🇩🇪 Deutsch](ANLEITUNG_FUER_CLAUDE.md)** &nbsp;|&nbsp; **🇬🇧 English**

# Image Workshop – guide for Claude

The user said: “I have an Image Workshop you can use.” This explains how to set it up for their project and use it.
Talk to the user in their language.

## Principles

- **Always free, no API.** The workshop renders locally on the user's NVIDIA graphics card with freely downloadable models.
  Never suggest or build in paid image APIs or services that need an API key.
- The workshop is a finished, compiled program (license: `LIZENZ.md`): **do not modify or reverse engineer it**. You only
  work through the files in the project's workshop branch and with the Claude tool.

## What the workshop is

- A program on the user's Windows PC (window + guardian **Argus**). It knows several projects (GitHub repos), clones only
  the **workshop branch** of each (default `bildwerkstatt`) and uploads there after every image.
- You communicate only through this branch. You don't need access to their PC.

```
<project>, branch bildwerkstatt
  bildwerkstatt.json                 configuration (by you)
  data/bilder.json                   image descriptions (by you; path configurable, can also be read from main)
  art/werkstatt/auftraege.json       re-render jobs (by you, via the tool)
  art/werkstatt/steuerung.json       remote commands (by you, via the tool)
  art/werkstatt/loeschbar.json       what Argus may delete when space runs low (by you)
  art/werkstatt/lokal/               status.json, protokoll.txt, argus.txt, auftraege_erledigt.txt … (by the workshop)
  art/kandidaten/<id>/<id>_v1.jpg …  variants; FERTIG = image done (by the workshop)
  art/kandidaten/<id>/runde2/…       variants from re-render jobs
```

(The file and key names are German – keep them exactly as they are.)

## Get the Claude tool (every session)

A single program that reads and writes the workshop branch (without checking it out; your working tree stays untouched).
Don't commit it to the project – download it to a temporary folder:

```sh
# Linux (e.g. Claude Code on the web)
mkdir -p ~/.cache/bildwerkstatt && curl -fsSL -o ~/.cache/bildwerkstatt/bwc \
  https://github.com/ExtremeLemming/Bildwerkstatt-Download/releases/latest/download/bildwerkstatt-claude-linux-x64 \
  && chmod +x ~/.cache/bildwerkstatt/bwc
BWC=~/.cache/bildwerkstatt/bwc
```

On Windows (Claude Code on the PC): `bildwerkstatt-claude-windows-x64.exe` from the same release.
Always run it **in the project folder** (needs git and the remote `origin`): `$BWC <command>`. Help: `$BWC --help`.
The tool's messages are German; the commands are listed below.

## Setup (once per project)

1. **Configuration:** `$BWC einrichten --name "<project name>"` creates `bildwerkstatt.json` (and an example description
   file if needed). Then adjust:
   - `beschreibungen`: where the descriptions are. If the project already has an image list in another format, set
     `felder` (field names) to match instead of rewriting it.
   - `beschreibungen_zweig`: `"main"` (main branch) if the workshop should read the descriptions straight from there – then
     you never need to transfer them. Empty = from the workshop branch.
   - `uebernehmen.ziel`: where chosen images go in the project (e.g. `assets/images`), `max_kante`, `format`.
   - `gruppen` (grouping in the window), `standard_format`, `formate`, `varianten`, `stil_standard`, `stil_praefix`,
     `stil_suffix`, `dateiformat`, `modell` (see below). Template with all keys: `vorlage/bildwerkstatt.json`.
2. **Write the descriptions** (format below), then `$BWC pruefen` (check).
3. **Commit to the project:** `bildwerkstatt.json`, the descriptions and `.claude/skills/bildwerkstatt/SKILL.md`
   (template: `vorlage/SKILL.md`) – so future sessions in this project know about the workshop.
4. **Create the workshop branch:** `$BWC zweig` creates the branch on GitHub (lean: workshop files only) or transfers the
   configuration and descriptions. **Run it again after every change to them** (unless `beschreibungen_zweig` is set).
5. **Tell the user:** click **“+ Add project”** in the workshop window and enter `<owner>/<repo>` (branch `bildwerkstatt`).
   If the workshop isn't installed yet: give them the link to this repo's `README.en.md`.

## Image descriptions

```json
{
 "stil": {"scene": "Style: … Absolutely no text, no letters, no logos, no watermarks."},
 "bilder": {
  "title_screen": {"prompt": "A lonely lighthouse on a cliff at dusk …", "format": "16:9"},
  "captain": {"prompt": "…", "format": "3:4", "stil": "portrait", "gruppe": "Characters", "varianten": 6, "seed": 5}
 }
}
```

- Prompt sent to the model = `stil_praefix` + `prompt` + style text + `stil_suffix`. A list with `"id"` works instead of an object.
- Prompts **in English**, concrete and visual (subject, setting, light, camera, mood); long natural sentences work well.
  Avoid text in images (“no text, no letters”) – unless the model is good at it (see models) and text is wanted.
- IDs only letters, digits, `_` and `-` (they become file names).
- `format`: a key from `formate` (default 16:9, 9:16, 4:3, 3:4, 3:2, 2:3, 1:1) or `"groesse": [w, h]`.

## Using it

| What | Command |
|---|---|
| status, end of log, open jobs | `$BWC status` (`--protokoll 200` for more) |
| all images with variants and rounds | `$BWC liste` |
| look at variants | `$BWC kontaktbogen /tmp/sheet.jpg [id …]`, then view the image with the Read tool |
| take a variant | `$BWC uebernehmen title_screen=v2 captain=runde2/v1`, then commit in the project |
| re-render | `$BWC auftrag captain [--prompt "…"] [--n 6] [--format 1:1]` (next round automatically) |
| release old rounds for deletion | `$BWC loeschbar "art/kandidaten/*/runde1/*"` |

Proven: look at the contact sheet → take the best variant; if none is good, improve the prompt and re-render with
`auftrag … --prompt`. New images in the descriptions are painted automatically (after `zweig`, or straight from main).

## Remote control and Argus

`$BWC befehl …` – every command runs exactly once:
- `befehl neustart` – the window fetches the latest program version and restarts (within 5 min);
- `befehl herunterfahren` – uploads everything and shuts the PC down, **only if** “Shut down PC at” is on in the window;
- `befehl argus start|stop|neustart|aktualisieren|bericht|testmeldung` (within 2 min); plus `programm_start NAME` and
  `programm_stop NAME` – only for programs the user allowed on the PC themselves.

## Image models

`modell` in `bildwerkstatt.json` (default `black-forest-labs/FLUX.2-klein-4B`). Choose per project and stick to one model
within a project, otherwise the images won't match in style. The user can set another model on their PC; if a model
doesn't fit their card, the workshop uses the recommended one (shown in `status`).

| id | Strengths | License |
|---|---|---|
| `black-forest-labs/FLUX.2-klein-4B` | very fast, versatile | Apache 2.0 |
| `Tongyi-MAI/Z-Image-Turbo` | very fast, photorealistic, text | Apache 2.0 |
| `Tongyi-MAI/Z-Image` | wide range of styles (slow) | Apache 2.0 |
| `baidu/ERNIE-Image-Turbo` | top quality, text, posters, comics | Apache 2.0 |
| `baidu/ERNIE-Image` | very high quality (slow) | Apache 2.0 |
| `meituan-longcat/LongCat-Image` | realism, text (put text in quotes) | Apache 2.0 |
| `Qwen/Qwen-Image-2512` | best text in images (very slow, lots of memory) | Apache 2.0 |
| `black-forest-labs/FLUX.2-klein-9B` | more detail – **non-commercial only** | FLUX Non-Commercial |

For projects that are sold, use Apache 2.0 models only. `status` shows the recommendation for their PC (`modell_vorschlag`).

## Troubleshooting

- `$BWC status` shows `konfig_fehler`, the end of the log and Argus' report; more with `--protokoll 300`.
- An image that crashes twice is skipped (`auftraege_fehler.txt`).
- The user can click “Copy errors” in the window and send you the text.
- You can't fix problems in the program itself (it's compiled) – describe them to the user so they can report them.
