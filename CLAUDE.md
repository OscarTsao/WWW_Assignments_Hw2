# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an interactive Maze Game built as a single-page application using vanilla HTML/CSS/JavaScript (no frameworks). The game features:
- Procedurally generated mazes using Depth-First Search (DFS) algorithm
- Multiple difficulty levels (10x10, 15x15, 25x25, 38x38)
- Random event system that modifies gameplay
- Optional foggy mode with limited vision
- Recording and replay functionality
- Keyboard-based movement controls (arrow keys)

## Running the Application

Open `WWW_Hw2.html` directly in a web browser:
```bash
# Using default browser
open WWW_Hw2.html        # macOS
xdg-open WWW_Hw2.html    # Linux
start WWW_Hw2.html       # Windows

# Or with specific browser
firefox WWW_Hw2.html
google-chrome WWW_Hw2.html
```

No build step or development server is required - this is a static HTML file.

## Architecture

### Single-File Structure
All code is contained within `WWW_Hw2.html`:
- **Lines 6-141**: CSS styles for UI components, maze cells, and visual effects
- **Lines 193-667**: JavaScript application logic

### Core Systems

**Maze Generation (lines 246-304)**
- Uses DFS algorithm with recursive backtracking
- Each cell tracks visited state and four walls (top, right, bottom, left)
- Walls are removed between cells to create paths

**Player Position & Movement (lines 465-529)**
- Player starts at a random corner, destination is diagonal opposite corner
- Movement controlled via arrow keys with wall collision detection
- Special events can temporarily enable wall-passing

**Event System**
- Events loaded from `events.json` (4 event types)
- Event effects: vision modification, wall passing, position reset
- Events placed randomly on empty cells (configurable 0-8 events)

**Recording & Replay (lines 630-649)**
- Records player movements as `{x, y}` coordinates
- Also records vision changes as `{type: "vision", visionRange, steps}`
- Replay animates recorded moves at 300ms intervals

**Foggy Mode (lines 411-463)**
- Limited vision range (default 3x3 area around player)
- Always shows start and destination positions
- Vision range can be modified by events (1x1 to 5x5)

### Game State Variables (lines 194-208)
- `maze`: 2D array of cell objects with walls and visited flags
- `playerPosition`, `destinationPosition`: {x, y} coordinates
- `recording`, `recordedMoves`: replay system state
- `foggyMode`, `visionRange`, `visionEffectSteps`: fog system state
- `events`, `eventsData`: event system state

## Key Implementation Details

**Cell Coordinates**: Uses `data-x` and `data-y` attributes on DOM elements to track grid positions.

**Wall Representation**: Each cell has boolean flags for four walls. Movement checks current cell's walls in the movement direction.

**Event Triggers**: After each move, `checkEvent()` (line 533) checks if player landed on an event cell, triggers the effect, and removes the event.

**Win Condition**: Checked after each move (line 615). Reaching destination triggers an alert and automatically starts a new game after 1 second.

## Assets

Required image files in `Images/` directory:
- `key.png` - Player icon
- `home.png` - Destination icon
- `dice.png` - Event marker
- `fog.jpg` - Fog overlay texture
- `Replay.png` - Replay button icon

## Event Configuration

Events are defined in `events.json` with structure:
```json
{
  "id": number,
  "name": string,
  "description": string,
  "effect": "increase_vision" | "reduce_vision" | "pass_through_wall" | "reset_position",
  "duration": number,
  "value": number
}
```

To add new event types, update both `events.json` and the switch statement in `triggerEvent()` (line 565).
