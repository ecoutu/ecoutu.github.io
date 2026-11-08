---
name: new-post
description: Create a new blog post draft in src/content/posts with valid AstroPaper frontmatter. Use when asked to start, draft, or scaffold a blog post.
argument-hint: "<post title>"
---

# New blog post

Create a post for the title given in the arguments (ask for one if none was given).

1. Make a kebab-case slug from the title. The file is `src/content/posts/<slug>.mdx`. Stop if it already exists.
2. Write frontmatter that matches the `posts` schema in `src/content.config.ts`. Re-read that schema first in case it changed.

   ```yaml
   ---
   title: <Title>
   description: <one sentence; ask if not obvious>
   pubDatetime: <current time, ISO 8601 UTC, from `date -u +%Y-%m-%dT%H:%M:%SZ`>
   tags: [<1-3 lowercase tags; reuse existing tags from other posts when they fit>]
   draft: true
   ---
   ```

3. Body: if the user gave notes or an outline, turn them into a first draft in plain, direct prose. Otherwise add `##` section headings with a one-line TODO under each.
4. Media and interactivity (MDX):
   - Video: `<video controls muted playsinline width="100%" src="/media/<file>.mp4"></video>`, with the file in `public/media/`. For anything bigger than a few MB, suggest YouTube or Vimeo and an `<iframe>` embed instead.
   - Images: put them in `src/assets/images/` and use a relative Markdown image so `astro:assets` optimizes them.
   - Components: import from `@/components/...` at the top of the MDX file.
5. Run `npx astro check` and fix any schema errors.
6. Tell the user the file path and that `draft: true` must be removed before publishing. Pushing to `main` deploys.

Use the `astro-docs` MCP server for Astro API questions instead of guessing.
