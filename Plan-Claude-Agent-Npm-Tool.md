# Plan: Claude Agents NPM Tool

## Overview
Create an npm package called `claude-agents` that provides an interactive CLI for installing AI agents from this repository into users' `.claude/agents` directory.

## Project Structure
```
claude-agents-npm/
├── package.json
├── README.md
├── LICENSE
├── .gitignore
├── bin/
│   └── claude-agents.js       # CLI entry point
├── src/
│   ├── index.js               # Main application logic
│   ├── installer.js           # Agent installation logic
│   ├── prompts.js            # Interactive prompts
│   ├── github.js             # GitHub API integration
│   └── utils.js              # Helper functions
└── config/
    └── repositories.json      # Repository configuration
```

## Implementation Plan

### 1. **Package Setup (package.json)**
```json
{
  "name": "claude-agents",
  "version": "1.0.0",
  "description": "Interactive CLI to install Claude AI agents from GitHub repositories",
  "bin": {
    "claude-agents": "./bin/claude-agents.js"
  },
  "dependencies": {
    "inquirer": "^9.0.0",
    "chalk": "^5.0.0",
    "fs-extra": "^11.0.0",
    "ora": "^6.0.0",
    "axios": "^1.6.0",
    "yaml": "^2.3.0",
    "node-cache": "^5.1.0",
    "commander": "^11.0.0"
  }
}
```

### 2. **CLI Features**
- **Interactive Selection Menu:**
  - Install all agents
  - Install by department (e.g., all marketing agents)
  - Select individual agents
  - Search agents by keyword
  
- **Installation Modes:**
  - Global: `~/.claude/agents/`
  - Project: `./.claude/agents/`
  - Custom path

- **Installation Types:**
  - **Agents**: Install as Claude Code sub-agents (default)
  - **Commands**: Install as Claude Code commands in `.claude/commands/`

- **Additional Options:**
  - View agent descriptions before installing
  - Update existing agents
  - Remove installed agents
  - List currently installed agents
  - Configure source repositories
  - Check for agent updates from GitHub
  - **Model selection:** Specify Claude model (opus, sonnet, inherit, none) - agents only
  - **Auto-assign colors:** Add random colors to agents without colors - agents only
  - **Install as commands:** Convert agents to Claude Code commands format

### 3. **Repository Configuration (repositories.json)**
```json
{
  "repositories": [
    {
      "name": "Contains Studio Agents",
      "url": "https://github.com/contains-studio/agents",
      "branch": "main",
      "default": true
    }
  ],
  "cache": {
    "ttl": 3600,
    "maxAge": 86400
  }
}
```

### 4. **User Flow**
```
$ npx claude-agents --model opus --assign-colors

⚡ Fetching latest agents from GitHub...
✓ Found 42 agents across 8 departments

? What would you like to do?
  ❯ Install agents
    Update agents
    Remove agents
    List installed agents
    Configure repositories

? Installation type:
  ❯ Agents (~/.claude/agents)
    Commands (~/.claude/commands)

? Where to install?
  ❯ Global (~/.claude/agents or ~/.claude/commands)
    Project (./.claude/agents or ./.claude/commands)
    Custom path

? Select installation method:
  ❯ All agents
    By department
    Individual agents
    Search agents

[If "By department"]
? Select departments: (Press <space> to select)
  ◯ Engineering (7 agents)
  ◯ Marketing (8 agents)
  ◯ Design (5 agents)
  ◯ Product (3 agents)
  ...

? Review selection:
  Installing 15 items as commands to ~/.claude/commands/
  Source: Contains Studio Agents (github.com/contains-studio/agents)
  Format: Commands (YAML removed, markdown headers added)
  Continue? (Y/n)

⚡ Downloading and converting to commands...
  ✓ 15 agents converted to command format
  ✓ Commands installed to ~/.claude/commands/
✓ Commands installed successfully!
  Restart Claude Code to load the new commands.
```

### 5. **Core Functions**

**github.js:**
- `fetchRepositoryStructure(repo)` - Get directory structure from GitHub API
- `downloadAgent(repo, path, targetPath)` - Download individual agent file
- `parseAgentMetadata(content)` - Extract YAML frontmatter
- `checkForUpdates(installedAgents, repo)` - Compare versions/timestamps
- `validateRepository(repoUrl)` - Ensure repository exists and is accessible

**installer.js:**
- `installAgents(agents, targetPath, repository, options)` - Download and install agents
- `processAgentContent(content, options)` - Add model/color to agent YAML frontmatter
- `convertToCommand(content)` - Convert agent format to command format
- `updateAgents(targetPath)` - Update existing agents from GitHub
- `removeAgents(agents, targetPath)` - Remove selected agents
- `listInstalledAgents(targetPath)` - Show installed agents with metadata

**prompts.js:**
- `selectAction()` - Main menu
- `selectInstallLocation()` - Choose install path
- `selectInstallationType()` - Choose agents or commands
- `selectInstallMethod()` - How to select agents
- `selectDepartments()` - Multi-select departments
- `selectIndividualAgents()` - Multi-select agents
- `searchAgents(keyword)` - Search functionality
- `selectRepository()` - Choose source repository

**utils.js:**
- `ensureDirectory(path)` - Create .claude/agents if needed
- `validateAgentFile(content)` - Check agent file format
- `backupExisting(path)` - Backup before overwrite
- `cacheManager()` - Handle response caching
- `getInstallationMetadata(targetPath)` - Track installed agents
- `parseYAMLFrontmatter(content)` - Extract and parse YAML frontmatter
- `reconstructAgentFile(frontmatter, content)` - Rebuild agent file with updated frontmatter
- `getRandomColor()` - Select random color from available pool

### 6. **Publishing Strategy**
1. Package contains only CLI tool and GitHub integration
2. Always fetches latest agents from configured repositories
3. Publish to npm registry as `claude-agents`
4. Users can install globally: `npm install -g claude-agents`
5. Or use without installing: `npx claude-agents`
6. No bundled agents - everything is dynamically fetched

### 7. **Additional Features**
- **Caching:** Cache GitHub responses to reduce API calls
- **Offline mode:** Use cached data when GitHub is unavailable
- **Dry run mode:** Preview what will be installed
- **Verbose mode:** Show detailed installation progress
- **Config file:** Remember user preferences and repositories
- **Agent preview:** Show agent description/capabilities before install
- **Multiple repositories:** Support custom agent repositories

### 8. **Error Handling**
- **Network Issues:** Graceful handling of GitHub API failures
- **Rate Limiting:** Respect GitHub API rate limits with backoff
- **Repository Access:** Handle private/missing repositories
- **File System:** Check permissions and directory access
- **Agent Validation:** Validate downloaded agent file integrity
- **Rollback:** Provide rollback on installation failure
- **Offline Fallback:** Use cached data when network unavailable

### 9. **Documentation (README.md)**
- Quick start guide
- Installation methods
- Repository configuration
- Custom repository setup
- Usage examples
- Troubleshooting GitHub connectivity
- Contributing guidelines

## File Creation Plan

1. Create `Plan-Claude-Agent-Npm-Tool.md` with this detailed plan
2. Set up npm package structure
3. Set up GitHub Actions workflow for automatic npm publishing
4. Implement GitHub API integration
5. Create repository configuration system
6. Add interactive prompts with repository selection
7. Implement dynamic installation logic
8. Add caching and offline support
9. Add error handling and validation
10. Write comprehensive documentation
11. Test with different repositories and scenarios
12. Configure release workflow and npm token

This tool will significantly simplify the agent installation process, making it accessible to users who aren't comfortable with manual file copying.

## Technical Implementation Details

### GitHub API Integration
The tool will use GitHub's REST API to:
- Fetch repository directory structures via `/repos/:owner/:repo/contents/:path`
- Download individual agent files via raw content URLs
- Check repository accessibility and permissions
- Handle rate limiting (5000 requests/hour for authenticated users)
- Support both public and private repositories (with tokens)

### Agent Discovery Strategy
Agents will be discovered dynamically by:
- Scanning repository directory structure
- Filtering for `.md` files (excluding README, LICENSE, etc.)
- Parsing YAML frontmatter to extract metadata
- Organizing by directory structure (engineering/, marketing/, etc.)
- Caching results to minimize API calls

### Installation Workflow
1. **Pre-installation checks:**
   - Fetch latest repository structure from GitHub
   - Verify target directory exists or can be created
   - Check for existing agents and offer backup/merge options
   - Validate write permissions

2. **Installation process:**
   - Download selected agent files from GitHub raw URLs
   - Create directory structure matching repository layout
   - Validate downloaded file integrity
   - Store installation metadata for future updates

3. **Post-installation:**
   - Display success message with source repository info
   - Remind user to restart Claude Code
   - Update local installation tracking

### Configuration Management
Store user preferences in `~/.claude-agents-config.json`:
```json
{
  "defaultInstallPath": "~/.claude/agents",
  "repositories": [
    {
      "name": "Contains Studio Agents",
      "url": "https://github.com/contains-studio/agents",
      "branch": "main",
      "lastUpdate": "2025-08-24T10:30:00Z"
    }
  ],
  "installedAgents": [
    {
      "name": "ai-engineer",
      "source": "contains-studio/agents",
      "path": "engineering/ai-engineer.md",
      "installedAt": "2025-08-24T10:30:00Z",
      "hash": "abc123def456"
    }
  ],
  "preferences": {
    "showDescriptions": true,
    "confirmBeforeInstall": true,
    "cacheTimeout": 3600,
    "defaultModel": "inherit",
    "autoAssignColors": false
  },
  "colorDistribution": {
    "lastUsed": ["blue", "green"],
    "frequency": {
      "red": 5,
      "blue": 6,
      "green": 4,
      "yellow": 3,
      "purple": 7,
      "orange": 2,
      "pink": 4,
      "cyan": 6
    }
  }
}
```

### Update Mechanism
- Compare local agent hashes with GitHub file hashes
- Check GitHub commit timestamps for newer versions
- Offer selective updates for modified agents only
- Preserve user customizations where possible
- Backup before updates with rollback capability

### Search Functionality
- Search by agent name
- Search by description keywords
- Search by category
- Filter by tools required
- Fuzzy matching for typos

## Model Selection and Color Assignment Features

### Command Line Interface
The tool supports additional flags for customizing agent installation:

```bash
# Install with specific Claude model
npx claude-agents --model opus
npx claude-agents -m sonnet

# Auto-assign colors to agents without colors
npx claude-agents --assign-colors
npx claude-agents -c

# Combine both features
npx claude-agents --model opus --assign-colors

# Install as commands instead of agents
npx claude-agents --as-commands
npx claude-agents --commands
```

### Model Selection Options
- **`opus`**: Assign Claude Opus model to all installed agents
- **`sonnet`**: Assign Claude Sonnet model to all installed agents
- **`inherit`**: Use default behavior (no model specified)
- **`none`**: Explicitly set no model preference

### YAML Frontmatter Modification
When model is specified, the tool modifies agent files by adding the model field:

**Original agent format:**
```yaml
---
name: linkedin-networker
description: B2B LinkedIn specialist...
tools: Read, Write, Edit, WebFetch, WebSearch
---
```

**With model selection (`--model opus`):**
```yaml
---
name: linkedin-networker
description: B2B LinkedIn specialist...
model: opus
tools: Read, Write, Edit, WebFetch, WebSearch
---
```

### Color Assignment System
Available colors for auto-assignment:
- Red, Blue, Green, Yellow, Purple, Orange, Pink, Cyan

**Color Assignment Logic:**
1. Check if agent already has a color in YAML frontmatter
2. If no color exists and `--assign-colors` flag is used:
   - Select random color from available pool
   - Track color distribution to ensure variety
   - Avoid excessive repetition of same colors

**Example with color assignment:**
```yaml
---
name: linkedin-networker
description: B2B LinkedIn specialist...
color: purple
tools: Read, Write, Edit, WebFetch, WebSearch
---
```

### Processing Workflow
1. **Download agent from GitHub**: Get original agent file content
2. **Parse YAML frontmatter**: Extract existing metadata
3. **Apply modifications**: Add model and/or color based on flags
4. **Validate structure**: Ensure YAML remains valid
5. **Write to disk**: Save modified agent to target directory

### Configuration Storage
User preferences for model and color options are stored in configuration:

```json
{
  "preferences": {
    "defaultModel": "inherit",
    "autoAssignColors": false
  },
  "colorDistribution": {
    "frequency": {
      "red": 5,
      "blue": 6,
      ...
    }
  }
}
```

## Commands Installation Feature

### Commands vs Agents
The tool can install content as either **agents** or **commands**:

- **Agents** (default): Installed to `~/.claude/agents/` with full YAML frontmatter
- **Commands**: Installed to `~/.claude/commands/` with simplified markdown format

### Command Line Interface for Commands
```bash
# Install as commands
npx claude-agents --as-commands
npx claude-agents --commands

# Commands don't support model/color options (YAML is removed)
npx claude-agents --commands  # model and color flags ignored
```

### Agent to Command Conversion

**Original Agent Format:**
```yaml
---
name: linkedin-networker
description: B2B LinkedIn specialist who builds professional networks, generates leads through thought leadership, and leverages LinkedIn's ecosystem for business growth
tools: Read, Write, Edit, WebFetch, WebSearch
color: blue
model: opus
---

You are a LinkedIn Networker specializing in B2B marketing...
```

**Converted Command Format:**
```markdown
### linkedin-networker

### B2B LinkedIn specialist who builds professional networks, generates leads through thought leadership, and leverages LinkedIn's ecosystem for business growth

You are a LinkedIn Networker specializing in B2B marketing...
```

### Conversion Process
1. **Parse YAML frontmatter**: Extract `name` and `description`
2. **Remove frontmatter**: Strip entire `---` YAML block
3. **Add markdown headers**: Convert to `### Name` and `### Description` format  
4. **Preserve content**: Keep the main system prompt unchanged
5. **Save to commands directory**: Install to `.claude/commands/` instead of `.claude/agents/`

### Commands Features
- **Simplified format**: No YAML frontmatter complexity
- **Markdown headers**: Clean `### Name` and `### Description` format
- **No metadata**: No `tools:`, `color:`, or `model:` fields needed
- **Same content**: Full system prompt preserved
- **Different location**: Installed to `.claude/commands/` directory

### Installation Flow for Commands
1. User selects "Commands" as installation type
2. Agents are downloaded from GitHub as usual
3. YAML frontmatter is parsed and removed
4. Content is reformatted with markdown headers
5. Files are saved to `.claude/commands/` directory
6. Model and color options are ignored (not applicable to commands)

### Configuration Tracking
Commands installations are tracked separately in configuration:

```json
{
  "installedCommands": [
    {
      "name": "linkedin-networker",
      "source": "contains-studio/agents",
      "originalPath": "marketing/linkedin-networker.md",
      "installedAt": "2025-08-24T10:30:00Z",
      "type": "command"
    }
  ]
}
```

## Package Distribution

### NPM Package Contents
- CLI executable and source code
- GitHub API integration modules
- Default repository configuration
- Caching and offline support
- Documentation and examples
- **No bundled agents** - all fetched dynamically

### Versioning Strategy
- Semantic versioning (semver)
- Major: Breaking changes to CLI interface or API
- Minor: New features, repository support improvements
- Patch: Bug fixes, performance improvements
- **Independent of agent versions** - agents always fetched fresh

### Release Process
1. Update CLI functionality and bug fixes
2. Test with various GitHub repositories
3. Update documentation
4. Update version in package.json
5. Run integration tests
6. **Manual**: Create git tag (e.g., `git tag v1.2.3 && git push --tags`)
7. **Automatic**: GitHub Actions publishes to npm registry using NPM token

## Key Benefits of GitHub-First Approach

### Always Up-to-Date
- Users get the latest agents without waiting for npm package updates
- New agents are immediately available once added to repositories
- Bug fixes and improvements are delivered instantly

### Repository Flexibility
- Support for multiple agent repositories
- Users can add custom agent collections
- Fork and customize agent repositories while staying connected
- Enterprise teams can maintain private agent repositories

### Scalability
- No package size limitations from bundling files
- Fast installation since only selected agents are downloaded
- Efficient updates by only downloading changed files
- Supports unlimited number of agents and repositories

### Community Driven
- Easy contribution to agent repositories via pull requests
- Version control for all agents through Git history
- Issue tracking and discussions on GitHub
- Collaborative improvement of agents

## Automated Release Workflow

### GitHub Actions Setup
Create `.github/workflows/release.yml` for automatic npm publishing:

```yaml
name: Release to NPM

on:
  push:
    tags:
      - 'v*'

jobs:
  release:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'
          registry-url: 'https://registry.npmjs.org'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run tests
        run: npm test
      
      - name: Verify version matches tag
        run: |
          PACKAGE_VERSION=$(node -p "require('./package.json').version")
          TAG_VERSION=${GITHUB_REF#refs/tags/v}
          if [ "$PACKAGE_VERSION" != "$TAG_VERSION" ]; then
            echo "Version mismatch: package.json ($PACKAGE_VERSION) vs tag ($TAG_VERSION)"
            exit 1
          fi
      
      - name: Publish to NPM
        run: npm publish
        env:
          NODE_AUTH_TOKEN: ${{ secrets.NPM_TOKEN }}
      
      - name: Create GitHub Release
        uses: actions/create-release@v1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          tag_name: ${{ github.ref }}
          release_name: Release ${{ github.ref }}
          draft: false
          prerelease: false
```

### Repository Secrets Configuration
Required secrets to be added to GitHub repository:

1. **`NPM_TOKEN`**: Your npm authentication token
   - Generate at: https://www.npmjs.com/settings/tokens
   - Select "Automation" token type for CI/CD
   
2. **`GITHUB_TOKEN`**: Automatically provided by GitHub Actions

### Release Workflow
1. **Development**: Make changes, commit to feature branches
2. **Testing**: Ensure all tests pass locally
3. **Version Bump**: Update `package.json` version following semver
4. **Commit & Push**: Push version bump to main branch
5. **Tag Release**: Create and push git tag matching package version
   ```bash
   git tag v1.2.3
   git push origin v1.2.3
   ```
6. **Automatic**: GitHub Actions triggers and publishes to npm
7. **Verification**: Check npm registry and GitHub releases page

### Rollback Strategy
If a release needs to be rolled back:
```bash
# Unpublish from npm (within 72 hours)
npm unpublish claude-agents@1.2.3

# Or deprecate the version
npm deprecate claude-agents@1.2.3 "Version deprecated due to critical bug"
```

This comprehensive plan ensures a user-friendly, robust tool that leverages GitHub's infrastructure for dynamic, always-current agent management.