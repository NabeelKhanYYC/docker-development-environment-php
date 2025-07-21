# Docker Development Environment for PHP

A comprehensive Docker-based development environment specifically designed for PHP development with integrated debugging, web-based IDE, and AI coding assistance.

## Overview

This project provides three containerized services that work together to create a complete PHP development environment:

- **IDE Container**: Web-based VS Code server with PHP debugging capabilities
- **CLI Container**: Command-line PHP environment for running scripts and commands  
- **Claude Container**: AI coding assistant integration via Anthropic's Claude Code

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

## Data Persistence

The following data is persisted across container restarts:
- Project files (`/opt/project/`)
- VS Code settings and extensions
- SSH keys and configuration
- XDebug data
- Application logs
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
- Check password in logs: `docker-compose logs ide`

**XDebug not working:**
- Verify XDebug is loaded: `php -m | grep xdebug`
- Check VS Code debug configuration
- Ensure path mappings are correct

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

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test in a clean environment
5. Submit a pull request