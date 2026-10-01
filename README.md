[README.en.md](https://github.com/user-attachments/files/32923568/README.en.md)
[README.md](https://github.com/user-attachments/files/32923538/README.md)
# 📚 学习进度统计

一款统计和管理**每周学习进度**的 Windows 11 桌面应用。

| 项目 | 说明 |
|---|---|
| 平台 | **仅 Windows 11**（10 1809+ 亦可） 因为一些技术原因，Android版停止更新，您仍可在现保留的分支中下载停更前的最新版本 |
| 技术栈 | .NET 10 + C# + WinUI 3 + Win2D + SQLite |
| 当前版本 | **v2.1.2** |
| 运行方式 | 完全本地、无需联网、**自带 .NET 运行时（免安装依赖）** |

> 📦 原 Android 版已停止开发并归档到 `archive\`，见 `archive\README.md`。

---

## 🚀 快速开始（普通用户）

### 绿色便携版（免安装）
- 双击项目根目录的 **`启动学习进度统计.lnk`**；
- 或直接运行 `releases\windows\portable\StudyTracker.exe`；
- 整个 `portable` 文件夹可拷到 U 盘使用，**换电脑无需装任何运行库**。

### 安装版
- 打开 `releases\windows\installers\`，双击 **`学习进度统计_安装包_v2.1.2.exe`**；
- 安装后自动创建桌面快捷方式和开始菜单入口；
- 数据保存在 `%LOCALAPPDATA%\StudyTracker`，**卸载不会删除学习数据**。

> 首次打开还没有科目，请先到「科目管理」添加（如：高等数学、英语），再回到「本周打卡」记录。

---

## 📂 目录结构

```
学习进度统计软件制作/
├─ 启动学习进度统计.lnk        ← 便携版快捷入口
├─ README.md
├─ global.json                 ← 锁定 .NET 10 SDK
├─ src/
│  ├─ StudyTrackerWinUI/       ← Windows 主版本源码（唯一活跃工程）
│  └─ legacy/                  ← 历史代码，仅参考
│     ├─ StudyTracker-WPF-Blazor/   ← 早期 WPF + Blazor Hybrid 版本
│     └─ python_prototype/          ← 最早的 Python 原型
├─ releases/windows/
│  ├─ portable/                ← 绿色便携版（自包含）
│  └─ installers/              ← 历史安装包 + 版本记录.txt（旧版一律保留）
├─ archive/                    ← 已停用内容（原 Android 版）
├─ scripts/                    ← 环境准备与一键构建
├─ docs/                       ← 开发文档
├─ tools/                      ← 工具下载缓存（不进 Git）
└─ _backup/                    ← 整理前源码快照
```

---

## 🛠️ 开发环境准备

只需要两个工具：

```powershell
# 一键准备（自动跳过已安装项，某个环节失败不影响其他环节）
scripts\setup-dev.bat
```

| 组件 | 说明 |
|---|---|
| .NET 10 SDK | 脚本自动安装到 `%USERPROFILE%\.dotnet` |
| Inno Setup 6 | 脚本自动安装，用于打安装包 |

---

## 🔨 构建

| 目标 | 命令 | 输出 |
|---|---|---|
| Windows 便携版 | `scripts\build-windows.bat` | `releases\windows\portable` |
| Windows 安装包 | `scripts\build-installer.bat` | `releases\windows\installers\学习进度统计_安装包_v*.exe` |
| 一次全打 | `scripts\build-all.bat` | 以上两项 |

> 打安装包前需先构建便携版；构建脚本**不会删除** `portable\data` 里的本地数据。

从源码手动构建（**`-r win-x64 --self-contained true` 不能省**）：

```powershell
dotnet publish src\StudyTrackerWinUI\StudyTrackerWinUI.csproj `
  -c Release -p:Platform=x64 -r win-x64 --self-contained true `
  -o releases\windows\portable
```

漏掉自包含参数会产出**依赖系统 .NET 运行时**的版本，换台电脑会启动即闪退。
`build-windows.ps1` 已内置校验：缺 `hostfxr.dll` / `coreclr.dll` 会直接报错并停止。

---

## 📦 版本更新规则（重要）

1. 新安装包放入 `releases\windows\installers`，文件名必须带版本号；
2. **旧版本安装包一律保留，不得删除**，方便随时回退；
3. 版本记录追加到 `releases\windows\installers\版本记录.txt`；
4. 版本号必须同步修改两处，否则 `build-installer.ps1` 会直接报错：
   - `src\StudyTrackerWinUI\StudyTrackerWinUI.csproj` 的 `<Version>`
   - `scripts\setup.iss` 的 `MyAppVersion`
5. 同名安装包已存在时，构建脚本会把新包输出到 `tools\build-verify\`，
   **不会覆盖历史发布文件**；确需写入正式目录请先升级版本号。

---

## ✨ 功能一览

**今日学习计划**：首页自动排出今天该学什么、每科学多久。算法综合考虑距周目标差额、本周剩余天数、科目优先级，以及"多久没碰这门课"，并用你的历史日均做现实性收敛。每科一行显示进度、原因说明，可一键打卡（自动预填建议时长）。

**成绩 / 考试记录**：记录每次考试、测验、模拟考与作业成绩，支持关联科目、满分与权重。统计平均得分率、加权平均、最好/最低、最近一次及涨跌，并给出得分率趋势折线图，支持科目与时间范围筛选。

**间隔复习提醒**：按遗忘曲线在学习后第 1/2/4/7/15/30 天自动安排复习（间隔可自定义）。复习计划页分区管理逾期 / 今天 / 未来 7 天 / 最近完成，支持标记完成、推迟、撤销；首页有到期提醒条。

**迷你悬浮计时器**：置顶小窗，可与主界面同时使用且状态完全同步。界面含**本段进度条**与**轮次圆点**（第 N / M 个番茄），运行状态点随阶段变色；支持开始 / 暂停 / 重置 / 跳过。可从番茄钟页面或系统托盘右键菜单唤起，窗口标题栏自动适配深浅色主题。

**本周打卡**：科目 × 一周 7 天网格，点击格子记录学习时长、完成度、是否完成、备注；
支持 `+0.5h / +1h / +1.5h / +2h` 快捷添加；顶部周导航（上一周 / 下一周 / 回到本周）；
每日总学时汇总行、科目周进度条、今日未打卡提醒。

**待办任务**：新增 / 编辑 / 删除 / 勾选完成；关联科目、优先级、截止日期、备注；
自动标记已逾期（红）与今日到期（橙）；顶部统计卡；已完成任务可恢复；支持上移 / 下移排序。

**统计分析**：累计总学时、学习总天数、当前 / 历史最长连续打卡、最常学科目；
近 12 周学时趋势、本周实际 vs 周目标、近 12 周时间分配、各科目近 8 周趋势、
近 14 周每日热力图、周内每天平均分布。图表全部为 **Win2D 原生绘制**，无网页嵌套，自动适配深浅色。

**周报总结**：自动生成周报（概览 / 各科目明细 / 未达标提醒 / 与上周对比 / 下周建议）；
可编辑「本周复盘」并保存；导出 HTML / TXT / Excel（Excel 含 3 个工作表）。

**日期倒计时**：考试、交作业、纪念日等；按剩余天数分级配色（7 天内红 / 8-30 天橙 / 更久蓝 / 过期灰）；
支持自定义颜色、备注、排序。

**番茄钟**：专注 / 短休 / 长休循环，时长可在页面内调整；完成后自动统计今日番茄数与专注分钟数；
可一键把本次专注计入某科目的今日记录。

**科目管理**：名称、颜色、周目标学时、优先级、说明；支持上移 / 下移排序、归档（保留历史记录）。

**数据管理**：导出 Excel / CSV / JSON 备份；导入 JSON 备份；数据库一键备份与恢复；清空所有数据（二次确认）。

**退出到托盘**：关闭窗口可选最小化到托盘 / 退出 / 取消；托盘图标左键双击恢复，右键菜单显示或退出。

**设置**：每周起始日、界面主题（浅色 / 深色 / 跟随系统）、新增科目默认周目标、启动提醒、番茄钟时长、**间隔复习开关与间隔配置**。

---

## 💾 数据位置

| 安装方式 | 数据库位置 |
|---|---|
| 便携版 | `releases\windows\portable\data\study_progress.db`（随程序目录走，可放 U 盘） |
| 安装版 | `%LOCALAPPDATA%\StudyTracker\study_progress.db` |

备份导出文件默认在 `data\backups`、`data\exports`。

---

## ❓ 常见问题

- **为什么 exe 叫 StudyTracker.exe？** 经测试部分 .NET 桌面应用在中文文件名下会异常退出，英文 exe 名 + 中文快捷方式最稳定。
- **需要装 .NET 吗？** 不需要。从当前版本起为自包含发布，运行时已随程序打包。
- **需要联网吗？** 不需要，全部本地运行。
- **双击没反应 / 一闪就退？** 说明拿到的是旧版（依赖运行时的构建）。用 `scripts\build-windows.ps1` 重新构建即可，脚本会校验自包含完整性。
- **快捷方式打不开？** 项目文件夹被移动过。重新运行一次 `scripts\build-windows.bat` 会自动重建快捷方式。

---

## 🧱 技术栈

.NET 10 / C# / WinUI 3（Microsoft.WindowsAppSDK）/ Win2D / Microsoft.Data.Sqlite / ClosedXML

数据 100% 保存在本机。


# 📚 Study Progress Tracker

A Windows desktop app for **tracking and managing your weekly study progress**.

| | |
|---|---|
| **Platform** | Windows 11 (Windows 10 1809+ also works) |
| **Built with** | .NET 10 · C# · WinUI 3 · Win2D · SQLite |
| **Current version** | **v2.1.2** |
| **How it runs** | Fully offline · **self-contained — no runtime installation required** |

> 📦 The Android version is no longer developed and has been archived under `archive\` — see `archive\README.md`.

---

## 🚀 Quick Start

### Portable version (no installation)
- Double-click **`启动学习进度统计.lnk`** in the project root, or
- Run `releases\windows\portable\StudyTracker.exe` directly.
- The whole `portable` folder can be copied to a USB drive or another PC — **no runtimes or dependencies needed**.

### Installed version
- Open `releases\windows\installers\` and run **`学习进度统计_安装包_v2.1.2.exe`**.
- Desktop and Start Menu shortcuts are created automatically.
- Data is stored in `%LOCALAPPDATA%\StudyTracker` and **is kept when you uninstall**.

> On first launch there are no subjects yet. Go to **Subjects** and add a few (e.g. Calculus, English), then return to **This Week** to start logging study time.

---

## 📂 Project Layout

```
Study progress tracker/            (学习进度统计软件制作)
├─ 启动学习进度统计.lnk            ← Portable-version shortcut
├─ README.md                       ← Chinese documentation
├─ README.en.md                    ← This file
├─ global.json                     ← Pins the .NET 10 SDK
├─ src/
│  ├─ StudyTrackerWinUI/           ← Windows app source (the only active project)
│  └─ legacy/                      ← Historical code, reference only
│     ├─ StudyTracker-WPF-Blazor/  ← Earlier WPF + Blazor Hybrid version
│     └─ python_prototype/         ← Original Python prototype
├─ releases/windows/
│  ├─ portable/                    ← Portable build (self-contained)
│  └─ installers/                  ← Installers + version history (old builds are never deleted)
├─ archive/                        ← Discontinued work (the former Android app)
├─ scripts/                        ← Environment setup and one-click builds
├─ docs/                           ← Developer documentation
├─ tools/                          ← Toolchain downloads and caches (not in Git)
└─ _backup/                        ← Source snapshots taken before reorganisations
```

---

## ✨ Features

**Today's study plan** — the dashboard works out what to study today and how long to spend on each subject. The recommendation weighs the remaining gap to each weekly target, the days left in the week, subject priority, and how long it has been since you last touched a subject; it is then scaled against your own historical daily average so the plan stays realistic. Each subject gets a progress row, a short explanation, and a one-click check-in that pre-fills the suggested hours.

**Grades & exam records** — log exam, quiz, mock-exam and assignment scores, optionally linked to a subject, with full marks and weighting. Reports average score rate, weighted average, best/worst result, latest result with the change since last time, and a score-rate trend chart, filterable by subject and time range.

**Spaced-repetition reminders** — review tasks are scheduled automatically 1 / 2 / 4 / 7 / 15 / 30 days after each study session (intervals are configurable). The review page groups work into overdue / today / next 7 days / recently completed, with mark-as-done, postpone, undo and delete. A reminder bar appears on the dashboard when reviews are due.

**Mini floating timer** — an always-on-top mini window that shares the exact same timer state as the main app. It shows a per-segment progress bar and cycle dots (pomodoro N of M), a status dot that follows the current phase, and start / pause / reset / skip controls. Open it from the Pomodoro page or the system-tray context menu. The title bar follows light and dark themes.

**Weekly check-in** — a subjects × 7-days grid. Click any cell to record study hours, completion percentage, a done flag and notes; quick buttons add `+0.5h / +1h / +1.5h / +2h`. Week navigation (previous / next / back to this week), a daily total row, per-subject progress bars and a reminder for subjects not yet studied today.

**To-do list** — create, edit, delete and tick off tasks, linked to a subject with priority, due date and notes. Overdue tasks turn red and tasks due today turn orange. Includes summary cards, restore-completed, and move-up / move-down ordering.

**Statistics** — total hours, days studied, current and longest streaks, most-studied subject; 12-week hours trend, this week's actual vs target per subject, 12-week time allocation, 8-week per-subject trends, a 14-week daily heat-map and average hours per weekday. All charts are drawn natively with **Win2D** — no embedded web views — and adapt automatically to light and dark themes.

**Weekly report** — generated automatically with an overview, per-subject breakdown, missed-target warnings, comparison with last week and suggestions for next week. The reflection note is editable and saved. Export as HTML / TXT / Excel (the Excel file contains three worksheets).

**Countdowns** — for exams, deadlines and anniversaries, colour-graded by remaining days (red within 7 days, orange 8–30 days, blue beyond 30, grey once passed). Custom colours, notes and ordering are supported.

**Pomodoro timer** — focus / short break / long break cycles with durations adjustable on the page. Completed sessions are counted, and the time can be added to a subject's record for today with one click.

**Subjects** — name, colour, weekly target hours, priority and notes, with re-ordering and archiving (history is preserved).

**Data management** — export Excel / CSV / JSON backups, import a JSON backup, one-click database backup and restore, and a confirmation-guarded "erase all data".

**Minimise to tray** — closing the window can ask each time, minimise to tray, or exit. The tray icon restores the window on double-click, with a right-click menu for show / mini timer / exit.

**Settings** — week start day, theme (light / dark / follow system), default weekly target for new subjects, startup reminder, pomodoro durations, and the spaced-repetition switch with configurable intervals.

---

## 💾 Where Your Data Lives

| Installation type | Database location |
|---|---|
| Portable | `releases\windows\portable\data\study_progress.db` (travels with the folder — USB-friendly) |
| Installed | `%LOCALAPPDATA%\StudyTracker\study_progress.db` |

Exported backups default to `data\backups` and `data\exports`.

---

## 🛠️ Development Environment

Only two tools are required:

```powershell
# One-shot setup (skips anything already installed; a failing step does not abort the rest)
scripts\setup-dev.bat
```

| Component | Notes |
|---|---|
| .NET 10 SDK | Installed by the script into `%USERPROFILE%\.dotnet` |
| Inno Setup 6 | Installed by the script; used to build the installer |

---

## 🔨 Building

| Target | Command | Output |
|---|---|---|
| Portable build | `scripts\build-windows.bat` | `releases\windows\portable` |
| Installer | `scripts\build-installer.bat` | `releases\windows\installers\学习进度统计_安装包_v*.exe` |
| Everything | `scripts\build-all.bat` | Both of the above |

> Build the portable version before building the installer.
> The build script **never deletes** user data in `portable\data`.

Manual build — **`-r win-x64 --self-contained true` is mandatory**:

```powershell
dotnet publish src\StudyTrackerWinUI\StudyTrackerWinUI.csproj `
  -c Release -p:Platform=x64 -r win-x64 --self-contained true `
  -o releases\windows\portable
```

Omitting the self-contained flags produces a build that depends on a system-wide .NET runtime and **will exit immediately on a machine that does not have one**.
`build-windows.ps1` verifies this and fails the build if `hostfxr.dll` / `coreclr.dll` are missing.

### Performance self-check

```powershell
scripts\measure-db.bat          # fails with exit code 1 if the threshold is exceeded
```

Measures how many database connections a start-up plus first dashboard render opens
(historically 72 before the snapshot architecture; currently 5).

---

## 📦 Versioning Rules (important)

1. New installers go into `releases\windows\installers` and must include the version number in the file name.
2. **Old installers are never deleted** so that any previous version can be reinstalled.
3. Append each release to `releases\windows\installers\版本记录.txt`.
4. The version number must be updated in both places, otherwise `build-installer.ps1` fails:
   - `<Version>` in `src\StudyTrackerWinUI\StudyTrackerWinUI.csproj`
   - `MyAppVersion` in `scripts\setup.iss`
5. If an installer with the same name already exists, the new one is written to `tools\build-verify\`
   instead — historical release files are never overwritten.

---

## ❓ FAQ

- **Why is the executable called `StudyTracker.exe`?** Some .NET desktop apps misbehave when the executable name contains non-ASCII characters; an English executable name plus a Chinese shortcut is the most reliable combination.
- **Do I need to install .NET?** No. The build is self-contained; the runtime ships with the app.
- **Does it need internet access?** No — everything runs locally.
- **Nothing happens when I double-click / the window flashes and closes.** That means you have an older build that depends on a system runtime. Rebuild with `scripts\build-windows.ps1`, which verifies self-containment.
- **The shortcut no longer works.** The project folder was moved. Run `scripts\build-windows.bat` once and the shortcut is recreated.

---

## 🧱 Technology

.NET 10 · C# · WinUI 3 (Microsoft.WindowsAppSDK) · Win2D · Microsoft.Data.Sqlite · ClosedXML

All data stays on your own machine.


