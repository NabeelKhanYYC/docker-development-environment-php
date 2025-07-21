# Docker Development Environment for PHP

A comprehensive Docker-based development environment specifically designed for PHP development with integrated debugging, web-based IDE, and AI coding assistance.

## Overview

This project provides four containerized services that work together to create a complete PHP development environment:

- **IDE Container**: Web-based VS Code server with PHP debugging capabilities
- **CLI Container**: Command-line PHP environment for running scripts and commands  
- **Claude Container**: AI coding assistant integration via Anthropic's Claude Code
- **Browser Container**: Static file browser for navigating project filesystem

## Prerequisites

- Docker
- Docker Compose
- Git (for cloning the repository)

## Quick Start

1. Clone this repository:
   ```bash
   git clone https://github.com/NabeelKhanYYC/docker-development-environment-php.git
   cd docker-development-environment-php
   ```

2. Start the development environment:
   ```bash
   docker-compose up -d
   ```

3. Access the services:
   - **Web IDE**: http://localhost:50001
   - **File Browser**: http://localhost:50031
   - **SSH to IDE**: `ssh root@localhost -p 50000`
   - **SSH to CLI**: `ssh root@localhost -p 50010`
   - **SSH to Claude**: `ssh root@localhost -p 50020`

## Services

### IDE Container
- **Base**: Ubuntu Latest
- **PHP**: 8.3 with CLI and development tools
- **Features**: 
  - VS Code Server accessible via web browser
  - XDebug for debugging
  - PHP Composer for dependency management
  - Automated backup system
  - Centralized logging to `/opt/log/`
- **Ports**: 
  - 50000 (SSH)
  - 50001 (Web IDE)

### CLI Container  
- **Base**: PHP 8.3 CLI
- **Features**:
  - Pure command-line PHP environment
  - XDebug support
  - Composer installed
- **Ports**: 
  - 50010 (SSH)

### Claude Container
- **Base**: Ubuntu Latest
- **Features**:
  - Node.js runtime
  - Claude Code CLI for AI assistance
  - Direct access to project files
- **Ports**: 
  - 50020 (SSH)

### Browser Container
- **Base**: Nginx Latest
- **Features**:
  - Static file browser with autoindex
  - Read-only access to project files
  - Web-based filesystem navigation
  - Centralized logging to `/opt/log/nginx/`
- **Ports**: 
  - 50031 (HTTP)

## Development Workflow

### Using the Web IDE
1. Open http://localhost:50001 in your browser
2. If prompted for a password, check the container logs: `docker-compose logs ide`
3. Your project files are mounted at `/opt/project/`
4. PHP debugging is pre-configured with XDebug

### Using the CLI Container
```bash
# SSH into CLI container
ssh root@localhost -p 50010

# Run PHP scripts
php your-script.php

# Use Composer
composer install
composer require package/name
```

### Using Claude AI Assistant
```bash
# SSH into Claude container
ssh root@localhost -p 50020

# Use Claude Code CLI (requires API key)
claude --help
```

## Configuration

### Environment Variables

Create a `.env` file in the project root:

```env
# VS Code password (optional - auto-generated if not set)
VSCODE_PASSWORD=your-secure-password

# Claude API key (required for AI features)
ANTHROPIC_API_KEY=your-api-key
```

### VS Code Settings
- Default theme: Visual Studio Dark
- Minimap: Disabled
- PHP executable: Pre-configured
- XDebug: Multiple debug configurations available

### XDebug Configuration
The environment includes three pre-configured debug setups:
- **localhost**: Direct debugging
- **via CLI container**: For CLI scripts
- **via Apache2**: For web applications

## Debugging Setup

### VS Code Debugging
1. Open the web IDE at http://localhost:50001
2. Set breakpoints in your PHP code
3. Use the debug panel to start debugging:
   - "xDebug (localhost)" - for direct debugging
   - "xDebug (via CLI container)" - for CLI scripts  
   - "xDebug (via Apache2)" - for web applications

## File Structure

```
docker/
├── ide/                 # Web IDE container
│   ├── Dockerfile
│   ├── docker-compose.yml
│   └── etc/
├── cli/                 # CLI container  
│   ├── Dockerfile
│   ├── docker-compose.yml
│   └── etc/
├── claude/              # Claude AI container
│   ├── Dockerfile
│   ├── docker-compose.yml
│   └── etc/
├── browser/             # File browser container
│   ├── Dockerfile
│   ├── docker-compose.yml
│   ├── etc/
│   └── bin/
├── common/              # Shared configuration
│   └── etc/
└── shared-folder/       # Shared data and backups
    ├── backup/
    └── ssh/
```

## Backup System

The IDE container includes an automated backup system:
- Backups are stored in `docker/shared-folder/backup/`
- Backups are organized by date
- Backup script located at `/opt/bin/backup`

## Logging

All containers share a common log volume mounted at `/opt/log/`, providing centralized logging accessible from any container or the host system.

### Available Log Files

**Browser Container (Nginx):**
- `/opt/log/nginx/access.log` - HTTP access logs
- `/opt/log/nginx/error.log` - Nginx error logs  
- `/opt/log/nginx/daemon.log` - Nginx daemon logs

**IDE Container:**
- `/opt/log/vscode.log` - VS Code server logs
- `/opt/log/cron.log` - Cron service logs
- `/opt/log/backup.log` - Automated backup logs

### Accessing Logs

**From any container:**
```bash
# SSH into any container and access logs directly
tail -f /opt/log/nginx/access.log
tail -f /opt/log/vscode.log
```

**From host system:**
```bash
# View logs using docker-compose
docker-compose logs [service-name]

# Or access the log volume directly
docker-compose exec ide tail -f /opt/log/nginx/access.log
```

## Data Persistence

The following data is persisted across container restarts:
- Project files (`/opt/project/`) - accessible via IDE, CLI, Claude, and browser
- VS Code settings and extensions
- SSH keys and configuration
- XDebug data
- Log files (shared volume at `/opt/log/`) - accessible from all containers
- Claude settings

## Troubleshooting

### Common Issues

**Container won't start:**
```bash
# Check container logs
docker-compose logs [service-name]

# Rebuild containers
docker-compose down
docker-compose build --no-cache
docker-compose up -d
```

**Can't access web IDE:**
- Check if port 50001 is available
- Verify the container is running: `docker-compose ps`
- Check password in logs: `docker-compose logs ide` or `/opt/log/vscode.log`

**XDebug not working:**
- Verify XDebug is loaded: `php -m | grep xdebug`
- Check VS Code debug configuration
- Ensure path mappings are correct

**Can't access file browser:**
- Check if port 50031 is available
- Verify the browser container is running: `docker-compose ps`
- Check nginx logs: `docker-compose logs browser` or `/opt/log/nginx/error.log`

**SSH connection refused:**
- Wait for containers to fully start
- Check SSH service status inside container
- Verify SSH keys are properly mounted

### Useful Commands

```bash
# View running containers
docker-compose ps

# View container logs
docker-compose logs [service-name]

# Restart specific service
docker-compose restart [service-name]

# Execute command in container
docker-compose exec [service-name] [command]

# Clean rebuild
docker-compose down -v
docker-compose build --no-cache
docker-compose up -d
```

## License

MIT License - see LICENSE file for details.