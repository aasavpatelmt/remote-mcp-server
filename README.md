# Expense Tracker MCP Server (Remote & Local)

An Model Context Protocol (MCP) server built with [FastMCP](https://github.com/jasonjmcghee/fastmcp) and SQLite (`aiosqlite`) to track and analyze expenses.

## How it Works (Remote vs. Local)

This project is structured to support two workflows:
1. **Remote MCP Server (Production)**: The actual server runs remotely in the cloud (e.g., hosted at `https://aasav-patel-mt.fastmcp.app/mcp`). To connect to this remote server, you run `proxy.py` locally. This script acts as a proxy, receiving STDIO commands from Claude Desktop and forwarding them over the internet to your remote server.
2. **Local MCP Server (Development/Testing)**: You can run the server locally on your own machine using `main.py`. This is useful for development, testing, and debugging.

---

## Table of Contents
1. [Features](#features)
2. [Prerequisites](#prerequisites)
3. [Setup & Installation](#setup--installation)
4. [How to Run](#how-to-run)
   - [Connect to the Remote Server (via Proxy)](#connect-to-the-remote-server-via-proxy)
   - [Run the Local Server (for Testing)](#run-the-local-server-for-testing)
5. [Connecting to Claude Desktop](#connecting-to-claude-desktop)
6. [MCP Interface & Capabilities](#mcp-interface--capabilities)
   - [Tools](#tools)
   - [Resources](#resources)

---

## Features

- **Asynchronous SQLite DB**: Fully non-blocking database operations powered by `aiosqlite`.
- **Expense Logging**: Add, list, and summarize expenses over specified date ranges.
- **Dynamic Categories**: Uses local `categories.json` (or safe defaults) to serve expense categories as an MCP resource.
- **Flexible Transport Protocols**:
  - **Local HTTP**: Expose the server endpoint over HTTP.
  - **Cloud Proxy (STDIO)**: Bridge a hosted deployment (e.g., FastMCP Cloud) to local STDIO, perfect for Claude Desktop integration.

---

## Prerequisites

- **Python**: `>= 3.11`
- **Package Manager**: Recommended to use [uv](https://github.com/astral-sh/uv) or standard `pip`.

---

## Setup & Installation

1. **Clone the repository** (or navigate to the workspace directory):
   ```bash
   cd test-remote-mcp-server-main
   ```

2. **Set up a Virtual Environment & Install Dependencies**:

   Using **`uv`** (Recommended):
   ```bash
   uv venv
   source .venv/bin/activate
   uv sync
   ```

   Using **`pip`**:
   ```bash
   python -m venv .venv
   source .venv/bin/activate
   pip install -e .
   ```

---

## How to Run

### Connect to the Remote Server (via Proxy)
This option allows Claude Desktop (or any local client) to communicate directly with your remote hosted server. The local script `proxy.py` forwards your local STDIO requests to the remote endpoint.

```bash
python proxy.py
```

### Run the Local Server (for Testing)
If you want to run the server logic and database locally (for testing, changes, or debugging), you can start the HTTP server directly:

```bash
python main.py
```

- **Host**: `0.0.0.0`
- **Port**: `8000`
- **Endpoint**: `http://localhost:8000/mcp`

---

## Connecting to Claude Desktop

To use this server with Claude Desktop, configure your `claude_desktop_config.json` file.

### Local Proxy Configuration (STDIO)
Point Claude Desktop to run `proxy.py` inside your virtual environment to relay requests:

```json
{
  "mcpServers": {
    "expense-tracker-proxy": {
      "command": "/Users/mtpc-386/Desktop/Aasav/test-remote-mcp-server-main/.venv/bin/python",
      "args": [
        "/Users/mtpc-386/Desktop/Aasav/test-remote-mcp-server-main/proxy.py"
      ]
    }
  }
}
```

> [!NOTE]
> Adjust the absolute paths to the python interpreter in your `.venv` and `proxy.py` to match your local setup exactly.

---

## MCP Interface & Capabilities

### Tools

- **`add_expense`**
  - **Description**: Add a new expense entry to the database.
  - **Arguments**:
    - `date` (string, e.g. `YYYY-MM-DD`)
    - `amount` (float/number)
    - `category` (string)
    - `subcategory` (string, optional)
    - `note` (string, optional)

- **`list_expenses`**
  - **Description**: List expense entries within an inclusive date range.
  - **Arguments**:
    - `start_date` (string)
    - `end_date` (string)

- **`summarize`**
  - **Description**: Summarize expenses grouped by category within an inclusive date range.
  - **Arguments**:
    - `start_date` (string)
    - `end_date` (string)
    - `category` (string, optional)

### Resources

- **`expense:///categories`**
  - **MIME Type**: `application/json`
  - **Description**: Provides the list of valid expense categories loaded from `categories.json`.
