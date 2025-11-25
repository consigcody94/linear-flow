# Linear Flow

A Model Context Protocol (MCP) server for Linear project management. Create issues, manage projects, track cycles, and automate your workflow.

## Overview

Linear Flow connects your AI assistant to Linear, enabling natural language management of your engineering workflow. Create issues, update statuses, manage sprints, and track progress without leaving your conversation.

### Why Use Linear Flow?

**Traditional workflow:**
- Open Linear in browser
- Navigate to team/project
- Click through forms to create issues
- Search for team IDs and state IDs

**With Linear Flow:**
```
"Create a high-priority bug for the authentication team"
"What's in the current sprint?"
"Move FEAT-123 to Done"
"Create a new project for Q1 platform improvements"
```

## Features

- **Issue Management** - Create and update issues with full field control
- **Team Information** - Retrieve team details and workflow states
- **Cycle Tracking** - Get active cycle progress and metrics
- **Project Creation** - Create projects with team associations
- **Label Management** - Create custom labels with colors

## Installation

```bash
# Clone the repository
git clone https://github.com/consigcody94/linear-flow.git
cd linear-flow

# Install dependencies
npm install

# Build the project
npm run build
```

## Configuration

### Getting Your API Key

1. Go to [Linear Settings](https://linear.app/settings/api)
2. Click "Create key" under "Personal API keys"
3. Give it a label (e.g., "MCP Server")
4. Copy the generated key (starts with `lin_api_`)

### Environment Variable

```bash
export LINEAR_API_KEY="lin_api_your_key_here"
```

### Claude Desktop Integration

Add to your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "linear-flow": {
      "command": "node",
      "args": ["/absolute/path/to/linear-flow/build/index.js"],
      "env": {
        "LINEAR_API_KEY": "lin_api_your_key_here"
      }
    }
  }
}
```

**Config file locations:**
- macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
- Windows: `%APPDATA%\Claude\claude_desktop_config.json`
- Linux: `~/.config/Claude/claude_desktop_config.json`

## Tools Reference

### create_issue

Create a new issue in Linear.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `title` | string | Yes | Issue title |
| `teamId` | string | Yes | Team ID or key |
| `description` | string | No | Issue description (markdown) |
| `priority` | number | No | 0=None, 1=Urgent, 2=High, 3=Medium, 4=Low |
| `assigneeId` | string | No | User ID to assign |
| `labelIds` | string[] | No | Array of label IDs |

**Example - Simple issue:**

```json
{
  "title": "Fix login button alignment",
  "teamId": "ENG"
}
```

**Example - Full issue:**

```json
{
  "title": "Implement OAuth2 authentication",
  "teamId": "BACKEND",
  "description": "## Requirements\n\n- Google OAuth support\n- GitHub OAuth support\n- Token refresh handling\n\n## Acceptance Criteria\n\n- [ ] Users can sign in with Google\n- [ ] Users can sign in with GitHub\n- [ ] Tokens refresh automatically",
  "priority": 2,
  "assigneeId": "user_abc123",
  "labelIds": ["label_feature", "label_auth"]
}
```

**Response includes:**
- Issue ID and identifier (e.g., `ENG-123`)
- Issue URL
- Current state
- Priority level

### update_issue

Update an existing issue.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `issueId` | string | Yes | Issue ID to update |
| `title` | string | No | New title |
| `description` | string | No | New description |
| `priority` | number | No | New priority |
| `stateId` | string | No | New state ID |
| `assigneeId` | string | No | New assignee ID |

**Example:**

```json
{
  "issueId": "issue_abc123",
  "priority": 1,
  "stateId": "state_in_progress"
}
```

### get_team

Get team information including workflow states.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `teamId` | string | Yes | Team ID or key (e.g., "ENG") |

**Example:**

```json
{
  "teamId": "ENG"
}
```

**Response includes:**
- Team ID, key, and name
- Team description
- Workflow states with IDs, names, types, and colors

### get_cycle

Get cycle (sprint) information.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `teamId` | string | Yes | Team ID |
| `cycleId` | string | No | Specific cycle (default: active) |

**Example - Active cycle:**

```json
{
  "teamId": "ENG"
}
```

**Example - Specific cycle:**

```json
{
  "teamId": "ENG",
  "cycleId": "cycle_abc123"
}
```

**Response includes:**
- Cycle ID and number
- Start and end dates
- Issue count and completed count
- Progress percentage

### create_project

Create a new project.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `name` | string | Yes | Project name |
| `teamIds` | string[] | Yes | Associated team IDs |
| `description` | string | No | Project description |
| `targetDate` | string | No | Target date (ISO 8601) |
| `state` | string | No | planned, started, paused, completed, canceled |

**Example:**

```json
{
  "name": "Q1 Platform Improvements",
  "teamIds": ["team_backend", "team_frontend"],
  "description": "Core platform enhancements for Q1 2024",
  "targetDate": "2024-03-31",
  "state": "started"
}
```

**Response includes:**
- Project ID and name
- Project URL
- State and target date

### add_label

Create a new label for a team.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `name` | string | Yes | Label name |
| `teamId` | string | Yes | Team ID |
| `color` | string | No | Hex color (e.g., "#FF6B6B") |
| `description` | string | No | Label description |

**Example:**

```json
{
  "name": "tech-debt",
  "teamId": "ENG",
  "color": "#FF6B6B",
  "description": "Technical debt to address"
}
```

## Workflow Examples

### Bug Triage Workflow

1. **Create the bug:**
   ```
   create_issue with title: "Login fails on Safari", teamId: "ENG", priority: 2
   ```

2. **Get team states to find the triage state:**
   ```
   get_team with teamId: "ENG"
   ```

3. **Update to triage state:**
   ```
   update_issue with issueId: "...", stateId: "state_triage"
   ```

### Sprint Planning

1. **Check current cycle:**
   ```
   get_cycle with teamId: "ENG"
   ```

2. **Create issues for the sprint:**
   ```
   create_issue with title: "...", teamId: "ENG", ...
   ```

3. **Create a project to group them:**
   ```
   create_project with name: "Sprint 5 Goals", teamIds: ["ENG"]
   ```

### Feature Development

1. **Create feature label:**
   ```
   add_label with name: "new-feature", teamId: "ENG", color: "#4CAF50"
   ```

2. **Create feature issue:**
   ```
   create_issue with title: "...", teamId: "ENG", labelIds: ["label_new_feature"]
   ```

3. **Update progress:**
   ```
   update_issue with issueId: "...", stateId: "state_in_progress"
   ```

## Finding IDs

### Team ID
- Use team key directly (e.g., "ENG", "BACKEND")
- Or find in Linear URL: `linear.app/team/TEAM_KEY`

### State IDs
Use `get_team` to list all states with their IDs

### User IDs
- Find in Linear settings or API
- Or use the Linear GraphQL API explorer

### Label IDs
- Created labels return their ID
- Or query via Linear API

## Requirements

- Node.js 18 or higher
- Linear API key
- Active Linear workspace

## Troubleshooting

### "LINEAR_API_KEY environment variable is required"

Ensure the API key is set in your environment or Claude Desktop config.

### "Team not found"

1. Verify the team key/ID is correct
2. Ensure your API key has access to that team

### Issue creation fails

1. Check that teamId is valid
2. Verify required fields are provided
3. Ensure assigneeId/labelIds exist if provided

### State transition fails

1. Get valid state IDs using `get_team`
2. Ensure the transition is allowed in your workflow

## Security Notes

- Never commit API keys to version control
- API keys have full access to your Linear workspace
- Rotate keys periodically
- Use separate keys for different applications

## License

MIT License - see [LICENSE](LICENSE) file for details.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## Author

consigcody94
