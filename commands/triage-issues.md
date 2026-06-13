---
description: Triage open issues in a repository
---

Triage the open issues in `$ARGUMENTS` (format: owner/repo, or the current
repository if no argument is given).

For each open issue that has no labels:
- Suggest one or more appropriate labels based on its title and body
  (e.g. bug, enhancement, documentation, question)
- Flag issues that look like duplicates of other open issues
- Flag issues that look stale (no activity for a long time) or that are
  missing information needed to act on them

Summarize the findings as a short list grouped by suggested action
(label, close as duplicate, request more info, etc.). Do not modify any
issues unless explicitly asked to.
