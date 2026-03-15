# bb-cli

A command-line interface for Bitbucket Cloud REST API v2. Manage repositories, pull requests, code reviews, and pipelines from your terminal.

Built for both human users and AI agent automation.

## Installation

```bash
# From source
pnpm install
pnpm build

# Link globally
npm link
```

Requires Node.js 18+.

## Authentication

```bash
# Set up with app password
bb auth setup --username <user> --app-password <password> --workspace <slug>

# Or use OAuth2 token
bb auth setup --access-token <token> --workspace <slug>

# Check current auth status
bb auth status
```

You can also use environment variables (these override config file values):

```
BITBUCKET_USERNAME
BITBUCKET_APP_PASSWORD
BITBUCKET_ACCESS_TOKEN
BITBUCKET_WORKSPACE
```

## Global Options

All commands support:

| Flag | Description |
|------|-------------|
| `-w, --workspace <slug>` | Override default workspace |
| `--json` | Output in JSON format |
| `-v, --verbose` | Show HTTP request/response details |

## Commands

### Repositories

```bash
bb repo list                          # List repos in workspace
bb repo list --role admin             # Filter by role (admin/contributor/member/owner)
bb repo list --sort -updated_on       # Sort by field
bb repo list --limit 10               # Limit results

bb repo view <repo>                   # View repository details
```

### Pull Requests

```bash
# List & view
bb pr list <repo>                     # List open PRs
bb pr list <repo> --state MERGED      # Filter: OPEN, MERGED, DECLINED, SUPERSEDED
bb pr view <repo> <pr-id>             # View PR details

# Create & update
bb pr create <repo> --title "Fix bug" --source feature-branch
bb pr create <repo> --title "Feature" --source dev --destination main \
  --description "Details" --reviewer alice --reviewer bob \
  --close-source-branch --draft

bb pr update <repo> <pr-id> --title "New title"
bb pr update <repo> <pr-id> --description "Updated" --reviewer charlie

# Review actions
bb pr approve <repo> <pr-id>
bb pr unapprove <repo> <pr-id>
bb pr request-changes <repo> <pr-id>
bb pr unrequest-changes <repo> <pr-id>

# Merge & lifecycle
bb pr merge <repo> <pr-id>
bb pr merge <repo> <pr-id> --strategy squash --message "Squash merge" --close-source-branch
bb pr decline <repo> <pr-id>

# Draft management
bb pr draft <repo> <pr-id>            # Convert to draft
bb pr publish <repo> <pr-id>          # Mark ready for review

# Inspection
bb pr diff <repo> <pr-id>             # Unified diff
bb pr diffstat <repo> <pr-id>         # Diff statistics
bb pr commits <repo> <pr-id>          # List commits
bb pr activity <repo> <pr-id>         # Activity log
bb pr tasks <repo> <pr-id>            # List tasks (read-only)
```

### PR Comments

```bash
bb pr comment list <repo> <pr-id>
bb pr comment add <repo> <pr-id> --body "Looks good!"
bb pr comment add <repo> <pr-id> --body "Fix this" --file src/app.ts --line 42
bb pr comment add <repo> <pr-id> --body "Reply" --parent 123

bb pr comment update <repo> <pr-id> <comment-id> --body "Updated text"
bb pr comment delete <repo> <pr-id> <comment-id>

bb pr comment resolve <repo> <pr-id> <comment-id>
bb pr comment reopen <repo> <pr-id> <comment-id>
```

### Pipelines

```bash
bb pipeline list <repo>               # List pipeline runs
bb pipeline list <repo> --sort -created_on --limit 5

bb pipeline run <repo> --branch main
bb pipeline run <repo> --branch main --pipeline custom-deploy --var ENV=prod --var REGION=us

bb pipeline view <repo> <pipeline-uuid>
bb pipeline stop <repo> <pipeline-uuid>

bb pipeline steps <repo> <pipeline-uuid>
bb pipeline step <repo> <pipeline-uuid> <step-uuid>
bb pipeline logs <repo> <pipeline-uuid> <step-uuid>
```

## Output Formats

By default, commands output human-readable tables and key-value pairs. Add `--json` for machine-readable JSON output:

```bash
bb pr list my-repo --json
bb pr view my-repo 42 --json
```

JSON error output follows the format: `{"error": {"message": "...", "status": N}}`

## Pagination

List commands support pagination:

| Flag | Description |
|------|-------------|
| `--limit <n>` | Maximum total results to return |
| `--page-size <n>` | Results per API request (max 100) |

The CLI auto-follows paginated responses until `--limit` is reached.

## Tech Stack

- TypeScript, Node.js 18+ (native fetch)
- Commander.js for CLI framework
- tsup for bundling
- Vitest for testing

## Development

```bash
pnpm install        # Install dependencies
pnpm build          # Build (output: dist/cli.js)
pnpm test           # Run tests
pnpm typecheck      # Type check
pnpm lint           # Lint
```

## License

MIT
