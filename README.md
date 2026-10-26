# Deploy to Pagehaus

Upload a plain HTML website from your GitHub repository to [Pagehaus](https://pagehaus.app) on every push. Before
uploading, it checks the site for broken links, links whose capital letters don't match the file, links to files on
your computer and failed Server Side Includes, and stops if it finds any.

```yaml
name: Deploy
on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pagehaus/deploy-action@v1
        with:
          token: ${{ secrets.PAGEHAUS_TOKEN }}
          site: bakery
```

## Setting it up

1. Make an API token at [pagehaus.app/account/api](https://pagehaus.app/account/api). For **Access**, choose
   **yoursite: read and write**.
2. In your repository: **Settings → Secrets and variables → Actions → New repository secret**, named
   `PAGEHAUS_TOKEN`, with the token as its value.
3. Add the workflow above as `.github/workflows/deploy.yml`, with your site's name.

## Inputs

| Input | | |
|---|---|---|
| `token` | required | A Pagehaus API token with write access to the site. Keep it in a secret. |
| `site` | | The site's name (`bakery` for bakery.pagehaus.site). Can be left out if the token is for one site. |
| `folder` | `.` | The folder to upload, if your site isn't the whole repository: `public`, `site`... |
| `dry-run` | `false` | `true` lists what would change, without changing anything. |
| `force` | `false` | `true` uploads even when the check finds problems. |
| `version` | `latest` | The [pagehaus tool](https://github.com/pagehaus/cli) version to use, such as `0.1.1`. |

The output `url` is the site's address after a deploy that changed something.

## What it does

It downloads the [pagehaus command-line tool](https://pagehaus.app/cli) for the runner (Linux, macOS or Windows) and
runs `pagehaus deploy`: the folder is checked, zipped without hidden files (`.git`, `.github`...) and uploaded. New and
changed files are written; files you deleted from the repository stay on the site, so delete those in the
dashboard. Every deploy is saved in your site's version history first, so a bad one can be undone.

To try it on a pull request without changing your site, use `dry-run: true`.

MIT licence. Made by [Pagehaus](https://pagehaus.app), plain HTML hosting.
