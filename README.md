# Cursor Rules

A collection of Cursor IDE rules and configurations that can be easily downloaded and applied to your projects.

## Overview

This repository serves as a central hub for Cursor IDE rules and configurations. It provides a collection of useful rules that can be directly downloaded and applied to your projects using simple curl commands.

## Features

- Collection of Cursor IDE rules and configurations
- Easy download and application of rules
- Version control for rules
- Direct integration with GitHub repositories

## Available Rules

- `basic.mdc`: Basic configuration and preferences for Cursor IDE
- `documents.mdc`: Documentation standards and guidelines
- `fornt-refine-vite.mdc`: Frontend development rules with Vite
- `git.mdc`: Git and GitHub workflow guidelines
- `supabase.mdc`: Supabase integration and usage rules

## Usage

### Quick Setup (All Rules)

You can download and extract all rules at once using the following command:

```bash
curl -L https://github.com/kater-iam/cursorrules/archive/main.tar.gz | tar xz --strip=2 "cursorrules-main/.cursor"
```

This command:
1. Downloads the GitHub repository archive
2. Extracts only the `.cursor` directory
3. Places it in your current directory

### Individual Rules Download

If you need to download rules individually, you can use these commands:

```bash
# Download basic configuration
curl -o .cursor/rules/basic.mdc https://raw.githubusercontent.com/kater-iam/cursorrules/main/.cursor/rules/basic.mdc

# Download documentation rules
curl -o .cursor/rules/documents.mdc https://raw.githubusercontent.com/kater-iam/cursorrules/main/.cursor/rules/documents.mdc

# Download all rules
curl -o .cursor/rules/basic.mdc https://raw.githubusercontent.com/kater-iam/cursorrules/main/.cursor/rules/basic.mdc
curl -o .cursor/rules/documents.mdc https://raw.githubusercontent.com/kater-iam/cursorrules/main/.cursor/rules/documents.mdc
curl -o .cursor/rules/fornt-refine-vite.mdc https://raw.githubusercontent.com/kater-iam/cursorrules/main/.cursor/rules/fornt-refine-vite.mdc
curl -o .cursor/rules/git.mdc https://raw.githubusercontent.com/kater-iam/cursorrules/main/.cursor/rules/git.mdc
curl -o .cursor/rules/supabase.mdc https://raw.githubusercontent.com/kater-iam/cursorrules/main/.cursor/rules/supabase.mdc
```

### Manual Setup

1. Clone this repository
2. Copy the desired rule files to your project's `.cursor/rules` directory
3. Customize the rules as needed

## Directory Structure

```
.cursor/
└── rules/
    ├── basic.mdc
    ├── documents.mdc
    ├── fornt-refine-vite.mdc
    ├── git.mdc
    └── supabase.mdc
```

## Contributing

Feel free to contribute by:
1. Creating a new rule
2. Improving existing rules
3. Adding documentation
4. Reporting issues

## License

MIT License 