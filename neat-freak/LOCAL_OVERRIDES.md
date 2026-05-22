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

方律的 `~/.claude/CLAUDE.md`（25 行）是**本机运行环境身份说明**（"你是 Mac mini 上的 Claude，不是 J4125 上的 Claude"），**不是项目规则手册**。

- **不要**按 SKILL.md 默认的"项目根 CLAUDE.md"规则处理 `~/.claude/CLAUDE.md`
- **不要**往里加"边界规则" / "命令速查" / "权限模型" — 这文件唯一职责是身份/坐标说明
- 想动它必须用户**明确点名**才能改

项目根的 `CLAUDE.md`（若存在）按 SKILL.md 默认规则处理，不受此条影响。

---

## 修改 4：跨设备同步意识

方律的环境涉及**多台机器同时跑 Claude / OpenClaw**：

- 本机 Mac mini（你现在跑的位置）
- J4125 上独立的 Claude Code 容器（不是你）
- J4125 上独立的 OpenClaw（6 agent）
- 办公室 Mac mini 上的 OpenClaw（110.21）

**判断变更影响时**：

- 看到"X 在远端" / "host 名 jackie" / "/root/..." / "docker exec openclaw" 字样 → 该事实关联的是 **J4125**，不是本机
- 看到"110.21" / "RAGFlow" 字样 → 该事实关联的是**办公室 Mac mini**
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
