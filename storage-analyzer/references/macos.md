# macOS 数据布局与分级参考

分析 macOS 扫描结果时读这份。讲"东西存在哪、怎么辨认、归哪一级"。

## 关键目录

| 目录 | 装什么 | 典型分级 |
|---|---|---|
| `~/Library/Caches/*` | 应用/工具缓存（浏览器缓存、Homebrew、pip） | 🟢 可自动清（**例外见下方"可再生但不免费"清单**） |
| `~/.cache/*`、`~/.npm`、`~/.cargo`、`~/.gradle`、`~/.m2` | 开发缓存 | 🟢 |
| `~/Library/Developer/Xcode/DerivedData`、`CoreSimulator` | Xcode 构建/模拟器 | 🟢 |
| `~/Library/Containers/<UUID 或 bundleid>` | 沙盒应用数据（聊天记录、离线视频、设置） | 🟡 多为用户数据 |
| `~/Library/Application Support/*` | 应用数据（Chrome Profile、Claude VM、飞书） | 🟡 |
| `~/Downloads` 里的 .dmg/.pkg | 安装包残留 | 🟢 |
| `/Applications/*.app` | 应用本体 | 🔴 仅当重复/想卸时上灯，否则归蓝色 |
| 系统文件、APFS 本地快照 | 系统 | 不上灯，归蓝色"系统及其他" |

## Caches 里的"可再生但不免费"例外（默认 🟡 不是 🟢）

Apple 约定 `~/Library/Caches/` 装"丢了能重建"的东西，但**有些项重建代价高，或删除会中断正在运行的程序**——这类即使在 Caches 里也默认 🟡，绿灯只能给那些「删了立即本地重生、零代价、不打断任何进程」的纯缓存。判断口诀：**重建是否要重新下载？删除是否会让某个跑着的工具当场崩？** 任一为是 → 🟡。

| 路径 | 表面 | 真相 | 分级 |
|---|---|---|---|
| `~/Library/Caches/ms-playwright` | Playwright 浏览器 | 二进制重建需 `playwright install` 重下数百 MB；`mcp-chrome-*` 是运行中浏览器的 profile，删了会崩掉正在跑的自动化会话。**注意**：MCP 若配 chrome channel（用系统 Chrome）则二进制是死重、删了无妨，但这要核实配置（看 `~/.claude.json` 的 playwright args 有无 `--browser`），默认别假设 | 🟡 |
| `~/Library/Developer/Xcode/iOS DeviceSupport`、`watchOS/tvOS DeviceSupport` | 真机调试符号 | 每个 iOS 版本一份，删了下次连真机要重新生成（慢） | 🟡 |
| `~/Library/Caches/com.docker.docker`、`~/Library/Containers/com.docker.docker/Data` | Docker 数据 | 含镜像/卷，删了镜像全没要重新 pull/build | 🟡 |
| `~/Library/Caches/Homebrew`、`~/.npm`、`~/.cargo`、pip wheel cache | 包下载缓存 | 删了只是下次装包重新下，不丢已装的东西，也不打断进程 | 🟢 |

> 教训：别被"在 Caches 里"骗了就一律标 🟢。Caches 是 Apple 的"可丢"约定，不等于"删了零成本"。涉及浏览器二进制 / 真机镜像 / 容器数据 / 正在被进程占用的 profile，先归 🟡 让用户自己判断。

## 辨认"神秘 UUID 容器"

`~/Library/Containers/` 下 UUID 命名的大目录，要查清属于哪个 App：
- `ls` 进 `Data/Documents/`、`Data/Library/`，找带 bundle id 的子目录（如 `com.bilibili.bbad` → 哔哩哔哩）
- 大头常藏在隐藏目录（如 `.Downloads/` 里的 `.bilitask` 离线视频）
- 仍只读，别动文件

## 间接释放（写进 long_term，不上红灯）

- 系统"可清除空间"磁盘紧张时自动回收
- 重启释放部分 swap / 临时快照
- `brew cleanup --prune=all`、清 Xcode DerivedData
- 调整 Time Machine 本地快照保留策略

## 删除机制

`server.py` 在 macOS 用 osascript 调访达入废纸篓；首次弹自动化授权，点允许。
