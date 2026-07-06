# Solitaire Universe Gameplay Inventory

This document provides a technical inventory of existing gameplay systems within the Solitaire Universe project. It does not redesign or propose new systems; it purely documents the current implementation state.

---

## Story Mode
- **Status**: Active / Implemented
- **Completion %**: 80%
- **Description**: The primary campaign mode where players navigate through a Universe Hub consisting of interconnected Worlds and Chapters. Completing a run unlocks the next node in the progression path.
- **Related scripts**: `UniverseHubController.cs`, `UniverseHubInfoPanel.cs`, `WorldHubConfig.cs`
- **Dependencies**: `WorldHubProgressStore.cs`, `HubGameLaunchContext.cs`

## Daily Anomaly (Daily Challenge)
- **Status**: Active / Partially Implemented
- **Completion %**: 60%
- **Description**: A daily rotating challenge with predefined seeds and rule variations. Players can participate in these runs directly from the Hub.
- **Related scripts**: `UniverseHubDailyBriefing.cs`, `UniverseHubDailyCardView.cs`, `DailyChallengeSet.cs`
- **Dependencies**: `HubGameLaunchContext.cs`, Time/Date management services.

## Progression
- **Status**: Active / Implemented
- **Completion %**: 90%
- **Description**: Tracks player completion of Worlds, Chapters, and overall runs. It dictates node unlocking in the Story Mode and persists chapter-specific progress data (e.g., stars/tasks completed).
- **Related scripts**: `WorldHubProgressStore.cs`
- **Dependencies**: `PlayerPrefs`, `UniverseHubController.cs`

## Runs
- **Status**: Active / Implemented
- **Completion %**: 90%
- **Description**: Represents a single session/attempt of a Solitaire variant. Runs are initialized with specific contexts (Story, Daily, Random) and modifiers, transitioning the player from the Hub to the Game scene and handling post-run resolution (Victory/Failure).
- **Related scripts**: `GameStartContext.cs`, `HubGameLaunchContext.cs`, `GameController.cs`
- **Dependencies**: `IVariantHandler.cs`, `PreMatch_Modifier_Panel`

## Chapters
- **Status**: Active / Implemented
- **Completion %**: 85%
- **Description**: Sub-sections of Worlds within the Story Mode. Each chapter has predefined attributes, difficulty curves, and associated modifiers.
- **Related scripts**: `UniverseHubChapterDetail.cs`, `WorldHubConfig.cs`
- **Dependencies**: `WorldHubProgressStore.cs`

## Seeds
- **Status**: Active / Implemented
- **Completion %**: 95%
- **Description**: The deterministic numerical foundation of card shuffling and dealing. Ensures that the same seed will always generate the exact same deck order and layout for fair challenges and replayability.
- **Related scripts**: `DealGenerator.cs`, `Deck.cs`, `FastSeedGenerator.cs`, `SeedPoolManager.cs`
- **Dependencies**: `System.Random`, `GameStartContext.cs`

## Random Seed Mode
- **Status**: Active / Implemented
- **Completion %**: 90%
- **Description**: A mode or state where the game utilizes dynamically generated, non-predefined seeds, providing infinite replayability outside of the strict deterministic Story Mode seeds.
- **Related scripts**: `GameStartContext.cs`, `DealGenerator.cs`
- **Dependencies**: `Deck.cs`, `GameController.cs`

## Hint System
- **Status**: Active / Implemented
- **Completion %**: 75%
- **Description**: Assists players by highlighting valid next moves. The implementation varies by Solitaire variant (e.g., finding pairs in Pyramid vs. consecutive stacks in Klondike).
- **Related scripts**: `UnifiedHighlightManager.cs`, `PyramidHintOverlay.cs`
- **Dependencies**: `IVariantHandler.cs`, `Card.cs`

## Undo
- **Status**: Active / Implemented
- **Completion %**: 85%
- **Description**: Allows the player to reverse their most recent card movements. Employs a command-pattern-like stack to store state changes and reverse physical movements/logical placements.
- **Related scripts**: `UndoMovement.cs`
- **Dependencies**: `GameController.cs`, `FlightManager.cs`, `Card.cs`

## Statistics
- **Status**: Active / Basic Implementation
- **Completion %**: 40%
- **Description**: Tracks fundamental player metrics such as games played, games won, current win streak, best win streak, and mastery XP per variant. Currently heavily reliant on simple key-value storage.
- **Related scripts**: `AnalyticsManager.cs`, `GranularDataEraserPanel.cs`
- **Dependencies**: `PlayerPrefs`, `GameController.cs`

## Achievements
- **Status**: Not Started / Planned
- **Completion %**: 0%
- **Description**: System to reward players for reaching specific milestones or performing unique actions. Currently, no dedicated structural framework exists for this in the codebase.
- **Related scripts**: N/A
- **Dependencies**: N/A
