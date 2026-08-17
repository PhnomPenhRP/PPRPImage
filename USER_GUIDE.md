# PPRP Particle Studio — User Guide

PPRP Particle Studio is a local, offline Windows editor for GTA V / FiveM
particle effects (`.ypt` binary, `.ypt.xml`, and project files). This guide
covers everyday use: opening effects, browsing vanilla game effects, editing,
previewing, and exporting a ready-to-stream FiveM resource.

The interface ships in Italian (base language) with English, French, Spanish,
Portuguese, Brazilian Portuguese, and Thai translations. Change the language
from the UI at any time; the choice persists between sessions. Menu names below
are given as `Italian (English)`.

---

## 1. Requirements and first launch

- Windows 10/11 x64
- .NET 9 Windows Desktop Runtime
- Microsoft Edge WebView2 Runtime
- A Direct3D 11-capable GPU

Launch `PPRP.ParticleStudio.exe`. All state lives under:

```text
%APPDATA%\PPRP\ParticleStudio      (settings, recents, language, GTA folder)
%LOCALAPPDATA%\PPRP\ParticleStudio (WebView2 browser data)
```

The app makes no network requests. Nothing you edit is uploaded anywhere.

First time in? Open **Aiuto → Primi passi (tutorial)** — a guided
"create a particle from zero" walkthrough inside the app.

---

## 2. The interface at a glance

| Area | What it does |
|------|--------------|
| Menu bar | `File`, `Progetto` (Project), `Strumenti` (Tools), `Aiuto` (Help) |
| Effects tree (left) | `Effetti del file` — every effect in the dictionary, expandable to **Effect → Emitter → Particle** |
| Inspector (right) | Parameters of the selected node, grouped: emission, movement, force/gravity, size, opacity, growth over time |
| Viewport (center) | Live Direct3D 11 preview. Camera presets `Fronte` / `Lato` / `Alto` (front/side/top), `Inquadra` (frame), move/rotate/scale gizmos, day/night toggle, world reference with a ~1.8 m ped for scale |
| Timeline | Scrub, seek, and replay the simulation (`Apri la Timeline ↓`) |

**Shortcuts:** `Ctrl+S` save · `Ctrl+Z` undo · `Ctrl+Y` redo.

The YPT data model, in one line: a **dictionary** (the `.ypt` file) contains
**effect rules**; each effect owns one or more **event emitters**; each emitter
spawns one **particle rule**, which carries the textures, shader behaviour,
and keyframed curves you edit.

---

## 3. Creating and opening effects

### New

- **Nuovo (da template)** — start from the gameplay template library
  (fire, smoke, sparks, healing aura, projectile, ritual circle, …). Each
  template is a complete working effect you then reshape.
- **Creazione guidata (preset wizard)** — step-by-step: pick a base preset
  (Fuoco, Fumo, …), name the dictionary/effect/emitter/particle, and the tool
  builds a valid starting effect.
- **Progetto vuoto (empty project)** — a blank dictionary; add
  `+ Aggiungi Effect`, `+ Aggiungi Emitter`, or use
  `Aggiungi coppia emitter+particella` (adds a linked emitter + particle pair
  in one click).

### Open

- **Apri…** — `.ypt` (binary RSC7) or `.ypt.xml` (CodeWalker XML). Both
  round-trip: whatever you open can be saved back to either format.
- **Apri progetto… / Apri recenti** — PPRP project files remember your
  session (textures, models, settings) beyond the raw YPT data.
- **Apri dal gioco (RPF)…** — browse effects straight out of the game
  archives (next section).

On load the tool runs automatic repairs where needed
(`RiparazioniUltimoCaricamento` reports what was fixed) and auto-imports any
matching textures it can find.

---

## 4. Browsing vanilla effects from the game (RPF)

The richest source of reference material is the game itself: ~90+ particle
dictionaries — `core.ypt` (hundreds of base fire/smoke/explosion/water
effects), `scr_*` (mission/script effects), `wpn_*`, `veh_*`, `env_*`,
`des_*`, `cut_*` (cutscenes), `proj_*`.

1. Open **Apri dal gioco (archivi RPF)** and press **Monta** (mount).
   - The tool auto-detects, in order: an extracted
     `PPRP-Vanilla-Extract\game-files` vanilla library (a lightweight local
     copy of just the particle archives — preferred, so the real install is
     never touched), any GTA V install under `<drive>\GAME\`, and the
     Rockstar/Steam/Epic registries/manifests. If nothing is found, point it
     at a folder manually.
   - Both **Legacy** (`GTA5.exe`) and **Enhanced / Gen9**
     (`GTA5_Enhanced.exe`) installs are supported. The folder must contain the
     game's `.rpf` archives and the executable (used to derive the archive
     keys).
   - The chosen folder is remembered for next time.
2. Filter the list with any substring of the `.ypt` path (e.g. `scr_`,
   `core`, `wpn`) and **Apri selezionato** (open selected).
3. Textures are resolved automatically and embedded into your editing
   session: the effect's own embedded textures load directly, missing shared
   textures are searched in the game's texture dictionaries (particle
   archives first) and in `core.ypt`'s embedded texture dictionary — where
   the shared `ptfx_*` glows, smokes, and embers actually live. Results are
   cached, so after the first lookup repeated loads are instant.

Everything is **read-only**: the tool never writes into game archives. Opened
vanilla effects become an unsaved editor session you can save as XML, binary,
or a project.

No game installed on this machine? The repository ships a small offline
library in `fixtures/vanilla-sources/` (6 vanilla dictionaries as XML) and
`fixtures/vanilla-textures/` (37 vanilla DDS textures) you can open directly.

---

## 5. Editing

### Tree operations

Right-panel / tree actions: add, duplicate (`Duplica`), rename (`Rinomina…`),
remove effects and rules; `Collega EventEmitter…` links an emitter to an
effect, `Scollega` unlinks it; `Rinomina dizionario` renames the dictionary
itself (this is the asset name FiveM will use).

### Parameters and curves

The inspector groups the most-used parameters: particles per second, single
burst emission, lifetime, gravity, air resistance, force/impact, size,
luminous intensity, opacity, growth over time. Anything time-varying is a
**keyframed curve**: pick `Lineare`, `Bézier`, or `Step` interpolation, insert
and drag keyframes directly in the curve editor, and watch the viewport update
live.

**Domains** (the emission volume: point source, box extents, radius) have
on-screen guides (`Mostra guida dominio`) and can be transformed with the
viewport gizmos or numerically per axis.

`Undo`/`Redo` cover every edit. `Anti Keddon (stampa parametri)` prints the
merged parameter set of the selection — useful when comparing against a
vanilla reference.

### Evolutions

`ElencaEvoluzioni / Aggiungi evoluzione` manage YPT *evolutions* — parameter
sets that blend by a game-driven value (e.g. strength of an explosion).
Advanced, but fully editable.

---

## 6. Textures, atlases, flipbooks

- **+ Importa texture… (.dds)** imports a DDS into the effect's embedded
  texture dictionary. On import the tool asks *"How should this texture
  behave?"* — pick the blending family that matches the art (additive glow,
  alpha-blended smoke, …). A wrong family is the single most common cause of
  "my texture looks wrong in game".
- Assign a texture to the selected particle, or remove it, from the particle
  inspector.
- **Strumenti → Flipbook / Atlas debug…** — inspect sprite-sheet textures,
  configure the grid (`gridX`/`gridY`, auto-grid detection), validate frame
  counts, and preview the animation. `Cerca atlas vanilla per griglia` finds
  vanilla atlases matching a given grid; FiveM atlas presets are built in.
- Vanilla DDS references live in `fixtures/vanilla-textures/`
  (`ptfx_*` glows, smokes, embers, tracers, muzzles, debris sheets…) — they
  are proven, game-ready starting points.

---

## 7. Models (mesh emitters)

Particles can be emitted from a mesh surface instead of a point/box:

- **Carica .obj…** imports a Wavefront OBJ as the emitter model.
- **Importa modello dal gioco** pulls a `.ydr` drawable straight from mounted
  RPF archives (searchable list, like the YPT browser).
- `Rimuovi modello` reverts to the plain domain.

---

## 8. Mathematical positioning

**Strumenti → Posizionamento matematico…** makes the spawn point *trace a
shape over time* (light-painting): pick a pattern, the particle count, and the
effect duration — the tool computes spawn rate and lifetime for you (both stay
editable). `Applica + anima` applies and previews immediately. Great for
rings, spirals, and ritual-circle style effects.

---

## 9. Validation

**Apri validazione completa** runs the document validator: structural
problems, suspicious parameter combinations, flipbook/grid mismatches, and
template conformance, each with severity and a pointer to the offending node.
`Rivalida` / `Re-check` re-runs after fixes. Validate before every export —
a dictionary that validates clean loads clean in game.

---

## 10. Saving and exporting

| Action | Output |
|--------|--------|
| `Salva progetto` | PPRP project (full session: YPT + textures + models + settings) |
| `Salva XML…` | CodeWalker-compatible `.ypt.xml` |
| `Esporta .ypt binario…` | Game-ready RSC7 `.ypt` |
| `Esporta kit FiveM…` | A complete streamable FiveM resource |
| `Genera ptxclipregions…` | `ptxclipregions.xml` entry for your effect (also included automatically in the FiveM kit) |

### FiveM export kit

The kit produces a ready-to-drop resource:

```text
your_effect/
├─ fxmanifest.lua
├─ stream/
│  └─ your_dictionary.ypt
└─ client (test command)
```

The generated client script demonstrates the exact natives your gameplay code
needs:

```lua
RequestNamedPtfxAsset('your_dictionary')
-- wait until HasNamedPtfxAssetLoaded('your_dictionary')
UseParticleFxAssetNextCall('your_dictionary')
StartParticleFxLoopedAtCoord('your_effect', x, y, z, ...)
-- or StartParticleFxNonLoopedAtCoord / ...LoopedOnEntity / ...OnEntityBone
```

The export dialog shows a **performance budget** for the effect:

- **Light (≤ 60 particles)** — safe for crowded servers.
- **Media** — fine for occasional effects.
- **Quality (> 150 particles)** — heavy; expect a cost on busy servers.

Keep looped ambient effects in the Light band; save Quality for one-shot
showcase moments.

---

## 11. Tips and troubleshooting

- **"GTA non trovato automaticamente"** — the auto-detect found no install;
  browse to the game folder manually (it must contain `GTA5.exe` or
  `GTA5_Enhanced.exe` next to the `.rpf` archives).
- **A texture shows as missing after opening a vanilla effect** — the first
  lookup scans the game's texture archives (particle archives first) with a
  time budget; re-trigger the load once — results are cached per session, and
  texture dictionaries already found resolve instantly.
- **Texture looks wrong in game but fine in the preview** — re-check the
  blending family chosen at import time (section 6).
- **Effect invisible in game** — confirm the dictionary name in
  `RequestNamedPtfxAsset` matches the renamed dictionary exactly, the asset
  streams (`stream/` + `fxmanifest.lua`), and the effect passed validation.
- **Scale sanity** — use the world reference (ped ≈ 1.8 m) in the viewport
  before exporting; most "too big in game" surprises are caught there.
- Settings, recents, and the saved GTA folder can be reset by deleting
  `%APPDATA%\PPRP\ParticleStudio`.
