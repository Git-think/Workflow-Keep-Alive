# GitHub Keep-Alive Workflow

这是一个 GitHub Actions 工作流，旨在通过定期创建空提交（Empty Commit）来防止您的仓库因长期不活跃而被 GitHub 归档。

为了避免被平台风控识别为机器人行为，此工作流每次运行时会从一组预定义的、看起来很自然的常规维护信息中随机选择一条作为提交信息。

## ✨ 功能特性

- **🛡️ 隐蔽安全**：通过带有随机提交信息（如 `chore: routine maintenance`）的空提交，模拟正常的维护活动，避免留下机器人痕迹。
- **⚙️ 高度可配置**：要保活的仓库列表和个人访问令牌（PAT）都通过仓库的 `Secrets` 和 `Variables` 进行管理，无需修改代码。
- **👥 多用户支持**：可以轻松配置多个用户的仓库，并为每个用户使用独立的 PAT。
- **🔁 自动运行**：默认每 28 天自动运行一次，也可以随时手动触发。
- **✅ 默认保活**：会自动将存放此工作流的仓库加入保活列表中，无需额外配置。

---

## 📖 详细配置指南

请严格按照以下步骤进行配置，以确保工作流正常运行。

### 步骤 1: 创建个人访问令牌 (PAT)

您需要为**每一个**希望保活其名下仓库的 GitHub 账户生成一个 PAT。

1.  **导航至 PAT 创建页面**:
    - 登录 GitHub，点击右上角头像 > `Settings` > `Developer settings` > `Personal access tokens` > `Tokens (classic)`。
    - 或者直接访问链接：[**Generate a new token (classic)**](https://github.com/settings/tokens/new)。

2.  **配置令牌**:
    - **Note**: 给令牌起一个描述性的名字，例如 `keep-alive-workflow-token`。
    - **Expiration**: 为了长期使用，建议选择 `No expiration`。
    - **Select scopes**: **这是最关键的一步**。请务셔勾选 `repo` 这个顶级复选框。它会自动选中其下的所有子权限，这赋予了令牌访问和修改您仓库的完整权限。
      

3.  **生成并保存令牌**:
    - 点击页面底部的 `Generate token` 按钮。
    - **立即复制生成的令牌** (例如 `ghp_xxxxxxxx`) 并妥善保管。**这个令牌只会显示一次**，关闭页面后将无法找回。

### 步骤 2: 配置仓库 Secrets (必填)

此 Secret 用于安全地存储所有需要用到的 PAT。

1.  **导航至 Secrets 设置页面**:
    - 在**存放此工作流的仓库**中，点击 `Settings` 标签页。
    - 在左侧菜单中找到 `Secrets and variables` > `Actions`。

2.  **创建 Secret**:
    - 点击 `New repository secret` 按钮。
    - **Name**: `KEEP_ALIVE_PATS` (必须完全匹配)
    - **Secret**: 粘贴一个**单行**的 JSON 字符串，将 GitHub 用户名映射到您在步骤 1 中生成的 PAT。
      > **重要**: JSON 的键是**大小写敏感**的 GitHub 用户名。

      **格式示例**:
      ```json
      {"Username1":"ghp_YourPatToken1","Username2":"ghp_AnotherPatToken2"}
      ```
      - 如果只有一个用户，格式如下:
      ```json
      {"YourUsername":"ghp_YourPatToken"}
      ```

### 步骤 3: 配置仓库 Variables (选填)

此 Variable 用于指定除当前仓库外，您还想保活的其他仓库列表。

1.  **导航至 Variables 设置页面**:
    - 在 `Settings` > `Secrets and variables` > `Actions` 页面，切换到 `Variables` 标签页。

2.  **创建 Variable**:
    - 点击 `New repository variable` 按钮。
    - **Name**: `REPOSITORIES_TO_KEEP_ALIVE` (必须完全匹配)
    - **Value**: 粘贴一个用逗号分隔的仓库列表 (`用户名/仓库名`)。
      > **注意**:
      > - **不要**包含运行此工作流的仓库，因为它会被自动添加。
      > - 如果您只想保活当前仓库，**可以不创建此 Variable**。

      **格式示例**:
      ```
      Username1/RepoA,Username2/RepoB
      ```

### 步骤 4: 部署

将 `.github` 目录和 `README.md` 文件提交并推送到您的仓库。工作流将在下次计划时间（每 28 天）自动运行，或者您也可以在仓库的 `Actions` 标签页找到 `Keep Repositories Alive` 工作流并手动触发它。

---

## 🤔 工作原理

1.  工作流被触发（定时或手动）。
2.  它首先获取自身所在的仓库名（例如 `YourUsername/Keep-Alive`）。
3.  然后读取 `REPOSITORIES_TO_KEEP_ALIVE` Variable 中的仓库列表。
4.  它将这两个列表合并，并去除重复项，生成最终需要保活的仓库全集。
5.  对于列表中的每一个仓库 (`user/repo`)：
    a. 它会从 `KEEP_ALIVE_PATS` Secret 中查找 `user` 对应的 PAT。
    b. 使用该 PAT 克隆目标仓库。
    c. 从预设的列表中随机选择一条提交信息。
    d. 执行一次空提交 (`git commit --allow-empty`)。
    e. 将这个空提交推送到目标仓库。
