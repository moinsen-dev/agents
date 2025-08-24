# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This repository contains a collection of specialized AI agents organized by department, plus a planned NPM tool (`claude-agents`) for dynamic installation from GitHub repositories.

### Two-Part Architecture

1. **Agent Collection**: Static `.md` files in departmental folders containing Claude Code sub-agents
2. **NPM Installation Tool**: Planned CLI tool that dynamically fetches agents from GitHub repositories

## Agent Repository Structure

Agents are organized by department in the following structure:
- `design/` - UI/UX and visual design specialists
- `engineering/` - Development and technical implementation  
- `marketing/` - Platform-specific marketing and growth specialists
- `product/` - Product strategy and user research
- `project-management/` - Project coordination and delivery
- `security/` - Security auditing and compliance
- `studio-operations/` - Business operations and analytics
- `testing/` - Quality assurance and performance testing
- `bonus/` - Specialized utility agents

### Agent File Format

Each agent follows a consistent structure:
- YAML frontmatter with `name`, `description`, `tools`, and optional `color`
- Detailed system prompt with responsibilities, frameworks, and best practices
- Integration with 6-day sprint methodology

## NPM Tool Architecture (Planned)

The `claude-agents` NPM tool will implement a **GitHub-first architecture**:

### Core Components
- **`github.js`**: GitHub API integration for dynamic agent discovery
- **`installer.js`**: Agent download and installation management
- **`prompts.js`**: Interactive CLI prompts using inquirer
- **`utils.js`**: File system operations and validation
- **`config/repositories.json`**: Repository configuration

### Key Design Principles
- **Dynamic Fetching**: No bundled agents - everything fetched from GitHub API
- **Repository Flexibility**: Support multiple agent repositories (public/private)
- **Caching Strategy**: Cache GitHub responses with TTL to minimize API calls
- **Offline Support**: Use cached data when GitHub unavailable

### Installation Flow
1. Fetch repository structure via GitHub API (`/repos/:owner/:repo/contents/:path`)
2. Parse agent metadata from YAML frontmatter
3. Present interactive selection (all, by department, individual, search)
4. Download selected agents from raw GitHub URLs
5. Install to `~/.claude/agents/` with metadata tracking

### Configuration Management
User preferences stored in `~/.claude-agents-config.json`:
- Repository URLs and branches
- Installation history with hashes for update detection
- Cache settings and user preferences

## Development Guidelines

### Agent Development
- Follow existing agent structure with YAML frontmatter
- Include 4 detailed usage examples in description
- Maintain consistent voice and 6-day sprint integration
- Each agent should be ~170 lines following department patterns

### NPM Tool Development
- GitHub API rate limiting: 5000 requests/hour (authenticated)
- Implement exponential backoff for API failures
- Support both public and private repositories via tokens
- Maintain backward compatibility for configuration format

## Repository Maintenance

### Agent Updates
- Agents are version-controlled through Git history
- Updates propagate immediately to NPM tool users
- No need for NPM package updates when adding/modifying agents

### NPM Tool Releases
- Semantic versioning independent of agent versions
- Major: Breaking CLI changes
- Minor: New features, repository support improvements  
- Patch: Bug fixes, performance improvements
- No agent bundling - tool updates separate from content updates