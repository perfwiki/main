# [perf.wiki.kernel.org](https://perf.wiki.kernel.org)

This repository contains the markdown migration of the perf.wiki.kernel.org pages.

We can browse perf wiki on terminal environment with [glow](https://github.com/charmbracelet/glow?tab=readme-ov-file#installation).

![glow-with-perfwiki](docs/img/readme/glow-with-perfwiki.gif)

# Check the [perf.wiki.kernel.org](https://perf.wiki.kernel.org) in the localhost

If you want to check the MkDocs UI as a webpage in this workspace,
You only need to do it once at the beginning: `pip install mkdocs-material mkdocs-git-revision-date-localized-plugin`.

Do `mkdocs serve`, Then go to http://127.0.0.1:8000/
```sh
$ virtualenv .venv
$ source .venv/bin/activate
# You only need to do it once at the beginning:
#   Debian: sudo apt-get install -y python3-virtualenv
#   Redhat: sudo dnf install -y python3-virtualenv
#   pip install mkdocs-material mkdocs-git-revision-date-localized-plugin
$ mkdocs serve
...
 Watching paths for changes: 'docs', 'mkdocs.yml'
 Serving on http://127.0.0.1:8000/
```

# Automated deployment from markdown

If the markdown results are pushed to `origin/main`, [Github CI](.github/workflow/ci.yml) will automatically run and deploy to the `gh-deploy` branch.
If manual updates to the [perf.wiki.kernel.org](https://perf.wiki.kernel.org) are needed, you can run `mkdocs gh-deploy` directly to deploy it.
If anyone would like to contribute content, feel free to submit a Pull Request on the [github.com/perfwiki/main](https://github.com/perfwiki/main) repo, or you can email me by including me in the mailing list.
```sh
$ git add .
$ git commit
$ git push
```

or You can also send me a patch file if you prefer. If you want to assign
other people to the email, you can add them.
```
# Don’t forget to change the address below to my "@kzalloc.com" :)
$ git send-email --to='Yunseong Kim <ysk at kzalloc dot com>; ' \
    --cc='linux-perf-users@vger.kernel.org; ' \
    --confirm=always your/path/0001-your-patch
```

# Adding Tab

You can add the links in the left-hand tab by modifying the [mkdocs.yml](mkdocs.yml) file.
```yml
nav:
  - Main Page: index.md
  - Tutorial : tutorial.md
  ...
  - Your New Tab: your-new-page.md
  ...
```
