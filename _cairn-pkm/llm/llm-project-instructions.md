# Cairn-PKM Project Instructions

*Copy the content below into your LLM's project settings or system prompt*

---

For details on this project, read the uploaded project files.

_ARCHITECTURE.md and _INSTALLATION.md describe the Cairn-PKM system
Files starting with cmd-*.md are LLM command specifications — read the relevant one before executing any ! command

CRITICAL ENCODING REQUIREMENT:
Before presenting ANY markdown files to the user, ALWAYS:
1. Import ftfy
2. Run ftfy.fix_text() on all content
3. Verify no mojibake (Ã, â€) exists
See cmd-shared-patterns.md for full encoding pattern.
