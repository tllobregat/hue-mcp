# Philips Hue MCP Server

A powerful Model Context Protocol (MCP) interface for controlling Philips Hue smart lighting systems.

## Table of Contents

- [Philips Hue MCP Server](#philips-hue-mcp-server)
  - [Overview](#overview)
  - [Features](#features)
  - [Quick Start](#quick-start)
  - [Setup](#setup)
    - [Prerequisites](#prerequisites)
    - [Installation](#installation)
    - [First Run](#first-run)
  - [Using with Claude](#using-with-claude)
  - [API Reference](#api-reference)
    - [Resources](#resources)
    - [Tools](#tools)
    - [Prompts](#prompts)
  - [Examples](#examples)
    - [Controlling Single Lights](#controlling-single-lights)
    - [Working with Groups](#working-with-groups)
    - [Creating Scenes](#creating-scenes)
  - [Advanced Options](#advanced-options)
  - [Troubleshooting](#troubleshooting)
  - [How It Works](#how-it-works)
  - [Contributing](#contributing)
  - [License](#license)

## Overview

This server leverages the Model Context Protocol (MCP) to provide a seamless integration between AI assistants like Claude and your Philips Hue lighting system. With it, you can control your smart lights using natural language, access detailed lighting information, and create advanced lighting setups through a standardized AI-friendly interface.

## Features

- **Complete Light Control**: Turn on/off, adjust brightness, change colors, set color temperature
- **Comprehensive Group Management**: Control multiple lights together, create custom groups
- **Scene Handling**: Apply existing scenes, create quick custom lighting scenes
- **Activity-Based Presets**: Ready-made settings for reading, relaxation, concentration, and more
- **Special Effects**: Access dynamic lighting effects like color loops
- **Natural Language Control**: Specialized prompts for lighting control through conversation
- **Secure Local Integration**: Connects directly to your Hue bridge on your local network

## Quick Start

```bash
# Install dependencies
pip install phue mcp

# Run the server (HTTP/SSE mode)
python hue_server.py

# Run the server (stdio mode for MCP clients)
python hue_server.py --stdio

# Install in Claude Desktop
mcp install hue_server.py --name "My Hue Lights"
```

Then in Claude, start with: "I'd like to control my Philips Hue lights. Can you show me which lights I have available?"

## Setup

### Prerequisites

- Python 3.9+
- A Philips Hue bridge on your local network
- Philips Hue lights paired with your bridge

### Installation

1. Clone this repository or download the `hue_server.py` file
2. Install the required dependencies:

```bash
pip install phue mcp
```

### First Run

1. Run the server:

```bash
python hue_server.py
```

2. When prompted, press the link button on your Hue bridge to authorize the connection
3. Your connection details will be saved in `~/.hue-mcp/config.json` for future use

## Using with Claude

### Option 1: Install in Claude Desktop

If you have Claude Desktop installed:

```bash
mcp install hue_server.py --name "Philips Hue Controller"
```

### Option 2: Test with the MCP Inspector

For development and testing:

```bash
mcp dev hue_server.py
```

## API Reference

### Resources

| Resource | Description |
|----------|-------------|
| `hue://lights` | Information about all lights |
| `hue://lights/{light_id}` | Detailed information about a specific light |
| `hue://groups` | Information about all light groups |
| `hue://groups/{group_id}` | Information about a specific group |
| `hue://scenes` | Information about all scenes |

### Tools

| Tool | Description |
|------|-------------|
| `get_all_lights` | Get information about all lights |
| `get_light` | Get detailed information about a specific light |
| `get_all_groups` | Get information about all light groups |
| `get_group` | Get information about a specific group |
| `get_all_scenes` | Get information about all scenes |
| `turn_on_light` | Turn on a specific light |
| `turn_off_light` | Turn off a specific light |
| `set_brightness` | Adjust light brightness (0-254) |
| `set_color_rgb` | Set light color using RGB values |
| `set_color_temperature` | Set light color temperature (2000-6500K) |
| `turn_on_group` | Turn on all lights in a group |
| `turn_off_group` | Turn off all lights in a group |
| `set_group_brightness` | Adjust group brightness (0-254) |
| `set_group_color_rgb` | Set color for all lights in a group |
| `set_scene` | Apply a scene to a group |
| `find_light_by_name` | Search for lights by name |
| `create_group` | Create a new light group |
| `quick_scene` | Apply custom settings to create a scene |
| `refresh_lights` | Update light information cache |
| `set_color_preset` | Apply a color preset to a light |
| `set_group_color_preset` | Apply a color preset to a group |
| `alert_light` | Make a light flash briefly |
| `set_light_effect` | Set dynamic effects like color loops |

### Prompts

| Prompt | Description |
|--------|-------------|
| `control_lights` | Natural language light control |
| `create_mood` | Setup mood lighting for activities |
| `light_schedule` | Learn about scheduling options |

## Examples

### Controlling Single Lights

```python
# Turn on a light
turn_on_light(1)

# Set a light to 50% brightness
set_brightness(1, 127)

# Change a light color to purple
set_color_rgb(1, 128, 0, 128)

# Set reading mode
set_color_preset(1, "reading")
```

### Working with Groups

```python
# Turn off all lights in living room (group 2)
turn_off_group(2)

# Create a new group
create_group("Bedroom", [3, 4, 5])

# Set all kitchen lights to energizing mode
set_group_color_preset(3, "energize")
```

### Creating Scenes

```python
# Apply an existing scene
set_scene(2, "abc123")  # Group 2, scene ID abc123

# Create a quick relaxing scene for the living room
quick_scene("Evening Relaxation", group_id=2, rgb=[255, 147, 41], brightness=120)
```

## Advanced Options

Run the server with custom settings:

```bash
# Run with custom host and port (HTTP/SSE mode)
python hue_server.py --host 0.0.0.0 --port 8888 --log-level debug

# Run in stdio mode for MCP clients
python hue_server.py --stdio --log-level debug

# All available options:
python hue_server.py --help
```

### Command Line Arguments

| Argument | Description | Default |
|----------|-------------|---------|
| `--host` | Host to bind the server to (HTTP/SSE mode only) | `127.0.0.1` |
| `--port` | Port to run the server on (HTTP/SSE mode only) | `8080` |
| `--log-level` | Logging level (debug, info, warning, error, critical) | `info` |
| `--stdio` | Run server using stdio transport instead of HTTP/SSE | `False` |

## Troubleshooting

- **Bridge not found**: If automatic discovery doesn't work, you have two options:
  1. Manually edit the `BRIDGE_IP` variable in the script with your bridge's IP address
  2. Manually create a config file:
     ```bash
     # Create the config directory
     mkdir -p ~/.hue-mcp
     
     # Create a config.json file with your bridge IP
     echo '{"bridge_ip": "192.168.1.x"}' > ~/.hue-mcp/config.json
     ```
     Replace "192.168.1.x" with your actual Hue bridge IP address
     
- **Connection issues**: Delete `~/.hue-mcp/config.json` and restart the server to re-authenticate
- **Light control not working**: Use `refresh_lights` tool to update the light information cache
- **Groups or scenes not showing up**: Restart the bridge and server to sync all data

## How It Works

This server connects to your Philips Hue bridge using the `phue` Python library and exposes functionality through the Model Context Protocol. When an AI like Claude connects:

1. The server authenticates with your bridge using stored credentials
2. It provides resources that describe your lighting setup
3. It exposes tools that Claude can use to control your lights
4. It offers prompts that help Claude understand how to interact with your lights

All communication with your Hue system happens locally within your network for security and privacy.

## Contributing

Contributions are welcome! Feel free to:

- Report bugs and suggest features in the issue tracker
- Submit pull requests with improvements
- Share examples of how you're using this with your smart home setup

## License

This project is available under the MIT license.
