# Cairn-PKM Development & Release Workflow

Guide for maintaining and releasing the Cairn-PKM system.

## Repository Structure

**GitHub:** https://github.com/SFZC-WebOps/cairn-pkm

**Branches:**
- `main` - Initial setup (not actively used)
- `dev` - Active development (auto-backup via Obsidian Git)
- `stable` - Release-ready versions

**Local Vault:** `/Users/sfzcwebops/Documents/CAIRN/obsidian-vaults/CAIRN-dev`

---

## Daily Development Workflow

### Working in the Vault

1. Open CAIRN-dev vault in Obsidian
2. Make changes (edit commands, add examples, update docs)
3. Obsidian Git auto-commits every 5 minutes to `dev` branch
4. All changes automatically pushed to GitHub

**Manual commit:** Cmd+P → "Git: Commit-and-sync"

### What to Work On

**Core system files** (`_CAIRN/`):
- Command specs in `llm/commands/`
- Templates in `templates/`
- Views/dashboards in `views/`
- Tool documentation in `tools/`
- Core docs: ARCHITECTURE.md, INSTALLATION.md, VERSION

**Example content** (distributed to users):
- Example tracks in `Tracks/`
- Example objects in `Objects/`
- Example captures in `Capture/`
- Setup guides in root

**Development-only** (`_dev/`):
- This workflow document
- Development notes
- Testing checklists
- Design decisions

---

## Release Workflow

### When to Release

Release when:
- Command specs are stable
- Examples are complete and tested
- Documentation is accurate
- Ready for others to use

### Version Numbering

Use semantic versioning: `MAJOR.MINOR.PATCH`

- **MAJOR** (1.0.0 → 2.0.0): Breaking changes, major restructure
- **MINOR** (1.0.0 → 1.1.0): New features, new commands, significant additions
- **PATCH** (1.0.0 → 1.0.1): Bug fixes, typo corrections, small improvements

### Release Steps

#### 1. Prepare Release

**Update VERSION file:**
```yaml
version: 1.1.0
released: 2025-01-20
repo: https://github.com/SFZC-WebOps/cairn-pkm
releases: https://github.com/SFZC-WebOps/cairn-pkm/releases
```

**Test everything:**
- All commands work as documented
- All wikilinks resolve correctly
- Example content makes sense
- No broken references

#### 2. Merge to Stable

**In Terminal:**
```bash
cd /Users/sfzcwebops/Documents/CAIRN/obsidian-vaults/CAIRN-dev

# Switch to stable branch
git checkout stable

# Merge dev into stable
git merge dev

# Tag the release
git tag v1.1.0

# Push to GitHub
git push origin stable --tags

# Return to dev for continued work
git checkout dev
```

#### 3. Create Release Zip

**In Terminal:**
```bash
cd /Users/sfzcwebops/Documents/CAIRN/obsidian-vaults

# Create zip (exclude .git, .obsidian workspace, and _dev/)
zip -r cairn-v1.1.0.zip CAIRN-dev \
  -x "CAIRN-dev/.git/*" \
  -x "CAIRN-dev/.obsidian/workspace*.json" \
  -x "CAIRN-dev/.obsidian/plugins/*" \
  -x "CAIRN-dev/_dev/*"
```

The zip file will be at: `/Users/sfzcwebops/Documents/CAIRN/obsidian-vaults/cairn-v1.1.0.zip`

#### 4. Create GitHub Release

1. Go to: https://github.com/SFZC-WebOps/cairn-pkm/releases
2. Click "Draft a new release"
3. **Tag:** v1.1.0 (select from existing tags)
4. **Target:** stable
5. **Release title:** CAIRN v1.1.0
6. **Description:** Write release notes (see template below)
7. **Attach:** Upload `cairn-v1.1.0.zip`
8. Click "Publish release"

---

## Release Notes Template

```markdown
# CAIRN v1.1.0

[Brief description of what's new or changed]

## ✨ New Features
- Added X command
- New Y template
- Enhanced Z functionality

## 🐛 Bug Fixes
- Fixed issue with A
- Corrected B behavior

## 📝 Documentation
- Updated C guide
- Added D examples

## 📦 Installation

### New Users
1. Download `cairn-v1.1.0.zip`
2. Unzip to your desired location
3. Open folder in Obsidian
4. Install required plugins: Dataview, Templater, Tasks
5. See INSTALLATION.md for setup guide

### Updating from Previous Version
1. Download `cairn-v1.1.0.zip`
2. Extract and copy `_CAIRN/` folder
3. Replace your existing `_CAIRN/` folder
4. Your content in Tracks/, Objects/, Capture/ stays untouched
5. Restart Obsidian

## 🔧 Requirements
- Obsidian 1.0+
- Dataview plugin (with JS queries enabled)
- Templater plugin
- Tasks plugin

---

**Full changelog:** https://github.com/SFZC-WebOps/cairn-pkm/compare/v1.0.0...v1.1.0
```

---

## Quick Reference Commands

### Check Current Status
```bash
cd /Users/sfzcwebops/Documents/CAIRN/obsidian-vaults/CAIRN-dev
git status
git branch
git log --oneline -5
```

### View Commit History
```bash
git log --oneline --graph --all
```

### See What Changed
```bash
git diff dev stable
```

### List All Tags
```bash
git tag -l
```

### Push Tags to GitHub
```bash
git push origin --tags
```

---

## Troubleshooting

### Obsidian Git Not Pushing
1. Check Settings → Obsidian Git → "Push on commit-and-sync" is enabled
2. Manually push: Cmd+P → "Git: Push"
3. Check git status in terminal: `git status`

### Merge Conflicts
```bash
# If merge has conflicts:
git status  # See conflicted files
# Edit files to resolve conflicts
git add .
git commit -m "Resolved merge conflicts"
git push
```

### Need to Undo Last Commit
```bash
# Undo last commit but keep changes
git reset --soft HEAD~1

# Undo last commit and discard changes (careful!)
git reset --hard HEAD~1
```

### Accidentally Committed to Wrong Branch
```bash
# Move last commit to different branch
git checkout correct-branch
git cherry-pick <commit-hash>
git checkout wrong-branch
git reset --hard HEAD~1
```

---

## File Organization

### What Goes Where

**`_CAIRN/` (distributed to all users):**
- System core: commands, templates, views
- Tool documentation
- VERSION file
- ARCHITECTURE.md, INSTALLATION.md

**`_dev/` (development only - NOT distributed):**
- This workflow document
- Development notes
- Design decisions
- Testing procedures

**Root & other folders (examples for users):**
- Sample tracks (area-admin, p001-office-move)
- Sample objects (contacts, vendors, tools)
- Sample captures (ideas, notes)
- SETUP-GUIDE.md

**Automatically excluded from releases:**
- `.git/` - Version control
- `.obsidian/workspace*.json` - User workspace state
- `.obsidian/plugins/*` - Plugin files
- `_dev/*` - Development documentation

---

## Versioning Strategy

### Pre-1.0 (Development)
- v0.1.0, v0.2.0, etc.
- Breaking changes allowed
- Experimental features

### Post-1.0 (Stable)
- v1.0.0 = First stable release
- v1.x.x = Stable API, backwards compatible
- v2.0.0 = Next major version (if breaking changes needed)

### Current Version: 1.0.0 (preparing for first release)

---

*Last updated: 2025-01-18*
