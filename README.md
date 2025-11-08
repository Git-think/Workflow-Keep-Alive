# GitHub Keep-Alive Workflow (Stealth Edition)

这是一个 GitHub Actions 工作流，旨在通过定期创建空提交（Empty Commit）来防止您的仓库因长期不活跃而被 GitHub 归档。

此版本经过终极优化，能够**模拟仓库所有者本人进行提交**，并支持**零配置**运行，是目前最智能、最隐蔽的解决方案。

## ✨ 功能特性

- **🎭 终极隐身**：工作流会自动将每次提交的作者设置为**目标仓库的所有者**。在提交历史上，这看起来就像是仓库所有者本人进行的一次常规维护，无法被识别为自动化行为。
- **🚀 零配置启动**：如果只想保活当前仓库，您**无需配置任何 Secrets 或 Variables**，只需将工作流文件放入仓库即可。
- **🧠 智能认证**：
  - **保活当前仓库时**：自动使用 `GITHUB_TOKEN` 进行授权。
  - **保活其他仓库时**：自动切换到您配置的个人访问令牌（PAT）进行授权。
- **🛡️ 隐蔽安全**：通过带有随机提交信息（如 `chore: routine maintenance`）的空提交，模拟正常的维护活动。
- **👥 多用户支持**：可以轻松配置多个用户的仓库，并为每个用户使用独立的 PAT。

---

## 📖 配置指南

### 场景一：只保活当前仓库 (最简单)

1.  将 `.github/workflows/keep-alive.yml` 文件放入您的仓库。
2.  提交并推送。

**完成！** 您不需要进行任何额外的配置。提交记录的作者将自动显示为您自己。

---

### 场景二：保活当前仓库 + 其他仓库 (高级)

如果您需要保活其他仓库，则需要进行以下配置。

#### 步骤 1: 创建个人访问令牌 (PAT) - (如果需要)

仅当您需要保活**其他用户**或**您自己其他**的仓库时，才需要此步骤。

1.  **导航至 PAT 创建页面**:
    - 访问链接：[**Generate a new token (classic)**](https://github.com/settings/tokens/new)。

2.  **配置令牌**:
    - **Note**: 起一个描述性的名字，例如 `keep-alive-workflow-token`。
    - **Expiration**: 建议选择 `No expiration`。
    - **Select scopes**: 务必勾选 `repo` 这个顶级复选框。

3.  **生成并保存令牌**:
    - 点击 `Generate token` 并**立即复制生成的令牌**。

#### 步骤 2: 配置仓库 Secrets - (如果需要)

1.  **导航至 Secrets 设置页面**:
    - 在**存放此工作流的仓库**中，点击 `Settings` > `Secrets and variables` > `Actions`。

2.  **创建 `KEEP_ALIVE_PATS` Secret**:
    - **Name**: `KEEP_ALIVE_PATS`
    - **Secret**: 粘贴一个**单行**的 JSON 字符串，将 GitHub 用户名映射到 PAT。
      ```json
      {"Username1":"ghp_YourPatToken1","Username2":"ghp_AnotherPatToken2"}
      ```

#### 步骤 3: 配置额外仓库列表 - (如果需要)

您可以选择将此列表设为 Secret（保密）或 Variable（公开）。

- **优先级**: 工作流会**优先检查 Secret** `REPOSITORIES_TO_KEEP_ALIVE`，若不存在则**回退到 Variable** `REPOSITORIES_TO_KEEP_ALIVE`。

##### 选项 A: 使用 Secret (推荐)
1.  在 `Secrets` 页面，点击 `New repository secret`。
2.  **Name**: `REPOSITORIES_TO_KEEP_ALIVE`
3.  **Secret**: 粘贴一个用逗号分隔的仓库列表 (`用户名/仓库名`)。

##### 选项 B: 使用 Variable
1.  切换到 `Variables` 标签页，点击 `New repository variable`。
2.  **Name**: `REPOSITORIES_TO_KEEP_ALIVE`
3.  **Value**: 粘贴一个用逗号分隔的仓库列表。

### 步骤 4: 部署

将您的更改提交并推送到仓库。工作流将根据您的配置智能运行，并且每一次提交都将完美地伪装成仓库所有者本人的操作。
