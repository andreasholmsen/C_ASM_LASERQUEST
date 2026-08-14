# LaserQuest — Frequency-Based Laser Tag on STM32

> Embedded laser tag scoring system: each pistol fires at its own frequency, and a fixed-point DFT in C and ARM assembly identifies who scored the hit.

## About

Student project from INSA Toulouse (3rd year MIC, "BE CHTI" design lab). The game supports six laser pistols, each modulating its beam at a distinct frequency between 85 and 120 kHz. A sensor signal is sampled by the STM32's ADC via DMA (64 samples at 320 kHz), and a single-bin 64-point DFT is evaluated at each player's frequency bin to detect hits, assign scores, and trigger a hit sound played through PWM.

The signal chain was first designed and validated in MATLAB/Simulink (Part 1), then implemented on an STM32F103RB microcontroller in C and ARM Thumb assembly (Part 2). The DFT uses fixed-point arithmetic (Q4.12 samples, Q1.15 cosine/sine tables) for speed, replacing an earlier floating-point version. The project received 16/20; the final physical assembly was not completed in time.

## Tech stack / Hardware

- **MCU:** STM32F103RB (ARM Cortex-M3, 72 MHz) on the INSA lab board
- **Languages:** C and ARM Thumb assembly (sound playback and PWM output in `GestionSon.s`, DFT and game logic in C)
- **Toolchain:** Keil µVision (MDK-ARM) with Arm Compiler 6, using the course-provided `DriverJeuLaser` library (clock, GPIO, ADC, DMA, timers, PWM)
- **Simulation & design:** MATLAB/Simulink (`laser.m` / `simlasergame.slx` for the six-frequency game signal, `ScriptDFT.m` / `SimulDFT.slx` for DFT validation), plus Bode diagram analysis of the analog front end

## Build & run

All sources live in `lasergame.zip` (kept as submitted, with weekly snapshots).

1. Unzip `lasergame.zip`.
2. **Part 2 (embedded):** open a `GestionSon.uvprojx` project in Keil µVision — the most complete version is under `Partie 2 (asm-c)/Uke 7 1_PjetKEL_GestionSon/`. Two build targets are provided: `Simulation` (µVision simulator) and `CibleSondeKEIL` (flash to the STM32F103RB via a Keil debug probe).
3. **Part 1 (simulation):** run `laser.m` or `ScriptDFT.m` in MATLAB with Simulink installed; they drive the accompanying `.slx` models and plot the sampled signals and their spectra.

## Project structure

- `Partie 1 (elec)/` — MATLAB/Simulink models, DFT simulation, Bode analysis, and design notes for the analog/signal part
- `Partie 2 (asm-c)/` — Keil µVision projects (weekly snapshots `Uke 1` through `Uke 7`, Norwegian for "week"), each containing:
  - `Principal/` — `principal.c` (game loop: DMA capture, per-player DFT, threshold detection, scoring) and `DFT.c` (fixed-point single-bin DFT)
  - `GestionSon/` — `GestionSon.s`, ARM assembly for hit-sound playback via PWM
  - `ServiceDFT/` — fixed-point lookup tables (`CosSin_Fract_1_15.h`, `Signal_4_12.h`)
  - `Driver/` — course-provided `DriverJeuLaser` peripheral library
