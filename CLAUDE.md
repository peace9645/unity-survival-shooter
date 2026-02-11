# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Unity 6 (6000.3.7f1) 3D survival shooter game based on Unity's official tutorial. Players fight waves of enemies (ZomBear, ZomBunny, Hellephant) using raycast-based shooting while managing health and score.

## Build & Development

- **IDE:** Open `unity-survival-shooter.slnx` in Rider or Visual Studio, or open the project folder in Unity Editor
- **C# version:** 9.0, targeting .NET Standard 2.1
- **Main scene:** `Assets/_CompletedAssets/Scenes/Complete-Game.unity`
- **No test suite exists** — test framework package is included but no test files are written
- **No CI/CD or linting configuration**

## Architecture

Nine MonoBehaviour scripts in `Assets/Scripts/`, organized into three systems:

### Player System (`Scripts/Player/`)
- **PlayerHealth** — Health state (100 HP), damage flash UI, death handling, level restart
- **PlayerMovement** — Currently an empty stub (no implementation)
- **PlayerShooting** — Raycast hitscan on "Shootable" layer, 20 damage/shot, 0.15s fire rate, visual/audio effects

### Enemy System (`Scripts/Enemy/`)
- **EnemyHealth** — Per-enemy health, hit particles, death animation, sinking removal after death
- **EnemyMovement** — NavMeshAgent pathfinding that continuously targets the player
- **EnemyAttack** — Trigger-based melee, 10 damage every 0.5s when player is in range

### Manager System (`Scripts/Managers/`)
- **EnemyManager** — Wave spawning from random spawn points every 3s, stops when player dies
- **GameOverManager** — Triggers "GameOver" animator state on player death
- **ScoreManager** — Static score field with UI text update (score increment in EnemyHealth is commented out)

### Key Patterns
- **Direct cross-component calls:** PlayerShooting calls `EnemyHealth.TakeDamage()`, EnemyAttack calls `PlayerHealth.TakeDamage()`
- **Tag-based discovery:** Enemies find the player via `FindGameObjectWithTag("Player")`
- **Layer-based raycasting:** Shooting uses `LayerMask.GetMask("Shootable")` to filter hits
- **Animator-driven state:** Death, game over, and attack states are driven by Animator triggers ("Die", "Dead", "PlayerDead", "GameOver")
- **State flags:** `isDead`, `isSinking`, `playerInRange` booleans track object state

### Data Flow
```
Player shoots → Raycast on Shootable layer → EnemyHealth.TakeDamage() → death/score
Enemy spawns → NavMesh pathfinding to player → Trigger enter → EnemyAttack → PlayerHealth.TakeDamage()
Player dies → EnemyManager stops spawning, GameOverManager triggers UI
```

## Notable Issues

- `PlayerMovement.cs` is an empty class — movement logic is missing
- Score incrementing is commented out in `EnemyHealth.cs`
- Several health/state checks in enemy scripts are commented out
- `ScoreManager` uses a static field for score, which doesn't reset between scenes without manual handling
