---
name: "n8n"
displayName: "n8n Workflow Automation"
description: "Manage n8n workflows through MCP — search, create, edit, execute, and organize workflows directly from Kiro."
keywords: ["n8n", "workflow", "automation", "n8n-mcp"]
author: "Ihor Musiienko (WWG)"
---

# n8n Workflow Automation

## Overview

This power connects Kiro to your n8n instance via n8n's built-in MCP server. Once connected, you can search workflows, read their details, trigger executions, create new workflows from TypeScript code, update existing ones, and manage their lifecycle — all without leaving your editor.

n8n's MCP server exposes tools for the full workflow lifecycle: introspection, creation, validation, execution, and publishing.

## Onboarding

### Prerequisites

- An n8n instance (Cloud or self-hosted) with MCP access enabled
- Instance owner or admin permissions to enable MCP
- n8n v2.13 or later is required for workflow creation/editing via code (SDK features)

### Enabling MCP on your n8n instance

1. Navigate to **Settings > Instance-level MCP** in your n8n dashboard
2. Toggle **Enable MCP access**
3. Click **Connection details** to get your server URL and access token

### Getting your Access Token

1. In the Connection details popup, switch to the **Access Token** tab
2. n8n auto-generates a personal MCP Access Token on first visit
3. Copy the token immediately — you won't be able to see it again later
4. If you lose it, generate a new one (this revokes the old token)

### Exposing workflows to MCP

By default, no workflows are visible to MCP clients. You must enable each workflow individually:

- **From MCP settings**: Click "Enable workflows", search and select workflows, click Enable
- **From workflow editor**: Open workflow menu (...) > Settings > Toggle "Available in MCP"
- **From workflows list**: Open workflow card menu > "Enable MCP access"

Adding a description to each workflow helps the AI understand what it does and what inputs it expects.

**Important**: If you try to use `get_workflow_details` or `execute_workflow` on a workflow that isn't MCP-enabled, you'll get an error: "Workflow is not available in MCP."

## Common Workflows

### Search and inspect workflows

Use `search_workflows` to find workflows by name or description. Then use `get_workflow_details` to inspect a specific workflow's full structure — nodes, connections, triggers, and parameters.

Note: `search_workflows` returns all workflows (including those not MCP-enabled), but `get_workflow_details` and `execute_workflow` only work on MCP-enabled workflows.

### Execute a workflow

Use `execute_workflow` to run a workflow by ID. Always call `get_workflow_details` first to understand the expected inputs. Supports "manual" (test current draft) or "production" (run published version) execution modes.

Limitations:
- 5-minute timeout for MCP-triggered executions
- Binary input data is not supported
- Multi-step forms and human-in-the-loop interactions are not supported

### Edit an existing workflow

To modify an existing workflow:
1. `search_workflows` — find the workflow
2. `get_workflow_details` — get the full current structure
3. `search_nodes` — find the right nodes for your changes
4. `get_node_types` — get exact TypeScript type definitions for node parameters
5. `validate_workflow` — validate the updated SDK code
6. `update_workflow` — push the changes to n8n

### Create a new workflow from code

1. `search_nodes` — find the right nodes for your use case
2. `get_node_types` — get exact parameter types and structures (must be called before writing code)
3. `get_suggested_nodes` — optionally get curated recommendations by category
4. `validate_workflow` — validate the TypeScript SDK code
5. `create_workflow_from_code` — save it to n8n (auto-enables MCP access and generates description)

### Publish / unpublish workflows

Use `publish_workflow` to activate a workflow for production execution, or `unpublish_workflow` to deactivate it.

## Available Tools

### Workflow Search and Introspection
- `search_workflows` — Search for workflows with optional filters (query, projectId, limit up to 200). Returns a preview of each workflow including name, active status, and MCP availability
- `get_workflow_details` — Get detailed information about a specific MCP-enabled workflow including all nodes, connections, parameters, and trigger details
- `get_execution` — Get full execution details and results using execution ID and workflow ID. Supports filtering by node names and data truncation

### Workflow Execution
- `execute_workflow` — Execute a workflow by ID. Supports "manual" or "production" execution modes with optional inputs. Returns execution ID for tracking

### Workflow Creation and Management (requires n8n v2.13+)
- `validate_workflow` — Validate TypeScript/JavaScript workflow SDK code for syntax and structural errors. Always call before creating or updating
- `create_workflow_from_code` — Create a new workflow from validated SDK code. Optionally specify name, description (max 255 chars), and projectId
- `update_workflow` — Update an existing workflow from validated SDK code
- `archive_workflow` — Archive a workflow by ID (soft delete)
- `publish_workflow` — Publish (activate) a workflow for production execution. Optionally specify a version ID
- `unpublish_workflow` — Unpublish (deactivate) a workflow

### SDK and Node Reference
- `search_nodes` — Search for n8n nodes by service name, trigger type, or utility function. Returns node IDs, discriminators (resource/operation/mode), and related nodes
- `get_node_types` — Get TypeScript type definitions for n8n nodes. Returns exact parameter names and structures. Must be called before writing workflow code — guessing parameter names creates invalid workflows
- `get_suggested_nodes` — Get curated node recommendations for workflow technique categories: chatbot, notification, scheduling, data_transformation, data_persistence, data_extraction, document_processing, form_input, content_generation, triage, scraping_and_research

## Version Compatibility

### Tools available on all versions
- `search_workflows`, `get_workflow_details`, `execute_workflow`, `get_execution`
- `publish_workflow`, `unpublish_workflow`, `archive_workflow`
- `search_nodes`, `get_node_types`, `get_suggested_nodes`

### Tools requiring n8n v2.13+
- `validate_workflow`, `create_workflow_from_code`, `update_workflow`
- These tools use the n8n Workflow SDK to parse TypeScript code into workflow JSON
- If `validate_workflow` returns "Cannot read properties of undefined (reading 'name')" errors regardless of code syntax, your n8n instance likely needs to be updated

### Tools requiring n8n v2.14+
- `create_workflow_from_code` with `projectId` and `folderId` parameters
- `get_execution` with `includeData`, `nodeNames`, and `truncateData` filters
- `search_projects`, `search_folders` (project/folder organization)

### Tools requiring n8n v2.15+
- `prepare_test_pin_data`, `test_workflow` (workflow testing with simulated data)

## Troubleshooting

### MCP server won't connect
- Verify your n8n instance is publicly accessible (required for cloud MCP clients)
- Check that MCP access is enabled in Settings > Instance-level MCP
- Confirm your access token is correct and not revoked
- Review n8n server logs for connection errors

### "Workflow is not available in MCP"
- The workflow must be explicitly enabled for MCP access before it can be read or executed
- Go to the workflow in n8n > menu (...) > Settings > Toggle "Available in MCP"
- Or enable from Settings > Instance-level MCP > Enable workflows button

### Workflows not showing up in search
- `search_workflows` returns all workflows regardless of MCP status
- But `get_workflow_details` and `execute_workflow` only work on MCP-enabled workflows
- Check Settings > Instance-level MCP > Workflows tab to see which are enabled

### Execution timeout
- n8n enforces a 5-minute timeout for MCP-triggered executions
- If your workflow takes longer, consider breaking it into smaller workflows
- The timeout ignores any custom timeout set in workflow settings

### "Authorization failed" errors
- Your access token may have been rotated — generate a new one and update the mcp.json config
- Ensure you have instance owner or admin permissions
- After updating the config, check the MCP Servers view in Kiro to confirm it reconnects

### validate_workflow always fails with "Cannot read properties of undefined"
- This indicates your n8n instance version doesn't fully support the Workflow SDK
- Update your n8n instance to v2.13 or later
- The `search_workflows`, `get_workflow_details`, and `execute_workflow` tools work on all versions

### Workflow creation errors
- Always use `validate_workflow` before `create_workflow_from_code` or `update_workflow`
- Use `get_node_types` with discriminators to get exact parameter names — never guess
- The SDK does not support destructuring imports or certain JavaScript patterns

## Best Practices

- Add clear descriptions to all MCP-exposed workflows so the AI can understand their purpose and expected inputs
- Always call `get_workflow_details` before `execute_workflow` to understand the input schema
- Use `get_node_types` with discriminators from `search_nodes` before writing any workflow code
- Use `validate_workflow` before creating or updating workflows to catch errors early
- Keep MCP-exposed workflows focused on single tasks for cleaner AI interactions
- Regularly review which workflows are exposed to MCP and disable ones no longer needed
- When editing workflows, retrieve the full details first to preserve existing node configurations

## MCP Config Placeholders

Before using this power, replace the following placeholders in `mcp.json`:

- **`YOUR_N8N_MCP_SERVER_URL`**: Your n8n instance MCP server URL.
  - **How to get it:**
    1. Go to your n8n dashboard
    2. Navigate to Settings > Instance-level MCP
    3. Click "Connection details"
    4. Copy the server URL (format: `https://your-instance.app.n8n.cloud/mcp-server/http`)

- **`YOUR_N8N_MCP_ACCESS_TOKEN`**: Your personal MCP access token.
  - **How to get it:**
    1. Go to Settings > Instance-level MCP
    2. Click "Connection details"
    3. Switch to the "Access Token" tab
    4. Copy the token (only visible on first visit — generate a new one if needed)

## License and Support

This power integrates with [n8n MCP Server](https://docs.n8n.io/advanced-ai/accessing-n8n-mcp-server/) ([Sustainable Use License](https://github.com/n8n-io/n8n/blob/master/LICENSE.md)).

- [n8n Privacy Policy](https://n8n.io/legal/privacy/)
- [Support](mailto:shark9248@gmail.com)

---

**MCP Server:** n8n-mcp
