**[🇩🇪 Deutsch](README.md)** &nbsp;|&nbsp; **🇬🇧 English**

# Image Workshop (Bildwerkstatt)

The Image Workshop paints images for your projects (games, websites, books …) **on your own NVIDIA graphics card** –
**free, no API, no subscription**. It uses freely downloadable image models and uploads every image to your project on
GitHub automatically. **Claude** writes the image descriptions, picks the best variants and adds them to your project.

> **Works with Claude only.** Setup, image descriptions, re-render jobs and picking images are done by Claude (e.g. Claude
> Code or claude.ai with GitHub). The Image Workshop is the tool Claude uses for this on your PC.

## Contents
- [System requirements](#system-requirements)
- [Installation](#installation)
- [Getting started with Claude](#getting-started-with-claude)
- [How it works](#how-it-works)
- [The window](#the-window)
- [Image models](#image-models)
- [Argus, the guardian (with your phone)](#argus-the-guardian-with-your-phone)
- [Storage, updates, uninstalling](#storage-updates-uninstalling)
- [Troubleshooting](#troubleshooting)
- [License](#license)

## System requirements

| | Minimum | Recommended |
|---|---|---|
| Operating system | Windows 10 (version 1809) or Windows 11, 64-bit | Windows 11 |
| Graphics card | NVIDIA with **6 GB** VRAM (GeForce RTX 20xx or newer) | NVIDIA with 12 GB or more (RTX 3060 12 GB, RTX 4070 Ti, RTX 50xx …) |
| Graphics driver | current NVIDIA driver (570 or newer) | always the latest ([nvidia.com](https://www.nvidia.com/drivers/)) |
| RAM | 16 GB | 32 GB (large models need 48–64 GB) |
| Disk | **30 GB free** (program, Python, libraries, one image model) | SSD with 100 GB free (each extra model 15–60 GB) |
| Internet | for installation, model downloads and uploading images | fast connection (the first start downloads 15–60 GB) |
| Accounts | **GitHub account** (your projects live on GitHub) and **Claude** | – |
| Phone (optional) | the free app “ntfy” for messages from Argus | – |

Not supported: AMD or Intel graphics cards, macOS, Linux (the program runs on Windows with NVIDIA only).
The window checks the requirements at start-up and shows what is missing in the **“System”** row.

## Installation

1. **[Download Bildwerkstatt.exe](../../releases/latest/download/Bildwerkstatt.exe)** (from the
   [releases](../../releases/latest)) and double-click it.
   Windows may show “Windows protected your PC” – click **More info → Run anyway** (the program isn't signed with an
   expensive certificate, but it is safe).
2. **Choose a location:** the Image Workshop asks where to go and suggests the drive with the most free space (e.g.
   `D:\Bildwerkstatt`). **Yes** = there, **No** = choose another folder. Any drive works. Everything big goes there:
   Python, libraries, image models, projects – none of it on C: if you pick another drive.
3. A black window sets things up: Git (if needed), Python 3.12, the workshop itself. Then the **workshop window** opens.
   It installs the graphics libraries matching your card (once, approx. 3–5 GB) and shows everything live.
4. Once, the **GitHub sign-in window** appears (with the first project) – sign in so the workshop may upload images to your projects.

Advanced: `Bildwerkstatt.exe /folder=D:\Path` installs without questions, `Bildwerkstatt.exe /folder` asks for the location again.

## Getting started with Claude

Tell Claude in your project:

> I have an Image Workshop you can use: https://github.com/ExtremeLemming/Bildwerkstatt-Download – read `CLAUDE_GUIDE.md`.

Claude then sets everything up: a configuration (`bildwerkstatt.json`), the image descriptions and a separate branch
`bildwerkstatt` in your project. Claude then tells you the address – click **“+ Add project”** at the bottom of the
workshop window and enter it (e.g. `yourname/MyGame`). From then on the workshop paints by itself.

## How it works

```
 Claude (in your project)                       GitHub (your project, branch “bildwerkstatt”)             Your PC (Image Workshop)
 ────────────────────────                       ─────────────────────────────────────────────            ────────────────────────
 writes image descriptions   ───────────────▶   bildwerkstatt.json, descriptions           ───────────▶  fetches them, paints
 gives re-render jobs        ───────────────▶   werkstatt/auftraege.json                   ───────────▶  several variants each
 sends commands (restart …)  ───────────────▶   werkstatt/steuerung.json                   ───────────▶  Argus/window execute
 looks at variants, picks    ◀───────────────   kandidaten/<image>/…_v1.jpg …              ◀───────────  uploads every image
 reads status and log        ◀───────────────   werkstatt/lokal/status.json, log           ◀───────────  a report every minute
```

- The workshop knows **several projects** and works through them in the order you set. Re-render jobs come first.
- It uploads after **every image** – nothing is lost, even if the PC goes off.
- When everything is done, it checks for new jobs every 5 minutes.
- Claude needs no access to your PC – everything goes through your GitHub project.

## The window

- **Language:** top right **Deutsch / English** – takes effect immediately, no restart.
- **Requirements:** version, Git & Python, libraries, graphics card, **System** (requirements), **Recommendation**
  (matching image model), projects, image models (download status).
- **Graphics card live**, **Latest image** (preview), **Live progress** with time left, **Live log**, **Errors** with
  “Copy errors” (paste into your chat with Claude).
- **Projects & image groups:** add, on/off, order (▲▼), remove.
- **Image model …:** all models with “fits this PC?”, license, download size, strengths – and **Render test image** to compare.
- **Storage …:** workshop folder and model folder (the model folder can be on a different drive).
- **Gentle mode:** at most 6.5 GB VRAM and low priority – you can watch videos alongside.
- **Shut down PC at …** with **night watch:** if the workshop hangs it restarts; everything is uploaded first.

## Image models

All models are **free** and run **locally**. The window detects your graphics card, suggests the best fitting model and
tells you when you install a new card. Which model a project uses is set in its `bildwerkstatt.json`; in the window you
can choose a different one for your PC.

| Model | VRAM | RAM | Download | License | Strengths |
|---|---|---|---|---|---|
| FLUX.2 klein 4B | from 6 GB | 16 GB | 16 GB | Apache 2.0 | very fast, versatile, default |
| Z-Image Turbo | from 12 GB | 32 GB | 33 GB | Apache 2.0 | very fast, photorealistic, text |
| Z-Image | from 12 GB | 32 GB | 33 GB | Apache 2.0 | wide range of styles (slower) |
| ERNIE-Image Turbo | from 12 GB | 48 GB | 32 GB | Apache 2.0 | top quality, text, posters |
| ERNIE-Image | from 12 GB | 48 GB | 32 GB | Apache 2.0 | one of the best free models (slow) |
| LongCat-Image | from 18 GB | 48 GB | 29 GB | Apache 2.0 | realism, text |
| Qwen-Image 2512 | from 24 GB | 64 GB | 58 GB | Apache 2.0 | best text in images (very slow) |
| FLUX.2 klein 9B | from 14 GB | 32 GB | 35 GB | **non-commercial only** | more detail; Hugging Face access needed |

“From” means economical (8-bit, with offloading) – more VRAM makes it much faster. The figures are estimates.
New models arrive with program updates.

## Argus, the guardian (with your phone)

Argus runs invisibly in the background:
- restarts the window if it crashes or hangs; optionally starts with Windows (and starts the workshop only if it was still
  running at shutdown);
- when space runs low, cleans up old image candidates Claude has released and stops the workshop before the disk is full;
- sends problems and a morning report to your **phone** (app **ntfy**, free, no account) and understands fixed phrases
  from your phone: `status`, `projects`, `start workshop`, `stop workshop`, `pc shutdown` … (`help` lists them all).
  Setup: the file `HANDY_EINRICHTEN.txt` in the workshop folder.
- executes commands Claude sends through your project (a fixed list only – never arbitrary programs).

## Storage, updates, uninstalling

- **Move the workshop:** close it, move the folder to the new drive in Explorer, start `Bildwerkstatt.exe` – it asks for the new location.
- **Only the models to another drive:** in the window **Storage … → Choose another model folder** (existing models can be taken along).
- **Updates:** the workshop notices new versions itself – the **Update** button then glows green.
- **Uninstall:** switch off “Start Argus with Windows” in the window, close it, delete the workshop folder.
  (Your images stay in your projects on GitHub.)

## Troubleshooting

| Problem | Solution |
|---|---|
| “No NVIDIA graphics card found” | Install/update the NVIDIA driver, restart the PC, then “Start”. |
| “Not enough disk space” | Free up space or choose a model folder on another drive under **Storage …**. |
| “Hugging Face access missing” | Only for FLUX.2 klein 9B: accept the license on huggingface.co, enter the token in the window – or choose another model. |
| Project shows “branch missing” | Claude hasn't set up the project yet – give Claude the guide (see above). |
| Anything else | Click **Copy errors** in the window and paste it into your chat with Claude. |

## License

Free to use, privately and commercially. Modifying, reverse engineering and redistributing are not permitted – see
[LIZENZ.md](LIZENZ.md). The image models have their own licenses (shown for every model in the window).

> **No modification.** The Image Workshop is distributed only as a finished, compiled program; the source code is not
> public. You may **not** modify, reverse engineer (decompile, disassemble) or redistribute it under another name. You
> may only share the link to the official download page.

> **Responsibility for the images.** **You** are responsible for the images you generate. Do not create content that
> breaks applicable law or the license of the image model you use (e.g. third-party rights, trademarks, personality
> rights, content harmful to minors or otherwise prohibited). The Image Workshop does not check the content.
