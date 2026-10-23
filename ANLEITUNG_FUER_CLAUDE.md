# Bildwerkstatt – Anleitung für Claude

**[English → CLAUDE_GUIDE.md](CLAUDE_GUIDE.md)**

Der Nutzer hat gesagt: „Ich habe eine Bildwerkstatt, die du benutzen kannst.“ Hier steht, wie du sie für sein Projekt
einrichtest und benutzt. Sprich mit ihm in seiner Sprache.

## Grundsätze

- **Immer kostenlos, ohne API.** Die Werkstatt rechnet lokal auf seiner NVIDIA-Grafikkarte mit frei herunterladbaren
  Modellen. Schlage nie bezahlte Bild-APIs oder Dienste mit API-Schlüssel vor und baue so etwas nicht ein.
- Die Werkstatt ist ein fertiges Programm (kompiliert, Lizenz siehe `LIZENZ.md`): **nicht verändern, nicht
  zurückübersetzen**. Du arbeitest nur über die Dateien im Werkstatt-Zweig des Projekts und mit dem Claude-Werkzeug.

## Was die Werkstatt ist

- Ein Programm auf seinem Windows-PC (Fenster + Wächter **Argus**). Es kennt mehrere Projekte (GitHub-Repos), klont von
  jedem nur den **Werkstatt-Zweig** (Standard `bildwerkstatt`) und lädt nach jedem Bild dorthin hoch.
- Ihr verständigt euch nur über diesen Zweig. Du brauchst keinen Zugang zu seinem PC.

```
<Projekt>, Zweig bildwerkstatt
  bildwerkstatt.json                 Konfiguration (von dir)
  data/bilder.json                   Bildbeschreibungen (von dir; Pfad einstellbar, auch direkt aus main lesbar)
  art/werkstatt/auftraege.json       Neumal-Aufträge (von dir, per Werkzeug)
  art/werkstatt/steuerung.json       Fernbefehle (von dir, per Werkzeug)
  art/werkstatt/loeschbar.json       was Argus bei Platzmangel löschen darf (von dir)
  art/werkstatt/lokal/               status.json, protokoll.txt, argus.txt, auftraege_erledigt.txt … (von der Werkstatt)
  art/kandidaten/<id>/<id>_v1.jpg …  Varianten; FERTIG = Bild erledigt (von der Werkstatt)
  art/kandidaten/<id>/runde2/…       Varianten aus Neumal-Aufträgen
```

## Das Claude-Werkzeug holen (in jeder Sitzung)

Ein einzelnes Programm, das den Werkstatt-Zweig liest und schreibt (ohne ihn auszuchecken; dein Arbeitsstand bleibt
unberührt). Nicht ins Projekt committen – in einen Zwischenordner laden:

```sh
# Linux (z. B. Claude Code im Web)
mkdir -p ~/.cache/bildwerkstatt && curl -fsSL -o ~/.cache/bildwerkstatt/bwc \
  https://github.com/ExtremeLemming/Bildwerkstatt-Download/releases/latest/download/bildwerkstatt-claude-linux-x64 \
  && chmod +x ~/.cache/bildwerkstatt/bwc
BWC=~/.cache/bildwerkstatt/bwc
```

Unter Windows (Claude Code auf dem PC): `bildwerkstatt-claude-windows-x64.exe` aus demselben Release.
Aufruf immer **im Projektordner** (braucht git und den Remote `origin`): `$BWC <befehl>`. Hilfe: `$BWC --help`.

## Einrichten (einmal je Projekt)

1. **Konfiguration:** `$BWC einrichten --name "<Projektname>"` legt `bildwerkstatt.json` (und, falls nötig, eine Beispiel-
   Beschreibungsdatei) an. Dann anpassen:
   - `beschreibungen`: wo die Beschreibungen liegen. Gibt es im Projekt schon eine Bildliste in anderem Format, stelle
     `felder` passend ein, statt sie umzuschreiben.
   - `beschreibungen_zweig`: `"main"` (Hauptzweig), wenn die Werkstatt die Beschreibungen direkt von dort lesen soll –
     dann musst du sie nie übertragen. Leer = aus dem Werkstatt-Zweig.
   - `uebernehmen.ziel`: wohin gewählte Bilder ins Projekt sollen (z. B. `assets/bilder`), `max_kante`, `format`.
   - `gruppen` (Anzeige im Fenster), `standard_format`, `formate`, `varianten`, `stil_standard`, `stil_praefix`,
     `stil_suffix`, `dateiformat`, `modell` (siehe unten). Vorlage mit allen Schlüsseln: `vorlage/bildwerkstatt.json`.
2. **Beschreibungen schreiben** (Format unten), dann `$BWC pruefen`.
3. **Ins Projekt committen:** `bildwerkstatt.json`, die Beschreibungen und `.claude/skills/bildwerkstatt/SKILL.md`
   (Vorlage: `vorlage/SKILL.md`) – damit künftige Sitzungen in diesem Projekt Bescheid wissen.
4. **Werkstatt-Zweig anlegen:** `$BWC zweig` legt den Zweig auf GitHub an (schlank: nur Werkstatt-Dateien) bzw. überträgt
   Konfiguration und Beschreibungen. **Nach jeder Änderung daran erneut aufrufen** (außer bei `beschreibungen_zweig`).
5. **Dem Nutzer sagen:** im Werkstatt-Fenster **„+ Projekt hinzufügen“** klicken und `<besitzer>/<repo>` eintragen
   (Zweig `bildwerkstatt`). Ist die Werkstatt noch nicht installiert: Link auf `README.md` dieses Repos geben.

## Bildbeschreibungen

```json
{
 "stil": {"szene": "Style: … Absolutely no text, no letters, no logos, no watermarks."},
 "bilder": {
  "titelbild": {"prompt": "A lonely lighthouse on a cliff at dusk …", "format": "16:9"},
  "kapitaenin": {"prompt": "…", "format": "3:4", "stil": "portraet", "gruppe": "Figuren", "varianten": 6, "seed": 5}
 }
}
```

- Prompt an das Modell = `stil_praefix` + `prompt` + Stiltext + `stil_suffix`. Statt eines Objekts geht auch eine Liste mit `"id"`.
- Prompts **auf Englisch**, konkret und bildhaft (Motiv, Umgebung, Licht, Kamera, Stimmung); lange, natürliche Sätze sind gut.
  Text im Bild vermeiden („no text, no letters“) – außer das Modell ist dafür gut (siehe Modelle) und Text ist gewollt.
- IDs nur aus Buchstaben, Ziffern, `_` und `-` (werden Dateinamen).
- `format`: Schlüssel aus `formate` (Standard 16:9, 9:16, 4:3, 3:4, 3:2, 2:3, 1:1) oder `"groesse": [b, h]`.

## Benutzen

| Was | Befehl |
|---|---|
| Stand, Protokollende, offene Aufträge | `$BWC status` (`--protokoll 200` für mehr) |
| alle Bilder mit Varianten und Runden | `$BWC liste` |
| Varianten ansehen | `$BWC kontaktbogen /tmp/bogen.jpg [id …]`, dann das Bild mit dem Read-Werkzeug ansehen |
| Variante übernehmen | `$BWC uebernehmen titelbild=v2 kapitaenin=runde2/v1`, danach im Projekt committen |
| neu malen lassen | `$BWC auftrag kapitaenin [--prompt "…"] [--n 6] [--format 1:1]` (nächste Runde automatisch) |
| alte Runden zum Löschen freigeben | `$BWC loeschbar "art/kandidaten/*/runde1/*"` |

Bewährt: Kontaktbogen ansehen → beste Variante übernehmen; ist keine gut, Prompt verbessern und mit `auftrag … --prompt`
neu malen lassen. Neue Bilder in den Beschreibungen malt die Werkstatt von selbst (nach `zweig` bzw. direkt aus main).

## Fernsteuerung und Argus

`$BWC befehl …` – jeder Befehl wird genau einmal ausgeführt:
- `befehl neustart` – das Fenster holt die neueste Programmversion und startet neu (innerhalb von 5 min);
- `befehl herunterfahren` – lädt alles hoch und fährt den PC herunter, **nur wenn** „PC herunterfahren um“ im Fenster an ist;
- `befehl argus start|stop|neustart|aktualisieren|bericht|testmeldung` (innerhalb von 2 min); dazu `programm_start NAME`
  und `programm_stop NAME` – nur für Programme, die der Nutzer selbst auf dem PC freigegeben hat.

## Bildmodelle

`modell` in `bildwerkstatt.json` (Standard `black-forest-labs/FLUX.2-klein-4B`). Wähle je Projekt und bleib innerhalb eines
Projekts bei einem Modell, sonst passen die Bilder im Stil nicht zusammen. Der Nutzer kann auf seinem PC ein anderes
festlegen; passt ein Modell nicht auf seine Karte, nimmt die Werkstatt das empfohlene (steht dann in `status`).

| id | Stärken | Lizenz |
|---|---|---|
| `black-forest-labs/FLUX.2-klein-4B` | sehr schnell, vielseitig | Apache 2.0 |
| `Tongyi-MAI/Z-Image-Turbo` | sehr schnell, fotorealistisch, Schrift | Apache 2.0 |
| `Tongyi-MAI/Z-Image` | große Stilvielfalt (langsam) | Apache 2.0 |
| `baidu/ERNIE-Image-Turbo` | Spitzenqualität, Text, Poster, Comics | Apache 2.0 |
| `baidu/ERNIE-Image` | sehr hohe Qualität (langsam) | Apache 2.0 |
| `meituan-longcat/LongCat-Image` | Realismus, Schrift (Text in Anführungszeichen) | Apache 2.0 |
| `Qwen/Qwen-Image-2512` | beste Schrift im Bild (sehr langsam, viel Speicher) | Apache 2.0 |
| `black-forest-labs/FLUX.2-klein-9B` | mehr Details – **nur nicht-kommerziell** | FLUX Non-Commercial |

Für Projekte, die verkauft werden, nur Modelle mit Apache 2.0 verwenden. `status` zeigt die Empfehlung für seinen PC
(`modell_vorschlag`).

## Fehlersuche

- `$BWC status` zeigt `konfig_fehler`, das Protokollende und den Argus-Bericht; mehr mit `--protokoll 300`.
- Ein Bild, das zweimal abstürzt, wird übersprungen (`auftraege_fehler.txt`).
- Der Nutzer kann im Fenster „Fehler kopieren“ drücken und dir den Text schicken.
- Probleme mit dem Programm selbst kannst du nicht beheben (kompiliert) – beschreibe sie dem Nutzer, damit er sie an den
  Hersteller meldet.
