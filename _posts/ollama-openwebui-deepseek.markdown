---
layout: post
title: "Running OpenWebUI with Podman: A Corporate-Friendly LLM Setup"
date: 2025-01-17 12:00:00 -0500
categories: ai tutorial
---

While setting up local LLMs has become increasingly popular, many of us face restrictions on corporate laptops that prevent using Docker. Here's how I successfully set up OpenWebUI using Podman on my IBM-issued MacBook, creating a secure and IT-compliant local AI environment.

## Why Podman?

Podman is a daemonless container engine that's often allowed in corporate environments where Docker isn't. It's:
- Compatible with Docker commands and images
- Runs in userspace without requiring root privileges
- Compliant with many corporate security policies
- Officially supported by Red Hat and IBM (my employer)

## Understanding the Daemon Difference
To understand why Podman is preferred in corporate environments, it's important to understand how it differs from Docker's architecture. Docker relies on a "root daemon" (dockerd), which is a persistent background process that:

Runs continuously with root/administrative privileges
Manages all Docker containers, images, and networking
Must be running at all times for Docker to work

This daemon approach presents several concerns in corporate environments:

Security Risk: A persistent process with root privileges could potentially be exploited
Administrative Overhead: Requires root/admin access to install and configure
IT Policy Conflicts: Many corporate policies explicitly prohibit running persistent root-level services

Podman's daemonless approach eliminates these concerns by:

Running containers without any persistent background process
Operating without requiring root privileges
Running each container directly under your user account
Starting and stopping containers without needing a central management service

Think of it this way: Docker is like having a privileged butler (the daemon) who must be present and actively managing everything, while Podman is more like having direct access to do things yourself, with regular user permissions. This architectural difference makes Podman a more secure and IT-friendly choice for corporate environments.

## Installation Steps

### 1. Installing Ollama

First, you'll need Ollama installed. On macOS, this is straightforward:
1. Download from [ollama.ai](https://ollama.ai)
2. Drag to Applications
3. Run Ollama from Applications folder

### 2. Setting Up Podman

Installing Podman on macOS using Homebrew:

```bash
# Install Podman
brew install podman

# Initialize and start Podman machine
podman machine init
podman machine start
```

### 3. Installing OpenWebUI

With Podman ready, we can pull and run OpenWebUI:

```bash
# Pull the latest OpenWebUI image
podman pull ghcr.io/open-webui/open-webui:main

# Run OpenWebUI
podman run -d -p 3000:8080 --name open-webui -v open-webui:/app/backend/data ghcr.io/open-webui/open-webui:main
```

If you get a port conflict (like I did), you can use a different port:

```bash
# Run on port 3001 instead
podman run -d -p 3001:8080 --name open-webui -v open-webui:/app/backend/data ghcr.io/open-webui/open-webui:main
```

### 4. Connecting to Ollama

Once OpenWebUI is running:
1. Open your browser to `http://localhost:3000` (or `3001` if you changed the port)
2. Click on "Settings"
3. Set the Ollama API endpoint to `http://localhost:11434`

## Useful Podman Commands

Here are some helpful commands for managing your OpenWebUI container:

```bash
# List running containers
podman ps

# Stop the OpenWebUI container
podman stop open-webui

# Start an existing container
podman start open-webui

# Remove the container
podman rm open-webui

# View container logs
podman logs open-webui
```

## Troubleshooting

Common issues and solutions:

### Port Conflicts
If you see a 500 error or can't access the UI, check for port conflicts:
```bash
lsof -i :3000
```
Use a different port number if needed, as shown in the installation steps above.

### Podman Machine Issues
If Podman isn't responding:
```bash
# Check machine status
podman machine list

# Restart the machine
podman machine stop
podman machine start
```

### Volume Persistence
If your settings aren't persisting between restarts, verify the volume mount:
```bash
podman volume ls
podman volume inspect open-webui
```

## Corporate Network Considerations

When running on a corporate network:
- Ensure you're not blocked by corporate firewalls
- Consider using VPN if accessing from outside the office
- Check with IT policies regarding local AI model usage
- Keep your Podman and OpenWebUI installations updated

## Benefits Over Docker Setup

Using Podman in a corporate environment offers several advantages:
- No root daemon running in the background
- Better security isolation
- Compatibility with corporate security policies
- Similar command syntax to Docker (easy transition)

## Looking Forward

This setup has allowed me to run local LLMs on my corporate laptop while staying compliant with IT policies. As more organizations adopt AI tools, having a secure, corporate-friendly way to run local models becomes increasingly important.

The combination of Ollama, Podman, and OpenWebUI provides a robust foundation for local AI development that works well within corporate constraints. Whether you're writing code, drafting documents, or exploring AI capabilities, this setup offers a practical solution that respects corporate IT policies while delivering the full power of local LLMs.
