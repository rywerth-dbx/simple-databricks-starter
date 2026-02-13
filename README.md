# Simple Databricks Starter

The simplest Databricks development toolkit for local development with Claude Code. This plugin provides skills for authentication, workspace management, job orchestration, Databricks Connect configuration, and direct SQL querying via MCP.

## Overview

This Claude Code plugin provides everything you need to get up and running with Claude Code and Databricks. It's built around three core capabilities:

### 1. 📝 Writing & Running Code - Databricks Connect

Write Python code locally that runs seamlessly on Databricks without modifications using [Databricks Connect](https://docs.databricks.com/dev-tools/databricks-connect/).

**Skills:**
- [`databricks-connect-config`](skills/databricks-connect-config/) - Configure DatabricksSession for local-to-remote execution

### 2. 🔧 Workspace Operations - Databricks CLI

Interact with your Databricks workspace using the [Databricks CLI](https://docs.databricks.com/dev-tools/cli/) to upload/download files, create jobs, and more.

**Skills:**
- [`databricks-environment-setup`](skills/databricks-environment-setup/) - Comprehensive environment validation (CLI, databricks-connect, auth, MCP)
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

Install directly from the GitHub repository:

1. Start Claude Code in any directory:
   ```bash
   claude
   ```

2. Install the plugin from GitHub:
   ```
   /plugin install rywerth-dbx/simple-databricks-starter
   ```

3. The plugin will be installed and its skills will be immediately available

Learn more about [Claude Code plugins](https://code.claude.com/docs/en/discover-plugins).

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

Navigate to a project directory and start Claude Code:

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

This verifies or creates a profile in `~/.databrickscfg` for secure authentication.

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


## FAQ

### Do I need to run the setup commands everytime I start a project?

**No!** Most setup is one-time:

**One-Time Setup (Global):**
- ✅ **Plugin installation** - Install once with `/plugin install rywerth-dbx/simple-databricks-starter`, available everywhere
- ✅ **Databricks CLI** - Install once, works across all projects
- ✅ **Authentication profiles** - Set up once in `~/.databrickscfg`, persists forever
- ✅ **databricks-connect** - Install once per Python environment (if using virtual environments)

**Per-Session (Optional):**
- ⚠️ **Environment variables** - Only needed if you want DBSQL MCP queries. Set before starting Claude Code:
  ```bash
  export DATABRICKS_WORKSPACE_URL=https://your-workspace.cloud.databricks.com
  export DATABRICKS_TOKEN=dapi...
  claude
  ```

**When to Re-Run `/databricks-environment-setup`:**
- First time using the plugin
- Troubleshooting environment issues
- Setting up a new machine
- Verifying everything is working correctly

**For New Projects:**
Just start Claude Code and start working! The plugin and all configurations are already available. Only set environment variables if you need MCP data querying.

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

**Ready to start?** Install the plugin with `/plugin install rywerth-dbx/simple-databricks-starter`, run `/databricks-environment-setup`, and start building with Databricks!
