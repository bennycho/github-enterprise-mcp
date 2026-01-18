
[![MseeP.ai Security Assessment Badge](https://mseep.net/pr/containerelic-github-enterprise-mcp-badge.png)](https://mseep.ai/app/containerelic-github-enterprise-mcp)

[![Trust Score](https://archestra.ai/mcp-catalog/api/badge/quality/ddukbg/github-enterprise-mcp)](https://archestra.ai/mcp-catalog/ddukbg__github-enterprise-mcp)

# GitHub Enterprise MCP Server

![image](https://github.com/user-attachments/assets/55403caa-c81d-486a-8ec7-3a2532e7545e)

這是一個用於與 GitHub Enterprise API 整合的 MCP (Model Context Protocol) 伺服器。此伺服器提供 MCP 介面，讓您能輕鬆地在 Cursor 中存取 GitHub Enterprise 的儲存庫資訊、Issues、PR 等內容。

## 相容性 (Compatibility)

本專案主要是為 GitHub Enterprise Server 環境設計，但也適用於：

- GitHub.com
- GitHub Enterprise Cloud

> **注意**：某些企業特定功能（如授權資訊和企業統計數據）在 GitHub.com 或 GitHub Enterprise Cloud 上無法運作。

## 主要功能 (Key Features)

- 從 GitHub Enterprise 實例檢索儲存庫列表
- 獲取詳細的儲存庫資訊
- 列出儲存庫分支
- 查看檔案和目錄內容
- 管理 Issues 和 Pull Requests (PR)
- 儲存庫管理（建立、更新、刪除）
- GitHub Actions 工作流程 (Workflows) 管理
- 使用者管理（列出、建立、更新、刪除、暫停/恢復使用者）
- 存取企業統計數據
- 增強的錯誤處理和友善的回應格式

## 開始使用 (Getting Started)

### 先決條件 (Prerequisites)

- Node.js 18 或更高版本
- 能夠存取 GitHub Enterprise 實例
- 個人存取權杖 (Personal Access Token, PAT)

### Docker 安裝與設定

#### 選項 1：使用 Docker 執行

1. 建置 Docker 映像檔：

    ```bash
    docker build -t github-enterprise-mcp .
    ```

2. 使用環境變數執行 Docker 容器：

    ```bash
    docker run -p 3000:3000 \
      -e GITHUB_TOKEN="your_github_token" \
      -e GITHUB_ENTERPRISE_URL="https://github.your-company.com/api/v3" \
      -e DEBUG=true \
      github-enterprise-mcp
    ```

> **注意**：Dockerfile 預設配置為使用 `--transport http` 執行。如果需要更改此設定，您可以覆蓋該命令：

```bash
docker run -p 3000:3000 \
  -e GITHUB_TOKEN="your_github_token" \
  -e GITHUB_ENTERPRISE_URL="https://github.your-company.com/api/v3" \
  -e DEBUG=true \
  github-enterprise-mcp node dist/index.js --transport http --debug
```

#### 選項 2：使用 Docker Compose

1. 在專案根目錄建立一個 `.env` 檔案，並填入所需的環境變數：

    ```shell
    GITHUB_ENTERPRISE_URL=https://github.your-company.com/api/v3
    GITHUB_TOKEN=your_github_token
    DEBUG=true
    ```

2. 使用 Docker Compose 啟動容器：

    ```bash
    docker-compose up -d
    ```

3. 查看日誌：

    ```bash
    docker-compose logs -f
    ```

4. 停止容器：

    ```bash
    docker-compose down
    ```

### 安裝與設定 (Installation and Setup)

#### 本地開發（使用並行模式）

此方法推薦用於需要自動重新編譯和伺服器重啟的活躍開發過程：

1. 複製儲存庫並安裝所需套件：

    ```bash
    git clone https://github.com/ddukbg/github-enterprise-mcp.git
    cd github-enterprise-mcp
    npm install
    ```

2. 執行開發伺服器：

    ```bash
    export GITHUB_TOKEN="your_github_token"
    export GITHUB_ENTERPRISE_URL="https://github.your-company.com/api/v3"
    npm run dev
    ```

    這將會：
    - 當檔案變更時自動編譯 TypeScript 程式碼
    - 當編譯後的檔案更新時重新啟動伺服器
    - 以 HTTP 模式執行伺服器以進行基於 URL 的連接

3. 如下所述使用 URL 模式連接到 Cursor

### 生產環境安裝與設定

#### 選項 1：使用 URL 模式（推薦用於本地開發）

此方法最穩定，推薦用於本地開發或測試：

1. 複製儲存庫並安裝所需套件：

    ```bash
    git clone https://github.com/ddukbg/github-enterprise-mcp.git
    cd github-enterprise-mcp
    npm install
    ```

2. 建置專案：

    ```bash
    npm run build
    chmod +x dist/index.js
    ```

3. 執行伺服器：

    ```bash
    export GITHUB_TOKEN="your_github_token"
    export GITHUB_ENTERPRISE_URL="https://github.your-company.com/api/v3"
    node dist/index.js --transport http --debug
    ```

4. 使用 URL 模式連接到 Cursor：
   - 將以下內容新增到您的 Cursor `.cursor/mcp.json` 檔案中：

   ```json
   {
     "mcpServers": {
       "github-enterprise": {
         "url": "http://localhost:3000/sse"
       }
     }
   }
   ```

#### 選項 2：安裝為全域指令 (npm link)

此方法適用於本地開發：

```bash
# 複製儲存庫後
git clone https://github.com/ddukbg/github-enterprise-mcp.git
cd github-enterprise-mcp

# 安裝所需套件
npm install

# 建置
npm run build
chmod +x dist/index.js

# 全域連結
npm link

# 作為全域指令執行
export GITHUB_TOKEN="your_github_token"
export GITHUB_ENTERPRISE_URL="https://github.your-company.com/api/v3"
github-enterprise-mcp --transport=http --debug
```

#### 選項 3：使用 npx（當套件已發布時）

如果套件已發布到公共 npm 註冊表：

```bash
npx @bennycho/github-enterprise-mcp --token=your_github_token --github-enterprise-url=https://github.your-company.com/api/v3
```

## 與 AI 工具整合 (Integration with AI Tools)

### Claude Desktop

將以下內容新增到您的 `claude_desktop_config.json`：

```json
{
  "mcpServers": {
    "github-enterprise": {
      "command": "npx",
      "args": ["-y", "@bennycho/github-enterprise-mcp", "--token=YOUR_GITHUB_TOKEN", "--github-enterprise-url=YOUR_GITHUB_ENTERPRISE_URL"]
    }
  }
}
```

請將 `YOUR_GITHUB_TOKEN` 和 `YOUR_GITHUB_ENTERPRISE_URL` 替換為您的實際值。

### Cursor

#### 推薦：URL 模式（最穩定）

為了在 Cursor 中獲得最可靠的操作體驗，建議使用 URL 模式：

1. 在單獨的終端機視窗中啟動伺服器：

   ```bash
   cd /path/to/github-enterprise-mcp
   GITHUB_ENTERPRISE_URL="https://github.your-company.com/api/v3" GITHUB_TOKEN="your_github_token" node dist/index.js --transport http
   ```

2. 設定 Cursor 的 MCP 設定：
   - 開啟 Cursor 並前往 **Settings (設定)**
   - 導航至 **AI > MCP Servers**
   - 編輯您的 `.cursor/mcp.json` 檔案：

   ```json
   {
     "mcpServers": {
       "github-enterprise": {
         "url": "http://localhost:3000/sse"
       }
     }
   }
   ```

3. 重啟 Cursor 以套用變更

#### 替代方案：Command 模式

或者，您可以將 Cursor 設定為使用 Command 模式，儘管 URL 模式更為可靠：

1. 開啟 Cursor 並前往 **Settings (設定)**
2. 導航至 **AI > MCP Servers**
3. 點擊 **Add MCP Server (新增 MCP 伺服器)**
4. 輸入以下詳細資訊：
   - **Name**: GitHub Enterprise
   - **Command**: `npx`
   - **Arguments**: `@bennycho/github-enterprise-mcp`
   - **Environment Variables**:
     - `GITHUB_ENTERPRISE_URL`: 您的 GitHub Enterprise API URL
     - `GITHUB_TOKEN`: 您的 GitHub 個人存取權杖 (PAT)

或者，您可以手動編輯 `.cursor/mcp.json` 檔案以包含：

```json
{
  "mcpServers": {
    "github-enterprise": {
      "command": "npx",
      "args": [
        "@bennycho/github-enterprise-mcp"
      ],
      "env": {
        "GITHUB_ENTERPRISE_URL": "https://github.your-company.com/api/v3",
        "GITHUB_TOKEN": "your_github_token"
      }
    }
  }
}
```

## 語言設定 (Language Configuration)

此 MCP 伺服器支援英文和韓文。您可以使用以下方式設定語言：

### 環境變數

```bash
# 設定語言為韓文
export LANGUAGE=ko

# 或者在 .env 檔案中
LANGUAGE=ko
```

### 命令列參數

```bash
# 設定語言為韓文
node dist/index.js --language ko
```

如果未指定，預設語言為英文。

## HTTP 模式下的其他選項

- `--debug`: 啟用除錯日誌
- `--github-enterprise-url <URL>`: 設定 GitHub Enterprise API URL
- `--token <TOKEN>`: 設定 GitHub 個人存取權杖
- `--language <LANG>`: 設定語言（en 或 ko，預設值：en）

## 可用的 MCP 工具 (Available MCP Tools)

此 MCP 伺服器提供以下工具：

| 工具名稱 | 描述 | 參數 | 必要的 PAT 權限 |
|---|---|---|---|
| `list-repositories` | 檢索使用者或組織的儲存庫列表 | `owner`: 使用者名稱/組織名稱<br>`isOrg`: 是否為組織<br>`type`: 儲存庫類型<br>`sort`: 排序標準<br>`page`: 頁碼<br>`perPage`: 每頁項目數 | `repo` |
| `get-repository` | 獲取詳細的儲存庫資訊 | `owner`: 儲存庫擁有者<br>`repo`: 儲存庫名稱 | `repo` |
| `list-branches` | 列出儲存庫的分支 | `owner`: 儲存庫擁有者<br>`repo`: 儲存庫名稱<br>`protected_only`: 是否只顯示受保護的分支<br>`page`: 頁碼<br>`perPage`: 每頁項目數 | `repo` |
| `get-content` | 檢索檔案或目錄內容 | `owner`: 儲存庫擁有者<br>`repo`: 儲存庫名稱<br>`path`: 檔案/目錄路徑<br>`ref`: 分支/提交 (選填) | `repo` |
| `list-pull-requests` | 列出儲存庫中的 Pull Requests | `owner`: 儲存庫擁有者<br>`repo`: 儲存庫名稱<br>`state`: PR 狀態篩選<br>`sort`: 排序標準<br>`direction`: 排序方向<br>`page`: 頁碼<br>`per_page`: 每頁項目數 | `repo` |
| `get-pull-request` | 獲取 Pull Request 詳情 | `owner`: 儲存庫擁有者<br>`repo`: 儲存庫名稱<br>`pull_number`: Pull Request 編號 | `repo` |
| `create-pull-request` | 建立新的 Pull Request | `owner`: 儲存庫擁有者<br>`repo`: 儲存庫名稱<br>`title`: PR 標題<br>`head`: Head 分支<br>`base`: Base 分支<br>`body`: PR 描述<br>`draft`: 建立為草稿 PR | `repo` |
| `merge-pull-request` | 合併 Pull Request | `owner`: 儲存庫擁有者<br>`repo`: 儲存庫名稱<br>`pull_number`: Pull Request 編號<br>`merge_method`: 合併方式<br>`commit_title`: 提交標題<br>`commit_message`: 提交訊息 | `repo` |
| `list-issues` | 列出儲存庫中的 Issues | `owner`: 儲存庫擁有者<br>`repo`: 儲存庫名稱<br>`state`: Issue 狀態篩選<br>`sort`: 排序標準<br>`direction`: 排序方向<br>`page`: 頁碼<br>`per_page`: 每頁項目數 | `repo` |
| `get-issue` | 獲取 Issue 詳情 | `owner`: 儲存庫擁有者<br>`repo`: 儲存庫名稱<br>`issue_number`: Issue 編號 | `repo` |
| `list-issue-comments` | 列出 Issue 或 Pull Request 的留言 | `owner`: 儲存庫擁有者<br>`repo`: 儲存庫名稱<br>`issue_number`: Issue/PR 編號<br>`page`: 頁碼<br>`per_page`: 每頁項目數 | `repo` |
| `create-issue` | 建立新的 Issue | `owner`: 儲存庫擁有者<br>`repo`: 儲存庫名稱<br>`title`: Issue 標題<br>`body`: Issue 內容<br>`labels`: 標籤名稱陣列<br>`assignees`: 被指派者登入帳號陣列<br>`milestone`: 里程碑 ID | `repo` |
| `create-repository` | 建立新的儲存庫 | `name`: 儲存庫名稱<br>`description`: 儲存庫描述<br>`private`: 是否私人<br>`auto_init`: 使用 README 初始化<br>`gitignore_template`: 新增 .gitignore<br>`license_template`: 新增授權條款<br>`org`: 組織名稱 | `repo` |
| `update-repository` | 更新儲存庫設定 | `owner`: 儲存庫擁有者<br>`repo`: 儲存庫名稱<br>`description`: 新描述<br>`private`: 變更隱私設定<br>`default_branch`: 變更預設分支<br>`has_issues`: 啟用/停用 issues<br>`has_projects`: 啟用/停用 projects<br>`has_wiki`: 啟用/停用 wiki<br>`archived`: 封存/取消封存 | `repo` |
| `delete-repository` | 刪除儲存庫 | `owner`: 儲存庫擁有者<br>`repo`: 儲存庫名稱<br>`confirm`: 確認 (必須為 true) | `delete_repo` |
| `list-workflows` | 列出 GitHub Actions 工作流程 | `owner`: 儲存庫擁有者<br>`repo`: 儲存庫名稱<br>`page`: 頁碼<br>`perPage`: 每頁項目數 | `actions:read` |
| `list-workflow-runs` | 列出工作流程執行記錄 | `owner`: 儲存庫擁有者<br>`repo`: 儲存庫名稱<br>`workflow_id`: 工作流程 ID/檔名<br>`branch`: 依分支篩選<br>`status`: 依狀態篩選<br>`page`: 頁碼<br>`perPage`: 每頁項目數 | `actions:read` |
| `trigger-workflow` | 觸發工作流程 | `owner`: 儲存庫擁有者<br>`repo`: 儲存庫名稱<br>`workflow_id`: 工作流程 ID/檔名<br>`ref`: Git 參照<br>`inputs`: 工作流程輸入 | `actions:write` |
| `get-license-info` | 獲取 GitHub Enterprise 授權資訊 | - | **需要 site_admin (管理員) 帳號** |
| `get-enterprise-stats` | 獲取 GitHub Enterprise 系統統計數據 | - | **需要 site_admin (管理員) 帳號** |

> **注意**：對於企業特定工具（`get-license-info` 和 `get-enterprise-stats`），需要具有 **網站管理員 (site administrator)** 權限的使用者。建議使用 Classic Personal Access Token，因為 Fine-grained tokens 可能不支援這些企業級權限。

## 在 Cursor 中使用工具

設定好 MCP 伺服器並配置 Cursor 連接後，您可以在 Cursor 的 AI 聊天中直接使用 GitHub Enterprise 工具。以下是一些範例：

### 列出儲存庫

```js
mcp_github_enterprise_list_repositories(owner="octocat")
```

### 獲取儲存庫資訊

```js
mcp_github_enterprise_get_repository(owner="octocat", repo="hello-world")
```

### 列出 Pull Requests

```js
mcp_github_enterprise_list_pull_requests(owner="octocat", repo="hello-world", state="open")
```

### 管理 Issues

```js
# 列出 issues
mcp_github_enterprise_list_issues(owner="octocat", repo="hello-world", state="all")
# 獲取 issue 詳情
mcp_github_enterprise_get_issue(owner="octocat", repo="hello-world", issue_number=1)

# 獲取 issue/PR 留言
mcp_github_enterprise_list_issue_comments(owner="octocat", repo="hello-world", issue_number=1)


# 建立新的 issue
mcp_github_enterprise_create_issue(
  owner="octocat", 
  repo="hello-world",
  title="Found a bug",
  body="Here is a description of the bug",
  labels=["bug", "important"]
)
```

### 使用儲存庫內容

```js
mcp_github_enterprise_get_content(owner="octocat", repo="hello-world", path="README.md")
```

### 儲存庫管理

```
# 建立新的儲存庫
mcp_github_enterprise_create_repository(
  name="new-project",
  description="This is a new project",
  private=true,
  auto_init=true
)

# 更新儲存庫設定
mcp_github_enterprise_update_repository(
  owner="octocat",
  repo="hello-world",
  description="Updated description",
  has_issues=true
)
```

### 使用者管理（僅限 Enterprise）

這些功能專為 GitHub Enterprise Server 環境設計，需要管理員權限：

```js
# 列出 GitHub Enterprise 實例中的所有使用者
mcp_github_enterprise_list_users(filter="active", per_page=100)

# 獲取特定使用者的詳細資訊
mcp_github_enterprise_get_user(username="octocat")

# 建立新使用者 (僅限 Enterprise)
mcp_github_enterprise_create_user(
  login="newuser",
  email="newuser@example.com",
  name="New User",
  company="ACME Inc."
)

# 更新使用者資訊 (僅限 Enterprise)
mcp_github_enterprise_update_user(
  username="octocat",
  email="updated-email@example.com",
  location="San Francisco"
)

# 暫停使用者 (僅限 Enterprise)
mcp_github_enterprise_suspend_user(
  username="octocat",
  reason="Violation of terms of service"
)

# 恢復使用者 (僅限 Enterprise)
mcp_github_enterprise_unsuspend_user(username="octocat")

# 列出使用者所屬的組織
mcp_github_enterprise_list_user_orgs(username="octocat")
```

## API 改進 (API Improvements)

- 靈活的 API URL 配置（支援各種環境變數和命令列參數）
- 增強的錯誤處理和逾時管理
- 友善的回應格式和訊息

## 貢獻 (Contributing)

歡迎提交貢獻！請隨意提交 Pull Request。

## 授權 (License)

ISC
