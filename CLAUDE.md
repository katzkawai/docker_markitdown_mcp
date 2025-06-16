# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Docker configuration for MarkItDown MCP (Model Context Protocol), a Python-based tool that converts various file formats to Markdown. The container includes media processing capabilities through ffmpeg and metadata extraction via exiftool.

## Key Commands

### Building the Docker Image
```bash
docker build -t markitdown-mcp .
```

### Running the Container
```bash
# Basic run with default user
docker run -v $(pwd):/workdir markitdown-mcp [arguments]

# Run with specific user permissions
docker build --build-arg USERID=$(id -u) --build-arg GROUPID=$(id -g) -t markitdown-mcp .
docker run -v $(pwd):/workdir markitdown-mcp [arguments]
```

## Architecture Notes

- **Base Image**: Python 3.13 slim (Debian Bullseye)
- **Dependencies**: ffmpeg (media processing), exiftool (metadata extraction)
- **Plugin System**: Enabled via `MARKITDOWN_ENABLE_PLUGINS=True`
- **Working Directory**: `/workdir` (mount your files here)
- **Entry Point**: `markitdown-mcp` command

## Development Considerations

- The actual Python application code should be placed in this directory before building
- The Dockerfile expects to copy local files (`COPY . /app`) and install them via pip
- User permissions can be customized using USERID and GROUPID build arguments to match host user