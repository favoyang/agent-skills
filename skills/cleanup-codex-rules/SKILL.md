---
name: cleanup-codex-rules
description: Cleans up Codex approval rules by making them more generic and compressing covered one-offs.
---

# Cleanup Codex Rules

Use this skill when the user wants to maintain, compress, or refresh Codex
approval rules, especially `~/.codex/rules/default.rules`.

## Workflow

1. Review `~/.codex/sessions` for commands that requested escalation.
2. Extract recurring safe command families.
3. Update `~/.codex/rules/default.rules` by:
   - adding generic prefix rules for repeated future workflows
   - removing one-off entries already covered by broader prefixes
4. Keep the final rule set minimal and effective.
5. Avoid destructive or overly broad patterns.
6. Create a backup next to the rules file with runtime local time:
   - `~/.codex/rules/default.rules.bak-$(date +%Y%m%d-%H%M%S)`
7. Write the candidate to `/tmp/default.rules.candidate`.
8. Verify the candidate before replacing the live file.
9. Replace `~/.codex/rules/default.rules` with the verified candidate.
10. Remove `/tmp/default.rules.candidate`.
11. Delete stale backup files older than 7 days:
   - `find ~/.codex/rules -maxdepth 1 -type f -name 'default.rules.bak-*' -mtime +7 -delete`
12. Report the cleanup result with concrete counts, for example:
   - rules reduced from `X` to `Y`
   - how many stale backups were deleted
   - whether any self-maintenance approval rules were added by the run

## Rules Of Thumb

- Prefer broad safe prefixes over many exact one-off rules.
- Keep exact rules only when a broader prefix would be unsafe.
- Treat destructive commands conservatively.
- If the maintenance run adds self-maintenance approval entries, compress them
  away on the next candidate when they are not needed as standing rules.
- Use `/tmp` for temporary candidate files instead of writing snapshots under
  `~/.codex/rules`.

## Verification

After install, verify:

- the live file exists
- the timestamped backup exists
- `/tmp/default.rules.candidate` is removed
- stale backups older than 7 days are removed
- the final response includes the before/after rule count

If a maintenance run required new approval just for the maintenance workflow
itself, mention that explicitly in the final response.
