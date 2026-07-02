# LOCAL_OVERRIDES — 方律本地化

> 本文件覆盖 `SKILL.md` 中的默认行为。**有冲突时以本文件为准**。
> 维护者：doctorllll  |  Fork: `doctorllll/khazix-skills` branch `2dogg-local`
> 上游同步策略：`git fetch upstream && git merge upstream/main` 时若 SKILL.md 大改，本文件可能也要随之调整。

---

## 红线 1：删除动作必须先列清单等用户确认

**默认 SKILL.md 鼓励"减优于加、删优于留"，直接 Edit/Write 修改文件。本 fork 推翻这一条**：

- 任何**删除 memory 文件**、**删除 memory 索引条目**、**删除 CLAUDE.md / docs 段落** 的动作
  → **必须先列出"建议删改清单"**（文件名 + 行号 + 理由），等用户回 OK 或逐条勾选后才执行
- "合并重复条目"、"拆分超长 memory"、"精简 CLAUDE.md 顶部叙事" 同样走清单流程
- **新增** docs 段落、**新增** memory 文件、**改正** 相对时间→绝对日期 这类**不会丢信息**的动作可以直接执行
- 修复明显的事实性错误（如"路径写错了"、"命令拼错了"）可以直接改

**判断口诀**：会丢失信息 → 先列清单；只是补充或修正 → 直接做。

**Why**：方律的 memory 有相当一部分是**有意保留的历史档案**（事故复盘、废弃方案、版本演进史），用来给未来排查或回滚做参考。skill 默认会按"已废弃就 99% 删"的逻辑清理，会把这些档案误删。

---

## 红线 2：标记"已废弃 / obsolete / 已被取代 / 历史保留"的 memory 不删

默认 SKILL.md 的 sync-matrix 写："记忆条目里'已被 X 取代' / '已废弃' / '保留作历史' 字样 → 99% 真的可以删，docs 已经是权威"。

**本 fork 推翻**：方律环境下这类字样表示**用户主动决定保留作档案**。

- 看到 `已废弃` / `obsolete` / `已被.*取代` / `历史保留` / `留作回滚` / `留作教训` / `留作参考` 字样
  → **保持不动**，连"建议删除"清单都**不要列**它们
- 真要清理这类档案，由用户主动发起（说"清一下废弃 memory"）才动

**Why**：方律 J4125 上有多次升级失败需要回滚的历史（openclaw race-condition-patch、busybox crond segfault、volces-ark provider 死亡等），保留这些"已废弃"条目能让未来回滚或排查参考完整上下文。

---

## 专规：共享 wiki 层（最高优先级，2026-07-02 加入）

**适用范围**：目标目录是共享 wiki 层时——Mac 路径 `~/.claude/projects/-Users-2-dogg/memory/`，J4125 claude-code 容器视角 `/root/.claude/projects/-root-work/memory/`（Syncthing 双向同步的同一份）。这是多机共享的 LLM wiki，**一切动作以该目录内 `__WIKI_SCHEMA__.md` 为准**，与 SKILL.md 通用规则冲突时 schema 赢。

- **五步法强制**：任何写动作走 grep 旧条目 → 判定 update / supersede / delete / cross-link / new → 写 → 改 MEMORY.md → append log.md。supersede 用 schema 的旗子格式。**"毕业进 docs"机制在此层不适用**——wiki 自己就是沉淀层，没有 docs/ 目标；长期档案是特性不是膨胀。
- **每个动作必须记账**：改 / 删 / 合并任何文件后 append log.md 一行 `## [<ISO8601+0800> <host>] <op> | <slug> | <one-line>`；纯清理（修断链、补 frontmatter、瘦身索引行）op=`lint`。host 按运行机器写（`mac-claude` / `j4125-claude` / `codex`）。**静默改文件 = 破坏共享层审计链，绝对禁止**——这是本 skill 在 wiki 层最容易犯的错。
- **Lint 检查清单以 schema 为准**（7 项）：矛盾条目、SUPERSEDED BY 断链、`[[]]` 孤儿 / 断链、MEMORY.md 单行 >150 char 或总量 >24KB、`.sync-conflict-*` 残留、frontmatter 缺字段、type 不在 user/feedback/project/reference 四枚举。
- **尺寸口径以 schema 为准**：MEMORY.md ≤24KB 且单行 ≤150 char（比 SKILL.md 的 25KB/200 行更紧）；单条 memory 上限仍按本文件修改 1（200 行 + 架构全图豁免）。
- **`inbox/` 是投稿箱不是 memory**：里面的候选文件不参与盘点、不合并、不当重复条目；见非空（README.md 以外）→ 摘要里提醒走 curator 清箱流程（五步法审入 / 退稿），除非用户明确让你当 curator。`inbox/README.md` 永远保留。
- **sync-conflict 分型**（细化修改 2）：`log.md` / `MEMORY.md` 这类 append-only 冲突可按 schema 冲突章节自动合并（按时间戳 sort + uniq）并记 `lint` 行；topic `.md` 冲突仍不自动处理，列清单等用户。
- 红线 1（删除先列清单）与红线 2（废弃档案不删）在 wiki 层**继续有效且最优先**；schema 的 delete 判定也必须先过这两条红线。

**Why**：本 skill 诞生早于 wiki schema（2026-06-29 立），不加此章会在共享层静默改文件不记账、把 inbox 候选误当重复条目、用错尺寸口径。

---

## 修改 1：单条 memory 软上限 100 → 200 行

默认 SKILL.md 写："单条 memory 文件 ~100 行：通常说明在塞多件事 / 写成事故复盘 → 拆成几条独立记忆"。

**本 fork 改为 200 行**，且对以下"项目大图"型 memory 永久豁免（不拆、不缩、不动）：

- `legalrag_project.md`（LegalRAG 全景：架构 + API + 部署 + 已知问题）
- `openclaw_architecture.md`（OpenClaw 7-agent 架构全图）
- `oa_system_architecture.md`（OA 监控系统架构）
- 任何 description 字段含 "架构" / "全景" / "全图" / "完整" 字样的 memory

**Why**：这类 memory 是 agent 进入项目时的**单文件百科入口**，故意做大才好用；拆成几条会破坏整体性。

---

## 修改 2：目录与文件排除

执行 "第一步：盘点现状" 的 `ls memory/` 时，**必须排除**：

- `**/.stversions/` — Syncthing 历史快照子目录（不是真 memory）
- `**/*.sync-conflict-*.md` — Syncthing 同步冲突残留（待人工处理，**不要自动合并或删除**）
- `**/MEMORY.sync-conflict-*` — 同上

**遇到 sync-conflict 文件**：列在最终摘要的"未处理 / 需用户决定"段，告知用户存在残留文件，但**不要自动处理**。

**Why**：方律本机用 Syncthing 在 Mac ↔ J4125 同步 OneDrive 等目录，memory 目录附近有这两类副产物。skill 不知道是什么，会误判为重复/冲突。

---

## 修改 3：CLAUDE.md 受众的特殊性

方律的 `~/.claude/CLAUDE.md` 是**本机运行环境身份说明**（"你是 MacBook Pro 上的 Claude，不是 J4125 上的 Claude"），**不是项目规则手册**。

- **不要**按 SKILL.md 默认的"项目根 CLAUDE.md"规则处理 `~/.claude/CLAUDE.md`
- **不要**往里加"边界规则" / "命令速查" / "权限模型" — 这文件唯一职责是身份/坐标说明
- 想动它必须用户**明确点名**才能改

项目根的 `CLAUDE.md`（若存在）按 SKILL.md 默认规则处理，不受此条影响。

---

## 修改 4：跨设备同步意识

方律的环境涉及**多台机器同时跑多个 agent**（2026-07-02 刷新；OpenClaw 已于 2026-06-04 全面退役，被 Hermes 取代）：

- 本机 MacBook Pro（你现在跑的位置）：Mac Claude（host `mac-claude`）+ Codex（host `codex`）+ 本机 Hermes gateway（`~/.hermes`，host `mac-hermes`）
- J4125（`jackie`）：独立 Claude Code 容器 `claude-code`（host `j4125-claude`，不是你）+ `hermes` 容器 7 profile（host `hermes:<profile>`，对共享 wiki 只读 + inbox 投稿）
- 办公室 Mac mini（110.21）：已分流到独立 project `office-mac-mini-ops`，共享 wiki 不再新增其条目

**判断变更影响时**：

- 看到 "host 名 jackie" / "/root/work" / "docker exec claude-code" → **J4125 的 Claude**；"/root/shared-wiki" / "docker exec hermes" / "profile" → **J4125 的 Hermes**
- 看到 "docker exec openclaw" / "~/.openclaw" → **历史档案**（OpenClaw 已退役），不要当现役事实同步
- 看到 "110.21" / "RAGFlow" → 办公室 Mac mini，归 office-mac-mini-ops project 管
- 不要把本机改动误当成 J4125 改动写进 memory（反之亦然）
- 跨设备改动要在 memory 里**明确写出影响范围**（如"本机 Mac + J4125 容器都改了"）

具体设备角色查 `~/.claude/CLAUDE.md` 顶部 "你的运行环境" 段。

---

## 修改 5：触发条件收紧（已在 frontmatter 同步）

SKILL.md 默认触发词太宽（"整理一下"、"收尾"、"这个阶段做完了"会误触发）。

本 fork 的 frontmatter 已收紧为**仅显式触发**：

- ✅ 触发：`/sync`、`/neat`、`sync up`、"整理文档"、"整理记忆"、"同步文档"、"同步记忆"、"更新记忆"
- ❌ 不触发：单独的"整理一下"、"收尾"、"这个阶段做完了"、"梳理一下"、"tidy"、"clean up"

模糊词来了**不要主动跑 skill**，由用户显式说明。

---

## 上游同步注意事项

`git fetch upstream && git merge upstream/main` 时关注：

1. **SKILL.md frontmatter description 段**：上游若改了触发词列表，需手动重新收紧
2. **SKILL.md 顶部那段 fork 提示**（"方律本地化 fork ..."）必须保留
3. **references/sync-matrix.md**：上游若新增"删除规则"，需检查是否违反本文件红线 1/2
4. **本文件本身**：上游通常不会动，除非他们后来也加了类似机制——届时酌情合并
