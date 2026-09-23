# bhardwaj-vedant.github.io

Personal academic website of Vedant Bhardwaj, built with the
[Academic Pages](https://github.com/academicpages/academicpages.github.io) Jekyll template
and served at <https://bhardwaj-vedant.github.io>.

## Where things live

| Content | File |
|---|---|
| Home / about | `_pages/about.md` |
| Research and projects | `_portfolio/*.md` (`category: research` or `project`, sorted by `order`) |
| Experience | `_pages/experience.md` |
| Hobbies | `_pages/hobbies.md` |
| CV (currently empty) | `_pages/cv.md` — drop a PDF into `files/` and link it there |
| Sidebar, site settings | `_config.yml` |
| Top navigation | `_data/navigation.yml` |

## Running locally

```bash
bundle install
bundle exec jekyll serve -l -H localhost
```
