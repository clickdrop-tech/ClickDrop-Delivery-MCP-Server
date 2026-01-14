# ClickDrop Delivery MCP Server

The ClickDrop Delivery(Send247) MCP is a **Model Context Protocol (MCP) server** that allows AI assistants to **quote and book UK deliveries** through a structured, safe, and auditable interface.

It is designed for **ChatGPT, Claude, GitHub Copilot, and other MCP-compatible clients**.

---

## What This App Does

The MCP server exposes delivery functionality as tools that an AI assistant can safely invoke:

* Get real-time delivery quotes between UK addresses
* Create (book) a delivery using a previously generated quote
* Enforce confirmation before any irreversible action
* Maintain server-side state so LLM session continuity is not required

This app is intended for **end users located in the United Kingdom**.

---

## Available Tools

### `get_delivery_quote`

Returns delivery options and prices for a shipment.

**Typical use**

* User wants to know delivery cost
* User compares services before booking

**Key properties**

* Not read-only (stateful quote caching)
* Open world (uses external delivery providers)
* Non-destructive

---

### `create_delivery`

Creates a delivery booking using a previously issued quote.

**Typical use**

* User explicitly confirms they want to book a delivery

**Key properties**

* Writes data
* Open world
* Destructive (creates irreversible delivery bookings)

This tool **must only be invoked after explicit user confirmation**.

---

## MCP Client Configuration

This MCP server is publicly accessible and can be connected to by any MCP-compatible client.

Authentication and request validation are handled **at the gateway level**.
No client-side API keys are required.

---

### ChatGPT / OpenAI MCP Configuration

```json
{
  "mcpServers": {
    "send247-delivery": {
      "url": "https://mcp.send247.uk/api/v1/mcp"
    }
  }
}
```

Once connected, enable the app in a conversation and prompt normally (for example: *“Get a delivery quote from London to Manchester”*).

---

### Claude MCP Configuration (Anthropic)
Claude Desktop & Claude Code

Claude runs MCP servers locally and does not yet support direct remote MCP URLs.
To connect Claude to send247, we use the official MCP remote bridge (@modelcontextprotocol/remote), which proxies the remote MCP server over stdio.

1. Install MCP Remote:
   npm install -g @modelcontextprotocol/remote

2. Add the following to your MCP config:
```json
{
  "mcpServers": {
    "send247-delivery": {
      "command": "npx",
      "args": [
        "@modelcontextprotocol/remote",
        "https://mcp.send247.uk/api/v1/mcp"
      ]
    }
  }
}

```

Claude will automatically discover available tools via `tools/list`.

---

### GitHub Copilot (VS Code) MCP Configuration

```json
{
  "mcpServers": {
    "send247-delivery": {
      "url": "https://mcp.send247.uk/api/v1/mcp"
    }
  }
}
```

When using Copilot Chat, tools will appear when delivery-related prompts are detected.

---

## Optional: MCP Manifest (Discoverability)

For platforms or registries that support MCP manifests, you may include the following file:

**`mcp.json`**

```json
{
  "name": "Send247 Delivery",
  "description": "UK delivery quotes and booking via the Model Context Protocol",
  "version": "1.0.0",
  "endpoint": "https://mcp.send247.uk/api/v1/mcp",
  "capabilities": {
    "tools": true
  }
}
```

This is optional but improves discoverability.

---

## Safety & Design Guarantees

* No deliveries are created without explicit confirmation
* Quotes expire automatically
* Tool schemas are strictly validated
* Server-side state ensures reliability across LLM sessions
* All write actions are auditable

---

## Intended Use

This MCP server is intended for:

* AI assistants offering delivery services
* Agentic workflows that require logistics actions
* End users in the United Kingdom

It is **not** a general logistics SDK and does not expose raw provider APIs.

---

## Status

* MCP server deployed and publicly accessible
* Tested with MCP Inspector and ChatGPT developer mode
* Submitted for review in the OpenAI Apps Directory

---

## Support

For integration questions or issues, please contact the maintainer via the repository or official project channels. Email tech@clickdrop.co.uk

---
