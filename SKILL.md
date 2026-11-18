---
name: whats-next
description: Find the next session-ready ticket in the current GitHub repository, explain blockers and ranking, and optionally claim a chosen ticket and route it through the ask-matt skill. Use when asked what to work on next, which ticket is ready, or to claim and start a Matt Pocock ticket workflow.
---

# Whats Next

Find work that is ready to start in the **current repository**. Keep discovery read-only. Present recommendations and their evidence, then wait for the user to choose and explicitly confirm before changing an assignment.

## Non-negotiable boundaries

- Inspect only the repository that is currently open or explicitly identified as the current repository. Do not search other repositories, organizations, or external sources to fill gaps.
- Use the available GitHub integration or authenticated GitHub CLI to read issues and relationships. If neither provides reliable access, explain the limitation and stop without guessing.
- Treat issue labels, including `ready-for-agent`, as metadata only. Labels never establish readiness or override an unresolved dependency.
- Read native GitHub blocking relationships and sub-issue/parent relationships, plus dependency statements in issue descriptions. Follow links only when they resolve to an issue in the current repository. Treat out-of-repository or unverifiable references as unresolved.
- Do not change issues during discovery. Never assign, edit, close, comment on, or create an issue until the user selects a specific ticket and explicitly confirms the claim.
- Never estimate duration, ask how much time the user has, rank by time available, or show duration filters or time estimates.
- Do not infer which Matt Pocock workflow or skill applies. After a successful claim, invoke the available `ask-matt` skill and let it route the selected ticket. If `ask-matt` is unavailable, say so and stop before starting another workflow.

## Gather the current frontier

1. Identify the current repository from the working context. If it is ambiguous, ask which repository is current; do not search for candidates.
2. Read the repository's open issues and the data needed to establish status, assignee, native blockers, hierarchy, and description-level dependencies. Use pagination until the available open issue set is complete, or state the coverage limitation.
3. Treat parent issues that contain sub-issues as containers. Do not recommend or claim a container as an executable ticket. Consider its actionable open sub-issues individually.
4. Classify each actionable issue using the readiness rules below. Keep excluded and uncertain issues in the backlog view with the reason.
5. Rank eligible issues using transparent judgment. Prioritize downstream impact, then immediate session readiness and the strength of evidence. Do not invent numeric scores or weights. For each position, state the evidence behind the ordering and any uncertainty.

## Readiness rules

An issue is in **Ready now** only when all of these are true:

- It is open and actionable, not a parent/container issue.
- No unresolved native blocker or description-level dependency remains.
- Its blocking and assignment status can be verified from current-repository data.
- It is unassigned or assigned to the current user. Issues assigned to someone else are excluded from Ready now to avoid collisions.

If assigned to the current user, an otherwise ready issue remains eligible. Mark it clearly as already assigned to the user. Do not treat it as unclaimed.

Use **Needs review** when a dependency is ambiguous, refers outside the current repository, cannot be verified, or when the data is incomplete or conflicting. Explain what must be checked. Never promote a Needs review issue to Ready now because of a label.

Use **Blocked** when a known unresolved dependency or blocker remains. Show the blocking issue or dependency text and its current status when available.

Use **Assigned to someone else** for open actionable issues assigned to another person. Exclude them from Ready now, and show only enough assignment information to explain why they are excluded.

## Present the result

Default to the **Ready now frontier**. Include a graph and a backlog view, with filters for status/category, assignee, and hierarchy where the host supports them. Show the ranked Ready now items first and group the remaining open work as Blocked, Needs review, Assigned to someone else, or Containers.

For every recommended ticket, show:

- Issue number and title, with a direct link when available.
- Assignee state: unassigned or assigned to the current user.
- A concise readiness explanation citing the blocker/dependency data you checked.
- Why it ranks here, emphasizing downstream impact and session readiness.
- Any uncertainty or missing evidence.

Use the host's native interactive visualization surface when one is available. Represent issues as nodes and blocking dependencies as directed links; distinguish Ready now, Blocked, Needs review, assigned elsewhere, and containers. Make the Ready now frontier the default view, with an accessible backlog view and on-demand refresh. Do not create or edit repository files just to render the view. If the host has no interactive visualization surface, provide a readable text graph and filterable-by-request grouped issue table, and state that the interactive view is unavailable in this environment.

Make ranking transparent: explain the observed factors and tradeoffs in ordinary language. Do not present an opaque AI score or suggest that ranking is an objective priority set by the repository.

End by offering the user the option to select a specific Ready now ticket to claim and start. Do not claim anything as part of showing recommendations.

## Claim and launch

When the user asks to claim or start work:

1. Confirm the exact issue number and title with the user if their choice is ambiguous.
2. Re-read that issue and its current status, assignee, native blocker relationships, sub-issue/parent state, and description-level dependencies. Refresh all relevant evidence immediately before the write. If it is no longer open, has a blocker, is assigned to someone else, or has become ambiguous, do not claim it; explain the change and refresh the frontier.
3. Show the exact proposed change: assign the selected issue to the authenticated current user. Ask for explicit confirmation unless the user has already explicitly authorized claiming that exact issue in the current interaction.
4. Only after confirmation, assign the issue to the current user using the available authorized GitHub action. Do not make any other issue changes.
5. Verify the assignment by reading the issue back. If the result is unclear, read again before considering any retry. Do not report success unless the assignment is verified.
6. Only after verified successful claim, invoke `ask-matt` with the selected issue context. Let that skill choose the relevant Matt Pocock workflow. Do not guess a workflow or invoke a different skill as a substitute.
7. Report the verified claim and the route returned by `ask-matt`. If `ask-matt` cannot be loaded or used, preserve the successful claim, state that routing could not be completed, and do not start work under an inferred workflow.

## Refresh

Refresh the frontier only when the user requests it or when a claim workflow requires a pre-claim recheck. On refresh, re-read current-repository issue status, assignees, blockers, hierarchy, and description dependencies. Do not imply that a previous result is current after repository state may have changed.
