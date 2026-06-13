---
description: Show the status of open pull requests for a repository
---

Check the open pull requests for `$ARGUMENTS` (format: owner/repo, or the
current repository if no argument is given).

For each open PR, report:
- PR number, title, and author
- CI status (passing, failing, or pending)
- Review status (approved, changes requested, or pending review)
- Whether it has merge conflicts with its base branch

Summarize the results in a short table, and call out any PRs that look
ready to merge or that are blocked.
