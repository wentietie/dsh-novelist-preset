---
name: webnovel-serial
description: 单章连载生产流水线。用于写任何一章正文的全过程：写前一致性准备、细纲、正文写作、审查、修订、归档与台账更新。每章必走完整循环，不可只写正文不归档。
---

# 单章连载流水线（webnovel-serial）

一章 = **第0步 准备 → 第1步 细纲 → 第2步 正文 → 第3步 审查 → 第4步 修订 → 第5步 归档**。顺序不可颠倒，步骤不可跳过。

## 第0步 写前一致性准备（每章必做）

0. 读 .story-system/project_spec.json → 本章目标字数区间、当前进度、体量基线、节奏核心、风格负面清单。
   缺失则：先按 webnovel-guide 的「规格补立」流程补齐，再写。
0b. 读 .story-system/fact_registry.md：
   - 本章要写的任何数字、口径、专名、伏笔，登记表里有没有？
   - 有登记但「计划揭示章」就是本章的 → 必须兑现，并更新「读者已知」栏。
   - 没有登记的 → **先补登记再写**，禁止现场编造数字（"三套时间线对不上，几乎都是各章现场编数导致的"）。
   - 已决定放弃的伏笔，登记为「弃用」，不得悬空。
1. 读 .webnovel/state.json → 当前章号、卷、下一步计划。
2. 读 大纲/总纲.md 的伏笔表 → 本章该埋 / 该收哪些伏笔。
3. 读当前卷 详细大纲 + 节拍表 中本章条目（含 CBN）。
4. 读最近 2-3 章正文；章数多时可用 .webnovel/summaries/ 快速回读。
5. 读 .story-system/events/ 最近 3 章事件流 → 核对人物当前位置、伤势、持有物、关系、知情范围。
6. 读 .story-system/anti_patterns.md。
7. **发现前文矛盾 → 先向用户报告并给修复选项，不要带着矛盾往下写。**

## 第1步 细纲

- 本章任务清单：推进哪条线 / 埋或收哪个伏笔 / 爽点 / 断章钩子。
- 场景列表：每场景（地点/人物/目标/冲突/转折/出口）。
- 对照节拍表：本章 beat 全覆盖，CBN 逐条确认。

产出：写入 .story-system/chapters/chapter_NNN.json 的 outline 字段（或既有大纲文件的对应位置）。

## 第2步 正文

- **本章任务头（动笔前必读，必写进上下文）**：
  1. 从 .story-system/structure_map.json 取本章对应的 beat：
     `位置百分比 / beat 名 / must_happen / 承接上章 / 本章要留的钩子`；
  2. 取本卷的 `volume_pivot` 与 `volume_units`，确认本章属于哪一段独立剧情；
  3. 取 project_spec 的 `scale.pacing_core`（每隔 N 章给主角一次提升），确认本章是不是提升章。
  把这三段原样写进本章的写作上下文，再动笔。
- **字数**：以 project_spec.json 的 `scale.chapter_words` 为准（缺则用目标平台标准，默认 2000-3200 字）。
  写完**必须实算**，不许目测（口径：不计空白字符、不计标题行）：

  ```powershell
  $p='<正文路径>'; $t=Get-Content -LiteralPath $p -Raw -Encoding UTF8; $b=(($t -split "`n") | Where-Object {$_ -notmatch '^#'}) -join ''; ($b -replace '\s','').Length
  ```

  实算值写进 chapter_NNN.json 的 `word_count`，审查报告里也必须写这个数。
  低于下限：优先补场景/信息量，**不许靠注水凑数**；超上限：压缩冗余描写与同义对白，不许删伏笔与关键台词。
- **POV 纪律**：单章单视角；切换视角必须是大纲指定。
- **禁元叙述**：不得用"第N章""前文""上一卷"指代剧情；前情用场景记忆、对话、物件自然带出。
- **对话与动作推进为主**，静态描写克制；展示，不陈述。
- **断章钩子**：悬念 / 期待 / 反转至少其一收尾；且章末必须造成"**不平衡**"——未解决的动作 / 信息差 / 身份落差，三者至少其一。没有不平衡，就不算有钩子。
- 命名：正文/第NNNN章-标题.md（四位零填充）。

## 第3步 审查（写完自查，输出报告）

审查维度（**七维，缺一不可**）：
1. **一致性**：时间锚点、人物位置/状态/知情范围、能力边界、称谓、物品——逐项与前文和设定集对照。
2. **大纲符合度**：CBN 达成情况；偏差如实登记，不隐瞒。
3. **反模式**：对照 anti_patterns.md 逐条扫。
4. **规格达标**（硬指标）：实算字数 vs project_spec 区间；全角引号；禁元叙述；单章单视角；平台合规红线。
5. **节奏与爽点**：本章爽点是否真的落地（读者看得见结果）、章末是否造成"不平衡"、爽点是否外显（被他人反应或环境变化所见证）、与前后章的功能是否重复（连续 2 章功能相同 → 触发注水告警）。
6. **机械感与重复**（可机检）：固定短语/句式频次（对照 `compliance.style_taboo`）、跨章连续 ≥15 字重复串、单章内同质动作堆叠（列举式罗列）、配角台词模板化。
7. **网文质量**：信息密度、废话率、情绪曲线、主角魅力、对话推进度。

问题分级：⛔ 阻断（逻辑硬伤/设定冲突/规格硬指标未达标，必须改）｜⚠️ 高优（强烈建议改）｜💡 低优（可留到打磨期）。

**第6维辅助命令（PowerShell）**：
```powershell
# 固定短语频次（对照 compliance.style_taboo）
$p='<正文路径>'; $t=Get-Content -LiteralPath $p -Raw -Encoding UTF8
@('眼中闪过一丝','深吸一口气','不禁','仿佛整个世界都安静了') | ForEach-Object {
  $c=([regex]::Matches($t,[regex]::Escape($_))).Count; if($c -gt 0){"$_ × $c"} }
```
> 第6维查的是"这段话是不是换个人说也一样"。78 万字全自动网文实验证明：光有总纲+爽点规划+每卷详细大纲/节拍表/时间线表**并不能防住注水**——碎片化水文、数据灌水、配角台词模板化、"眼睛亮了/深吸一口气"式情感模板、同一角色跨章说话格式漂移，全程都存在。前五维全都查不到这五类问题。

产出：审查报告/第N章审查报告.md（总览 + 分级问题清单 + 修复方向）。

## 第4步 修订

- ⛔ 全部修复；⚠️ 列给用户选择；💡 报告中留档。
- 修订后复查，阻断项必须清零。
- 正文改动超过段落级时，向用户展示前后对照。

## 第5步 归档（更新台账，缺一不可）

1. **摘要**：.webnovel/summaries/chNNNN.md —— 150-300 字剧情摘要，供未来章节写前回读。
2. **事件流**：.story-system/events/chapter_NNN.events.json —— 从正文抽取结构化事件（event_id 用 `evt-chNNN-XXX` 格式）：
   - `character_state_changed`：`{ event_id, chapter, event_type, subject, payload: { field, old, new } }`
   - `world_rule_revealed`：`{ ..., subject, payload: { rule_content, rule_category, scope } }`
   - `open_loop_created`：`{ ..., subject, payload: { content, loop_type, unanswered_question, urgency, planted_chapter, expected_payoff } }`
   - `open_loop_closed`：`{ ..., subject, payload: { content, payoff_chapter, 效果评估 } }`
   - `relationship_changed`：`{ ..., subject, payload: { to_entity, relationship_type } }`
   - `artifact_obtained`：`{ ..., subject, payload: { artifact_id, name, owner } }`
3. **章节元数据**：.story-system/chapters/chapter_NNN.json —— 章号/标题/字数/POV/场景列表/伏笔埋收记录。
4. **伏笔表**：更新 大纲/总纲.md —— 新埋伏笔登记，已回收的标注回收章。
5. **进度指针**：.webnovel/state.json 前移到下一章。
6. **灵感池**：写作中冒出的未入纲点子 → .webnovel/idea_bank.json，不丢掉。
7. **体量进度**：回写 .story-system/project_spec.json 的 `progress`：
   - `written_chapters` / `written_words` 前移，`updated_at` 刷新；
   - 章数到达某卷末尾时，回填该卷 `words_actual`，并重算 `on_schedule`。
   同时在 .story-system/chapters/chapter_NNN.json 的 `review` 段写入机器可读结论：
   `{ "passed": true, "words_measured": 3193, "blockers": 0, "high": 0, "low": 5, "spec_check": "字数达标/引号达标/钩子合格" }`

## 节奏自觉

- **节奏定义以 project_spec 的 `scale.pacing_core` 为准**（如"每隔 8-10 章给主角一次实力或身份提升"），而不是默认套用"三章一小爽、十章一大爽"——后者是行业口口相传的**经验值，官方无出处**。
- 每卷末 3 章进入高潮区间；高潮的前置铺垫提前 3-5 章在节拍表中核对。
- 连续 2 章无冲突推进 → 触发节奏告警，主动向用户提议提速方案。
- **章末判据（可判定）**：本章结尾是否造成了"**不平衡**"——未解决的动作 / 信息差 / 身份落差，三者至少其一。没有不平衡，就不算有钩子（对照官方编辑口径："签到得跑车但主角自己拿着看，并不能和周围环境对比出现不平衡，就没有丝毫的冲突和爽点"）。
- **爽点外显检查**：主角的获得是否改变了处境、或被他人反应所见证？自嗨式获得不算爽点。
- **体量进度对账（每 5 章 / 每卷收尾做一次）**：
  - 实际已写字数 vs `project_spec.progress.written_words` 目标；
  - 实际章数 vs 按 `delivery.words_per_day` 应完成章数；
  - 落后计划 >10% → 主动给出加速方案（提高日更章数 / 收窄单元案长度 / 调整卷划分）；
  - 领先计划 >20% → 提醒体量可能注水，检查是否有功能重复章。
