# Git-Flow Policy

This document describes the branching strategy and repository rules of the Campus Lost and Found Portal team (seng321-team24). The rules are enforced with a GitHub ruleset on `main` and `develop`.

## Branch model

```text
main
  Assessed and released versions only.
  Every sprint submission is tagged: sprint-1, sprint-2, sprint-3, v1.0.0

develop
  Integrated work intended for the next release.
  Default branch of the repository.

feature/<issue-no>-<short-name>
  Short-lived branch for one story, defect, or task.
  Example: feature/12-reservation-create

release/<version>
  Optional stabilization branch, used only if a real release need appears.

hotfix/<issue-no>-<short-name>
  Optional urgent correction based on a released main version.
```

## Repository rules

1. No direct push to `main` or `develop`. All changes go through pull requests.
2. Every change starts with an issue that has a purpose, an owner (assignee) and acceptance criteria.
3. Feature branches are created from `develop` and named `feature/<issue-no>-<short-name>`.
4. Every pull request is opened against `develop`, links its issue, and contains test or review evidence (test output, lint result, or screenshot).
5. Every pull request needs at least one approving review from a teammate before merge. The author cannot approve their own pull request.
6. Review conversations must be resolved before merge.
7. Required status checks must pass before merge, when checks are configured.
8. Force pushes and branch deletion are blocked on `main` and `develop`.
9. Every member works with their own GitHub account. Nobody commits on behalf of another member.
10. Feature branches are deleted after merge.

## Commit messages

Format: `<type>: <short description> (#<issue-no>)`

Types: `feat`, `fix`, `docs`, `chore`, `test`, `refactor`.

Example: `docs: add api folder (#1)`

## Pull request process

1. Open an issue and assign it to yourself.
2. Create `feature/<issue-no>-<short-name>` from `develop`.
3. Commit your work with messages that reference the issue.
4. Open a pull request into `develop` using the pull request template. Use `Closes #<issue-no>` for task issues, `Refs #<issue-no>` for issues that stay open.
5. Request a review from the assigned teammate.
6. Fix review comments, then merge after approval.

## Cross-review order (Sprint 1)


| PR author | Reviewer |
|---|---|
| irem-ks | BerraB1 |
| tugbacay21 | irem-ks |
| Aslhnnkcmaaz | tugbacay21 |
| ccerentrn | Aslhnnkcmaaz |
| BerraB1  | ccerentrn |

## Sprint release

At the end of each sprint:

1. A pull request merges `develop` into `main`, reviewed by a teammate.
2. The merge commit on `main` is tagged `sprint-N` (for Sprint 1: `sprint-1`).
3. A GitHub Release is created from that tag.
