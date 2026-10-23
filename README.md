**🇩🇪 Deutsch** &nbsp;|&nbsp; **[🇬🇧 English](README.en.md)**

# Bildwerkstatt

Die Bildwerkstatt malt Bilder für deine Projekte (Spiele, Webseiten, Bücher …) **auf deiner eigenen NVIDIA-Grafikkarte** –
**kostenlos, ohne API, ohne Abo**. Sie arbeitet mit frei herunterladbaren Bildmodellen und lädt jedes Bild automatisch in
dein Projekt auf GitHub. **Claude** schreibt die Bildbeschreibungen, sucht die besten Varianten aus und übernimmt sie in
dein Projekt.

> **Funktioniert nur mit Claude.** Einrichten, Bildbeschreibungen, Neumal-Aufträge und die Auswahl der Bilder macht Claude
> (z. B. Claude Code oder claude.ai mit GitHub-Anbindung). Die Bildwerkstatt ist das Werkzeug, das Claude dafür auf deinem
> PC benutzt.

## Inhalt
- [Systemanforderungen](#systemanforderungen)
- [Installation](#installation)
- [Erste Schritte mit Claude](#erste-schritte-mit-claude)
- [So funktioniert es](#so-funktioniert-es)
- [Das Fenster](#das-fenster)
- [Bildmodelle](#bildmodelle)
- [Argus, der Wächter (mit Handy)](#argus-der-wächter-mit-handy)
- [Speicherorte, Aktualisieren, Deinstallieren](#speicherorte-aktualisieren-deinstallieren)
- [Probleme lösen](#probleme-lösen)
- [Lizenz](#lizenz)

## Systemanforderungen

| | Mindestens | Empfohlen |
|---|---|---|
| Betriebssystem | Windows 10 (Version 1809) oder Windows 11, 64 Bit | Windows 11 |
| Grafikkarte | NVIDIA mit **6 GB** Grafikspeicher (ab GeForce RTX 20xx) | NVIDIA mit 12 GB oder mehr (RTX 3060 12 GB, RTX 4070 Ti, RTX 50xx …) |
| Grafiktreiber | aktueller NVIDIA-Treiber (ab Version 570) | immer der neueste ([nvidia.de](https://www.nvidia.com/de-de/drivers/)) |
| Arbeitsspeicher | 16 GB | 32 GB (große Modelle brauchen 48–64 GB) |
| Festplatte | **30 GB frei** (Programm, Python, Bibliotheken, ein Bildmodell) | SSD mit 100 GB frei (je weiteres Modell 15–60 GB) |
| Internet | für Installation, Modell-Downloads und das Hochladen der Bilder | schnelle Leitung (der erste Start lädt 15–60 GB) |
| Konten | **GitHub-Konto** (deine Projekte liegen auf GitHub) und **Claude** | – |
| Handy (optional) | App „ntfy“ (kostenlos) für Meldungen von Argus | – |

Nicht unterstützt: Grafikkarten von AMD oder Intel, macOS, Linux (das Programm läuft nur unter Windows mit NVIDIA).
Das Fenster prüft die Anforderungen beim Start selbst und zeigt in der Zeile **„System“**, was fehlt.

## Installation

1. **[Bildwerkstatt.exe herunterladen](../../releases/latest/download/Bildwerkstatt.exe)** (aus den
   [Releases](../../releases/latest)) und doppelklicken.
   Windows zeigt eventuell „Der Computer wurde durch Windows geschützt“ – dann auf **Weitere Informationen → Trotzdem
   ausführen** klicken (das Programm ist nicht teuer signiert, aber sicher).
2. **Speicherort wählen:** Die Bildwerkstatt fragt, wohin sie soll, und schlägt die Festplatte mit dem meisten freien
   Platz vor (z. B. `D:\Bildwerkstatt`). **Ja** = dort, **Nein** = anderen Ordner wählen. Jede Festplatte geht.
   Alles Große landet dort: Python, Bibliotheken, Bildmodelle, Projekte – nichts davon auf C:, wenn du eine andere Platte wählst.
3. Ein schwarzes Fenster richtet ein: Git (falls nötig), Python 3.12, die Werkstatt selbst. Danach öffnet sich das
   **Werkstatt-Fenster**. Es installiert die Grafikbibliotheken passend zu deiner Karte (einmalig ca. 3–5 GB) und zeigt
   alles live an.
4. Einmal öffnet sich das **GitHub-Anmeldefenster** (beim ersten Projekt) – dort anmelden, damit die Werkstatt Bilder in
   deine Projekte hochladen darf.

Für Fortgeschrittene: `Bildwerkstatt.exe /ordner=D:\Pfad` installiert ohne Rückfragen, `Bildwerkstatt.exe /ordner` fragt
den Speicherort neu ab.

## Erste Schritte mit Claude

Sag in deinem Projekt zu Claude:

> Ich habe eine Bildwerkstatt, die du benutzen kannst: https://github.com/ExtremeLemming/Bildwerkstatt-Download – lies
> `ANLEITUNG_FUER_CLAUDE.md` (bzw. `CLAUDE_GUIDE.md`).

Claude richtet dann alles ein: eine Konfiguration (`bildwerkstatt.json`), die Bildbeschreibungen und einen eigenen Zweig
`bildwerkstatt` in deinem Projekt. Danach sagt Claude dir die Adresse – im Werkstatt-Fenster unten auf
**„+ Projekt hinzufügen“** klicken und sie eintragen (z. B. `deinname/MeinSpiel`). Ab dann malt die Werkstatt von selbst.

## So funktioniert es

```
 Claude (in deinem Projekt)                     GitHub (dein Projekt, Zweig „bildwerkstatt“)            Dein PC (Bildwerkstatt)
 ───────────────────────────                    ───────────────────────────────────────────            ────────────────────────
 schreibt Bildbeschreibungen  ───────────────▶  bildwerkstatt.json, Beschreibungen        ───────────▶  holt sie, malt je Bild
 gibt Neumal-Aufträge         ───────────────▶  werkstatt/auftraege.json                  ───────────▶  mehrere Varianten
 schickt Befehle (Neustart …) ───────────────▶  werkstatt/steuerung.json                  ───────────▶  Argus/Fenster führen aus
 sieht Varianten an, wählt aus ◀───────────────  kandidaten/<bild>/…_v1.jpg …              ◀───────────  lädt jedes Bild hoch
 liest Status und Protokoll   ◀───────────────  werkstatt/lokal/status.json, protokoll    ◀───────────  jede Minute ein Bericht
```

- Die Werkstatt kennt **mehrere Projekte** und arbeitet sie in der eingestellten Reihenfolge ab. Neumal-Aufträge haben Vorrang.
- Nach **jedem Bild** wird hochgeladen – es geht nichts verloren, auch wenn der PC ausgeht.
- Ist alles fertig, sieht sie alle 5 Minuten nach neuen Aufträgen.
- Claude braucht keinen Zugang zu deinem PC – alles läuft über dein GitHub-Projekt.

## Das Fenster

- **Sprache:** oben rechts **Deutsch / English** – wirkt sofort, ohne Neustart.
- **Voraussetzungen:** Version, Git & Python, Bibliotheken, Grafikkarte, **System** (Anforderungen), **Empfehlung**
  (passendes Bildmodell), Projekte, Bildmodelle (Download-Stand).
- **Grafikkarte live**, **Zuletzt erzeugt** (Vorschau), **Live-Fortschritt** mit Restzeit, **Live-Protokoll**,
  **Fehler** mit „Fehler kopieren“ (in den Chat mit Claude einfügen).
- **Projekte & Bildgruppen:** hinzufügen, an/aus, Reihenfolge (▲▼), entfernen.
- **Bildmodell …:** alle Modelle mit „passt auf diesen PC?“, Lizenz, Download-Größe, Stärken – und **Probebild malen** zum Vergleichen.
- **Speicherorte …:** Werkstatt-Ordner und Modell-Ordner (der Modell-Ordner kann auf eine andere Festplatte).
- **Schonmodus:** höchstens 6,5 GB Grafikspeicher und niedrige Priorität – du kannst nebenbei Videos schauen.
- **PC herunterfahren um …** mit **Nachtwache:** hängt die Werkstatt, startet sie neu; vorher wird alles hochgeladen.

## Bildmodelle

Alle Modelle sind **kostenlos** und laufen **lokal**. Das Fenster erkennt deine Grafikkarte, schlägt das beste passende
Modell vor und meldet sich, wenn du eine neue Karte einbaust. Welches Modell ein Projekt nutzt, steht in seiner
`bildwerkstatt.json`; im Fenster kannst du für deinen PC ein anderes festlegen.

| Modell | Grafikspeicher | Arbeitsspeicher | Download | Lizenz | Stärken |
|---|---|---|---|---|---|
| FLUX.2 klein 4B | ab 6 GB | 16 GB | 16 GB | Apache 2.0 | sehr schnell, vielseitig, Standard |
| Z-Image Turbo | ab 12 GB | 32 GB | 33 GB | Apache 2.0 | sehr schnell, fotorealistisch, Schrift |
| Z-Image | ab 12 GB | 32 GB | 33 GB | Apache 2.0 | große Stilvielfalt (langsamer) |
| ERNIE-Image Turbo | ab 12 GB | 48 GB | 32 GB | Apache 2.0 | Spitzenqualität, Text, Poster |
| ERNIE-Image | ab 12 GB | 48 GB | 32 GB | Apache 2.0 | eines der besten freien Modelle (langsam) |
| LongCat-Image | ab 18 GB | 48 GB | 29 GB | Apache 2.0 | Realismus, Schrift |
| Qwen-Image 2512 | ab 24 GB | 64 GB | 58 GB | Apache 2.0 | beste Schrift im Bild (sehr langsam) |
| FLUX.2 klein 9B | ab 14 GB | 32 GB | 35 GB | **nur nicht-kommerziell** | mehr Details; Hugging-Face-Zugang nötig |

„Ab“ bedeutet: sparsam (8 Bit, mit Auslagern) – mit mehr Grafikspeicher wird es deutlich schneller. Die Angaben sind
Schätzungen. Neue Modelle kommen mit Programm-Updates dazu.

## Argus, der Wächter (mit Handy)

Argus läuft unsichtbar im Hintergrund:
- startet das Fenster neu, wenn es abstürzt oder hängt; auf Wunsch startet er mit Windows (und die Werkstatt nur, wenn sie
  beim Ausschalten noch lief);
- räumt bei knappem Platz alte, von Claude freigegebene Bildvorschläge auf und hält die Werkstatt an, bevor die Platte voll ist;
- schickt Probleme und jeden Morgen einen Bericht aufs **Handy** (App **ntfy**, kostenlos, ohne Konto) und versteht feste
  Sätze vom Handy: `status`, `projekte`, `starte werkstatt`, `stoppe werkstatt`, `pc herunterfahren` … (`hilfe` zeigt alle).
  Einrichten: Datei `HANDY_EINRICHTEN.txt` im Werkstatt-Ordner.
- führt Befehle aus, die Claude über dein Projekt schickt (nur eine feste Liste – nie beliebige Programme).

## Speicherorte, Aktualisieren, Deinstallieren

- **Werkstatt verschieben:** Werkstatt schließen, den Ordner im Explorer auf die neue Platte verschieben,
  `Bildwerkstatt.exe` starten – sie fragt nach dem neuen Ort.
- **Nur die Modelle auf eine andere Platte:** im Fenster **Speicherorte … → Anderen Modell-Ordner wählen** (vorhandene
  Modelle werden auf Wunsch mitgenommen).
- **Aktualisieren:** Neue Versionen erkennt die Werkstatt selbst – der Knopf **Aktualisieren** leuchtet dann grün.
- **Deinstallieren:** im Fenster „Argus mit Windows starten“ ausschalten, Fenster schließen, den Werkstatt-Ordner löschen.
  (Deine Bilder liegen weiterhin in deinen Projekten auf GitHub.)

## Probleme lösen

| Problem | Lösung |
|---|---|
| „Keine NVIDIA-Grafikkarte gefunden“ | NVIDIA-Treiber installieren/aktualisieren, PC neu starten, dann „Start“. |
| „Zu wenig Speicherplatz“ | Platz schaffen oder unter **Speicherorte …** einen Modell-Ordner auf einer anderen Platte wählen. |
| „Hugging-Face-Zugang fehlt“ | Nur bei FLUX.2 klein 9B: Lizenz auf huggingface.co annehmen, Token im Fenster eintragen – oder ein anderes Modell wählen. |
| Projekt zeigt „Zweig fehlt“ | Claude hat das Projekt noch nicht eingerichtet – Claude die Anleitung geben (siehe oben). |
| Etwas anderes | Im Fenster **Fehler kopieren** und Claude in den Chat einfügen. |

## Lizenz

Kostenlos nutzbar, privat und gewerblich. Verändern, Zurückübersetzen und Weiterverbreiten sind nicht erlaubt – Details in
[LIZENZ.md](LIZENZ.md). Die Bildmodelle haben eigene Lizenzen (im Fenster bei jedem Modell angezeigt).
