# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Reddit MCP (Model Context Protocol) server that provides AI-enhanced Reddit interaction capabilities through 13 specialized tools. The server uses FastMCP framework and implements both read-only and authenticated Reddit operations.

## Development Commands

```bash
# Install dependencies
uv install

# Run the MCP server
uv run server.py

# Lint code (must pass before committing)
ruff check

# Format code
ruff format

# Check and format in one go
ruff check && ruff format
```

## Architecture

### Core Components

- **server.py**: Single-file MCP server implementation (1,901 lines)
  - `RedditClientManager`: Singleton pattern for Reddit client management
  - `@require_write_access`: Decorator for authentication enforcement
  - 13 tool implementations with comprehensive error handling

### Key Patterns

1. **Dual Authentication Modes**:
   - Read-only: Uses only client credentials
   - Authenticated: Requires username/password for write operations

2. **Error Handling Strategy**:
   - Multi-level fallbacks for client initialization
   - Structured error responses with actionable guidance
   - Comprehensive logging throughout

3. **Data Enhancement**:
   - AI-driven insights and recommendations in responses
   - Engagement analysis and performance metrics
   - Strategic timing suggestions

### Environment Variables

```bash
# Required for basic operation
REDDIT_CLIENT_ID=your_client_id
REDDIT_CLIENT_SECRET=your_client_secret

# Optional for write access
REDDIT_USERNAME=your_username
REDDIT_PASSWORD=your_password

# Optional customization
REDDIT_USER_AGENT=custom_user_agent
```

## Code Standards

- **Python 3.10+** required
- **Line length**: 88 characters (ruff enforced)
- **Quotes**: Double quotes for strings
- **Type hints**: Required for all function parameters and returns
- **Docstrings**: Comprehensive with structured return type documentation
- **Error handling**: Always use try-except with specific error messages

## Available Tools

### Read-only Tools (8):
- `get_user_info(username)` - User analysis with engagement insights
- `get_top_posts(subreddit, time_filter, limit)` - Top posts with analysis
- `get_subreddit_stats(subreddit)` - Comprehensive subreddit metrics
- `get_trending_subreddits(limit)` - Popular subreddits list
- `get_submission_by_url(url)` - Retrieve post by URL
- `get_submission_by_id(submission_id)` - Retrieve post by ID
- `get_submission_comments(submission_id, limit, sort, include_replies, max_depth)` - Fetch comments in LLM-friendly format
- `get_submission_with_media(submission_id)` - Enhanced media handling for images, videos, galleries, and polls

### Authenticated Tools (3):
- `who_am_i()` - Current user information
- `create_post(subreddit, title, content, flair, is_self)` - Create posts
- `reply_to_post(post_id, content, subreddit)` - Reply to posts

## Common Development Tasks

### Adding a New Tool

1. Define the tool function with `@mcp_server.tool()` decorator
2. Add comprehensive docstring with parameter descriptions
3. Implement error handling with meaningful messages
4. For write operations, add `@require_write_access` decorator
5. Return structured JSON with metadata and AI insights

### Modifying Authentication

Authentication logic is centralized in:
- `RedditClientManager._initialize_client()` (server.py:77-153)
- `@require_write_access` decorator (server.py:156-174)

### Testing Changes

Currently no automated tests exist. When testing:
1. Run `ruff check` and `ruff format` before committing
2. Test both read-only and authenticated modes
3. Verify error handling with invalid inputs
4. Check rate limiting behavior

## Important Considerations

1. **Rate Limiting**: Reddit API has strict rate limits. The code handles this gracefully but be aware during testing.

2. **Authentication States**: The server can operate in three states:
   - No client (initialization failed)
   - Read-only client (client credentials only)
   - Authenticated client (full credentials)

3. **Error Messages**: Always provide user-friendly, actionable error messages that guide users to resolution.

4. **AI Enhancement**: All responses should include AI-driven insights when relevant - this is a key differentiator of this MCP server.