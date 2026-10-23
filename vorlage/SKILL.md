---
name: bildwerkstatt
description: Create images for this project with the user's Image Workshop / Bildwerkstatt (runs locally on their NVIDIA graphics card – free, no API). Use whenever graphics, illustrations, portraits or image variants for this project come up – write image descriptions, order re-renders, pick variants from a contact sheet and take them into the project, remote-control the workshop and its guardian Argus. / Bilder für dieses Projekt mit der Bildwerkstatt des Nutzers erzeugen (lokal, kostenlos, ohne API).
---

# Bildwerkstatt / Image Workshop

This project is connected to the user's Image Workshop. Full guide: https://github.com/ExtremeLemming/Bildwerkstatt-Download
(`CLAUDE_GUIDE.md` English, `ANLEITUNG_FUER_CLAUDE.md` Deutsch). Everything goes through this repo's workshop branch – see
`bildwerkstatt.json` (`zweig`, `ordner`, `beschreibungen`, `modell`).

**Always free, no API** – never suggest or build in paid image APIs. Don't try to modify the workshop program itself.

Get the tool (every session, don't commit it):

```sh
mkdir -p ~/.cache/bildwerkstatt && curl -fsSL -o ~/.cache/bildwerkstatt/bwc \
  https://github.com/ExtremeLemming/Bildwerkstatt-Download/releases/latest/download/bildwerkstatt-claude-linux-x64 \
  && chmod +x ~/.cache/bildwerkstatt/bwc; BWC=~/.cache/bildwerkstatt/bwc
```

Run in the project folder:
- `$BWC pruefen` – check config and descriptions
- `$BWC zweig` – transfer config and descriptions to the workshop branch (**after every change**, unless `beschreibungen_zweig` is set)
- `$BWC status [--protokoll N]`, `$BWC liste` – what the workshop is doing / what's finished
- `$BWC auftrag ID [--prompt "…"] [--n 6] [--format 1:1]` – re-render (next round automatically)
- `$BWC kontaktbogen /tmp/sheet.jpg [ID …]` – variants side by side, then look at it with Read
- `$BWC uebernehmen ID=v2 ID2=runde2/v1` – take a variant into the project (target: `uebernehmen.ziel`), then commit
- `$BWC loeschbar "PATTERN"` – release old candidates for deletion
- `$BWC befehl neustart | herunterfahren | argus start|stop|neustart|aktualisieren|bericht|testmeldung`

Descriptions: prompts in English, concrete (subject, setting, light, camera, mood), no text in the image unless wanted.
Prompt = `stil_praefix` + `prompt` + style text + `stil_suffix`. IDs: letters, digits, `_`, `-` only.
Keep one model per project (`modell`) so the images match in style.
