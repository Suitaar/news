# Suitaar news

The source of [news.suitaar.com](https://news.suitaar.com): updates, maintenance windows and blog posts from [Suitaar](https://suitaar.com). A static Jekyll site on GitHub Pages. No scripts, no trackers, no cookies.

## Categories and feeds

| Category | Folder of the list page | Feed |
|---|---|---|
| Updates (new features) | `/updates/` | `/feed/updates.xml` |
| Maintenance windows | `/maintenance/` | `/feed/maintenance.xml` |
| Blog | `/blog/` | `/feed/blog.xml` |
| Everything | `/` | `/feed.xml` |

There is no mailing list. People follow a feed.

## Write a post

1. Write it in `_drafts/` first. Drafts are not published.
2. When it is approved, move it to `_posts/YYYY-MM-DD-slug.md`.
3. Front matter: `layout: post`, `title`, `categories: [updates]` (or `maintenance`, `blog`), and a `description` of at most about 155 characters.
4. A maintenance post also has `status` (planned, in progress, done), `start` and `end` in UTC. To update a window, change the same post; set `last_update`. Use `_drafts/maintenance-template.md`.

## Rules

- Say only what is live for every team. Never promise an uptime.
- Plain, simple words. No AI-sounding phrases.
- Pricing: "The free plan stays free after the beta: 3 channels, no payment details." Never "free during the beta", never "free forever". Do not announce paid plans.
- Describe other tools neutrally.
- Never post keys, webhook URLs or alert contents.

## Build locally

```sh
bundle config set --local path vendor/bundle
bundle install
bundle exec jekyll serve --drafts
```
