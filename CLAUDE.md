# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a LaTeX-based resume automation project that uses GitHub Actions to automatically compile `resume.tex` into a PDF whenever changes are pushed. The workflow is designed to maintain professional CVs as code with version control.

## Core Architecture

### LaTeX Template System
- **Base Template**: LuxSleek-CV 1.1 (originally from Overleaf)
- **Philosophy**: No hardcoded settings, all customizations via transparent command definitions
- **Target Audience**: IT and business professionals
- **Key Feature**: Flexible date alignment and two-column layout (dark sidebar + white content area)

### Custom LaTeX Commands
The template defines several key commands in `resume.tex`:
- `\dates{text}` - Bold, non-breaking date formatting
- `\is` - Item spacing control
- `\headleft{text}` - Sidebar section headers with underlines
- `\headright{text}` - Main content section headers in cvblue
- `\jobtitle{title}{dates}` - Job title and date formatting

### Automation Workflow
The GitHub Action (`.github/workflows/convert-and-upload.yml`) runs on PRs to main and:
1. Compiles `resume.tex` using pdflatex (requires texlive-full)
2. Renames output to custom filename (currently `Alexandre_Jammes_CV.pdf`)
3. Moves PDF to `outputs/` directory
4. Commits the PDF back to the repository

## Development Commands

### Local LaTeX Compilation
```bash
# Compile the resume locally (requires texlive-full)
pdflatex resume.tex

# Output will be resume.pdf in the current directory
```

### Pre-commit Hooks
```bash
# Install pre-commit hooks
pre-commit install

# Run all hooks manually
pre-commit run --all-files

# Run specific hook
pre-commit run chktex --all-files
```

### Quality Checks
- **LaTeX Linting**: `chktex resume.tex` - checks LaTeX code quality
- **YAML Linting**: `yamllint .github/workflows/` - validates workflow files
- **Line Ending Normalization**: Automated via pre-commit hooks

## Key Configuration Points

### Customizing Output Filename
To change the resume filename, update these locations in `.github/workflows/convert-and-upload.yml`:
- Line 25-26: Rename command (`mv resume.pdf YOUR_NAME_CV.pdf`)
- Line 31: Move command destination
- Line 40: Git add path

### LaTeX Document Structure
- **Left Column (28% width)**: Profile picture, summary, contact details, skills
- **Right Column (61% width)**: Experience, education, additional sections
- **Color Scheme**: Primary color `cvblue` (#304263) for headers and sidebar
- **Font**: Fira Sans (sans-serif default)

### Required Permissions
GitHub Actions needs "Read and write permissions" in repository settings under Actions → General to commit generated PDFs.

## Pre-commit Configuration

The `.pre-commit-config.yaml` enforces:
- CRLF to LF conversion
- Trailing whitespace removal
- End-of-file fixing
- JSON formatting
- GitHub workflow schema validation
- Devcontainer schema validation
- LaTeX linting via chktex
- YAML linting with 120-char line limit

## Development Environment

### Devcontainer
Uses custom Docker image: `ghcr.io/ajlab-gh/devcontainer:latest`
- Pre-configured with LaTeX tools and linters
- Container name: `resume_as_code`

### Required Tools
- **LaTeX**: texlive-full package
- **Linters**: chktex, yamllint
- **Pre-commit**: Python-based hook framework
- **Git**: For version control and automation

## File Organization

```
├── .devcontainer/          # Development container configuration
├── .github/workflows/      # GitHub Actions automation
├── images/                 # Resume assets (profile picture)
├── outputs/                # Generated PDF destination
├── resume.tex              # Main resume source file
└── .pre-commit-config.yaml # Code quality automation
```

## Important Constraints

1. **LaTeX Syntax**: The template uses specific spacing and minipage structures - preserve exact indentation and structure when modifying layout
2. **Color Changes**: Update `\definecolor{cvblue}` for theme changes, used throughout for consistency
3. **Image Paths**: Profile pictures referenced as `images/resume_profile_picture.jpg`
4. **Workflow Triggers**: Only runs on PRs to main branch, not direct pushes
5. **PDF Output**: GitHub Action commits PDFs automatically - avoid manual PDF commits
