# MCP SuperiorAPIs Local

本專案是一個基於 Python 的 **MCP 伺服器**，能夠動態地從 **SuperiorAPIs** 獲取插件定義，並基於 OpenAPI 架構自動生成 MCP 工具函數。

它採用 `stdio` 模式運行，非常適合本地開發和與 AI 客戶端進行測試。

如果您需要使用 HTTP 或 SSE 協議進行整合，請參考：[CTeaminfo/mcp_superiorapis_remote](https://github.com/CTeaminfo/mcp_superiorapis_remote)

## 📂 專案結構

```
mcp_superiorapis_local/
├── src/mcp_superiorapis_local/     # 主程式
│   ├── __init__.py           # 套件初始化
│   └── server.py             # MCP 伺服器實作
├── tests/                    # 測試檔案
├── pyproject.toml            # 專案配置與依賴套件
├── uv.lock                   # 鎖定的依賴套件
└── README.md                 # 專案說明文件
```

## 🚀 快速開始

### 1. 環境準備

**前置條件：**
- Python 3.13+
- Superior APIs Token ([如何取得](https://superiorapis-creator.cteam.com.tw))

### 2. 克隆專案

```bash
# 使用 HTTPS
git clone https://github.com/CTeaminfo/mcp_superiorapis_local.git

# 使用 SSH
git clone git@github.com:CTeaminfo/mcp_superiorapis_local.git
cd mcp_superiorapis_local
```

### 3. 安裝 uv（如果未安裝）

```bash
# macOS/Linux
curl -LsSf https://astral.sh/uv/install.sh | sh

# Windows
powershell -c "irm https://astral.sh/uv/install.ps1 | iex"

# 或使用 pip
pip install uv
```

### 4. 安裝依賴套件

```bash
# 建立虛擬環境
uv venv --python 3.13

# 安裝依賴套件
uv sync

# 或使用 pip
pip install -e .
```

### 5. 配置環境變數

```bash
# 設定您的 Superior APIs token
export TOKEN=your_superior_apis_token_here

# Windows CMD
set TOKEN=your_superior_apis_token_here
```

**Token 驗證說明：**
- 從 [Superior APIs](https://superiorapis-creator.cteam.com.tw) 取得您的 token
- 在執行伺服器前設定 TOKEN 環境變數

### 6. 啟動伺服器

```bash
# 啟動虛擬環境（Windows）
venv\Scripts\activate

# 設定環境變數
set TOKEN=your_superior_apis_token_here

# 執行伺服器
python -m mcp_superiorapis_local
```

### 7. 驗證部署

伺服器將會：
1. 從 SuperiorAPIs 取得插件資料
2. 動態生成 MCP 工具函數
3. 註冊工具
4. 以 stdio 模式啟動 MCP 伺服器

## 🔌 MCP 客戶端整合

### 使用 uvx 與 Pip

透過 uvx 配置 MCP 伺服器（無需下載原始碼）：

```json
{
  "mcpServers": {
    "mcp_superiorapis_local": {
      "command": "uvx",
      "args": [
        "mcp-superiorapis" // https://pypi.org/project/mcp-superiorapis/
      ],
      "env": {
        "TOKEN": "your_superior_apis_token_here"
      }
    }
  }
}
```

### 本地模式

```json
{
  "mcp_superiorapis_local": {
    "command": "uv",
    "args": [
      "run",
      "--directory",
      "/path/to/mcp_superiorapis_local",
      "python",
      "-m",
      "mcp_superiorapis_local"
    ],
    "env": {
      "TOKEN": "your_superior_apis_token_here"
    }
  }
}
```

## 🔧 重開機後的執行流程

每次重開機後，請依照以下步驟：

```cmd
# 1. 進入專案目錄
cd C:\xampp\htdocs\mcp_superiorapis_local

# 2. 啟動虛擬環境
venv\Scripts\activate

# 3. 設定環境變數
set TOKEN=your_superior_apis_token_here

# 4. 執行專案
python -m mcp_superiorapis_local
```

**注意：**
- 套件只需要安裝一次（使用 `pip install -e .` 或 `uv sync`）
- 重開機後只需要啟動虛擬環境和設定環境變數
- 虛擬環境啟動後，命令提示符會顯示 `(venv)` 前綴

## 📝 開發說明

### 核心元件

1. **入口點** (`src/mcp_superiorapis_local/__init__.py:4-6`)
   - 啟動 asyncio 事件循環並調用 server.main()

2. **伺服器核心** (`src/mcp_superiorapis_local/server.py`)
   - **動態工具註冊** (`server.py:94-125`)：從 SuperiorAPIs 取得插件定義並生成工具函數
   - **架構扁平化** (`server.py:27-76`)：處理 OpenAPI 架構，將 enum 移動到描述中
   - **API 整合** (`server.py:77-91`)：處理 SuperiorAPIs 的驗證和通訊
   - **工具執行** (`server.py:147-190`)：動態路由工具調用到適當的 API 端點

### 主要設計模式

- **動態工具生成**：工具不是硬編碼的，而是根據 API 架構在運行時生成
- **架構轉換**：OpenAPI enum 被扁平化為描述文字以符合 MCP 相容性
- **非同步架構**：所有 API 操作都採用完整的 async/await 模式
- **錯誤處理**：對 API 失敗和網路問題進行全面的錯誤處理

### 資料流

1. 伺服器啟動並調用 `register_tools()`
2. `fetch_api_data()` 從 SuperiorAPIs 獲取插件定義
3. 每個插件的 OpenAPI 架構都被處理和扁平化
4. 工具動態註冊到 MCP 伺服器
5. 當工具被調用時，請求被轉發到 SuperiorAPIs 端點

### 驗證

- 使用 TOKEN 環境變數進行 SuperiorAPIs 驗證
- Token 在所有 API 調用的請求標頭中傳遞
- 無本地憑證儲存 - 依賴環境變數

## 🔗 相關連結

- [Superior APIs](https://superiorapis-creator.cteam.com.tw) - 取得 API Token
- [MCP SuperiorAPIs Remote](https://github.com/CTeaminfo/mcp_superiorapis_remote) - HTTP/SSE 協議版本
- [MCP Protocol](https://modelcontextprotocol.io/) - 模型上下文協議官方文件