# Decky 插件模板 [![Chat](https://img.shields.io/badge/chat-on%20discord-7289da.svg)](https://deckbrew.xyz/discord)

这是一个在 [decky-loader](https://github.com/SteamDeckHomebrew/decky-loader) 插件中使用 [decky-frontend-lib](https://github.com/SteamDeckHomebrew/decky-frontend-lib) (@decky/ui) 的参考示例。

### **请同时参阅 [wiki](https://wiki.deckbrew.xyz/en/user-guide/home#plugin-development) 获取有关插件开发和提交/更新的重要信息。目前文档分散在本 README 和 wiki 之间，我们希望在将来解决这个问题。**

## 开发者指南

### 依赖项

此模板需要用户在系统上安装 Node.js v16.14+ 和 `pnpm` (v9)。请确保安装 pnpm v9 以防止在插件提交期间出现 CI 问题。推荐从 `npm` 本身下载 `pnpm`。

#### Linux

```bash
sudo npm i -g pnpm@9
```

如果您想构建具有自定义后端的插件，则需要 Docker，因为 Decky CLI 工具会使用它。

### 创建您自己的插件

1. 您可以 fork 此仓库或使用 Github 上的 "Use this template" 按钮。
2. 在您本地的 fork/插件仓库中运行以下命令：
   1. `pnpm i`
   2. `pnpm run build`
   - 这些命令会设置 pnpm 并构建前端代码以供测试。
3. 查阅 [decky-frontend-lib](https://github.com/SteamDeckHomebrew/decky-frontend-lib) 仓库以了解如何完成您的任务。
   - 文档和示例仍然比较粗糙，
   - Decky loader 主要针对 Steam Deck 硬件，因此在开发插件时请牢记这一点。
4. 如果使用 VSCodium/VSCode，请运行 `setup`、`build` 和 `deploy` 任务。如果不使用 VSCodium 等，您可以派生自己的 makefile 或根据需要手动使用这些命令的脚本。

如果您使用 VSCode 或其衍生版本（我们推荐 [VSCodium](https://vscodium.com/)！），只需运行 `setup` 和 `build` 任务即可。就是这么简单。

#### 其他重要信息

每次更改前端代码（`index.tsx` 等）时，您都需要使用上面步骤 2 中的命令重新构建，如果您使用的是 VSCode 或其衍生版本，则使用 build 任务。

注意：如果由于库版本过旧而收到构建错误，您应该在仓库内运行以下命令：

```bash
pnpm update @decky/ui --latest
```

### 后端支持

如果您正在为插件开发后端并希望将其提交到 [decky-plugin-database](https://github.com/SteamDeckHomebrew/decky-plugin-database)，您需要将所有后端代码放在 `backend/src` 中，backend 目录位于 git 仓库的根目录。构建插件时，源代码将被编译，任何完成的二进制文件将输出到 `backend/out`（在 CI 期间创建）。如果您的构建脚本、makefile 或任何其他构建方法未将二进制文件放在 `backend/out` 目录中，它们将无法在 CI 期间被正确识别，您的插件将不会包含分发所需的二进制文件。

示例：在我们用于演示构建和分发插件后端 CI 流程的 makefile 中，请注意 makefile 明确创建了 `out` 文件夹（`backend/out`），然后将二进制文件编译到该文件夹中。以下是相关代码片段。

```make
hello:
	mkdir -p ./out
	gcc -o ./out/hello ./src/main.c
```

CI 确实会自行创建 `out` 文件夹，但我们建议您在构建过程中尽可能自己创建它，以确保构建过程顺利进行。

注意：在本地构建插件时，它将被放置到一个名为 'out' 的文件夹中，这与上述概念不同。

out 文件夹不会发送到最终插件，而是被放入一个 `bin` 文件夹，该文件夹位于插件目录的根目录。有关 bin 文件夹的更多信息，请参阅下面的分发部分。

### 分发

我们建议按照 [decky-plugin-database](https://github.com/SteamDeckHomebrew/decky-plugin-database) 中的说明将您的插件发布到插件商店。这是让用户看到您插件的最佳方式。您也可以选择通过包含所需文件的 zip 文件进行分发，如果该 zip 文件上传到 URL，则可以通过 decky-loader 下载和安装。

**注意：由于缺少可用的文件选择对话框，我们目前没有在"游戏模式"下从下载的 zip 文件安装的方法。**

准备分发的插件 zip 布局：

```
pluginname-v1.0.0.zip（版本号可选，但为了用户方便建议添加）
   |
   pluginname/ <目录>
   |  |  |
   |  |  bin/ <目录>（可选）
   |  |     |
   |  |     binary（可选）
   |  |
   |  dist/ <目录> [必需]
   |      |
   |      index.js [必需]
   | 
   package.json [必需]
   plugin.json [必需]
   main.py {如果使用 decky-loader 的 Python 后端 serverAPI 则必需}
   README.md（可选但推荐）
   LICENSE(.md) [必需，文件名应大致相似，不需要后缀]
```

关于许可证的说明：如果您选择的许可证要求在使用源代码/二进制文件时包含许可证，则插件商店需要包含许可证！

许可证的标准程序是将您选择的许可证放在文件顶部，并将插件模板的原始许可证保留在底部。如果在提交到插件数据库时不是这种情况，您将被要求修复此差异。

如果您的许可证要求包含许可证文件，但您没有在 git 仓库根目录中包含可随插件重新分发的许可证，我们将无法也不会在插件商店上分发您的插件。
