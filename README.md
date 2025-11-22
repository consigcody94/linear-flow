# linear-flow

A production-ready Model Context Protocol (MCP) server for Linear project management integration.

## Features

- **6 powerful tools** for Linear project management
- Full TypeScript strict mode implementation
- Complete Linear SDK integration
- Production-ready error handling

## Tools

### create_issue
Create a new issue in Linear with full control over title, description, team, priority, assignee, and labels.

### update_issue
Update an existing issue's properties including title, description, priority, state, and assignee.

### get_team
Retrieve team information including workflow states, team key, and description.

### get_cycle
Get active or specific cycle information with progress metrics and issue counts.

### create_project
Create new projects with team associations, target dates, and state management.

### add_label
Create new labels for teams with custom colors and descriptions.

## Installation

```bash
npm install
npm run build
```

## Configuration

Set your Linear API key as an environment variable:

```bash
export LINEAR_API_KEY="your_linear_api_key_here"
```

## Usage with Claude Desktop

Add to your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "linear-flow": {
      "command": "node",
      "args": ["/path/to/linear-flow/build/index.js"],
      "env": {
        "LINEAR_API_KEY": "your_linear_api_key_here"
      }
    }
  }
}
```

## Example Usage

```typescript
// Create an issue
{
  "tool": "create_issue",
  "arguments": {
    "title": "Implement user authentication",
    "description": "Add OAuth2 support with Google and GitHub providers",
    "teamId": "team_123",
    "priority": 2
  }
}

// Get team information
{
  "tool": "get_team",
  "arguments": {
    "teamId": "ENG"
  }
}

// Create a project
{
  "tool": "create_project",
  "arguments": {
    "name": "Q1 Platform Improvements",
    "description": "Core platform enhancements for Q1",
    "teamIds": ["team_123"],
    "state": "started"
  }
}
```

## Development

```bash
# Install dependencies
npm install

# Build
npm run build

# The build process automatically makes the binary executable
```

## Requirements

- Node.js 18+
- Linear API key
- Active Linear workspace

## License

MIT
