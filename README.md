# n8n Kiro Power

A [Kiro Power](https://kiro.dev) that connects your IDE to your n8n instance via MCP. Search, inspect, execute, create, and edit n8n workflows without leaving your editor.

## What it can do

- **Search workflows** — find workflows by name or description across your entire n8n instance
- **Inspect workflows** — view full workflow details including nodes, connections, triggers, and parameters
- **Execute workflows** — run workflows in manual or production mode and retrieve results
- **Create workflows from code** — write workflows in TypeScript using n8n's Workflow SDK and deploy them directly (requires n8n v2.13+)
- **Edit existing workflows** — modify node configurations, swap nodes, update connections, and push changes back (requires n8n v2.13+)
- **Publish / unpublish** — activate or deactivate workflows for production
- **Archive workflows** — soft-delete workflows you no longer need
- **Node discovery** — search n8n's 400+ nodes, get TypeScript type definitions, and get curated recommendations by category

## Getting started

### 1. Enable MCP on your n8n instance

1. Go to **Settings > Instance-level MCP** in your n8n dashboard
2. Toggle **Enable MCP access** (requires admin permissions)
3. Click **Connection details** and copy your server URL and access token

### 2. Install the power in Kiro

**Option A — From local directory:**

1. Clone this repo
2. Open Kiro and go to the Powers panel
3. Click **Add Custom Power** > **Local Directory**
4. Point it to the `powers/n8n` folder
5. Edit `powers/n8n/mcp.json` and replace the placeholders with your real values:
   - `YOUR_N8N_MCP_SERVER_URL` → your n8n MCP URL (e.g. `https://your-instance.app.n8n.cloud/mcp-server/http`)
   - `YOUR_N8N_MCP_ACCESS_TOKEN` → your MCP access token
6. The power auto-detects config changes — check the MCP Servers view in Kiro to confirm it reconnects

**Option B — From GitHub:**

1. Open Kiro Powers panel
2. Click **Add Custom Power** > **Git Repository**
3. Enter: `https://github.com/Urakanchik/n8n-Kiro-Power`
4. After installing, update the `mcp.json` placeholders with your real values and reinstall

### 3. Enable workflows for MCP access

By default, no workflows are visible. For each workflow you want to use:

- Open the workflow in n8n > menu (**...**) > **Settings** > Toggle **Available in MCP**

### 4. Start using it

Ask Kiro things like:
- "List my n8n workflows"
- "Show me the details of workflow XYZ"
- "Execute workflow ABC with these inputs"
- "Create a new workflow that sends a Slack message on a schedule"

## Version requirements

| Feature | Minimum n8n version |
|---|---|
| Search, inspect, execute workflows | Any version with MCP |
| Create/edit workflows via SDK code | v2.13+ |
| Project and folder organization | v2.14+ |
| Workflow testing with pin data | v2.15+ |

## Project structure

```
powers/n8n/
├── POWER.md    # Full documentation, onboarding, tool reference, troubleshooting
└── mcp.json    # MCP server configuration (replace placeholders before use)
```

## Troubleshooting

| Problem | Solution |
|---|---|
| "Invalid MCP Server URL" | You haven't replaced the placeholders in `mcp.json` — update and reinstall the power |
| "Workflow is not available in MCP" | Enable MCP access on the workflow in n8n settings |
| `validate_workflow` always fails | Update your n8n instance to v2.13+ |
| "Authorization failed" | Rotate your access token in n8n and update `mcp.json` |
| Tools not showing after install | Check MCP Servers view in Kiro sidebar for connection status |

## License

MIT
