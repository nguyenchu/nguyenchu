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

## nginx

`deploy/nginx/nguyenchu.com.conf` is the site config for `nguyenchu.com` and `www.nguyenchu.com`. Besides the portfolio it serves Themepark (`/themepark/`, with save endpoints proxied to port 5130) and the Matkupp data under `/matkupp/`. It also redirects `/index.html` to `/` with a 301.

The portfolio deploy workflow does not install this file. The file was rebuilt from the server inventory of 2026-11-09, not copied from the server, so compare it with the live config before the first install:

```bash
scp nguyenchu.com:/etc/nginx/sites-available/nguyenchu.com /tmp/nguyenchu.com.live
diff /tmp/nguyenchu.com.live deploy/nginx/nguyenchu.com.conf
```

Keep anything the live file has that this one lacks (headers, cache rules, extra locations), then remove the "RECONSTRUCTED" note. To install:

```bash
scp deploy/nginx/nguyenchu.com.conf nguyenchu.com:/tmp/nguyenchu.com.conf
ssh nguyenchu.com
sudo cp /etc/nginx/sites-available/nguyenchu.com ~/nguyenchu.com.nginx.bak
sudo cp /tmp/nguyenchu.com.conf /etc/nginx/sites-available/nguyenchu.com
sudo nginx -t && sudo systemctl reload nginx
```

Check that `curl -sI https://nguyenchu.com/index.html` returns `301` to `/`, and that `/`, `/themepark/` and `/matkupp/products.json` still return `200`. To roll back, copy `~/nguyenchu.com.nginx.bak` back and reload.
