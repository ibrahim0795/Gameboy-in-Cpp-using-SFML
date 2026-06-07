# Gameboy in C++ using SFML

I'm working on an exciting Gameboy project in C++ using SFML, where I've brought together three different games into one package. It's a great way to combine creativity and coding!

Everything is done in C++ using SFML and OOP concepts. Each and every file is connected with each other either through the relationship of Composition or Inheritance.

---

## Table of Contents
1. [Project Overview](#project-overview)
2. [Getting Started & Dependencies](#getting-started--dependencies)
3. [Controls](#controls)
4. [Asset & Directory Structure](#asset--directory-structure)
5. [Core Architecture](#core-architecture)
6. [Game Modules](#game-modules)
   - [Snake Game](#snake-game)
   - [Wordle Game](#wordle-game)
   - [Hangman Game](#hangman-game)
7. [Leaderboard & Persistence](#leaderboard--persistence)

---

## Project Overview

The **Gameboy-in-Cpp-using-SFML** project is a multi-game emulator-style application developed in C++ using the Simple and Fast Multimedia Library (SFML). It serves as a unified platform hosting three distinct classic games: **Snake**, **Wordle**, and **Hangman**.

The project emphasizes Object-Oriented Programming (OOP) principles, utilizing **inheritance** and **composition** to share core systems like input handling, audio management, and leaderboard persistence across all game modules.

### Included Games
- **Snake Game** – A grid-based survival game featuring sprite-sheet animations, directional movement, and growth mechanics.
- **Wordle Game** – A word-guessing puzzle with a virtual QWERTY keyboard, color-coded feedback, and a 100-word dictionary.
- **Hangman Game** – A classic word discovery game with visual figure progression and category-based word selection.

---

## Getting Started & Dependencies

To build and run the project, the following are required:

- **Compiler:** A C++ compiler supporting C++11 or higher.
- **SFML Library:** Used for graphics rendering, audio management, and input handling.
- **Asset Directories:** The executable expects a specific directory structure to load textures, sounds, and fonts (see [Asset & Directory Structure](#asset--directory-structure)).

---

## Controls

### Pause / Unpause

| Game     | Pause Key | Unpause Key |
|----------|-----------|-------------|
| Snake    | `P`       | `Left CTRL` |
| Wordle   | `Space`   | `Left CTRL` |
| Hangman  | N/A (no pause feature) | N/A |

### Global Audio & VFX Controls

| Action                | Key |
|-----------------------|-----|
| Increase Music Volume | `+` |
| Decrease Music Volume | `-` |
| Increase VFX Volume   | `*` |
| Decrease VFX Volume   | `/` |

> Music controls (`+` / `-`) apply to Snake and Wordle only. VFX controls (`*` / `/`) apply to all three games.

### Game-Specific Shortcuts

- **Snake:** `Space` — Manually increase the length of the snake player.
- **Hangman:** `Space` — Skip the introductory sequence.
- **Wordle:** Supports both physical keyboard typing and mouse clicks on the on-screen QWERTY keyboard.

---

## Asset & Directory Structure

All paths are relative to the project root:

| Directory / File      | Description                                              |
|-----------------------|----------------------------------------------------------|
| `Fonts/arial/arial.ttf` | Global typeface used for all UI and game text          |
| `gameboypic/`         | Textures for the main menu and startup sequences         |
| `Gameboysound/`       | Audio files for system startup and main menu             |
| `hangmanpic/`         | Visual assets for Hangman (backgrounds, figure states)   |
| `Hangmansound/`       | Audio effects and music for Hangman                      |
| `snakegamepic/`       | Sprite sheets for the Snake game                         |
| `snakegamesound/`     | Audio effects and music for Snake                        |
| `wordlegamepic/`      | Visual assets for the Wordle game                        |
| `leaderboard.txt`     | Flat-file database for storing high scores               |

### Key Asset Details

- **Startup transition:** `gameboypic/nintendo.png` (Nintendo logo fade-in/fade-out)
- **Snake sprite sheet:** `snakegamepic/snake-graphics_11zon.png` (single atlas for head, body, tail, food, walls)
- **Hangman backgrounds:** `hangmanpic/bg1.png` through `hangmanpic/bg7.png` (one per life lost)
- **Startup sound:** `Gameboysound/nintendosound.ogg`
- **Snake sounds:** `snakegamesound/music/food.ogg`, `snakegamesound/music/gameover.ogg`
- **Hangman sounds:** `Hangmansound/correct.ogg`, `Hangmansound/wrong.ogg`, `Hangmansound/win.ogg`, `Hangmansound/gameover.ogg`

---

## Core Architecture

The application is orchestrated by the `Gameboy` class, which manages the window lifecycle and transitions between game states. The architecture uses both **composition** and **inheritance**:

- **Composition:** `Gameboy` contains `Screen`, `MenuSystem`, `Leaderboard`, `InputSystem`, and `SoundSystem`.
- **Inheritance:** `SnakeGame`, `WordleGame`, and `HangmanGame` all inherit from a common `Game` base class, sharing player data, scoring, and asset loading.

### Class Hierarchy

```
Gameboy
├── Screen          (window management, 1200x800 @ 60 FPS)
├── MenuSystem      (main menu & sub-menu navigation)
├── Leaderboard     (top-10 score persistence)
├── InputSystem     (keyboard abstraction)
├── SoundSystem     (music & SFX management)
├── SnakeGame       ──► inherits Game
├── WordleGame      ──► inherits Game
└── HangmanGame     ──► inherits Game
```

### Module Integration Flow
When a user selects a game from the menu:
1. `isMainMenuActive` is disabled; the specific game's menu flag is enabled.
2. An instance of the chosen game class is created.
3. The game's `run()` / `startGame()` method is called with the shared `sf::RenderWindow`.
4. Upon exit, control returns to the `Gameboy` loop and the main menu is re-enabled.

---

## Game Modules

### Snake Game

A tile-based implementation of the classic arcade game on a **34×23 grid** with a tile size of 35 pixels.

**Key mechanics:**
- Movement occurs in discrete steps every **0.15 seconds**.
- Direction encoding: `0` = Up, `1` = Right, `2` = Down, `3` = Left. 180-degree turns are prevented.
- **Growth:** When food is eaten, `growCount` increments and a new segment is added at the tail.
- **Collision detection:** Checks wall boundaries, wall tiles, and self-collision before each move.
- **Rendering:** Uses a single sprite-sheet atlas with `sf::IntRect` to render head, body, tail, corners, food, and walls.

**Scoring & Game Over:**
- Score increments on food consumption.
- On collision, `gameover.ogg` plays and the score is submitted to the Leaderboard.

**State flow:** `Initializing → Playing ⇄ Paused → GameOver → LeaderboardUpdate`

---

### Wordle Game

A 5-letter word guessing game with a **5×6 grid** (5 letters, 6 attempts) and a 100-word dictionary.

**Color-coded feedback:**
| Color  | Meaning                                      |
|--------|----------------------------------------------|
| Green  | Correct letter, correct position (+2 pts)    |
| Yellow | Correct letter, wrong position (+1 pt)       |
| Gray   | Letter not in word (-1 pt)                   |

**Additional scoring:** +5 points for guessing the correct word.

**Input:** Supports both physical keyboard and mouse clicks on the on-screen QWERTY keyboard.

**Continuous play:** After winning or exhausting 6 attempts, press `Enter` to reset the grid and play again with a new word while keeping the cumulative score.

**Dictionary:** 100 hardcoded five-letter words; word is selected randomly each round.

---

### Hangman Game

A word-guessing game with **6 lives** and category-based word selection (up to 100 words, max 10 characters each).

**Guess outcomes:**
| Outcome   | Score Change | Audio         |
|-----------|-------------|---------------|
| Correct   | +10 points  | `correct.ogg` |
| Incorrect | -5 points   | `wrong.ogg`   |

**Visual progression:** `HangmanFigure` loads 7 background textures (`bg1.png`–`bg7.png`); each wrong guess advances to the next image.

**Easter egg:** If the player name is "Sayori" and lives reach 0, a special sprite is rendered.

**No pause feature.** Press `Space` to skip the intro.

**Win/Loss:**
- Win: `isWordGuessed()` triggers `win.ogg` and leaderboard update.
- Loss: Lives reach 0, triggers `gameover.ogg`.

---

## Leaderboard & Persistence

- **File:** `leaderboard.txt`
- **Format:** Space-separated `Name Score` entries (top 10 scores tracked).
- **Example:**
  ```
  Sayori 60
  ggff 14
  ```
- The `Leaderboard` class reads entries into `ScoreEntry` structs and uses `std::ios::trunc` to overwrite the file with sorted results on save.
