# Universe Hub Audit

## Purpose

The Universe Hub serves as the primary meta-game interface and main menu for Solitaire Universe. Its product purpose is to provide a visually striking, interconnected galaxy where players can visually comprehend their progression, access different Solitaire variants (Worlds), and select specific challenges (Chapters and Runs). It acts as the central connective tissue between all distinct gameplay sessions.

---

# Current Player Journey

Launch
↓
Intro (Cinematic/Logo Sequence)
↓
Universe Hub (Global View)
↓
Select World Node
↓
World Detail / World Home
↓
Chapter Node Selection
↓
Chapter Detail / Run Selection
↓
Gameplay (GameScene)
↓
Results (Victory/Failure Popup)
↓
Hub (if returning) or Next Run

---

# Hub Structure

The current Hub hierarchy is structured in a 3D-to-2D hybrid space with the following core components:
- **Core**: The central focal point of the Universe Hub, pulsating and acting as the origin of energy.
- **World Nodes**: Interactive planetary bodies representing different Solitaire Variants (e.g., Klondike, Spider).
- **Chapter Rings**: Orbiting tracks around World Nodes containing individual Chapter challenges.
- **Energy Lines**: Visual connective tissue linking the Core to Worlds, showing unlock paths and energy flow.
- **Navigation**: Swipe/drag-based panning across the star map, with tap-to-focus zooming on specific nodes.
- **Camera**: An orthographic or constrained perspective camera managed by `UniverseHubController` or `UniverseHubMotionController`.
- **Background & Nebula**: Layered visual elements providing depth, utilizing parallax and particle systems.
- **Theme System**: Dynamic palette switching based on the selected World (`UniverseThemePalette`).
- **Panels & HUD**: Docked overlays (Info, Settings, Shop) and transitional panels (Chapter Detail, World Codex).
- **Transitions**: Smooth full-screen overlays (`UniverseHubTransitionOverlay`) bridging Hub and Game scenes.

---

# Hub Systems

## Core
- **Purpose**: Serve as the visual origin of the player's journey and energy flow.
- **Current Player Experience**: A glowing central object that pulses rhythmically in the background of the hub map.
- **Current Implementation**: Implemented via `UniverseHubCorePulse.cs` with scaling and alpha lerping.
- **Completion Status**: Complete
- **Estimated Completion %**: 100%
- **Dependencies**: Progress Visualization.
- **Related Scripts**: `UniverseHubCorePulse.cs`
- **Related Prefabs**: `HubCore_Prefab` (Assumed)
- **Related ScriptableObjects**: N/A
- **Related Scenes**: `UniverseHub`
- **Known Constraints**: Static position; pulse logic is purely visual and not directly tied to complex audio spectrum data.

## World Nodes
- **Purpose**: Represent selectable game variants (Worlds) and their macro progression.
- **Current Player Experience**: Players tap a World Node to zoom in and access its specific Chapter Rings. Unlocked nodes glow; locked nodes are dimmed.
- **Current Implementation**: Handled by `WorldNodeView`, listening to taps and dispatching events to the `UniverseHubController`.
- **Completion Status**: Complete
- **Estimated Completion %**: 95%
- **Dependencies**: Hub Navigation, Progression.
- **Related Scripts**: `WorldNodeView.cs`, `WorldThemeGlowView.cs`
- **Related Prefabs**: `WorldNode_Prefab`
- **Related ScriptableObjects**: `WorldHubConfig`
- **Related Scenes**: `UniverseHub`
- **Known Constraints**: Layout relies on absolute or manually authored positions in the Canvas/World space.

## Chapter Rings
- **Purpose**: Group and present Chapter nodes orbiting a specific World.
- **Current Player Experience**: After focusing on a World, players see sub-nodes (Chapters) arranged in a ring, tracking completion via stars.
- **Current Implementation**: `UniverseHubChapterRing` manages the radial layout and rotation of `UniverseHubChapterNodeView` instances.
- **Completion Status**: Complete
- **Estimated Completion %**: 90%
- **Dependencies**: World Nodes, Hub Navigation.
- **Related Scripts**: `UniverseHubChapterRing.cs`, `UniverseHubChapterNodeView.cs`
- **Related Prefabs**: `ChapterRing_Prefab`
- **Related ScriptableObjects**: `WorldHubConfig`
- **Related Scenes**: `UniverseHub`
- **Known Constraints**: Fixed radial math limits how many chapters can comfortably fit without overlapping on smaller screens.

## Energy Lines
- **Purpose**: Visually map the connections and progression path between the Core, Worlds, and Chapters.
- **Current Player Experience**: Glowing lines connect the UI. Unlocked paths show animated flowing energy.
- **Current Implementation**: `UniverseHubEnergyLines` and `EnergyLineFlow` use Unity's LineRenderer or Canvas lines with animated UV/materials to simulate flow.
- **Completion Status**: Mostly Complete
- **Estimated Completion %**: 85%
- **Dependencies**: Core, World Nodes.
- **Related Scripts**: `UniverseHubEnergyLines.cs`, `EnergyLineFlow.cs`
- **Related Prefabs**: `EnergyLine_Prefab`
- **Related ScriptableObjects**: N/A
- **Related Scenes**: `UniverseHub`
- **Known Constraints**: Dynamic re-routing is not fully automated; endpoints must be carefully anchored.

## Progress Visualization
- **Purpose**: Communicate unlock status and mastery to the player at a glance.
- **Current Player Experience**: Locked nodes are greyed out. Completed runs fill progress bars or light up stars. 
- **Current Implementation**: `WorldHubProgressUtility` queries the store and updates UI elements like `UniverseHubRunProgressView`.
- **Completion Status**: Mostly Complete
- **Estimated Completion %**: 90%
- **Dependencies**: Save Data (`WorldHubProgressStore`).
- **Related Scripts**: `UniverseHubRunProgressView.cs`, `WorldHubProgressUtility.cs`
- **Related Prefabs**: Progress bar UI assets.
- **Related ScriptableObjects**: N/A
- **Related Scenes**: `UniverseHub`
- **Known Constraints**: Direct dependency on local PlayerPrefs for visual state updates.

## Hub Camera / Hub Navigation
- **Purpose**: Allow players to frame and focus on different parts of the expansive Hub map.
- **Current Player Experience**: Panning via touch drag. Tapping a node smoothly interpolates the camera (or Canvas pivot) to frame the target.
- **Current Implementation**: `UniverseHubMotionController` handles damping, lerping, and input delegation.
- **Completion Status**: Complete
- **Estimated Completion %**: 95%
- **Dependencies**: World Nodes, Input System.
- **Related Scripts**: `UniverseHubMotionController.cs`, `UniverseHubController.cs`
- **Related Prefabs**: Hub Camera Rig.
- **Related ScriptableObjects**: N/A
- **Related Scenes**: `UniverseHub`
- **Known Constraints**: Boundary clamping requires manual tuning based on device aspect ratio.

## Hub Animation
- **Purpose**: Bring the UI to life with breathing, pulsing, and orbiting motions.
- **Current Player Experience**: Icons slowly rotate, UI elements pulse to draw attention.
- **Current Implementation**: Standalone components (`IconElementPulse`, `IconElementRotator`, `UniverseHubNodeGlowPulse`) drive continuous DoTween or Unity update-based animations.
- **Completion Status**: Complete
- **Estimated Completion %**: 100%
- **Dependencies**: N/A.
- **Related Scripts**: `IconElementPulse.cs`, `IconElementRotator.cs`, `IconElementPathMover.cs`
- **Related Prefabs**: Embedded in node prefabs.
- **Related ScriptableObjects**: N/A
- **Related Scenes**: `UniverseHub`
- **Known Constraints**: High count of simultaneous continuous tweens can slightly impact battery life on low-end devices.

## World Detail / Chapter Detail
- **Purpose**: Present specific context, stats, or challenges before a run begins.
- **Current Player Experience**: Slides in as a panel when a node is selected, revealing "START RUN" buttons and modifier details.
- **Current Implementation**: Managed by `UniverseHubWorldHome` and `UniverseHubChapterDetail`, toggled via UI states in the Controller.
- **Completion Status**: Mostly Complete
- **Estimated Completion %**: 90%
- **Dependencies**: Hub Navigation.
- **Related Scripts**: `UniverseHubWorldHome.cs`, `UniverseHubChapterDetail.cs`
- **Related Prefabs**: `WorldHomePanel_Prefab`, `ChapterDetailPanel_Prefab`
- **Related ScriptableObjects**: N/A
- **Related Scenes**: `UniverseHub`
- **Known Constraints**: Heavy reliance on Canvas enables/disables which can cause minor layout rebuild spikes.

## Daily Anomaly Panel
- **Purpose**: Surface the daily challenge to the player on the main screen.
- **Current Player Experience**: A dedicated card/panel indicating today's special variant and rewards.
- **Current Implementation**: `UniverseHubDailyBriefing` populates `UniverseHubDailyCardView` based on active configurations.
- **Completion Status**: Partial
- **Estimated Completion %**: 60%
- **Dependencies**: Daily Challenge System.
- **Related Scripts**: `UniverseHubDailyBriefing.cs`, `UniverseHubDailyCardView.cs`
- **Related Prefabs**: `DailyBriefing_Prefab`
- **Related ScriptableObjects**: `DailyChallengeSet`
- **Related Scenes**: `UniverseHub`
- **Known Constraints**: UI flow for returning directly to this panel after a daily challenge is incomplete.

## Info Panels
- **Purpose**: Display global user data (settings, shop, codex, profile).
- **Current Player Experience**: Accessible via docked buttons (e.g., side menu or top bar).
- **Current Implementation**: Handled via `UniverseHubInfoPanel`, `UniverseHubSettingsPanel`, `UniverseHubShopPanel`.
- **Completion Status**: Mostly Complete
- **Estimated Completion %**: 85%
- **Dependencies**: UI Controller.
- **Related Scripts**: `UniverseHubInfoPanel.cs`, `UniverseHubSettingsPanel.cs`, `UniverseHubShopPanel.cs`
- **Related Prefabs**: Various Panel Prefabs.
- **Related ScriptableObjects**: N/A
- **Related Scenes**: `UniverseHub`
- **Known Constraints**: Panel depth sorting (Canvas sorting orders) requires careful manual configuration.

## Hub Theme
- **Purpose**: Modify the visual palette to reflect the currently focused World.
- **Current Player Experience**: The background color and button glows shift smoothly when navigating from Klondike to Spider.
- **Current Implementation**: `Theme` folder scripts (likely `UniverseThemePalette` and material tweens).
- **Completion Status**: Mostly Complete
- **Estimated Completion %**: 80%
- **Dependencies**: World Nodes.
- **Related Scripts**: `WorldThemeGlowView.cs` (and Theme data structures)
- **Related Prefabs**: N/A
- **Related ScriptableObjects**: Theme Palette Objects.
- **Related Scenes**: `UniverseHub`
- **Known Constraints**: Hardcoded material assignments in some legacy UI elements resist theme changes.

## Hub Background & Nebula
- **Purpose**: Provide deep, cosmic parallax visual interest.
- **Current Player Experience**: A dark, slow-moving space backdrop with swirling nebulas that reacts to camera panning.
- **Current Implementation**: `UniverseHubBackground` controls parallax layering of images or shaders.
- **Completion Status**: Complete
- **Estimated Completion %**: 95%
- **Dependencies**: Hub Camera.
- **Related Scripts**: `UniverseHubBackground.cs`
- **Related Prefabs**: Background Quad/Canvas layers.
- **Related ScriptableObjects**: N/A
- **Related Scenes**: `UniverseHub`
- **Known Constraints**: High-resolution textures for nebulas increase memory footprint.

## Particle Systems
- **Purpose**: Add dynamic flair (stars, shooting stars, impacts).
- **Current Player Experience**: Ambient drifting stars and occasional shooting stars.
- **Current Implementation**: `UniverseHubStarfield` and `UniverseHubShootingStars` orchestrate Unity `ParticleSystem` components.
- **Completion Status**: Complete
- **Estimated Completion %**: 100%
- **Dependencies**: N/A.
- **Related Scripts**: `UniverseHubStarfield.cs`, `UniverseHubShootingStars.cs`
- **Related Prefabs**: Particle prefabs.
- **Related ScriptableObjects**: N/A
- **Related Scenes**: `UniverseHub`
- **Known Constraints**: Fill rate issues on older mobile GPUs if overdraw becomes too dense.

## Intro & Logo Integration
- **Purpose**: Provide a seamless, branded entry sequence from cold boot.
- **Current Player Experience**: App launches into a cinematic logo sequence that smoothly transitions into the interactive Hub state.
- **Current Implementation**: `UniverseHubIntroController` sequences the initial animations, waits for loading, and yields control to `UniverseHubController`.
- **Completion Status**: Complete
- **Estimated Completion %**: 95%
- **Dependencies**: App Bootstrapper.
- **Related Scripts**: `UniverseHubIntroController.cs`
- **Related Prefabs**: `IntroSequence_Prefab`
- **Related ScriptableObjects**: N/A
- **Related Scenes**: `UniverseHub`
- **Known Constraints**: Intro sequence cannot be easily skipped by rapid tapping in the current implementation.

## Transitions
- **Purpose**: Mask scene loads and provide polished UX between Hub and Game.
- **Current Player Experience**: Fade to black/glass transitions wiping across the screen when starting a run.
- **Current Implementation**: `UniverseHubTransitionOverlay` uses full-screen UI blocks and DoTween.
- **Completion Status**: Complete
- **Estimated Completion %**: 95%
- **Dependencies**: Scene Manager, HubGameLaunchContext.
- **Related Scripts**: `UniverseHubTransitionOverlay.cs`
- **Related Prefabs**: `TransitionOverlay_Prefab`
- **Related ScriptableObjects**: N/A
- **Related Scenes**: `UniverseHub`
- **Known Constraints**: Transition timing must be carefully synced with asynchronous scene loading to avoid frame hitches being visible.

---

# Current Progression Visualization

- **Core brightness**: Pulses at a constant rate; does not currently scale dynamically with total game completion.
- **World brightness**: Locked worlds use a desaturated, low-alpha material. Unlocked worlds feature an active `WorldThemeGlowView` and solid opacity.
- **Chapter Rings**: Appear only when a World is interacted with. Locked chapters on the ring are greyed out.
- **Unlocked Nodes**: Display active "Play" icons or glowing borders.
- **Energy Lines**: Lines leading to locked nodes are dark/static. Lines to unlocked nodes display scrolling UV animations (energy flow).
- **Stars**: Individual Chapter nodes feature UI star containers (`UniverseHubRunProgressView`) that fill in with gold sprites based on run completion metrics.

---

# Current Motion Language

- **Camera movement**: Smooth, damped orthographic panning with eased zooming (Focus states).
- **Core pulse**: Continuous sine-wave scale and alpha modulation.
- **Glow**: Material emission lerping for interactable elements.
- **Rotation**: Slow, continuous Z-axis rotation for planetary nodes and chapter rings (`IconElementRotator`).
- **Particles**: Ambient, slow-moving starfields with localized burst particles on node taps (`UniverseHubNodeImpactPulse`).
- **UI transitions**: Slide-in/slide-out with EaseOutExpo or EaseOutBack for panels (`UniverseHubChapterDetail`).
- **Icon movement**: Hover/floating effects along spline paths or sine waves (`IconElementPathMover`).

---

# Current Visual Language

- **Color palette**: Black and Gold primary, with deep purples, teals, and crimsons serving as World-specific theme accents.
- **Glass UI**: UI panels use semi-transparent dark backgrounds with high-contrast, thin glowing borders and blur (if supported) to emulate frosted glass.
- **Gold accents**: Used strictly for progression (Stars, Coins) and primary Call-to-Action buttons.
- **Typography**: Clean, sans-serif modern fonts (likely relying on customized TextMeshPro assets). High tracking (letter spacing) for headers.
- **Icons**: Minimalist, line-art or flat vector style.
- **World appearance**: Abstract geometric or cosmic representations rather than literal planets (e.g., glowing orbs, geometric cages).
- **Core appearance**: A dense, bright center of light.
- **Background**: Deep space, devoid of distracting bright nebulas in the center to maintain UI contrast.
- **Lighting & Bloom**: Heavy reliance on Unity post-processing Bloom to make the Gold and UI highlights "pop" against the dark backgrounds.
- **Theme support**: The Hub dynamically tints energy lines, glows, and particle colors based on the `UniverseThemePalette` of the active World.

---

# Hub Assets

- **Scenes**: `UniverseHub.unity`
- **Prefabs**: 
  - `UniverseHub_Root`
  - `WorldNode`
  - `ChapterRing`
  - `DailyBriefingCard`
  - `IntroSequence`
- **Sprites**: Glass panel 9-slices, Star icons, UI iconography, Nebula textures.
- **Materials**: Additive particle materials, LineRenderer energy flow materials, UI default/custom shaders.
- **Particles**: `StarfieldAmbient`, `ShootingStar`, `NodeTapBurst`.
- **Fonts**: Primary TMP Font Assets (Sans-serif).
- **Localization**: Hub-specific UI string tables.
- **ScriptableObjects**: `WorldHubConfig`, `DailyChallengeSet`, Theme palettes.

---

# Current Strengths

- Highly modular UI architecture separating node views (`WorldNodeView`, `ChapterNodeView`) from progression data.
- Consistent and performant motion language relying on dedicated micro-scripts (`IconElementPulse`, `IconElementRotator`) rather than heavy Unity Animators.
- Seamless transition handling between the intro sequence, the main map, and the game scene (`UniverseHubIntroController` and `UniverseHubTransitionOverlay`).
- Strong visual hierarchy; the dark aesthetic ensures interactive nodes and progression stars are immediately visible.

---

# Known Technical Constraints

- Heavy utilization of Canvas UI for spatial mapping (World Nodes) can lead to complex rect transform calculations and layout rebuilds if the node count grows significantly.
- Particle systems and high-resolution background parallaxing pose potential fill-rate bottlenecks on minimum-spec mobile devices.
- State management relies heavily on the monolithic `UniverseHubController` to coordinate panel toggles and camera focus, creating tight coupling for UI flow changes.
- Energy line routing is static/manual; procedural generation of paths between nodes is not implemented, requiring manual authoring in the Editor for new Worlds.

---

# Overall Assessment

- **Hub maturity**: High. The structural and navigational paradigms are fully functional and robust.
- **UX maturity**: High. The flow from cold boot to game launch is uninterrupted and logically grouped.
- **Visual maturity**: High. The cosmic aesthetic, bloom, and motion language are consistent and applied globally.
- **Production readiness**: Beta Candidate. The system is ready for live-ops configuration, pending QA on edge-case navigation interrupting.
- **Major completed milestones**: Implementation of the dynamic camera, Intro-to-Hub seamless handoff, World/Chapter nested navigation, and robust `WorldHubConfig` data driving.
- **Major missing milestones**: Deep integration of the Daily Anomaly panel with real backend data, and automated layout tools for scaling to dozens of future worlds without manual Editor placement.
