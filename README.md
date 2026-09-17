# Watt Player

A Game Boy emulator built from scratch in vanilla JavaScript, running entirely client-side in the browser. No frameworks, no external emulator libraries — just a CPU interpreter, a scanline-based graphics renderer, and a real-time audio synthesizer, all in a single self-contained HTML file.

**[Try it live →](#)** *(replace with your hosted link)*

---

## What it emulates

| Component | Details |
|---|---|
| **CPU** | Full SM83 instruction set — all 256 base opcodes and 256 extended (`0xCB`-prefixed) opcodes, decoded generically via bitmask pattern-matching rather than a hardcoded per-opcode table |
| **Graphics (PPU)** | Scanline-accurate rendering: background layer, window layer, and up to 40 sprites (8×8 or 8×16) with correct priority and flipping, driven by the real hardware mode-timing (OAM search → pixel transfer → h-blank → v-blank) |
| **Audio (APU)** | All four original sound channels — two pulse/square channels (with volume envelope and frequency sweep), a programmable wave channel, and a noise channel — synthesized live via the Web Audio API from the game's own sound registers |
| **Cartridges** | ROM-only carts plus MBC1, MBC3, and MBC5 memory bank controllers, supporting games larger than the CPU's native 32KB address space |
| **Interrupts & timers** | VBlank, LCD STAT, timer, and joypad interrupts; DIV/TIMA timer registers |

## Controls

| Game Boy | Keyboard | Touch |
|---|---|---|
| D-pad | Arrow keys | On-screen D-pad |
| A | <kbd>Z</kbd> | On-screen A button |
| B | <kbd>X</kbd> | On-screen B button |
| Start | <kbd>Enter</kbd> | On-screen Start |
| Select | <kbd>Shift</kbd> | On-screen Select |

## Usage

1. Open `gameboy-emulator.html` in any modern browser (Chrome, Firefox, Safari, Edge).
2. Click **Load ROM** and select a `.gb` file you legally own.
3. Play. Audio starts automatically on load (browsers require a user action before playing sound, and clicking "Load ROM" satisfies that).

No build step, no installation, no server required — it's a single static HTML file.

## How it works

- **CPU decoding**: rather than writing ~500 explicit instruction handlers, most opcodes are decoded by their bit pattern (e.g. every opcode of the form `01dddsss` is a register-to-register load, where `ddd`/`sss` select the source and destination). This mirrors how the SM83's instruction encoding is actually structured in hardware.
- **Graphics timing**: the PPU is stepped in lockstep with the CPU's cycle count and transitions through the same four hardware modes real silicon uses, firing interrupts at the correct points so games that rely on precise VBlank/STAT timing behave correctly.
- **Audio synthesis**: instead of emulating the sound chip cycle-by-cycle, a Web Audio callback reads the live state of the sound registers each sample and reconstructs the correct waveform, pitch, and volume in real time — a simpler approach that still produces accurate music and effects for the vast majority of games.

## Known limitations

- **No cycle-exact timing.** Rendering and audio are accurate at the scanline/instruction level, not exact hardware dot-timing — a small number of games using tight mid-scanline raster tricks may render incorrectly.
- **No save persistence.** Cartridge RAM (in-game saves) resets on reload; nothing is written to disk or browser storage.
- **No Game Boy Color support.** DMG (original Game Boy) only.

## Tech stack

Vanilla JavaScript, HTML5 Canvas (2D), Web Audio API. No dependencies.

## Disclaimer

This project does not include or distribute any copyrighted game ROMs. You are responsible for supplying your own ROM files, which you should only use for games you legally own.
