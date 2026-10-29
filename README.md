<div align="center">

# Memora

### 把随手记下的想法，整理成可以完成的清单。

面向 macOS 的橙紫深色记事本：记录文字、管理优先级、勾选任务，并按时间快速查找。<br />
使用 SwiftUI 与 SwiftData，源码包含语音转写和关键词解析流程。

![macOS](https://img.shields.io/badge/target-macOS%2014%2B-6366F1?style=flat-square)
![UI](https://img.shields.io/badge/UI-SwiftUI-F59E0B?style=flat-square)
![Storage](https://img.shields.io/badge/storage-SwiftData-334155?style=flat-square)
[![License](https://img.shields.io/badge/license-MIT-22C55E?style=flat-square)](LICENSE)

**[查看源码](https://github.com/Eileenes/memoranote)** · **[快速开始](#quick-start)** · **[使用指南](#usage)** · **[当前限制](#limitations)** · **[提交问题](https://github.com/Eileenes/memoranote/issues)**

</div>

---

## 能做什么

| 记录与清单 | 查找与管理 | 语音与界面 |
| --- | --- | --- |
| 创建、编辑与删除记事 | 高／中／低三级优先级 | 麦克风录音与实时语音转写 |
| 标题、正文和多个可勾选任务项 | 搜索标题与正文 | 从部分时间、优先级关键词生成记事 |
| 显示任务完成百分比与数量 | 按今天、最近 7 天、最近 30 天筛选 | 识别部分编号清单格式 |
| 使用 SwiftData 保存记事 | 搜索、优先级和时间组合筛选 | 橙紫配色、深色背景与进度状态 |

**当前仓库尚无 GitHub Release 安装包，源码构建存在已知阻塞。** 以下功能根据仓库实现整理；克隆后需要先处理重复源码参与编译的问题，再进行实际运行验证，详见[当前限制](#limitations)。

<a id="quick-start"></a>

## 快速开始

### 1. 获取源码并打开工程

```bash
git clone https://github.com/Eileenes/memoranote.git
cd memoranote
open Memora.xcodeproj
```

应用 target 的最低系统版本为 **macOS 14.0**，Swift 语言模式为 **5.0**。工程记录由 **Xcode 26.2** 创建，包含文件系统同步组；开发时请使用能读取当前工程格式的 Xcode，不能仅按旧说明假定 Xcode 15 可直接打开。

### 2. 检查 target 源文件与签名

当前 `Memora/` 和 `Memora/Memora/` 同时保留了 `MemoraApp`、`Note`、视图等重复定义。默认同步组会将两套源码纳入同一 target，构建报 `Multiple commands produce ... .stringsdata`。先在本地整理 target membership，确保只编译一套应用入口、模型和视图；新语音功能位于外层 `Memora/`。

工程保留了原开发团队的自动签名配置。在 Xcode 的 **Signing & Capabilities** 中选择你自己的团队和适用签名设置，选择 **Memora → My Mac** 后再尝试运行。处理重复文件是构建的必要步骤，不代表其余编译和运行问题已全部排除。

### 3. 构建与本地安装

处理上述问题后，可以在 Xcode 中按 `⌘R`，或使用命令检查构建：

```bash
xcodebuild -project Memora.xcodeproj \
  -scheme Memora \
  -configuration Release \
  -derivedDataPath ./build \
  build
```

成功时应用位于 `build/Build/Products/Release/Memora.app`。

仓库另有 [install_memora.sh](install_memora.sh)：它会构建，并**删除 `/Applications/Memora.app` 中的旧应用后复制新版本**。确认构建成功、了解覆盖行为后再运行 `bash install_memora.sh`。不要把脚本执行完毕等同于应用已通过签名、公证或全部功能验收。

<a id="usage"></a>

## 使用指南

### 文字记事与清单

1. 点击工具栏的 **＋**，输入标题、正文和任务项。
2. 选择优先级，点击 **CREATE** 保存；编辑现有记事时使用 **SAVE**。
3. 在列表或详情中勾选清单项，查看完成百分比和已完成数量。
4. 使用搜索框查找标题／正文，用筛选菜单组合优先级和时间范围。

时间筛选依据**记事创建时间 `createdAt`**，不是截止时间；“最近 7 天”和“最近 30 天”按当前时刻向前计算。列表按创建时间倒序显示。

### 语音创建记事

1. 点击麦克风按钮，允许系统的麦克风和语音识别权限。
2. 点击录音按钮开始说话，查看实时转写以及 `PARSED RESULT`。
3. 检查文本、优先级和日期后，点击 **SAVE NOTE** 创建记事。

例如：

```text
明天上午9点，紧急，准备项目演示。

第一整理会议记录，第二发送项目进展。
```

转写默认使用 `zh-CN` 识别器；若无法创建该识别器，代码尝试 `en-US`。关键词解析由本地规则实现：支持“明天”“后天”“下周”“数字天后”等部分表达，以及“紧急”“不急”等优先级词和部分编号清单。它不调用大语言模型，也不能保证理解任意自然语言；中文数字时刻、复杂日期和否定语义需要自行核对。

<a id="limitations"></a>

## 使用条件与当前限制

| 场景 | 需要什么／当前状态 |
| --- | --- |
| 从源码运行 | macOS、兼容当前工程格式的 Xcode；先处理重复源码导致的构建阻塞 |
| 语音录入 | 麦克风、系统权限和可用的 Apple Speech 服务；代码未强制仅在设备端识别，不承诺完全离线 |
| 数据保存 | 使用 SwiftData；当前工程没有配置 iCloud／CloudKit 同步 |
| 时间与任务 | 支持时间筛选及规则解析日期；没有实现系统提醒通知 |
| 跨平台 | 当前工程仅有 macOS 应用 target；源码中的 iOS 条件分支不代表已经提供 iOS 版本 |
| 功能扩展 | 标签、Markdown 渲染、多主题切换与云同步仍未实现 |

一次禁用代码签名的本地构建已复现重复 `.stringsdata` 产物错误，尚未完成成功构建、语音录入或安装后的端到端验证。旧文档中的“直接安装”“完全安全”等表述不作为当前版本的质量或安全保证。

## 文档与维护

| 文档 | 内容 |
| --- | --- |
| [时间筛选说明](TIME_FILTER_FEATURE.md) | 时间范围和组合筛选 |
| [安装指南](INSTALLATION_GUIDE.md) | 历史本地安装说明 |
| [分享指南](SHARE_GUIDE.md) | 历史打包与分享流程 |
| [主题升级指南](UPGRADE_GUIDE.md) | 橙紫主题的调整记录 |
| [图标设计指南](EVA_ICON_DESIGN.md) | 图标设计与导入背景 |
| [编译修复记录](COMPILE_FIX_REPORT.md) | 早期编译问题的处理记录 |

这些文档保留了部分旧路径、旧环境和历史结论，使用时应结合本页的当前状态及实际工程配置核对。

<details>
<summary><b>目录结构</b></summary>

```text
memoranote/
├── Memora.xcodeproj/          # macOS 工程、target 与签名配置
├── Memora/
│   ├── MemoraApp.swift        # 应用入口、SwiftData 容器
│   ├── Note.swift            # 记事模型
│   ├── ChecklistItem.swift   # 清单项模型
│   ├── NoteRepository.swift  # 数据保存与查询
│   ├── Note*View.swift       # 列表、详情、编辑与卡片
│   ├── VoiceRecognitionService.swift  # Apple Speech 录音转写
│   ├── VoiceNoteCreator.swift # 关键词解析与语音记事创建
│   ├── VoiceInputView.swift  # 语音输入界面
│   ├── DesignSystem.swift    # 橙紫深色主题
│   ├── Assets.xcassets/      # 图标与资源
│   └── Memora/               # 重复源码目录，需整理编译归属
├── install_memora.sh          # 构建并替换本地应用
└── LICENSE                   # MIT 许可证
```

</details>

欢迎[提交 Issue](https://github.com/Eileenes/memoranote/issues) 或 Pull Request。报告时注明 macOS、Xcode 版本、操作步骤与错误日志；附图前请移除私人记事内容。

## 来源与授权

本项目采用 [MIT 许可证](LICENSE)，保留 **Copyright (c) 2025 duke-yeah** 的版权声明。仓库现由 [Eileenes](https://github.com/Eileenes) 维护；感谢 Apple 提供的 SwiftUI、SwiftData、Speech 与 AVFoundation 框架。
