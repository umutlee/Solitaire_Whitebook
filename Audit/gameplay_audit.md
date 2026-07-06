# Gameplay Audit

## Purpose
目前 Gameplay 领域的主要目的是为玩家提供稳定、流畅且公平的纸牌游戏体验。该领域负责管理从大厅（Hub）过渡到具体牌局的流程、维持每局游戏的规则执行（透过各变体 Handler）、确保随机性与确定性（Seed System），并在牌局结束后正确处理胜负结算及保存进度。

---

# Current Gameplay Loop

Launch
↓
Universe Hub (世界视图/章节视图)
↓
Chapter Node Selection (选择关卡节点)
↓
Run Pre-Match (展示当前关卡词缀与难度)
↓
Gameplay (核心牌局，执行特定纸牌变体规则)
↓
Results (胜利或失败弹窗)
↓
Progression (更新世界进度与统计数据)
↓
Hub (如果章节完成) / Next Run (如果直接进入下一局)

---

# Gameplay Systems

## Story Mode
- **Purpose**: 提供线性的关卡解锁体验，玩家按顺序通关世界节点。
- **Current Player Experience**: 玩家在大厅星图中滑动，点击已解锁的星球（World）与章节（Chapter）并开始固定种子的对局。
- **Current Implementation**: 由 `UniverseHubController` 与 `WorldHubProgressStore` 管理节点状态。
- **Completion Status**: Mostly Complete
- **Estimated Completion %**: 85%
- **Dependencies**: Progression, Save & Progress, Seed System.
- **Related Scripts**: `UniverseHubController.cs`, `WorldHubConfig.cs`, `UniverseHubChapterDetail.cs`
- **Related Data**: `WorldHubConfig` (ScriptableObject), PlayerPrefs.
- **Known Constraints**: 强依赖于预先配置的静态数据（`WorldHubConfig`）。

## Daily Anomaly
- **Purpose**: 提供每日轮换的特殊规则挑战。
- **Current Player Experience**: 玩家点击大厅内的每日简报卡片进入特定的每日挑战。
- **Current Implementation**: 使用时间戳和 `DailyChallengeSet` 确定当天的挑战。
- **Completion Status**: Partial
- **Estimated Completion %**: 60%
- **Dependencies**: Progression, Seed System, UI.
- **Related Scripts**: `UniverseHubDailyBriefing.cs`, `UniverseHubDailyCardView.cs`, `DailyChallengeSet.cs`
- **Related Data**: `DailyChallengeSet` (ScriptableObject), System Time.
- **Known Constraints**: 缺乏完整的末端奖励发放与长期的月度统计日历。

## Endless / Replay Mode
- **Purpose**: 允许玩家在不影响主线进度的情况下重新游玩或挑战随机生成的新牌局。
- **Current Player Experience**: 失败后玩家可以选择重玩相同种子（Replay），或在大厅直接开随机局。
- **Current Implementation**: 通过传入不同的 `GameStartContext` 来触发 `GameController` 走随机逻辑。
- **Completion Status**: Mostly Complete
- **Estimated Completion %**: 85%
- **Dependencies**: Seed System, GameController.
- **Related Scripts**: `GameController.cs`, `GameStartContext.cs`
- **Related Data**: N/A.
- **Known Constraints**: Random Seed 模式下可能会出现无法解开的死局（如果 Solver 未强制拦截）。

## Chapters
- **Purpose**: 作为世界（World）的子集，对多个 Run 进行分组并定义阶段性环境。
- **Current Player Experience**: 玩家查看章节内的挑战（Runs）与解锁条件。
- **Current Implementation**: 静态数据结构，进度被记录在 `WorldHubProgressStore`。
- **Completion Status**: Mostly Complete
- **Estimated Completion %**: 85%
- **Dependencies**: Story Mode, Progression.
- **Related Scripts**: `UniverseHubChapterDetail.cs`, `WorldHubConfig.cs`
- **Related Data**: `WorldHubConfig` (ScriptableObject).
- **Known Constraints**: 章节进度直接与单机 PlayerPrefs 强绑定，防作弊能力弱。

## Runs
- **Purpose**: 代表一次独立的纸牌游戏对局。
- **Current Player Experience**: 对局内的卡牌移动、交互及胜负判定。
- **Current Implementation**: 实例化 `IVariantHandler`，通过 `GameController` 进行生命周期管理。
- **Completion Status**: Complete
- **Estimated Completion %**: 95%
- **Dependencies**: Difficulty, Seed System, Undo, Hint.
- **Related Scripts**: `GameController.cs`, `IVariantHandler.cs`, `BaseVariantHandler.cs`
- **Related Data**: `GameStartContext`.
- **Known Constraints**: 对局中内存对象（Card）产生较多，存在轻微垃圾回收（GC）开销。

## Seed System
- **Purpose**: 确保牌组生成的确定性，实现公平挑战和录像功能。
- **Current Player Experience**: 玩家游玩同一关卡时，每次发牌的初始状态完全一致。
- **Current Implementation**: `DealGenerator` 根据给定的 32 位整型种子初始化 `System.Random`。
- **Completion Status**: Complete
- **Estimated Completion %**: 100%
- **Dependencies**: Solvers, DealGenerator.
- **Related Scripts**: `DealGenerator.cs`, `FastSeedGenerator.cs`, `Deck.cs`
- **Related Data**: 预置种子库 / 算法生成。
- **Known Constraints**: 未实现基于 C# 独立实现的 PRNG（使用的是原生的 `System.Random`，跨平台或跨版本升级可能导致随机序列变更）。

## Random Seed
- **Purpose**: 为 Endless 模式或快速游戏提供不重复的牌局。
- **Current Player Experience**: 每次进入该模式都能获得随机发牌。
- **Current Implementation**: 通过时间戳或随机类产生初始种子并馈入 Seed System。
- **Completion Status**: Complete
- **Estimated Completion %**: 100%
- **Dependencies**: Seed System.
- **Related Scripts**: `GameController.cs`, `DealGenerator.cs`
- **Related Data**: N/A.
- **Known Constraints**: 无解率不可控。

## Difficulty
- **Purpose**: 调节牌局的通关难度。
- **Current Player Experience**: 关卡前提示词缀（Modifiers），感受不同的抽牌规则或限制。
- **Current Implementation**: `ModifierRuntimeApplier` 在牌局开始时根据章节配置动态注入难度规则。
- **Completion Status**: Mostly Complete
- **Estimated Completion %**: 80%
- **Dependencies**: Modifiers, Chapters.
- **Related Scripts**: `ModifierRuntimeApplier.cs`, `GameModifierUISetup.cs`
- **Related Data**: Modifier Configurations.
- **Known Constraints**: 难度计算未自动化，需依赖策划手动配置验证。

## Progression
- **Purpose**: 记录玩家的游戏推进状态。
- **Current Player Experience**: 解锁新世界、新章节，看到星星/任务点亮。
- **Current Implementation**: 基于 `WorldHubProgressStore` 和 PlayerPrefs 进行离线存储。
- **Completion Status**: Mostly Complete
- **Estimated Completion %**: 90%
- **Dependencies**: Save & Progress.
- **Related Scripts**: `WorldHubProgressStore.cs`
- **Related Data**: PlayerPrefs keys.
- **Known Constraints**: 本地存档，容易受到本地数据清理的影响。

## Rewards
- **Purpose**: 激励玩家通关。
- **Current Player Experience**: 获得金币（Coins）与宝石（Gems）。
- **Current Implementation**: `CurrencyManager` 处理数值增加。
- **Completion Status**: Partial
- **Estimated Completion %**: 60%
- **Dependencies**: Win Detection.
- **Related Scripts**: `CurrencyManager.cs`
- **Related Data**: `Eco_Coins`, `Eco_Gems` in PlayerPrefs.
- **Known Constraints**: 奖励结算展现逻辑尚未与大厅深度整合，缺少复杂的掉落池（Loot Tables）。

## Statistics
- **Purpose**: 记录玩家的历史表现。
- **Current Player Experience**: 查看胜场、连胜、游玩次数等。
- **Current Implementation**: 基础的 PlayerPrefs 增量统计（`Stats_Played_` 等），以及 `AnalyticsManager` 打点。
- **Completion Status**: Partial
- **Estimated Completion %**: 40%
- **Dependencies**: Save & Progress.
- **Related Scripts**: `AnalyticsManager.cs`, `GranularDataEraserPanel.cs` (Debug)
- **Related Data**: 本地 PlayerPrefs。
- **Known Constraints**: 仅有粗粒度数据，缺乏历史局回放或详细的折线图走势记录。

## Hint System
- **Purpose**: 帮助卡关的玩家寻找下一步可行操作。
- **Current Player Experience**: 玩家点击提示按钮，高亮显示两张或多张可交互的卡牌。
- **Current Implementation**: `UnifiedHighlightManager` 与各个 `IVariantHandler` 中的自定义搜寻逻辑结合。
- **Completion Status**: Mostly Complete
- **Estimated Completion %**: 85%
- **Dependencies**: IVariantHandler, Gameplay Rules.
- **Related Scripts**: `UnifiedHighlightManager.cs`, `PyramidHintOverlay.cs`
- **Related Data**: N/A.
- **Known Constraints**: 提示仅寻找第一层合法移动，非最佳解（非 Solver 驱动），可能将玩家引入死局。

## Undo
- **Purpose**: 允许玩家撤销失误操作。
- **Current Player Experience**: 点击撤销，卡牌飞回原位，状态恢复。
- **Current Implementation**: 使用基于命令（Command）栈的模式，在 `UndoMovement` 中记录每步状态。
- **Completion Status**: Mostly Complete
- **Estimated Completion %**: 85%
- **Dependencies**: Card, Piles.
- **Related Scripts**: `UndoMovement.cs`
- **Related Data**: 运行时撤销栈（Memory Stack）。
- **Known Constraints**: 复杂的多张卡牌同时撤销（如撤销某次整体洗牌或撤销特定技能）处理较难，存在状态不同步风险。

## Auto Move
- **Purpose**: 在玩家必定能获胜的尾声阶段自动收牌，减少垃圾时间。
- **Current Player Experience**: 胜利条件清晰时，卡牌自动快速飞向基础叠（Foundation）。
- **Current Implementation**: `GameController` 检测到无其他可行反向操作后，触发连续的自动飞行逻辑。
- **Completion Status**: Complete
- **Estimated Completion %**: 95%
- **Dependencies**: Win Detection, FlightManager.
- **Related Scripts**: `GameController.cs`, `FlightManager.cs`
- **Related Data**: N/A.
- **Known Constraints**: 部分变体（如 Pyramid 或 TriPeaks）的自动收牌逻辑不适用或较为特殊。

## Solver
- **Purpose**: 验证牌局的可解性，确保为玩家提供的种子是“有效”的。
- **Current Player Experience**: 隐性体验（玩家不可见，但在后台确保了体验质量）。
- **Current Implementation**: 拥有独立的求解器实现（如 `KlondikeSolver`, `SpiderSolver`），通过批处理测试。
- **Completion Status**: Mostly Complete
- **Estimated Completion %**: 80%
- **Dependencies**: Seed System, Rules.
- **Related Scripts**: `SolverManager.cs`, `ISolver.cs`, `KlondikeSolver.cs`, `SpiderSolver.cs`
- **Related Data**: `SolverBatchTest` 配置。
- **Known Constraints**: 深度优先/A*搜索在大步数或复杂牌局中会有性能瓶颈，难以在客户端移动端设备上实时执行，主要依赖 Editor 离线验证。

## Validation
- **Purpose**: 判定单次卡牌拖放或点击操作是否合法。
- **Current Player Experience**: 无法将红心 10 放在方块 10 上（根据规则被退回）。
- **Current Implementation**: 核心规则编写在具体的 Handler 中，透过接口接收尝试移动的请求并返回布尔值。
- **Completion Status**: Complete
- **Estimated Completion %**: 100%
- **Dependencies**: Piles, IVariantHandler.
- **Related Scripts**: `IVariantHandler.cs`, 具体变体类。
- **Related Data**: N/A.
- **Known Constraints**: 规则散落在各个变体 Handler 中，尚未做到高度模块化的规则配置。

## Win Detection
- **Purpose**: 判定游戏是否结束并获胜。
- **Current Player Experience**: 所有卡牌回收或消除完毕，弹出胜利界面。
- **Current Implementation**: 变体 Handler 在每次有效操作后检查获胜条件并回调触发事件。
- **Completion Status**: Complete
- **Estimated Completion %**: 100%
- **Dependencies**: IVariantHandler.
- **Related Scripts**: `GameController.cs`, 具体变体类。
- **Related Data**: N/A.
- **Known Constraints**: 触发依赖于操作结算点，存在与动画（FlightManager）完成时机的竞态可能。

## Lose Detection
- **Purpose**: 判定玩家是否无牌可动且无计可施。
- **Current Player Experience**: 牌库耗尽且无法移动时，提示失败或给出复活提示。
- **Current Implementation**: 透过 `VariantErrorResolver` 或全局状态检查判断。
- **Completion Status**: Mostly Complete
- **Estimated Completion %**: 80%
- **Dependencies**: IVariantHandler.
- **Related Scripts**: `VariantErrorResolver.cs`
- **Related Data**: N/A.
- **Known Constraints**: 玩家有时很难分辨是真正的死局，还是他们错过了一个隐蔽的可移动步骤。

## Tutorials
- **Purpose**: 教导新玩家规则与交互方式。
- **Current Player Experience**: 首次进入时，强制的遮罩与手势动画指引。
- **Current Implementation**: `TutorialManager` 与 `TutorialPrompt`，基于步骤 ID 推进。
- **Completion Status**: Mostly Complete
- **Estimated Completion %**: 75%
- **Dependencies**: UI, Localization.
- **Related Scripts**: `TutorialManager.cs`, `TutorialPrompt.cs`
- **Related Data**: PlayerPrefs (`Tut_Complete_`, `Tut_Step_`).
- **Known Constraints**: 硬编码较多，针对特定界面调整时容易失效。

## Achievements
- **Purpose**: 赋予玩家长期目标。
- **Current Player Experience**: 获得里程碑（未实装）。
- **Current Implementation**: 未实装核心框架。
- **Completion Status**: Prototype / Not Started
- **Estimated Completion %**: 0%
- **Dependencies**: Statistics, Platform Services.
- **Related Scripts**: N/A.
- **Related Data**: N/A.
- **Known Constraints**: 完全缺失。

---

# Gameplay Progression

World
↓
Chapter (通过解锁上一级 World 并达成特定条件开启)
↓
Run (章节内包含 1-N 个必须顺序通过的对局)
↓
Completion (通关单局，记录胜利状态)
↓
Rewards (发放星星、Coins、Gems)
↓
Unlocks (达到特定星数或章节通关时解锁下一阶段节点)
↓
Hub Progress (WorldHubProgressStore 更新并序列化至本地，大厅 UI 自动刷新)

---

# Save & Progress

- **Player Progress**: 全局等级与资源（Coins, Gems）。存储于 `PlayerPrefs` (`Eco_Coins`, `Eco_Gems`)。
- **Story Progress**: World 与 Chapter 的解锁状态及已通关任务数。储存于 `WorldHubProgressStore`。
- **Run Progress**: 单次对局中途的牌局快照状态（挂起恢复）。通过 `GameSaveManager` 将对象转换为 `GameSaveData` 进行 JSON/二进制 序列化。
- **Daily Progress**: 记录特定日期的挑战完成情况。存在于本地 Key-Value 存储。
- **Statistics**: 各玩法胜场、游玩次数等。存储于 `PlayerPrefs` (`Stats_Played_`, `Stats_Won_`)。
- **Unlocks**: 商店皮肤（牌背、背景）的购买状态。存储于 `PlayerPrefs` (`Unlock_[ID]`)。
- **Current Save Flow**: 每当玩家完成一个关键动作（如关卡胜利、购买物品、或者在游戏中按下暂停/退入后台）时，触发对应的保存管理器（如 `GameSaveManager.SaveCurrent()` 或直接 `PlayerPrefs.Save()`）进行本地 IO 写入。

---

# Gameplay Assets

- **Gameplay Scenes**: 
  - `GameScene.unity` (核心运行场景)
  - `UniverseHub.unity` (大厅场景)
- **Card Assets**: 
  - 标准 52 张扑克牌 Sprite Atlas
  - 动态生成的 `Card` Prefabs
- **Animation Controllers**: 
  - 粒子与 UI 进出场 Animation Clips (通过 DoTween 或 Unity Animator)
- **ScriptableObjects**: 
  - `WorldHubConfig` (宇宙关卡树)
  - `ShopContentCatalog` (商店系统)
  - `DailyChallengeSet` (每日轮换池)
- **CSV**: 
  - 无，主要依靠 Unity 的 ScriptableObject 作为资料容器。
- **Localization Keys**: 
  - 存放在 Localization 文件夹，通过 `UILocalizationBinder` 绑定文本系统。

---

# Current Strengths

- 已实现 `IVariantHandler` 接口高度抽象化，接龙的变体玩法（如 Spider, Klondike 等）被完全解耦，可快速新增模式。
- `GameController` 负责了严谨的生命周期与状态机管理，使得游戏核心逻辑极少产生崩溃。
- Deterministic Seed System 稳定，配合现成的 `DealGenerator`，能够确保 100% 重现特定对局，有利于调试与分享。
- 引入了指令模式思想实现的 Undo 功能，状态回滚一致性强。

---

# Known Technical Constraints

- **Large systems**: `GameController.cs` 文件依然庞大（超过 250KB），承担了过多的 UI 中转和初始化工作，尽管核心逻辑已外包给 VariantHandler。
- **Complex dependencies**: HUD 与大厅的过渡（特别是 `Panels_Canvas` 的启用与禁用）依赖了较多散落的静态调用与直接引用，容易在场景过渡时发生竞态条件。
- **Temporary implementations**: 统计系统与成就系统主要依赖基础的 `PlayerPrefs`，缺乏可维护、防篡改的本地数据库或云端强同步支持。
- **Validation cost**: 高频率的拖动与高亮检测目前主要在主线程执行。
- **Solver limitations**: 求解器（Solver）为 Editor Offline 工具，算法深度限制使得无法对复杂残局在运行时提供带步骤的最优解回放。

---

# Overall Assessment

- **Gameplay maturity**: 高。游戏核心牌局运行顺畅，交互体验已经达到准上线级别。
- **Current production stage**: Alpha 尾声 / Beta 测试准备阶段。
- **Production readiness**: 可以针对单机体验进行试软发布（Soft Launch），但缺乏健全的云端防弊与线上运营数据验证能力。
- **Major completed milestones**: 核心引擎集成、Variant 变体架构剥离、大厅星图视觉实装、Seed 随机系统闭环。
- **Major missing milestones**: 整体效能最佳化（特别是内存与垃圾回收）、全面的每日挑战循环实装、完整的成就与防作弊统计系统。
