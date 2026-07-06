# Story Audit

## Narrative Purpose

目前 Narrative (叙事) 层面的主要目的是为经典的纸牌游戏赋予高沉浸感的包装，将原本互不相关的独立牌局通过“恢复宇宙秩序”的抽象概念串联起来。叙事不依赖大量的长篇文本或角色对话，而是通过视觉符号、极简的引导语以及星图解锁的过程，让玩家感受到在星际间探索、逐步点亮未知世界的成就感与责任感。

---

# Current Narrative Journey

Launch
↓
Intro (展示初始宇宙状态与 Logo)
↓
Universe Hub (以宏观视角审视星系)
↓
World Selection (接触不同的游戏变体/星球)
↓
Chapter Node (了解该阶段的挑战环境)
↓
Gameplay (执行牌局，即修复秩序的过程)
↓
Completion (获得奖励，视觉上的能量回馈)
↓
Universe Progress (返回大厅，看到星系因玩家的行动而逐渐亮起)

---

# Narrative Structure

目前项目中已实现的叙事与世界观分层包含：
- **Intro (引言)**: 启动时的简短演出，奠定神秘、深邃的基调。
- **Universe (宇宙)**: 宏观的容器，代表所有游戏模式的总和。
- **Core (核心)**: 宇宙的能量发源地，视觉上暗示一切挑战的源头。
- **Worlds (世界)**: 具体的 Solitaire 变体（Klondike, Spider 等），在叙事上被包装为不同的星球或抽象美德。
- **Chapters (章节)**: 世界内部的阶段性旅程。
- **Codex (图鉴/百科)**: 提供关于该世界（玩法变体）的背景描述与规则说明。
- **Daily Anomaly (每日异常)**: 包装了每日挑战任务，在叙事上视为宇宙中临时出现的能量波动或时空异常。
- **Tutorial Narrative (新手教程)**: 包含在 `TutorialManager` 中的操作指引文本，带有轻微的引导者口吻。

---

# Narrative Systems

## Intro
- **Purpose**: 将玩家从现实环境平稳过渡到游戏的宇宙设定中。
- **Current Player Experience**: 观看一段短动画或 Logo 浮现序列，带有环境音效，无大量旁白。
- **Current Implementation**: 由 `UniverseHubIntroController` 控制的过场序列。
- **Completion Status**: Mostly Complete
- **Estimated Completion %**: 90%
- **Dependencies**: UI, Audio.
- **Related Scripts**: `UniverseHubIntroController.cs`
- **Related Assets**: Logo Prefabs, Transition Timelines.
- **Known Constraints**: 叙事性偏弱，缺乏交代具体冲突（为什么要打牌）的背景文本。

## Codex (World Detail)
- **Purpose**: 为玩家提供特定世界的背景故事与规则。
- **Current Player Experience**: 点击信息按钮，呼出面板，阅读该世界代表的含义及玩法说明。
- **Current Implementation**: 透过 `UniverseHubWorldCodexPanel` 展示本地化文本。
- **Completion Status**: Mostly Complete
- **Estimated Completion %**: 80%
- **Dependencies**: Localization, Hub.
- **Related Scripts**: `UniverseHubWorldCodexPanel.cs`, `VariantLocalization.cs`
- **Related Assets**: 本地化字符串表 (CSV/JSON 格式通过 `LocalizationManager` 载入)。
- **Known Constraints**: 文本表现形式单一（仅有滚动文本），缺乏插画或配音支持。

## Tutorial Narrative
- **Purpose**: 在新手局中通过友好的提示教授基本规则。
- **Current Player Experience**: 画面变暗，高亮特定卡牌，屏幕上出现简短指令（如 "Drag the card here"）。
- **Current Implementation**: `TutorialManager` 和 `TutorialPrompt` 协调步骤和文本显示。
- **Completion Status**: Mostly Complete
- **Estimated Completion %**: 75%
- **Dependencies**: Gameplay, Localization.
- **Related Scripts**: `TutorialManager.cs`, `TutorialPrompt.cs`
- **Related Assets**: 指引遮罩 Prefab，多语言文本。
- **Known Constraints**: 纯功能性教学，缺乏叙事角色的包装（例如没有“导师”或“AI 助手”的人设）。

## Daily Anomaly
- **Purpose**: 为每日任务提供合理的设定背景。
- **Current Player Experience**: 在大厅看到名为 "Daily Anomaly" 的简报，暗示这是宇宙中的异常事件。
- **Current Implementation**: 仅通过标题名称及 `UniverseHubDailyBriefing` 面板的视觉设计传达。
- **Completion Status**: Partial
- **Estimated Completion %**: 50%
- **Dependencies**: Hub, Gameplay.
- **Related Scripts**: `UniverseHubDailyBriefing.cs`
- **Related Assets**: Anomaly 图标，警告/特殊的 UI 颜色（可能与主题系统联动）。
- **Known Constraints**: 除了标题为 Anomaly，缺乏更深层的剧情支撑（例如异常是如何产生的）。

---

# Narrative Progression

Intro (建立基调)
↓
Universe (呈现总体目标：充满未知的暗淡星图)
↓
World (选择特定的探索方向/游戏模式)
↓
Chapter (面对逐渐增加的难度/环境挑战)
↓
Run (沉浸于战术操作中)
↓
Completion (胜利后获得强烈的视觉能量反馈)
↓
Hub (星球变亮、能量线连通，玩家直观感受到自己对这个宇宙的“治愈”与推进)

---

# Current World Building

目前已实现的世界观要素（基于现状文档推导，未添加额外虚构）：
- **Universe**: 一个抽象的、以星座和轨道为视觉基础的空间。
- **Core**: 宇宙中心脉动的能量源。
- **Worlds**: 散布在宇宙中的节点，每个节点实际上代表一种接龙变体（已知包含 Klondike、Spider、TriPeaks、FreeCell、Pyramid）。
- **Codex**: 存储世界基本信息的系统面板。
- **Daily Anomaly**: 被描述为每日异常的特殊挑战。
- **Chapter Descriptions**: 章节层面可能存在的简短命名或描述（依赖本地化表）。

---

# Current Emotional Journey

- **Mystery (神秘感)**: 打开游戏，面对暗色调的宇宙与发光节点时的未知感。
- **Discovery (探索与发现)**: 解锁新 World 或新 Chapter 时的视觉解锁反馈。
- **Responsibility (责任/专注)**: 沉浸在具体牌局中，通过解题来完成当前节点的修复。
- **Progress & Achievement (进度与成就感)**: 通关后能量线亮起、进度条/星星充满时的满足感，看着大厅从暗淡变得充满活力。

---

# Narrative Assets

- **Timeline**: `UniverseHubIntroController` 相关的序列/动画状态机。
- **Localization**: 由 `LocalizationManager`、`VariantLocalization.cs` 管理的文本键值对（Key-Value Tables）。
- **Dialogue**: 无显式对话文本，仅有 Tutorial 提示与系统描述语。
- **Images**: 宇宙背景、星球图标、星图 UI 素材。
- **Videos**: 无。
- **Codex**: `UniverseHubWorldCodexPanel` 相关的界面与预制体。
- **UI Panels**: 各类承载标题和描述的界面（Chapter Detail, Daily Briefing）。
- **Fonts**: 配合深邃氛围的现代化无衬线字体（TMP Font Assets）。
- **Voice**: 无。
- **Audio**: 环境音、UI 交互音、卡牌音效（共同营造宏大且空灵的氛围）。

---

# Current Strengths

- **抽象且克制**：没有强加累赘的长篇大论，世界观依靠强大的视觉隐喻（点亮星图、连结能量线）自然传递给玩家，不打断核心打牌心流。
- **高度集成化**：叙事包装与 UI 架构（Hub）完全融合，World 与 Variant 的概念绑定得非常紧密。
- **本地化支持**：底层文本通过 `UILocalizationBinder` 与 `LocalizationManager` 进行了完全的分离，支持多语言扩展。

---

# Known Constraints

- 缺乏“Ending（结局）”反馈：玩家完成所有章节后，宇宙会发生什么变化，目前缺乏顶层的最终叙事收尾。
- 文本载体单一：世界观传递极度依赖 Codex 面板和节点命名，缺乏关卡内环境叙事（如特殊卡牌说明、场景物件互动）。
- 角色缺失：宇宙中没有具体的 NPC、导师或对手，使得部分玩家可能觉得设定过于冷淡或缺乏情感连结锚点。

---

# Overall Assessment

- **Narrative maturity**: 中低。世界观处于骨架阶段，依赖美术堆砌氛围，文本叙事较薄弱。
- **World-building maturity**: 中等。已确立了“宇宙 - 星球(变体) - 轨道(章节)”的核心逻辑。
- **Player immersion**: 高。得益于美术、动画与音效的协同，即使没有复杂剧情，氛围沉浸感依然很强。
- **Production readiness**: Beta 状态。核心包装已能说服玩家，但需要填充更多细节文案。
- **Major completed milestones**: 确定了以 Universe Hub 为中心的环境叙事框架，实装了教程文本系统与 Codex 框架。
- **Major missing milestones**: 整体通关结局动画/演出、各个世界的具体深度介绍文本实装、每日异常设定的深度拓展。
