[README.md](https://github.com/user-attachments/files/32923538/README.md)
# 📚 学习进度统计

一款统计和管理**每周学习进度**的 Windows 11 桌面应用。

| 项目 | 说明 |
|---|---|
| 平台 | **仅 Windows 11**（10 1809+ 亦可） |
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




