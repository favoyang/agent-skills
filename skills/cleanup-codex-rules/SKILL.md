---
name: cleanup-codex-rules
description: Learns recurring safe Codex approval rules from session history, then compresses covered one-offs.
---

# Cleanup Codex Rules

Use this skill when the user wants to maintain, compress, or refresh Codex
approval rules, especially `~/.codex/rules/default.rules`.

This skill should optimize for coverage first and compactness second. The goal
is not merely to make the rules file shorter; it is to learn durable, recurring
safe workflows from Codex history so future runs ask for approval less often.

## Workflow

1. Review all relevant files under `~/.codex/sessions`, not just the current
   rules file.
2. Extract recurring safe command families from session history, especially:
   - commands that repeatedly requested escalation
   - commands that were approved multiple times with the same leading argv
   - recurring tool wrappers such as shell launchers, env setters, task runners,
     and stable subcommands
3. Compare the learned command families against the current
   `~/.codex/rules/default.rules`.
4. Update `~/.codex/rules/default.rules` by:
   - adding generic prefix rules for repeated future workflows discovered from
     session history
   - keeping wrapper prefixes that are required for a real match
   - removing one-off entries already covered by broader prefixes
5. Prefer adding a missing durable rule over merely shortening an existing
   exact rule.
6. Keep the final rule set minimal and effective without losing useful
   historical coverage.
7. Avoid destructive or overly broad patterns.
8. Create a backup next to the rules file with runtime local time:
   - `~/.codex/rules/default.rules.bak-$(date +%Y%m%d-%H%M%S)`
9. Write the candidate to `/tmp/default.rules.candidate`.
10. Verify the candidate before replacing the live file.
11. Replace `~/.codex/rules/default.rules` with the verified candidate.
12. Remove `/tmp/default.rules.candidate`.
13. Delete stale backup files older than 7 days:
   - `find ~/.codex/rules -maxdepth 1 -type f -name 'default.rules.bak-*' -mtime +7 -delete`
14. Report the cleanup result with concrete counts, for example:
   - how many session files were reviewed
   - how many recurring command families were learned
   - how many new standing rules were added from session history
   - rules reduced from `X` to `Y`
   - how many stale backups were deleted
   - whether any self-maintenance approval rules were added by the run

## Rules Of Thumb

- Prefer broad safe prefixes over many exact one-off rules.
- Mine session history broadly enough to discover missing durable rules; do not
  treat the current rules file as the only source of truth.
- Optimize for fewer future approval prompts across recurring workflows, not
  just a shorter file.
- Codex approval rules are prefix matches on the actual argv. Preserve required
  launcher/wrapper segments such as `["/bin/bash", "-lc", ...]`,
  `["bash", "-lc", ...]`, `["sh", "-lc", ...]`, or similar wrappers when they
  are part of the command shape that Codex really executes.
- Do not "simplify" a rule by dropping a shell wrapper unless the replacement
  prefix still matches the real command invocation. A shorter pattern that no
  longer matches is not a cleanup; it causes repeated approval prompts.
- Keep exact rules only when a broader prefix would be unsafe.
- Treat destructive commands conservatively.
- If the maintenance run adds self-maintenance approval entries, compress them
  away on the next candidate when they are not needed as standing rules.
- Use `/tmp` for temporary candidate files instead of writing snapshots under
  `~/.codex/rules`.

## Prefix Matching Guidance

- Generalize from left to right and keep every argv segment that is required to
  preserve a real prefix match.
- When a command is executed through a shell wrapper, the safe compression point
  is usually inside the shell payload string, not by removing the shell binary
  and flags.
- Example:
  Keep `["/bin/bash", "-lc", "ANSIBLE_LOCAL_TEMP=/workspace/project/.ansible/tmp"]`
  if Codex invokes the workflow through `bash -lc`.
- Example:
  Replacing
  `["/bin/bash", "-lc", "mkdir -p .ansible/tmp && ANSIBLE_LOCAL_TEMP=/workspace/project/.ansible/tmp"]`
  with
  `["ANSIBLE_LOCAL_TEMP=/workspace/project/.ansible/tmp"]`
  is incorrect, because the shorter rule no longer matches the actual prefix.
- Before removing a longer rule, confirm that the proposed shorter rule matches
  the same command family as executed, including wrappers added by Codex,
  shells, env setters, or task runners.

## Verification

After install, verify:

- session history was actually reviewed
- the live file exists
- the timestamped backup exists
- `/tmp/default.rules.candidate` is removed
- stale backups older than 7 days are removed
- the final response includes the before/after rule count
- the final response states whether new rules were learned from sessions

If a maintenance run required new approval just for the maintenance workflow
itself, mention that explicitly in the final response.
