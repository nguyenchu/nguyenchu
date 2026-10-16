# Personal portfolio

`site/` contains the buildless Norwegian portfolio for https://nguyenchu.com/.
Serve it locally with `python3 -m http.server 4173 --directory site`.

The homepage distinguishes production experience from personal projects. Case studies link to implementation details and describe tradeoffs without adding unverified employers, dates, or performance claims. The contact address matches the public GitHub profile.

## Publish

The existing production connection is held in the `production` environment of `nguyenchu/kjorebok`. Its manual **Deploy Production** workflow supports:

- `portfolio-inspect`: read the website's configured document root.
- `portfolio`: check out this repository at `portfolio_ref`, upload `site/`, back up the files being replaced, and publish the portfolio.

Use a full 40-character commit SHA for a reproducible release. Select the workflow ref `main`. Do not run a portfolio deployment alongside an app deployment; they share the existing production concurrency group.

Publication verifies the nginx host and `/var/www/html` document root. It updates only the homepage, stylesheet, favicon, and the two named project pages. Existing app routes and files remain in place. Previous files are kept on the server in `~/portfolio-backups/<workflow-run-id>/`.

The Kjørebok demo is maintained separately in `nguyenchu/kjorebok` under `apps/web/src/app/demo/`. It uses invented data held in memory, not a shared demo account or the production trip API.
