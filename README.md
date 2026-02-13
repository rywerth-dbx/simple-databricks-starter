# Simple Databricks Starter

A complete Databricks development toolkit for local development with Claude Code. This plugin provides skills for authentication, workspace management, job orchestration, Databricks Connect configuration, and direct SQL querying via MCP.

## Overview

This Claude Code plugin provides everything you need to develop Databricks applications locally. It's built around three core capabilities:

### 1. 📝 Writing & Running Code - Databricks Connect

Write Python code locally that runs seamlessly on Databricks without modifications using [Databricks Connect](https://docs.databricks.com/dev-tools/databricks-connect/).

**Skills:**
- [`databricks-connect-config`](skills/databricks-connect-config/) - Configure DatabricksSession for local-to-remote execution

### 2. 🔧 Workspace Operations - Databricks CLI

Interact with your Databricks workspace using the [Databricks CLI](https://docs.databricks.com/dev-tools/cli/) to upload/download files, create jobs, and more.

**Skills:**
- [`databricks-environment-setup`](skills/databricks-environment-setup/) - Comprehensive environment validation (CLI, databricks-connect, auth, .env)
- [`databricks-auth-manager`](skills/databricks-auth-manager/) - Configure OAuth authentication with profiles
- [`databricks-job-orchestrator`](skills/databricks-job-orchestrator/) - Create, run, and monitor jobs
- [`databricks-workspace-sync`](skills/databricks-workspace-sync/) - Upload and download files to/from workspace

### 3. 🔍 Querying Data - Databricks DBSQL MCP Server

Query your data directly using the [Databricks DBSQL MCP Server](https://docs.databricks.com/generative-ai/mcp/managed-mcp).

**Configuration:** `.mcp.json` (included in plugin)

## Installation

### Prerequisites

- [Claude Code](https://claude.ai/code) - Claude's official CLI
- A Databricks workspace
- macOS, Linux, or Windows with WSL

### Install the Plugin

```bash
# Clone the repository
git clone https://github.com/rywerth-dbx/simple-databricks-starter.git

# Link or install the plugin
# (Installation method depends on Claude Code plugin support)
```

**Note:** Once Claude Code supports plugin installation, you'll be able to install directly from GitHub. Until then, you can manually link skills to your `.claude/skills/` directory or use the plugin locally.

## Quick Start

### Step 0: Set Environment Variables (Optional but Recommended)

To enable **DBSQL MCP queries** (so Claude can query your data directly), set these environment variables **before starting Claude Code**:

```bash
export DATABRICKS_WORKSPACE_URL=https://your-workspace.cloud.databricks.com
export DATABRICKS_TOKEN=dapi...your-token-here
```

**How to get these values:**
- **Workspace URL**: Your Databricks workspace URL (check your browser when logged in)
- **Token**: Generate a personal access token:
  - Go to User Settings → Developer → Access Tokens
  - Click "Generate New Token"
  - Copy the token value
  - [PAT Documentation](https://docs.databricks.com/dev-tools/auth/pat.html)

**Important:** If you skip this step, all skills will work (auth, connect, workspace sync, jobs) **except** DBSQL MCP queries. You can always set these later and start a new session.

### Step 1: Run Environment Setup

Navigate to your Databricks project directory and start Claude Code:

```bash
cd ~/my-databricks-project
claude
```

In Claude Code, run the comprehensive environment setup skill:

```
/databricks-environment-setup
```

This skill will:
- ✅ Check for Databricks CLI (v0.205+)
- ✅ Check for databricks-connect in your Python environment
- ✅ Verify authentication profiles exist
- ✅ Check if MCP environment variables are set (for data querying)
- ✅ Detect conflicts (like pyspark)

**What happens if something is missing?**

The skill will guide you through installation:

**Databricks CLI missing?**
```bash
# macOS
brew tap databricks/tap
brew install databricks

# Linux
curl -fsSL https://raw.githubusercontent.com/databricks/setup-cli/main/install.sh | sh

# Windows
winget install Databricks.DatabricksCLI
```

**databricks-connect missing?**
```bash
# Using uv (recommended)
uv add "databricks-connect==17.3.*"

# Or using pip
pip install "databricks-connect==17.3.*"
```

### Step 2: Set Up Authentication

Configure OAuth authentication for CLI operations:

```
/databricks-auth-manager
```

This creates a profile in `~/.databrickscfg` for secure authentication.

### Step 3: Validate Databricks Connect

Verify that local-to-remote execution works:

```
/databricks-connect-config
```

### Step 4: Start Building!

You're now ready to work with Databricks! Ask Claude to:
- Analyze tables and explore schemas
- Write PySpark code using Databricks Connect
- Upload notebooks and scripts to your workspace
- Create and manage Databricks jobs
- Query your data directly

## Skills Reference

### Core Skills

| Skill | Description | When to Use |
|-------|-------------|-------------|
| **databricks-environment-setup** | Comprehensive environment validation | **Run this FIRST** when starting any project |
| **databricks-auth-manager** | OAuth authentication setup | When setting up authentication or managing profiles |
| **databricks-connect-config** | DatabricksSession configuration | After auth setup, to validate local-to-remote execution |
| **databricks-workspace-sync** | Upload/download workspace files | When managing notebooks and scripts |
| **databricks-job-orchestrator** | Create and manage jobs | When orchestrating workflows |

### Skill Descriptions

#### databricks-environment-setup
Validates ALL prerequisites for Databricks development:
- Checks Databricks CLI installation (v0.205+)
- Checks databricks-connect in Python environment
- Verifies authentication profiles exist
- Checks for `.env` configuration
- Detects conflicts (pyspark)
- Provides installation guidance for missing components

#### databricks-auth-manager
Manages Databricks CLI authentication:
- OAuth 2.0 flow (recommended)
- Profile management in `~/.databrickscfg`
- Multi-workspace support

#### databricks-connect-config
Configures and validates Databricks Connect:
- DatabricksSession setup
- Connection testing
- Troubleshooting guidance
- Remote cluster configuration

#### databricks-workspace-sync
Upload and download files to/from Databricks workspace:
- Upload notebooks (.py, .ipynb)
- Download files from workspace
- Directory synchronization

#### databricks-job-orchestrator
Create and manage Databricks jobs:
- Job creation from notebooks/scripts
- Job monitoring and status checking
- Run history and logs

## Configuration

### Environment Variables (Optional - For MCP)

The DBSQL MCP server requires two environment variables to be set **before starting Claude Code**:

```bash
export DATABRICKS_WORKSPACE_URL=https://your-workspace.cloud.databricks.com
export DATABRICKS_TOKEN=dapi...your-token-here
claude
```

**What these enable:**
- Direct data querying via DBSQL MCP server
- Claude can explore your tables and schemas
- Claude can write and execute SQL queries

**What still works without them:**
- All skills (auth, connect, workspace sync, jobs)
- Databricks Connect for PySpark code
- CLI operations

If you skip setting these initially, you can always set them later and start a new Claude Code session.

### MCP Server Configuration (.mcp.json)

The plugin includes pre-configured MCP server settings for Databricks DBSQL:

```json
{
  "mcpServers": {
    "databricks-dbsql": {
      "type": "http",
      "url": "${DATABRICKS_WORKSPACE_URL}/api/2.0/mcp/sql",
      "headers": {
        "Authorization": "Bearer ${DATABRICKS_TOKEN}"
      }
    }
  }
}
```

This configuration is automatically loaded when you install the plugin.

## Troubleshooting

### CLI Not Found
**Problem:** `databricks: command not found`

**Solution:** Run `/databricks-environment-setup` to install the CLI.

### databricks-connect Not Found
**Problem:** `ModuleNotFoundError: No module named 'databricks.connect'`

**Solution:**
```bash
# Using uv
uv add "databricks-connect==17.3.*"

# Or using pip
pip install "databricks-connect==17.3.*"
```

Then re-run `/databricks-environment-setup` to validate.

### Authentication Failed
**Problem:** CLI commands return authentication errors

**Solution:** Run `/databricks-auth-manager` to configure OAuth authentication.

### Connection Error
**Problem:** Databricks Connect can't connect to workspace

**Solution:**
1. Verify authentication: Run `/databricks-auth-manager`
2. Test connection: Run `/databricks-connect-config`
3. Check profile: Ensure `DATABRICKS_CONFIG_PROFILE` in `.env` matches your `~/.databrickscfg` profile

### MCP Server Issues
**Problem:** Claude can't query your data

**Solution:**
The DBSQL MCP server requires environment variables to be set **before starting Claude Code**:

```bash
export DATABRICKS_WORKSPACE_URL=https://your-workspace.cloud.databricks.com
export DATABRICKS_TOKEN=dapi...your-token-here
claude
```

To verify they're set:
```bash
echo $DATABRICKS_WORKSPACE_URL
echo $DATABRICKS_TOKEN
```

If they're not set or incorrect:
1. Set the environment variables in your terminal
2. Exit Claude Code
3. Start a new Claude Code session
4. Run `/databricks-environment-setup` to verify

### PySpark Conflict
**Problem:** Import errors with databricks-connect

**Solution:** databricks-connect and pyspark are mutually exclusive. Uninstall pyspark:
```bash
pip uninstall pyspark
```

## Contributing

Contributions are welcome! Please feel free to submit issues or pull requests.

### Areas for Contribution
- Additional skills for Databricks features (Lakebase, Apps, etc.)
- Enhanced error handling and diagnostics
- Documentation improvements
- Platform-specific installation guides

## Resources

- [Databricks Connect](https://docs.databricks.com/dev-tools/databricks-connect/)
- [Databricks CLI](https://docs.databricks.com/dev-tools/cli/)
- [Databricks DBSQL MCP Server](https://docs.databricks.com/generative-ai/mcp/managed-mcp)
- [Claude Code](https://claude.ai/code)
- [uv - Fast Python package manager](https://docs.astral.sh/uv/)

## License

MIT License - see [LICENSE](LICENSE) file for details.

## Author

Ryan Werth

---

**Ready to start?** Clone this repository, run `/databricks-environment-setup` in Claude Code, and start building with Databricks!
