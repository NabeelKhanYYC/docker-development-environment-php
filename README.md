# Docker Development Environment for PHP

A comprehensive Docker-based development environment specifically designed for PHP development with integrated debugging, web-based IDE, and AI coding assistance.

## Overview

This project provides six containerized services that work together to create a complete PHP development environment:

- **IDE Container**: Web-based VS Code server with PHP debugging capabilities
- **CLI Container**: Command-line PHP environment for running scripts and commands  
- **Claude Container**: AI coding assistant integration via Anthropic's Claude Code
- **Browser Container**: Static file browser for navigating project filesystem
- **Web Container**: Apache web server for executing and serving PHP files
- **Webgrind Container**: Web-based profiling tool for analyzing PHP performance

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
   docker compose up -d
   ```

3. Access the services:
   - **Web IDE**: http://localhost:50001
   - **File Browser**: http://localhost:50031
   - **Web Server**: http://localhost:50041
   - **Webgrind Profiler**: http://localhost:50051
   - **SSH to IDE**: `ssh -i ./docker/shared-folder/ssh/key root@localhost -p 50000`
   - **SSH to CLI**: `ssh -i ./docker/shared-folder/ssh/key root@localhost -p 50010`
   - **SSH to Claude**: `ssh -i ./docker/shared-folder/ssh/key root@localhost -p 50020`

## Services

### IDE Container
- **Base**: [Ubuntu Latest](https://hub.docker.com/_/ubuntu)
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
- **Base**: [PHP 8.3 CLI](https://hub.docker.com/_/php)
- **Features**:
  - Pure command-line PHP environment
  - XDebug support
  - Composer installed
- **Ports**: 
  - 50010 (SSH)

### Claude Container
- **Base**: [Ubuntu Latest](https://hub.docker.com/_/ubuntu)
- **Features**:
  - Node.js runtime
  - Claude Code CLI for AI assistance
  - Direct access to project files
- **Ports**: 
  - 50020 (SSH)

### Browser Container
- **Base**: [Nginx Latest](https://hub.docker.com/_/nginx)
- **Features**:
  - Static file browser with autoindex
  - Read-only access to project files
  - Web-based filesystem navigation
  - PHP source code viewing (serves .php files as text/plain)
  - Centralized logging to `/opt/log/nginx/`
- **Ports**: 
  - 50031 (HTTP)

### Web Container
- **Base**: [PHP 8.3 with Apache](https://hub.docker.com/_/php)
- **Features**:
  - Apache web server for PHP execution
  - XDebug support for web debugging
  - PHP file execution and rendering
  - Centralized logging to `/opt/log/`
- **Ports**: 
  - 50041 (HTTP)

### Webgrind Container
- **Base**: [PHP 8.3 with Apache](https://hub.docker.com/_/php)
- **Features**:
  - WebGrind from [jokkedk/webgrind](https://github.com/jokkedk/webgrind)
  - Web-based XDebug profiling frontend
  - Performance analysis and call graph generation
  - Automatic integration with existing XDebug setup
  - Function execution time tracking
  - Memory usage analysis
- **Ports**: 
  - 50051 (HTTP)

## Development Workflow

### Using the Web IDE
1. Open http://localhost:50001 in your browser
2. If prompted for a password, check the container logs: `docker compose logs ide`
3. Your project files are mounted at `/opt/project/`
4. PHP debugging is pre-configured with XDebug

### Using the CLI Container
```bash
# SSH into CLI container
ssh -i ./docker/shared-folder/ssh/key root@localhost -p 50010

# Run PHP scripts
php your-script.php

# Use Composer
composer install
composer require package/name
```

### Using Claude AI Assistant
```bash
# SSH into Claude container
ssh -i ./docker/shared-folder/ssh/key root@localhost -p 50020

# Use Claude Code CLI (requires API key)
claude --help
```

### Using the Web Container
```bash
# Access PHP files via web browser
# Navigate to http://localhost:50041/your-file.php

# PHP files are executed by Apache and rendered in the browser
# Perfect for testing web applications and viewing output

# Example: Create a simple PHP file and access it
echo "<?php phpinfo(); ?>" > /opt/project/info.php
# Then visit: http://localhost:50041/info.php
```

### PHP Profiling with Webgrind
```bash
# Access Webgrind profiling interface
# Navigate to http://localhost:50051

# Webgrind automatically detects XDebug profiling data from:
# - Web Container (Apache/PHP execution)
# - CLI Container (command-line scripts)  
# - IDE Container (VS Code debugging sessions)

# No configuration required - works out of the box
# Analyze function performance, execution times, and call graphs
# Perfect for identifying performance bottlenecks in PHP applications
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

## SSH Key Management

### Developer SSH Key
A developer SSH key is automatically generated when the environment starts and is stored at:
```
./docker/shared-folder/ssh/key
```

This key provides secure access to all containers in the development environment. The key is:
- **Automatically generated**: No manual setup required
- **Persistent**: Stored in the shared folder and reused across container restarts
- **Universal**: Works for all containers (IDE, CLI, Claude)

### Using the SSH Key
All SSH connections to containers should use this developer key:

```bash
# General format
ssh -i ./docker/shared-folder/ssh/key root@localhost -p [PORT]

# Example: Connect to IDE container
ssh -i ./docker/shared-folder/ssh/key root@localhost -p 50000
```

The key file will be created automatically on first startup, so ensure the containers have been started at least once before attempting SSH connections.

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
├── web/                 # Web server container
│   ├── Dockerfile
│   ├── docker-compose.yml
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
# View logs using docker compose
docker compose logs [service-name]

# Or access the log volume directly
docker compose exec ide tail -f /opt/log/nginx/access.log
```

## Data Persistence

The following data is persisted across container restarts:
- Project files (`/opt/project/`) - accessible via IDE, CLI, Claude, and browser
- VS Code settings and extensions
- Developer SSH key (`./docker/shared-folder/ssh/key`) and SSH configuration
- XDebug data
- Log files (shared volume at `/opt/log/`) - accessible from all containers
- Claude settings

## Troubleshooting

### Common Issues

**Container won't start:**
```bash
# Check container logs
docker compose logs [service-name]

# Rebuild containers
docker compose down
docker compose build --no-cache
docker compose up -d
```

**Can't access web IDE:**
- Check if port 50001 is available
- Verify the container is running: `docker compose ps`
- Check password in logs: `docker compose logs ide` or `/opt/log/vscode.log`

**XDebug not working:**
- Verify XDebug is loaded: `php -m | grep xdebug`
- Check VS Code debug configuration
- Ensure path mappings are correct

**Can't access file browser:**
- Check if port 50031 is available
- Verify the browser container is running: `docker compose ps`
- Check nginx logs: `docker compose logs browser` or `/opt/log/nginx/error.log`

**Can't access web server:**
- Check if port 50041 is available
- Verify the web container is running: `docker compose ps`
- Check Apache logs: `docker compose logs web`
- Ensure PHP files are in `/opt/project/` directory

**Can't access Webgrind profiler:**
- Check if port 50051 is available
- Verify the webgrind container is running: `docker compose ps`
- Check webgrind logs: `docker compose logs webgrind`
- Ensure XDebug profiling data is being generated by running PHP scripts in other containers

**SSH connection refused:**
- Wait for containers to fully start
- Check SSH service status inside container
- Verify the developer SSH key exists at `./docker/shared-folder/ssh/key`
- Ensure you're using the correct SSH command format: `ssh -i ./docker/shared-folder/ssh/key root@localhost -p [PORT]`

### Useful Commands

```bash
# View running containers
docker compose ps

# View container logs
docker compose logs [service-name]

# Restart specific service
docker compose restart [service-name]

# Execute command in container
docker compose exec [service-name] [command]

# Clean rebuild
docker compose down -v
docker compose build --no-cache
docker compose up -d
```

## License

MIT License - see LICENSE file for details.