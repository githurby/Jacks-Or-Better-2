# Crown Table — Jacks or Better

A polished, free-play **Jacks or Better 9/6** video-poker game with two implementations:

- a responsive **browser version** using HTML, CSS, JavaScript, WebGL2 and Web Audio; and
- a **native Windows C++17 version** using OpenGL 3.3, GLFW, GLEW and OpenAL.

The native project includes a Code::Blocks project file at `windows/JacksOrBetter.cbp`. The same structured asset pack powers both builds; the Crown Table monogram is also embedded as the browser favicon and the Windows executable/window icon.

> **Practice only.** Crown Table has no deposits, cash value, or real-money wagering.

## What is included

- `web/` — browser game, responsive layout, inline SVG favicon, card selection, keyboard controls, accessible dialogs, shader effects, saved settings, and sample-based audio.
- `windows/` — native C++17 source, OpenGL renderer, OpenAL audio, embedded Windows icon resource, game-logic tests, CMake project and the requested Code::Blocks `.cbp` project.
- `assets_source/` — the included classic-deck CC0 card PNGs, switchable table scenes, Crown Table app-icon PNG, WAV source cues, and the card-art license.
- `windows/assets.bin` — structured v2 runtime pack containing the complete card deck, three table backgrounds, app-icon PNG and all sound cues. Neither game loads loose runtime images or audio.
- `tools/generate_assets.py` and `windows/tools/pack_assets.cpp` — source-asset generator and pack builder.

## Game rules

1. Start with 1,000 practice credits and choose a bet of 1–5 coins.
2. Deal five cards from a freshly shuffled standard 52-card deck.
3. Hold any cards you want to keep. Draw replaces every unheld card from the remaining deck; it is not reshuffled during the hand.
4. Only the highest-paying final hand wins. The bet is deducted when the hand is dealt. The listed payout is the total credit award.
5. Aces can be high or low for straights, including A-2-3-4-5. There are no jokers or wild cards.

### Full-pay 9/6 paytable

| Winning hand | 1 coin | 2 coins | 3 coins | 4 coins | 5 coins |
|---|---:|---:|---:|---:|---:|
| Royal Flush | 250 | 500 | 750 | 1,000 | **4,000** |
| Straight Flush | 50 | 100 | 150 | 200 | 250 |
| Four of a Kind | 25 | 50 | 75 | 100 | 125 |
| Full House | 9 | 18 | 27 | 36 | 45 |
| Flush | 6 | 12 | 18 | 24 | 30 |
| Straight | 4 | 8 | 12 | 16 | 20 |
| Three of a Kind | 3 | 6 | 9 | 12 | 15 |
| Two Pair | 2 | 4 | 6 | 8 | 10 |
| Jacks or Better | 1 | 2 | 3 | 4 | 5 |

The five-coin Royal Flush is the special **4,000-credit award** (800 credits per coin), not 250 × 5. The 99.54% theoretical return is a reference for optimal max-bet play, not a promise about an individual session.

## Controls

| Input | Action |
|---|---|
| Click a card / `1`–`5` | Toggle hold on that card |
| **Deal / Draw** / `Space` / `Enter` | Deal or draw, depending on the game phase |
| `B` | Cycle bet from 1 through 5, then back to 1 |
| `M` | Set max bet (5 coins) |
| `H` / `U` | Hold all / clear all holds |
| `S` | Toggle sound |
| `T` | Cycle the table background |
| `V` | Toggle visual effects |
| `F1` | Open help and controls |
| `Esc` | Open/close the practice-balance dialog |
| `C` | Open/close the practice-balance dialog (native build) |
| `F11` | Toggle fullscreen where supported |

Mouse controls are also available for the bet, Deal/Draw, cash-out information, background selector and sound/effects/help buttons. The browser saves credits, bet, background, sound and effects preferences locally. The cash-out panel is informational and never transfers money.

## Visual design and shader/video effects

The visual target is a tactile, readable casino table—not a noisy arcade filter. Card artwork and information stay above effects, the responsive layout keeps panels and controls in their own lanes, and buttons use translucent beveled surfaces with clear hover/press depth.

### PNG cards and changeable table backgrounds

- The complete **52-card deck** uses individual, traditional **720 × 1008 PNG face images** from a CC0 standard playing-card deck, plus its matching blue card back. These replace the earlier custom-looking card artwork with familiar, properly indexed playing cards.
- Three **1920 × 1080 PNG table scenes** are included: Emerald, Sapphire and Mahogany. The supplied mahogany-table reference is the included `assets_source/backgrounds/mahogany_table.png`; Mahogany is the default on a fresh install. Change table scenes with the browser menu or native `BG` control / `T` key.
- The selected table artwork is rendered **inside the card-play panel**, kept at its full 16:9 aspect ratio, and scaled down to fit rather than cropped or stretched. The five-card row is centered within the artwork's inner playing field; card size scales to that safe zone, leaving the ornamental rails, round message, held labels, hint and controls clear. Card faces are centered and fully contained within each card.
- Both builds render the PNG cards as readable, correctly proportioned, perspective-flipping objects. Cards are dealt face-down, then turn over one at a time to reveal their values; replacement cards do the same during the draw.
- The metric cards, status area, hand count, bet controls and full payout table are deliberately prominent; the paytable updates for the selected bet and highlights a winning category.
- Card artwork source: [Public-Domain-and-CC0-Playing-Cards](https://github.com/AustinGabriel/Public-Domain-and-CC0-Playing-Cards), under CC0 1.0. The license is included as `assets_source/CARDS_LICENSE_CC0.txt`.

### Shader passes

Both renderers keep the selected table artwork sharp in the center play panel and composite GPU shader effects behind it:

- **Idle plasma background:** a smooth, visibly animated multi-wave plasma field in emerald, teal and amber, with flowing filaments around the fitted table panel. The motion is faster and brighter than the initial pass, without grain or scanline flicker.
- **Deal / draw:** short teal-to-gold ripple and a small particle shimmer. The browser and native builds deal face-down cards, then stagger a 3D-style turn to reveal each face and value.
- **Held cards:** each selected card gets its own smooth shader-generated green/gold halo and thin rim outside the card edge, plus a small lift and clear `HELD` marker. The highlight stays local to held cards rather than washing over the hand or paytable.
- **Small win** (Jacks or Better / Two Pair): a modest green-gold pulse and a highlighted paytable row.
- **Medium win** (Three of a Kind through Full House): a brighter halo and short sparkle pulse.
- **Major win** (Four of a Kind / Straight Flush): wider gold rays and a richer, brief glow.
- **Royal Flush:** the strongest but still short-lived layered gold/blue halo, rays and particles, accompanied by the special payout and a brighter cue.

The native renderer uses GLSL 3.30 with separate background and transparent effect passes; the browser renderer uses WebGL2 canvases behind DOM-based cards and controls. The browser honors `prefers-reduced-motion`; `V` disables event effects without disabling play. If WebGL2 is unavailable, the CSS image background and game UI remain as a fallback.

### Transition sound

The WAV cues bundled in `assets.bin` now layer a soft deck riffle, card slide, individual flip/land sounds, chip/bet clicks, UI taps, a miss cue and three distinct win tiers. A quiet ambient table bed starts after the first user-initiated deal. Sound is optional: mute is available at any time, and the native game stays playable if OpenAL cannot open an audio device.

### Safety and tuning guidance

- Keep overlay opacity low enough that cards, balances, holds and payout values remain readable.
- Avoid rapid full-screen flashing, persistent distortion and blur over text.
- Keep shader work short and GPU-bounded. Browser shaders are capped to a practical device-pixel ratio and follow reduced-motion preferences.
- An optional future enhancement is a native framebuffer bloom chain (bright-pass, horizontal/vertical blur and composite). It is **not** required by the current renderer; any later bloom should be subtle, Royal-only where possible, and profiled on integrated graphics.

## Browser version

No build step or external CDN is required. From the project root, serve the folder so the browser can fetch the shared pack at `windows/assets.bin`:

```bash
cd JacksOrBetter
python3 -m http.server 4173 --bind 0.0.0.0
```

Open `http://localhost:4173/web/`. The browser waits for the asset pack before enabling play and presents a retry screen if loading fails. It uses WebGL2 for the background/effect canvases and Web Audio for the bundled WAV cues.

## Native Windows build

### Requirements

- Windows 10/11, 64-bit
- C++17 compiler (MinGW-w64/MSYS2 or Code::Blocks configured for MinGW)
- GLFW 3, GLEW and OpenAL Soft development libraries
- OpenGL 3.3-capable graphics driver
- CMake 3.20+ for the optional CMake workflow

GLU is not required. Keep the matching 64-bit headers, import libraries and runtime DLLs available to the compiler/loader.

### Rebuild the asset pack

The committed `windows/assets.bin` is ready to use. The card PNGs, table backgrounds and app-icon PNG are included; the generator verifies the CC0 deck, normalizes nonstandard background dimensions, and recreates the WAV cues before rebuilding the pack:

```bash
cd JacksOrBetter
python3 tools/generate_assets.py

g++ -std=c++17 -O2 -Wall -Wextra -pedantic \
  windows/tools/pack_assets.cpp -o pack_assets
./pack_assets assets_source windows/assets.bin
```

The packer prints the entry count, media counts and output size. Runtime builds use the pack only; `assets_source/` is not needed beside the game executable.

### CMake build and tests

```bash
cd JacksOrBetter/windows
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build --config Release
ctest --test-dir build --output-on-failure
```

CMake copies `assets.bin` into the build directory and compiles the Windows icon resource on Windows. The game searches its working directory, the project folder and the executable's nearby folders for the pack.

### Code::Blocks

Open **`windows/JacksOrBetter.cbp`** in Code::Blocks and select Debug or Release. It links GLFW, GLEW, OpenAL and OpenGL and compiles as C++17. `JacksOrBetter.rc` embeds the multi-resolution `resources/crown_table.ico` in the executable; the PNG in `assets.bin` also sets the GLFW window icon. The project file and icon resources are included in the release archive. The native Windows build and Code::Blocks launch have not been exercised in this environment; use the dependency list above when configuring MinGW.

### Optional logic test without CMake

```bash
g++ -std=c++17 -Wall -Wextra -pedantic \
  windows/src/GameLogic.cpp windows/tests/test_game_logic.cpp \
  -o test_game_logic
./test_game_logic
```

The test covers all paying categories, the wheel straight and straight flush, low-pair nonpayment, key payout values and 52-card deck uniqueness.

## `assets.bin` v2 structure

`windows/assets.bin` is a little-endian, named-entry **JOBASSET v2** container. It is uncompressed and self-contained:

- **64-byte header:** 8-byte `JOBASSET` magic, `uint32` version, `uint32` entry count, `uint64` directory offset, `uint64` string-table offset, `uint64` payload-start offset, then 24 reserved bytes.
- **64-byte directory entries:** `uint8` media type, `uint8` flags, `uint16` name length, `uint32` FNV-1a name hash, `uint64` payload offset, `uint64` stored size, `uint64` raw size, `uint32` width, `uint32` height, `uint32` sample rate, `uint16` channels, `uint16` bits per sample, `uint64` absolute name offset, and 8 reserved bytes.
- **String table:** UTF-8 relative names, one name per entry, no terminating NUL required.
- **Payload area:** original PNG or PCM WAV bytes. Type `1` is PNG; type `2` is WAV. `flags=0` means uncompressed, so stored and raw sizes match.

Current pack: **71 entries** — 57 PNGs (52 faces, one card back, three backgrounds and one app icon) plus 14 WAVs. Named entries are grouped as:

```text
cards/2C.png … cards/AS.png   # 52 individual 720×1008 card faces
cards/back.png
backgrounds/emerald_table.png
backgrounds/sapphire_table.png
backgrounds/mahogany_table.png
icons/crown_table.png
ui/button.wav
chips/bet.wav
cards/deal.wav
cards/riffle.wav
cards/slide.wav
cards/land.wav
cards/hold.wav
cards/flip.wav
cards/draw.wav
wins/miss.wav
wins/small.wav
wins/major.wav
wins/royal.wav
ambient/table.wav
```

The loader rejects an invalid header, unsupported version, corrupt offsets, name-hash mismatches, missing deck/background assets, or missing required transition sounds. The PNG/WAV source files are for editing and repacking; the game loads the structured bundle.

## Source layout

```text
JacksOrBetter/
├── README.md
├── assets_source/
│   ├── cards/                 # 52 faces + blue back (PNG), plus card WAVs
│   ├── backgrounds/           # three switchable table scenes (PNG)
│   ├── icons/crown_table.png  # embedded native-window app icon (PNG)
│   ├── CARDS_LICENSE_CC0.txt
│   ├── ui/button.wav
│   ├── wins/                  # miss and three win-tier cues
│   └── ambient/table.wav
├── tools/generate_assets.py
├── web/
│   ├── index.html
│   ├── style.css
│   └── game.js
└── windows/
    ├── CMakeLists.txt
    ├── JacksOrBetter.cbp
    ├── JacksOrBetter.rc       # Code::Blocks executable icon resource
    ├── resources/
    │   ├── crown_table.ico    # multi-resolution Windows app icon
    │   └── JacksOrBetter.rc.in # CMake resource template
    ├── assets.bin
    ├── src/                   # OpenGL game + shared rules
    ├── tests/test_game_logic.cpp
    ├── third_party/stb_image.h
    └── tools/pack_assets.cpp
```

## Validation status

- **Passed:** browser JavaScript and asset-generator syntax checks; C++ game-logic tests; reproducible asset-packer build; full 71-entry v2 bundle verification (names, hashes, offsets, PNG dimensions and source-byte matches); HTML/CSS structure and Code::Blocks XML checks; local HTTP smoke checks; native-source syntax-only compilation against local API stubs; and native panel/card/control spacing checks at supported window sizes.
- **Not yet verified here:** an interactive browser playthrough and a native Windows graphics/audio build or Code::Blocks launch. A stub-based syntax check is not a substitute for building against the real Windows/MinGW graphics and audio libraries listed above.
