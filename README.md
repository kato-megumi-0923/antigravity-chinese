# Antigravity 中文汉化维护版

基于 yiheng8023/antigravity-chinese 的个人维护分支，针对实际使用中发现的漏译补充词库和界面匹配规则。

当前源码版本 **v3.3.59**，维护目标为 **Antigravity App 2.34.0 / VS Code 扩展 1.13.0**。这是社区开源项目，提供桌面客户端与 VS Code 扩展的本地中文补丁、备份还原，以及可选的中文交互插件。

本版统一汉化 Antigravity App 2.34.0 项目设置、未归属会话设置、插件市场、已安装列表和详情页中已核实的官方插件介绍。插件名、用户自定义内容及未匹配的介绍按原文显示。界面与安装验证见 [2.34.0 维护记录](docs/maintenance/antigravity-2.34.0.md)。Linux 便携路径支持见 [Linux 便携安装维护记录](docs/maintenance/linux-portable-install.md)。

VS Code 扩展更新会覆盖本地汉化。更新扩展后请重新执行 `node cli.js install:vscode`，再重新加载 VS Code 窗口。

## 本分支的汉化补充

- 补充带重置时间的周限额组合文本。
- 补充推理强度的低、中、高、极高及相关说明，并调整模型选择菜单的翻译范围。
- 补充首页创建网页应用、添加 Gemini 功能、迁移开发工具配置的三条提示。

这些改动已通过词库构建和静态语法检查，并安装到 Windows 上的 Antigravity App 2.34.0。完整测试套件及所有界面的实机验收仍待完成。

## 支持与验证范围

| 组件 | 实现 | 当前验证范围 |
| --- | --- | --- |
| Antigravity App | ASAR preload、原生菜单和弹窗文本补丁；版本备份与还原 | Windows x64 2.34.0 安装、重复安装、还原与外置文件核验。Linux x64 2.33.0 的安装验证见便携安装记录 |
| VS Code 扩展 | 清单、原生菜单和通知翻译、Webview 本地代理、独立备份/还原 | Windows 1.13.0 安装、重复安装、还原与界面检查见 [1.13.0 维护记录](docs/maintenance/antigravity-2.33.1.md) |
| 社区中文插件 | 中文交互规则与维护诊断技能 | 可选安装；不代表 Google 官方插件 |
| 独立 IDE、其他 IDE 扩展 | 尚无专用汉化安装器 | 不声明已适配 |
| Antigravity CLI | 保留官方原生行为 | 不修改 CLI 二进制或终端界面 |

以下验证范围沿用上游记录。本分支额外验证范围见上方说明。

上游本轮桌面 2.34.0 的实测范围是 Windows x64。macOS 和 Linux 的本轮验证范围为跨平台自动化。语音、远程智能体等官方灰度功能以实际账号开放情况为准。静态检查与候选字符串匹配均不代表整个上游产品的翻译覆盖率。

## 安装与使用

需要 Node.js **>=18** 及 npm/npx。首次安装开发依赖或 ASAR 工具可能需要联网。先保存工作并正常退出客户端，再在仓库目录运行：

```bash
npm install
node cli.js install
```

`install` 自动检测桌面客户端及 VS Code 扩展，处理已安装的组件。Windows 可双击 `install.bat`；macOS/Linux 可运行 `bash install.sh`。这两个脚本还会安装可选的社区中文插件。

```bash
node cli.js status
node cli.js install:vscode
node cli.js status:vscode
node cli.js restore:vscode
node cli.js install-plugin
node cli.js uninstall-plugin
node cli.js restore
node cli.js help
```

`restore` 尝试还原桌面及已检测到的 VS Code 汉化，并卸载本项目全局插件。指定 `--path` 时仅还原指定目标。缺少有效原版备份时会失败，避免用不明副本覆盖客户端。保留安装器生成的备份；官方更新后应重新核查适配。

自定义目标：

```bash
node cli.js install --path "<安装目录或 app.asar>"
node cli.js install:vscode --path "<扩展目录>"
```

Linux 解压版支持自动探测 `~/Applications/Portable`、`~/Applications` 和汉化工具同级目录下的 `Antigravity`、`antigravity` 或对应架构的官方解压目录（`Antigravity-x64` 或 `Antigravity-arm64`）。多个安装目录共存时，传入 `--path` 指定要处理的副本。

```bash
bash install.sh --path "$HOME/Applications/Portable/Antigravity"
bash uninstall.sh --path "$HOME/Applications/Portable/Antigravity"
```

`install.sh` 同时安装社区中文插件。只安装指定桌面补丁时可以直接使用 `node cli.js install --path`。带 `--path` 的还原只处理该目标。

安装器保留原有 unpacked 标记，替换前逐文件比对外置文件；不支持安全保留的布局时中止。当前 CLI 的桌面流程使用 ASAR；`core/cdp-client.js` 是供开发集成的独立协议模块。

## 开发、检查与维护

词库源文件在 `dict/src/`，`dict/zh-CN.json` 与 `dist/zh-CN.bundle.json` 是生成产物。

```bash
npm run build:dict
# 运行全套自动化测试（聚合 13 套测试入口）
npm test
npm run audit
npm run audit:ui -- --bundle <official-main.js>
```

测试包括 DOM/代码及用户内容保护、黄金语义、CDP、菜单、ASAR、路径探测、词库质检、VS Code 和边界回归。数量以当前脚本输出为准。CI 覆盖 Windows、macOS、Ubuntu × Node.js 18/20/22。

内容保护按明确的界面边界保留消息正文、Markdown、文件树、路径导航、文件引用、项目/会话名称及自定义扩展的作者内容；同一区域的固定操作按钮继续翻译。修复依据与验证范围见[用户内容保护维护记录](docs/maintenance/user-content-boundaries.md)和[自定义扩展内容边界记录](docs/maintenance/customization-content-boundaries.md)。

提供明确来源的官方前端脚本后，可分析候选文本：

```bash
npm run scan:drift -- "<frontend-bundle.js>" --report "<report.json>"
```

该工具还检查本机发现的内置技能与插件描述；报告是启发式摘要和样本。缺少有效 bundle 时退出失败；没有候选文本时覆盖率为未知。它不能代替实机检查。

每次更新维护执行 [贡献指南中的维护与发版流程](CONTRIBUTING.md#更新维护与发版)：核查官方日志、评估全局影响、按需修复、对齐文档、完成相应验证并回读发布结果。维护约定由 `AGENTS.md` 和社区诊断技能指向同一流程。

主要模块：`cli.js` 管理生命周期，`core/` 提供运行时/协议/扩展与 ASAR 处理，`tools/` 提供构建和检查，`test/` 提供回归，`docs/maintenance/` 保存维护依据。问题报告见 [SECURITY.md](SECURITY.md)，贡献方式见 [CONTRIBUTING.md](CONTRIBUTING.md)。

## 来源与许可证

本项目派生自上游社区项目，感谢原作者与贡献者的工作。历史维护记录保留了上游实现和验证依据。

- 当前仓库为 https://github.com/kato-megumi-0923/antigravity-chinese
- 上游项目为 https://github.com/yiheng8023/antigravity-chinese
- 项目采用 [MIT License](LICENSE)，原有版权和许可证声明予以保留。
