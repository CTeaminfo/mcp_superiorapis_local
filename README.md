# MCP SuperiorAPIs Local

This project is a Python-based **MCP Server** that dynamically retrieves plugin definitions from **SuperiorAPIs** and auto-generates MCP tool functions based on their OpenAPI schemas.

It operates in `stdio` mode, making it ideal for local development and testing with AI clients.

If you need to integrate using HTTP or SSE protocols, please refer to: [CTeaminfo/mcp_superiorapis_remote](https://github.com/CTeaminfo/mcp_superiorapis_remote)

## 📂 Project Structure

```
mcp_superiorapis_local/
├── src/mcp_superiorapis_local/     # Main program
│   ├── __init__.py           # Package initialization
│   └── server.py             # MCP server implementation
├── tests/                    # Test files
├── pyproject.toml            # Project config & dependencies
├── uv.lock                   # Locked dependencies
└── README.md                 # Project documentation (this file)
```

## 🚀 Quick Start

### 1. Environment Preparation

**Prerequisites:**
- Python 3.13+
- Superior APIs Token ([How to get](https://superiorapis-creator.cteam.com.tw))

### 2. Clone the Project

```bash
# Using HTTPS
git clone https://github.com/CTeaminfo/mcp_superiorapis_local.git

# Using SSH
git clone git@github.com:CTeaminfo/mcp_superiorapis_local.git
cd mcp_superiorapis_local
```

### 3. Install uv (if not installed)

```bash
# macOS/Linux
curl -LsSf https://astral.sh/uv/install.sh | sh

# Windows
powershell -c "irm https://astral.sh/uv/install.ps1 | iex"

# Or use pip
pip install uv
```

### 4. Install Dependencies

```bash
# Create virtual environment
uv venv --python 3.13

# Install dependencies
uv sync

# Or use pip
pip install -e .
```

### 5. Configure Environment Variables

```bash
# Set your Superior APIs token
export TOKEN=your_superior_apis_token_here

# Windows CMD
set TOKEN=your_superior_apis_token_here
```

**Token Authentication Instructions:**
- Get your token from [Superior APIs](https://superiorapis-creator.cteam.com.tw)
- Set the TOKEN environment variable before running the server

### 6. Start the Server

```bash

python src/mcp_superiorapis_local/server.py
```

### 7. Verify Deployment

The server will:
1. Fetch plugin data from SuperiorAPIs
2. Dynamically generate MCP tool functions
3. Register the tools
4. Start the MCP server in stdio mode

## 🔌 MCP Client Integration

### With uvx on Pip

Configure MCP server with uvx on pip(No need to download source code):

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

### Local Mode

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
