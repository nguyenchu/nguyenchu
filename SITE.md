# Personal portfolio

`site/` contains the buildless Norwegian portfolio for https://nguyenchu.com/.
Serve it locally with `python3 -m http.server 4173 --directory site`.

The homepage distinguishes production experience from personal projects. Case studies link to implementation details and describe tradeoffs without adding unverified employers, dates, or performance claims. The contact address kontakt@nguyenchu.com is forwarded to a personal inbox by Cloudflare Email Routing.

## Publish

The existing production connection is held in the `production` environment of `nguyenchu/kjorebok`. Its manual **Deploy Production** workflow supports:

- `portfolio-inspect`: read the website's configured document root.
- `portfolio`: check out this repository at `portfolio_ref`, upload `site/`, back up the files being replaced, and publish the portfolio.

Use a full 40-character commit SHA for a reproducible release. Select the workflow ref `main`. Do not run a portfolio deployment alongside an app deployment; they share the existing production concurrency group.

Publication verifies the nginx host and `/var/www/html` document root. It updates only the homepage, stylesheet, favicon, and the two named project pages. Existing app routes and files remain in place. Previous files are kept on the server in `~/portfolio-backups/<workflow-run-id>/`.

The Kjørebok demo is maintained separately in `nguyenchu/kjorebok` under `apps/web/src/app/demo/`. It uses invented data held in memory, not a shared demo account or the production trip API.

## nginx

`deploy/nginx/nguyenchu.com.conf` is the site config for `nguyenchu.com` and `www.nguyenchu.com`. Besides the portfolio it serves Themepark (`/themepark/`, with save endpoints proxied to port 5130) and the Matkupp data under `/matkupp/`. It also redirects `/index.html` to `/` with a 301.

The file matches `/etc/nginx/sites-available/nguyenchu.com` on the server. The portfolio deploy workflow does not install it, so after changing it, install it by hand:

```bash
scp deploy/nginx/nguyenchu.com.conf nguyenchu.com:/tmp/nguyenchu.com.conf
ssh nguyenchu.com
sudo cp /etc/nginx/sites-available/nguyenchu.com ~/nguyenchu.com.nginx.bak
sudo cp /tmp/nguyenchu.com.conf /etc/nginx/sites-available/nguyenchu.com
sudo nginx -t && sudo systemctl reload nginx
```

Check that `curl -sI https://nguyenchu.com/index.html` returns `301` to `/`, and that `/`, `/themepark/` and `/matkupp/products.json` still return `200`. To roll back, copy `~/nguyenchu.com.nginx.bak` back and reload.
