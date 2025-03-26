# Cursor Rules

A collection of Cursor IDE rules and configurations that can be easily downloaded and applied to your projects.

## Overview

This repository serves as a central hub for Cursor IDE rules and configurations. It provides a collection of useful rules that can be directly downloaded and applied to your projects using simple curl commands.

## Features

- Collection of Cursor IDE rules and configurations
- Easy download and application of rules
- Version control for rules
- Direct integration with GitHub repositories

## Usage

### Downloading Rules

You can download rules directly from this repository using curl commands. Here are some examples:

```bash
# Download a specific rule file
curl -o .cursor/rules/your-rule.json https://raw.githubusercontent.com/kater-iam/cursorrules/main/rules/your-rule.json

# Download multiple rules
curl -o .cursor/rules/rule1.json https://raw.githubusercontent.com/kater-iam/cursorrules/main/rules/rule1.json
curl -o .cursor/rules/rule2.json https://raw.githubusercontent.com/kater-iam/cursorrules/main/rules/rule2.json
```

### Manual Setup

1. Clone this repository
2. Copy the desired rule files to your project's `.cursor/rules` directory
3. Customize the rules as needed

## Directory Structure

```
.cursor/
└── rules/
    ├── rule1.json
    ├── rule2.json
    └── ...
```

## Contributing

Feel free to contribute by:
1. Creating a new rule
2. Improving existing rules
3. Adding documentation
4. Reporting issues

## License

MIT License 