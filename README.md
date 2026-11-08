# blog.ecoutu.io

Emma Coutu's blog, built with [Astro](https://astro.build/) on the [AstroPaper](https://github.com/satnaing/astro-paper) theme.

## Develop

```sh
npm install
npm run dev        # http://localhost:4321
npm run build      # astro check + build + Pagefind index
npm run lint
npm run format
```

## Writing

- Posts live in `src/content/posts/*.md(x)`. The frontmatter schema is in `src/content.config.ts`.
- New posts start with `draft: true`. Remove it to publish.
- In Claude Code, `/new-post <title>` scaffolds a post. The `astro-docs` MCP server (`.mcp.json`) gives Claude current Astro docs.

## Commits and releases

- Commits follow [Conventional Commits](https://www.conventionalcommits.org/), for example `feat(posts): introduce tabchef`. The husky `commit-msg` hook runs commitlint, and the `pr-title` check requires PR titles in the same format, since a squash merge uses the title as the commit message.
- On every push to `main`, `.github/workflows/release.yml` runs CI and then semantic-release. It bumps the version, updates `CHANGELOG.md` and creates a GitHub release. `feat` makes a minor release, `fix`/`perf` a patch, and `!` or `BREAKING CHANGE` a major one.
- Renovate keeps dependencies up to date with `chore(deps)` commits (`renovate.json`).

## Site

- Site settings are in `astro-paper.config.ts`.
- Comments: Giscus (GitHub Discussions, "Announcements" category), in `src/components/Comments.astro`.
- Search: Pagefind, indexed at build time.
- Deploy: `.github/workflows/deploy.yml` publishes to GitHub Pages on every push to `main`. The custom domain is in `public/CNAME`.
