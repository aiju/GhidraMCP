[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://www.apache.org/licenses/LICENSE-2.0)
[![GitHub release (latest by date)](https://img.shields.io/github/v/release/LaurieWired/GhidraMCP)](https://github.com/LaurieWired/GhidraMCP/releases)
[![GitHub stars](https://img.shields.io/github/stars/LaurieWired/GhidraMCP)](https://github.com/LaurieWired/GhidraMCP/stargazers)
[![GitHub forks](https://img.shields.io/github/forks/LaurieWired/GhidraMCP)](https://github.com/LaurieWired/GhidraMCP/network/members)
[![GitHub contributors](https://img.shields.io/github/contributors/LaurieWired/GhidraMCP)](https://github.com/LaurieWired/GhidraMCP/graphs/contributors)
[![Follow @lauriewired](https://img.shields.io/twitter/follow/lauriewired?style=social)](https://twitter.com/lauriewired)

![ghidra_MCP_logo](https://github.com/user-attachments/assets/4986d702-be3f-4697-acce-aea55cd79ad3)


# ghidraMCP
ghidraMCP is a Model Context Protocol server for allowing LLMs to autonomously reverse engineer applications. It exposes numerous tools from core Ghidra functionality to MCP clients.

https://github.com/user-attachments/assets/36080514-f227-44bd-af84-78e29ee1d7f9


# Features

MCP Server + Ghidra Plugin providing 30+ tools:

### Program Enumeration
- List functions, classes, namespaces, imports, exports, memory segments
- List defined data items and strings (with optional filter)
- Search functions and symbols by name

### Decompilation & Disassembly
- Decompile functions by name or address
- Disassemble functions (address, instruction, comments)
- Read raw bytes at an address (hexdump with hex + ASCII)

### Renaming & Annotation
- Rename functions (by name or address), variables, and data labels
- Set decompiler comments (PRE_COMMENT) and disassembly comments (EOL_COMMENT)
- Set function prototypes (full C signature parsing)
- Set local variable types

### Data Types
- Search data types by name
- Inspect data type details (struct fields, enum values, typedefs, etc.)
- Create data types from C definitions (structs, enums, typedefs)
- Rename struct fields by byte offset
- Set data type at an address (for global variables)

### Cross-References
- Get references to an address (xrefs to)
- Get references from an address (xrefs from)
- Get all references to a function by name

### Navigation
- Get current address and function selected in Ghidra's GUI
- Look up functions by address

# Installation

## Prerequisites
- [Ghidra](https://ghidra-sre.org)
- Python 3
- MCP [SDK](https://github.com/modelcontextprotocol/python-sdk)

## Ghidra Extension
First, download the latest [release](https://github.com/LaurieWired/GhidraMCP/releases) from this repository. This contains the Ghidra plugin and Python MCP client. Then, you can directly import the plugin into Ghidra.

1. Run Ghidra
2. Select `File` -> `Install Extensions`
3. Click the `+` button
4. Select the release zip file
5. Restart Ghidra
6. Make sure the GhidraMCPPlugin is enabled in `File` -> `Configure` -> `Developer`
7. *Optional*: Configure the port in Ghidra with `Edit` -> `Tool Options` -> `GhidraMCP HTTP Server` (default: 9876)

Video Installation Guide:


https://github.com/user-attachments/assets/75f0c176-6da1-48dc-ad96-c182eb4648c3



## MCP Clients

Theoretically, any MCP client should work with ghidraMCP. Three examples are given below.

### Example 1: Claude Desktop
To set up Claude Desktop as a Ghidra MCP client, go to `Claude` -> `Settings` -> `Developer` -> `Edit Config` -> `claude_desktop_config.json` and add the following:

```json
{
  "mcpServers": {
    "ghidra": {
      "command": "python3",
      "args": [
        "/ABSOLUTE_PATH_TO/bridge_mcp_ghidra.py"
      ]
    }
  }
}
```

Alternatively, edit this file directly:
```
/Users/YOUR_USER/Library/Application Support/Claude/claude_desktop_config.json
```

The server IP and port are configurable via command-line arguments and should be set to point to the target Ghidra instance. If not set, both will default to `localhost:9876`.

```
python3 bridge_mcp_ghidra.py --ghidra-server http://127.0.0.1:9876/
```

### Example 2: Claude Code
Add a `.mcp.json` file to your project root:

```json
{
  "mcpServers": {
    "ghidra-mcp": {
      "command": "python3",
      "args": ["path/to/bridge_mcp_ghidra.py"]
    }
  }
}
```

### Example 3: Cline
To use GhidraMCP with [Cline](https://cline.bot), this requires manually running the MCP server as well. First run the following command:

```
python3 bridge_mcp_ghidra.py --transport sse --mcp-host 127.0.0.1 --mcp-port 8081
```

The only *required* argument is the transport. If all other arguments are unspecified, they will default to the above. Once the MCP server is running, open up Cline and select `MCP Servers` at the top.

![Cline select](https://github.com/user-attachments/assets/88e1f336-4729-46ee-9b81-53271e9c0ce0)

Then select `Remote Servers` and add the following, ensuring that the url matches the MCP host and port:

1. Server Name: GhidraMCP
2. Server URL: `http://127.0.0.1:8081/sse`

### Example 4: 5ire
Another MCP client that supports multiple models on the backend is [5ire](https://github.com/nanbingxyz/5ire). To set up GhidraMCP, open 5ire and go to `Tools` -> `New` and set the following configurations:

1. Tool Key: ghidra
2. Name: GhidraMCP
3. Command: `python3 /ABSOLUTE_PATH_TO/bridge_mcp_ghidra.py`

# Building from Source

Set the `GHIDRA_INSTALL_DIR` environment variable or Gradle property to point to your Ghidra installation, then build with Gradle:

```
export GHIDRA_INSTALL_DIR=/path/to/ghidra
gradle distributeExtension
```

Or set it in `gradle.properties`:
```
GHIDRA_INSTALL_DIR=/path/to/ghidra
```

The generated zip file in `dist/` includes the built Ghidra extension and its resources.
