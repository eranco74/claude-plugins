# Publishing Guide: OpenShift Operations Plugin

This guide walks you through the process of publishing your Claude Code plugin to the marketplace.

## Prerequisites

- [x] GitHub account
- [x] Git installed locally
- [x] Plugin files ready (manifest.json, README.md, LICENSE, skills/)
- [ ] GitHub repository created

## Step 1: Prepare Your Plugin

### Verify Plugin Structure

Run this command to verify all required files are present:

```bash
cd /home/eran/go/src/github/eranco74/openshift-ops

# Check file structure
ls -la
# Should show:
# - manifest.json
# - README.md
# - LICENSE
# - skills/ (directory)

# Verify skills directory
ls -la skills/
# Should show all 4 skill directories
```

### Validate manifest.json

```bash
# Check if manifest.json is valid JSON
cat manifest.json | jq .

# If jq is not installed, use python
python3 -m json.tool manifest.json
```

## Step 2: Initialize Git Repository

```bash
cd /home/eran/go/src/github/eranco74/openshift-ops

# Initialize git (if not already done)
git init

# Check git status
git status
```

## Step 3: Create .gitignore

Create a `.gitignore` file to exclude unnecessary files:

```bash
cat > .gitignore << 'EOF'
# Claude Code
.claude/

# OS files
.DS_Store
Thumbs.db

# Editor files
.vscode/
.idea/
*.swp
*.swo

# Logs
*.log

# Temp files
tmp/
temp/
EOF
```

## Step 4: Commit Your Files

```bash
# Add all files
git add .

# Check what will be committed
git status

# Create initial commit
git commit -m "Initial commit: OpenShift Operations Plugin v1.0.0

- Added 4 core skills for OpenShift operations
- Comprehensive troubleshooting and upgrade workflows
- Node operations and operator debugging capabilities
- Full documentation and examples"

# Verify commit
git log
```

## Step 5: Create GitHub Repository

### Option A: Using GitHub CLI (gh)

```bash
# Install gh if not already installed
# https://cli.github.com/

# Authenticate
gh auth login

# Create repository
gh repo create claude-code-plugin-openshift-ops \
  --public \
  --description "Comprehensive Claude Code plugin for OpenShift cluster management, troubleshooting, and operations" \
  --source=. \
  --remote=origin \
  --push
```

### Option B: Using GitHub Web Interface

1. Go to https://github.com/new
2. Repository name: `claude-code-plugin-openshift-ops`
3. Description: "Comprehensive Claude Code plugin for OpenShift cluster management, troubleshooting, and operations"
4. Public repository
5. Do NOT initialize with README (we already have one)
6. Click "Create repository"

Then push your local code:

```bash
# Add remote
git remote add origin https://github.com/eranco74/claude-code-plugin-openshift-ops.git

# Verify remote
git remote -v

# Push to GitHub
git branch -M main
git push -u origin main
```

## Step 6: Create Release Tag

```bash
# Create annotated tag
git tag -a v1.0.0 -m "Release v1.0.0

Features:
- OpenShift Debugging skill
- Cluster Upgrade skill
- Node Operations skill
- Operator Troubleshooting skill

Initial release with comprehensive OpenShift operations support."

# Push tag to GitHub
git push origin v1.0.0
```

## Step 7: Create GitHub Release

### Using GitHub CLI

```bash
gh release create v1.0.0 \
  --title "OpenShift Operations Plugin v1.0.0" \
  --notes "Initial release with 4 core skills for OpenShift cluster management.

**Skills Included:**
- OpenShift Debugging
- Cluster Upgrade
- Node Operations
- Operator Troubleshooting

**Features:**
- Comprehensive troubleshooting workflows
- Step-by-step upgrade procedures
- Node lifecycle management
- Operator diagnosis and remediation

For installation and usage, see README.md"
```

### Using GitHub Web Interface

1. Go to https://github.com/eranco74/claude-code-plugin-openshift-ops/releases/new
2. Tag: `v1.0.0`
3. Release title: "OpenShift Operations Plugin v1.0.0"
4. Add release notes (see above)
5. Click "Publish release"

## Step 8: Test Installation

Test that others can install your plugin:

```bash
# Install from GitHub
claude plugin install eranco74/claude-code-plugin-openshift-ops

# Or with specific version
claude plugin install eranco74/claude-code-plugin-openshift-ops@v1.0.0

# Verify installation
claude plugin list
```

## Step 9: Submit to Claude Code Marketplace

### Find the Official Marketplace Repository

Check the Claude Code documentation for the official plugins/marketplace repository URL.

### Create Marketplace Submission

1. Fork the official Claude Code marketplace repository
2. Create a new branch: `add-openshift-ops-plugin`
3. Add your plugin entry to the marketplace catalog (usually `plugins.json` or similar)
4. Create a pull request with:
   - Plugin name
   - Repository URL
   - Description
   - Categories/tags
   - Screenshots or demo (if applicable)

Example marketplace entry:

```json
{
  "name": "openshift-ops",
  "displayName": "OpenShift Operations",
  "author": "Eran Cohen",
  "repository": "https://github.com/eranco74/claude-code-plugin-openshift-ops",
  "description": "Comprehensive plugin for OpenShift cluster management, troubleshooting, and operations",
  "version": "1.0.0",
  "categories": ["DevOps", "Infrastructure", "SRE"],
  "keywords": ["openshift", "kubernetes", "troubleshooting", "operations"],
  "license": "MIT"
}
```

## Step 10: Promote Your Plugin

### Add Topics to GitHub Repository

```bash
# Using gh CLI
gh repo edit --add-topic claude-code-plugin
gh repo edit --add-topic openshift
gh repo edit --add-topic kubernetes
gh repo edit --add-topic sre
gh repo edit --add-topic troubleshooting
```

### Create a README Badge

Add this to your README.md:

```markdown
[![Claude Code Plugin](https://img.shields.io/badge/Claude_Code-Plugin-purple)](https://github.com/eranco74/claude-code-plugin-openshift-ops)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Version](https://img.shields.io/github/v/release/eranco74/claude-code-plugin-openshift-ops)](https://github.com/eranco74/claude-code-plugin-openshift-ops/releases)
```

### Share on Social Media / Communities

- OpenShift community forums
- Kubernetes Slack channels
- Reddit (r/openshift, r/kubernetes)
- LinkedIn
- Twitter/X with hashtags: #OpenShift #ClaudeCode #SRE

## Maintenance

### Updating Your Plugin

```bash
# Make changes to skills or docs
# ...

# Commit changes
git add .
git commit -m "Update: Added new troubleshooting techniques"

# Create new version tag
git tag -a v1.1.0 -m "Version 1.1.0 - Added new features"

# Push changes and tag
git push origin main
git push origin v1.1.0

# Create new release
gh release create v1.1.0 --title "v1.1.0" --notes "See CHANGELOG.md"
```

### Responding to Issues

- Monitor GitHub issues
- Respond to user feedback
- Fix bugs promptly
- Consider feature requests

## Troubleshooting

### Common Issues

**Problem:** Git push fails with authentication error
**Solution:** Set up SSH keys or use GitHub CLI authentication

**Problem:** Plugin not found when installing
**Solution:** Ensure repository is public and manifest.json is valid

**Problem:** Skills not loading
**Solution:** Verify SKILL.md files have proper frontmatter (---name---description---)

## Next Steps

- [ ] Set up GitHub Actions for automated testing
- [ ] Create contribution guidelines (CONTRIBUTING.md)
- [ ] Add issue templates
- [ ] Set up documentation site (GitHub Pages)
- [ ] Create demo video or screenshots
- [ ] Write blog post about the plugin

## Resources

- [Claude Code Documentation](https://docs.claude.ai/claude-code)
- [GitHub CLI Documentation](https://cli.github.com/manual/)
- [Semantic Versioning](https://semver.org/)
- [OpenShift Documentation](https://docs.openshift.com/)

## Support

If you encounter issues during publishing:
1. Check Claude Code documentation
2. Review GitHub's publishing guides
3. Open an issue in the Claude Code marketplace repository
4. Ask in the Claude Code community forums

---

**Ready to publish?** Start with Step 1 and work through each step carefully. Good luck!
