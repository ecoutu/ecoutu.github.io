# blog.ecoutu.io: instructions for Claude

Emma Coutu's blog: Astro + the AstroPaper theme, deployed to GitHub Pages at https://blog.ecoutu.io.

## Writing posts
- Posts are `src/content/posts/*.md(x)`. The frontmatter schema is in `src/content.config.ts`. Use `/new-post <title>` to scaffold one.
- New posts start as `draft: true`. Drafts are hidden in dev and production; to preview one, flip it to `false` temporarily.
- Use the `astro-docs` MCP server (`.mcp.json`) for Astro API questions instead of guessing.
- Site settings are in `astro-paper.config.ts`. Comments are Giscus (`src/components/Comments.astro`).

## Commits: Conventional Commits only
- Every commit message follows [Conventional Commits](https://www.conventionalcommits.org/): `<type>(<optional scope>): <summary>`.
  - Types: `feat`, `fix`, `perf`, `refactor`, `test`, `docs`, `build`, `ci`, `chore`, `style`, `revert`.
  - Keep the summary imperative, lower case, and without a trailing period.
  - Useful scopes: `posts`, `theme`, `comments`, `search`, `deps`.
- The `commit-msg` hook runs commitlint (`@commitlint/config-conventional`, see `commitlint.config.js`). Never bypass it with `--no-verify`.
- Breaking changes: add `!` after the type/scope (`feat(theme)!: ...`) or a `BREAKING CHANGE:` footer.
- Keep each commit to one logical change, so its type describes all of it.

## Commits: write them for the changelog
- Write each summary as the change a reader sees, not the code that moved: `feat(posts): introduce tabchef`, not `feat: add mdx file`.
- Every commit that reaches `main` must read well as a changelog line. No `wip`, `address review`, `fix lint`, `fix typo` or `fix tests` commits.
- Clean up a branch's history before opening or updating a PR:
  - fold follow-up fixes into the commit they fix (`git commit --fixup=<sha>`, then `git rebase -i --autosquash origin/main`);
  - reword a summary that is vague or has the wrong type.
- A fix of something that was never released is folded into that commit, never a separate `fix`.
- After rebasing a branch that is already pushed, update it with `--force-with-lease`.

## Releases: semantic-release
- Merging to `main` runs `.github/workflows/release.yml`: CI, then semantic-release (config in `.releaserc.json`). It reads the commits since the last tag:
  - `feat` (a new post, a new site feature) → minor
  - `fix` / `perf` → patch
  - `!` / `BREAKING CHANGE` → major
  - anything else → no release
- semantic-release alone decides the version. Never bump `package.json` versions, create tags, or edit release notes by hand.
- Pick commit types carefully, because they *are* the changelog.
- `main` is protected by the `protect-main` ruleset. Changes reach it through PRs. The admin bypass exists for the release job's changelog commit; don't use it to push work that skipped review.
- Deploys are separate from releases: `.github/workflows/deploy.yml` publishes every push to `main`.

## History: never use merge commits
- **Never create merge commits.** No `git merge` without `--ff-only`, no `git pull` without `--rebase`, and no `gh pr merge --merge`.
- To update a branch from `main`, rebase: `git fetch && git rebase origin/main`.
- To land a PR, use one of these:
  - `gh pr merge --squash`: the squash commit's title (the PR title) must be a Conventional Commit. The `pr-title` check enforces this.
  - `gh pr merge --rebase`: every commit on the branch must be a Conventional Commit.
- Use `--force-with-lease` for force pushes, never plain `--force`. Never force-push `main`.

## Issues, milestones and the project board
Work is tracked on the **Blog** project board (https://github.com/users/ecoutu/projects/4), with milestones `Launch` and `Growth`.
- **Site changes start with an issue.** Any change to code, config, CI, dependencies or design gets an issue *before* its branch or PR: one PR = one issue. Check `gh issue list` first and reuse an existing issue rather than opening a duplicate.
- **Posts are exempt.** A `feat(posts)` / `fix(posts)` PR that only touches `src/content/posts/` or post media needs no issue. A planned post may still have an issue labelled `post`, as a backlog idea; if it does, close it from the PR. Renovate and release PRs are exempt too.
- An issue is assigned to `ecoutu`, put on the matching milestone, and labelled: `bug` for fixes, `enhancement` for features, `post`, or `chore` / `ci` / `documentation` / `idea` as fits. When work starts, make sure it's assigned (`gh issue edit N --add-assignee ecoutu`).
- The PR is assigned to `ecoutu` (`--assignee ecoutu`), has the same milestone, and links the issue in its body with `Closes #N`. Never write close/fix/resolve #N unless the PR should close it; use `Refs #N` otherwise.
- **Branch names:** `<type>/<issue number>[-short-suffix]`, where the type matches the Conventional Commit type: `fix/12`, `feat/12-analytics`, `ci/8-project-board`. Post branches without an issue use `<type>/posts-<slug>`, for example `feat/posts-introducing-tabchef`.
- **Never rename a PR's head branch on GitHub.** That closes every open PR using it as its head. Name the branch right at creation; to rename later, open a new PR from a new branch and close the old one with a link.
- Relationships:
  - A change that takes more than one PR gets a parent issue with one sub-issue per PR: `gh api -X POST repos/ecoutu/ecoutu.github.io/issues/<parent>/sub_issues -F sub_issue_id=<child .id>`.
  - Ordering dependencies, including stacked PRs: `gh api -X POST repos/ecoutu/ecoutu.github.io/issues/<blocked>/dependencies/blocked_by -F issue_id=<blocker .id>`.
  - Both APIs take the issue's numeric `.id` (`gh api repos/ecoutu/ecoutu.github.io/issues/N -q .id`), not its number.
- `.github/workflows/project-board.yml` adds new issues and PRs to the board and moves Status:
  - issues start in Backlog, draft PRs in In progress, ready PRs in In review;
  - closed or merged items move to Done;
  - a PR without a milestone takes the milestone of the issue it closes.
  - Move an issue to Next or In progress by hand when you pick it up.
- Before merging, re-read the PR's test plan and tick only what has really been verified. Show the user any unticked box and wait.

## Never commit
`MEMORY.md`, `docs/superpowers/**`. These are local only and listed in `.git/info/exclude`. The `/handoff-mnext` and `/resume-mnext` skills and the SessionStart hook in `.claude/` are local too.

## Before committing
Run the scripts from `package.json`: `npm run lint`, `npm run format:check`, `npm run build` (which includes `astro check`). The `pre-push` hook runs lint, the format check and `astro check`. Never guess other commands.
