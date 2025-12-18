# Claude Code Plugins

A collection of Claude Code plugins for various operations and workflows.

**Author:** Eran Cohen
**License:** MIT

## Available Plugins

### OpenShift Operations (`openshift-ops`)

Comprehensive Claude Code plugin providing expert-level skills for OpenShift cluster management, troubleshooting, and operations.

**Version:** 1.0.0
**Installation:**
```bash
claude plugin install eranco74/claude-plugins/openshift-ops
```

**Skills Included:**
- OpenShift Debugging - Troubleshoot pods, nodes, operators, networking, and storage
- Cluster Upgrade - Plan, execute, and troubleshoot cluster upgrades
- Node Operations - Manage node lifecycle, cordoning, draining, and maintenance
- Operator Troubleshooting - Debug cluster operators, OLM, and subscription issues

**[Documentation →](openshift-ops/README.md)**

---

## Installation

### Install a Specific Plugin

```bash
# Install the OpenShift Operations plugin
claude plugin install eranco74/claude-plugins/openshift-ops

# Verify installation
claude plugin list
```

### Install from Local Development

```bash
# Clone the repository
git clone https://github.com/eranco74/claude-plugins.git
cd claude-plugins

# Install a specific plugin
claude plugin install ./openshift-ops
```

## Repository Structure

```
claude-plugins/
├── README.md                    # This file
├── openshift-ops/              # OpenShift Operations Plugin
│   ├── manifest.json
│   ├── marketplace.json
│   ├── README.md
│   ├── PUBLISHING.md
│   ├── LICENSE
│   └── skills/
│       ├── openshift-debugging/
│       ├── openshift-cluster-upgrade/
│       ├── openshift-node-operations/
│       └── openshift-operator-troubleshooting/
└── [future-plugins]/           # Additional plugins will go here
```

## Contributing

Contributions are welcome! To add a new plugin or improve existing ones:

1. Fork the repository
2. Create a new branch for your changes
3. Add your plugin in a new subdirectory
4. Ensure proper `manifest.json` and documentation
5. Test your plugin locally
6. Submit a pull request

## Plugin Development

Each plugin should follow this structure:

```
plugin-name/
├── manifest.json          # Plugin metadata and configuration
├── marketplace.json       # Marketplace submission metadata
├── README.md             # Plugin documentation
├── LICENSE               # License file
└── skills/               # Skill definitions
    └── skill-name/
        └── SKILL.md
```

### Creating a New Plugin

1. Create a new directory with your plugin name
2. Add required files (manifest.json, README.md, etc.)
3. Follow the structure of existing plugins
4. Test locally before submitting

## Support

For issues, questions, or suggestions:
- Open an issue in this repository
- Reference the specific plugin in your issue title

## License

Each plugin is licensed under MIT unless otherwise specified in its directory.

Copyright (c) 2025 Eran Cohen
