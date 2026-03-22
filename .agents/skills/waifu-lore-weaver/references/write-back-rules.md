# Write-Back Rules

When lore-weaver generates content, it may discover information that belongs in upstream files. This document defines when and how to write back.

---

## Trigger Conditions & Targets

| Trigger | Target File | What to Write | Frequency |
|---------|------------|---------------|-----------|
| Deep secret reveals entirely new core motivation/trauma | basics.md「背景介绍」 | One vague foreshadowing line (never spoil the secret) | Rare |
| Event has world-level impact (not just personal) | timeline.md | One objective record line | Occasional |
| Secrets/events reveal new faction information | factions.md | Supplement entry | Rare |
| Events/secrets reveal new location details | locations.md | Supplement entry | Rare |
| Event has major impact on a related character | Related character's events.md | Append entry (from their perspective) | Occasional |
| Relationship discovers important person not in basics.md | basics.md「关联角色」 | Append one-line summary | Occasional |
| Same-tier factual conflict in secrets/events | The conflicting old entry | Fix to be consistent | As needed |

---

## When NOT to Write Back

- Pure personal emotions, daily trivia, details that don't affect core character design — these stay in lore files
- Information that basics.md already hints at (even vaguely) — no need to add more
- Relationships that are trivial or transient — not every acquaintance needs a basics.md entry

---

## Write-Back Principles

1. **Supplement, never rewrite** — always append or micro-edit; never large-scale modify existing content
2. **Foreshadow, never spoil** — when writing back to basics.md, maintain its position as "surface knowledge." Add a hint, not the full reveal
3. **When in doubt, don't write back** — better to miss one than to pollute upstream files with unnecessary details
4. **Consistency check** — before writing back, verify the information doesn't contradict what's already in the target file. If it does, you may have a same-layer conflict to resolve in the lore files first

---

## How to Apply Write-Backs

### To basics.md
- Find the relevant section (背景介绍 for secrets/events, 关联角色 for relationships)
- Append a sentence or add a bullet — blend with existing writing style
- The addition should feel like it was always there, not a footnote

### To timeline.md
- Find the correct chronological position
- Add one line in the same format as existing entries
- Use objective, historical voice (not the character's voice)

### To factions.md
- Find the relevant faction section
- Append under the appropriate subsection
- Maintain the existing format

### To locations.md
- Find the relevant location section or append a new one
- Add details discovered through lore (hidden areas, secret passages, etc.)
- Maintain the existing format

### To another character's events.md
- Only if that character already has events.md (from a prior lore-weaver run)
- Append as a new event entry, written from THAT character's perspective
- If the other character has no events.md yet, note it in the report as a suggestion for a future lore-weaver run
