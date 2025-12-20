# Cairn-PKM Versioning Policy

*Established: 2025-12-19*

---

## System Status

**Cairn-PKM is in active development (pre-release).**

All components use **0.x.y** versioning until the system reaches production readiness.

---

## Version Scheme

### Format: 0.MINOR.PATCH

**The leading zero signals pre-release status:**
- Breaking changes may occur at any time
- Interfaces may evolve
- Behavior may change between versions
- Not recommended for production use without careful testing

**MINOR (0.x.0):**
- New features added
- Significant workflow changes
- New command capabilities
- Interface modifications

**PATCH (0.x.y):**
- Bug fixes
- Documentation improvements
- Clarifications to existing behavior
- Error handling improvements

---

## Maturity Ranges

Commands are versioned according to their current state:

### 0.8.x - 0.9.x: Highly Developed
**Criteria:**
- Well-tested in real-world use
- Stable interface and behavior
- Comprehensive documentation
- Only minor refinements expected
- Could approach 1.0 with system release

**Example:** A task management command that's been used daily for months

### 0.4.x - 0.7.x: Working & Evolving
**Criteria:**
- Functional and documented
- Interface may still change
- Active development and refinement
- Used regularly but still improving

**Example:** A command that works but workflow is being optimized

### 0.1.x - 0.3.x: Experimental
**Criteria:**
- New functionality
- Proof of concept
- Interface unstable
- May change significantly
- Limited real-world testing

**Example:** A newly introduced command or major rewrite

---

## Update Rules

### Before Updating Any Command:

1. **Read this policy**
2. Check the current version in the file header
3. Determine the type of change you're making
4. Increment the version appropriately
5. **Never bump to 1.x.y or higher** (pre-release only)
6. Update the "Updated" date in the header
7. Add an entry to the version history table at the end of the file

### Version Increment Decision Tree

**Ask yourself:**

1. **Did I add a new feature or capability?**
   - Yes → Bump MINOR (0.5.x → 0.6.0)
   - No → Continue

2. **Did I change the command workflow or interface?**
   - Yes → Bump MINOR (0.5.x → 0.6.0)
   - No → Continue

3. **Did I fix a bug or update documentation?**
   - Yes → Bump PATCH (0.5.2 → 0.5.3)

4. **Did I only update a date or make trivial changes?**
   - Yes → No version bump needed, just update date

### Examples

| Change | Old Version | New Version |
|--------|-------------|-------------|
| Added interactive menu to !hi | 0.11.0 | 0.12.0 |
| Fixed typo in !task output | 0.5.2 | 0.5.3 |
| Added Google Drive support to output pattern | 0.1.1 | 0.2.0 |
| Clarified error handling documentation | 0.3.4 | 0.3.5 |
| Updated date in header only | 0.3.5 | 0.3.5 |

---

## What Triggers 1.0

**Version 1.0.0 will be released when:**

1. **System Stability**
   - All core commands are in 0.8.x+ range
   - No known critical bugs
   - Interface changes are minimal

2. **Documentation Complete**
   - All commands fully documented
   - Architecture guide complete
   - Installation guide tested
   - User workflows documented

3. **Real-World Validation**
   - System used successfully in production
   - Multiple users/vaults tested
   - Edge cases identified and handled

4. **Explicit Decision**
   - Maintainer makes conscious decision to release
   - Release notes prepared
   - Migration guide (if needed) created

**Until then: Everything stays in 0.x.y**

---

## Enforcement

### For LLMs (Claude, GPT, etc.)

**When asked to update a command file:**
1. Read `/mnt/project/_VERSION-POLICY.md` first
2. Check current version in the file you're editing
3. Apply version increment rules from this policy
4. Add version history entry
5. Keep version in 0.x.y range

**Example instruction to include in prompts:**
```
Before updating any cmd-*.md file, read _VERSION-POLICY.md 
and follow the versioning rules. Never bump to 1.x.y.
```

### For Human Maintainers

**When creating/updating commands:**
- [ ] Read _VERSION-POLICY.md
- [ ] Check current version
- [ ] Determine MINOR vs PATCH
- [ ] Update version in header
- [ ] Update "Updated" date
- [ ] Add entry to version history table
- [ ] Verify version is 0.x.y

**PR/Review Checklist:**
- [ ] Version increment follows policy
- [ ] Version history table updated
- [ ] Version is in 0.x.y range
- [ ] "Updated" date is current

---

## Version History Table Format

Every command file should have a version history table at the end:

```markdown
## Version History

| Version | Date | Changes |
|---------|------|---------|
| 0.6.0 | 2025-12-19 | Added interactive menu system |
| 0.5.3 | 2025-12-18 | Fixed error handling for missing files |
| 0.5.2 | 2025-12-16 | Updated documentation examples |
| 0.5.1 | 2025-12-15 | Clarified output format |
| 0.5.0 | 2025-12-14 | Added user preferences support |
```

**Guidelines:**
- Newest entries at top
- Keep all history (don't delete old entries)
- One line per change summary
- Include the date

---

## Current System Version

**Cairn-PKM: 0.5.0**

This represents the overall system maturity. Individual commands may be at different versions based on their own development state.

---

## FAQ

**Q: Why not just use 1.x.y and increment normally?**
A: The 0.x.y range signals to users that breaking changes may occur. It manages expectations and prevents confusion when workflows evolve.

**Q: Can a command ever go backwards in version?**
A: No. Versions always increment forward. If you need to indicate a rewrite, bump the MINOR version significantly (0.4.x → 0.8.0).

**Q: What if I'm uncertain whether something is MINOR or PATCH?**
A: Ask yourself: "Would a user need to change how they use this command?" If yes → MINOR. If no → PATCH.

**Q: When should we consider moving to 1.0?**
A: When the system has been successfully used in production for several months, documentation is complete, and we're confident the core workflows won't change significantly.

---

## Policy Updates

This policy itself may evolve. Changes to this policy will be tracked:

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2025-12-19 | Initial policy established |

---

*This policy ensures consistent versioning across Cairn-PKM during its development phase.*
