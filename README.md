# GitHub Keep-Alive Workflow

这是一个 GitHub Actions 工作流，旨在通过定期创建空提交（Empty Commit）来防止您的仓库因长期不活跃而被 GitHub 归档。

为了避免被平台风控识别为机器人行为，此工作流每次运行时会从一组预定义的、看起来很自然的常规维护信息中随机选择一条作为提交信息。

## ✨ 功能特性

- **🛡️ 真实模拟**：通过带有随机提交信息（如 `chore: routine maintenance`）的空提交，模拟正常的维护活动。
- **⚙️ 高度可配置**：要保活的仓库列表和个人访问令牌（PAT）都通过仓库的 `Secrets` 和 `Variables` 进行管理，无需修改代码。
- **👥 多用户支持**：可以轻松配置多个用户的仓库，并为每个用户使用独立的 PAT。
- **🔁 自动运行**：默认每 28 天自动运行一次，也可以随时手动触发。
- **✅ 默认保活**：会自动将存放此工作流的仓库加入保活列表中，无需额外配置。

## 🚀 如何使用

### 1. 准备个人访问令牌 (PAT)

您需要为每个希望保活其仓库的 GitHub 账户生成一个个人访问令牌（PAT）。

- 前往 [GitHub PAT 创建页面](https://github.com/settings/tokens/new)。
- 勾选 `repo` 范围的权限。
- 生成并**立即复制**您的令牌。

### 2. 在仓库中添加 Secrets

在存放此工作流的 GitHub 仓库中，配置一个 Secret 来存放所有用户的 PAT。

- 导航到 `Settings` > `Secrets and variables` > `Actions`。
- 点击 `New repository secret`。
- **名称**: `KEEP_ALIVE_PATS`
- **内容**: 一个**单行**的 JSON 字符串，将用户名映射到他们的 PAT。
  ```json
  {"Username1":"ghp_YourPatToken1","Username2":"ghp_AnotherPatToken2"}
  ```

### 3. 在仓库中添加 Variables

配置一个 Variable 来存放除当前仓库外，其他需要保活的仓库列表。

- 导航到 `Settings` > `Secrets and variables` > `Actions`，然后切换到 `Variables` 标签页。
- 点击 `New repository variable`。
- **名称**: `REPOSITORIES_TO_KEEP_ALIVE`
- **内容**: 一个用逗号分隔的仓库列表 (`用户名/仓库名`)。如果只想保活当前仓库，可以留空。
  ```
  Username1/RepoA,Username2/RepoB
  ```

### 4. 部署

将 `.github` 目录和 `README.md` 文件提交并推送到您的仓库。工作流将在下次计划时间自动运行，或者您也可以在仓库的 `Actions` 标签页手动触发它。

---

祝您的项目永葆青春！
