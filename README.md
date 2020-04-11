# wz2100.net website

- [Description](#description)
- [How to Edit Content](#how-to-edit-content)
- [How to Edit the Theme](#how-to-edit-the-theme)
- [How to Build / Preview Locally](#how-to-build--preview-locally)
- [Automatically Updated Data](#automatically-updated-data)
- [Building for Production](#building-for-production)

## Description
This repo contains the source code used to generate wz2100.net, including themes.

It is powered by [Hugo](https://gohugo.io), has a responsive design, [supports translations](docs/Translations.md#translating-wz2100net), and automatically updates when new releases are made.

## How to Edit Content

Generally, you should familiarize yourself with [the basics of how Hugo works](https://gohugo.io/getting-started/quick-start/#step-4-add-some-content).

- Content files are stored under: `content/en`
- The base language is English, and all new content should be created in English.
   - Translations should _not_ be manually commited to the repo. They should [go through the translation integration service](docs/Translations.md#translating-wz2100net).
- Although Hugo supports multiple formats, please author new content files as Markdown (.md) files. ([Learn Markdown](https://gohugo.io/content-management/formats/#learn-markdown))

> You can manually create content files (for example as `content/<CATEGORY>/<FILE>.<FORMAT>`) and provide metadata in them, however you can use the `new` command to do few things for you (like add title and date):

```sh
hugo new news/news-##.md
```
Will generate a new `news-##.md` file (replace the `##` with the desired number), using the `archetypes/news.md` template.

### Special-Case: FAQ Entries

FAQ entries are only displayed on the homepage, but are pulled from folders in the `content` directory.
Specifically, `content/en/faq/*/`.

Each `faq` subfolder must contain an `index.md` containing the front-matter:
```markdown
---
headless: true # no-translate
---
```
to mark it as a "[Headless Bundle](https://gohugo.io/content-management/page-bundles/#headless-bundle)".

The homepage pulls the list of `faq` subfolders ("categories") that it will display from: `data/homepage_faq_categories.json`.
Each `"category"` string in this `.json` file should exist as a translated string in the `themes/wz2100net/i18n/en.json` strings file.

## How to Edit the Theme

See Hugo's documentation on themes.

Some tips:
- The home page template is located in `themes/wz2100net/layouts/index.html`. This contains the bulk of the home page's body.
- The navbar on all pages is generated by: `themes/wz2100net/layouts/partials/navbar.html`
   - This uses a combination of fixed links, and drop-downs generated by processing [Hugo menus](https://gohugo.io/content-management/menus/) and available translations.
   - The `main` menu is generated from front-matter in the content. See, for example, `webchat.md`.
   - Other menus are declared in the `config.yaml`.
- You'll also find `head.html` and `footer.html` templates in the `partials` folder.

### Translatable Strings

Adding new translatable strings into theme partials / templates is accomplished in two steps:
1. Wrap the string in `{{ i18n "..." }}` inside the partial / template.
2. Add a new entry for the string (if it does not already exist) in `themes/wz2100net/i18n/en.json`

## How to Build / Preview Locally

Hugo makes it very easy to build / preview the full website locally.

1. [Download & install Hugo.](https://gohugo.io/getting-started/quick-start/#step-1-install-hugo)
2. Clone this repo.
3. Inside the repo folder, run `hugo server`.
Example:
```sh
hugo server --minify --i18n-warnings --gc --path-warnings
```
Hugo will output a localhost url you can simply open in your local web browser.

## Automatically Updated Data

Certain [`data`](/data) is updated automatically / on a schedule:
- `github_repo_info.json` (updated on a schedule; see: [`.github/workflows/update_data.yml`](/.github/workflows/update_data.yml))
- `github_releases_info/index.json` (updated on a schedule; see: [`.github/workflows/update_data.yml`](/.github/workflows/update_data.yml))
- `github_releases_info/latest.json` (updated [when there is a Release on the main repo](https://github.com/Warzone2100/warzone2100/blob/master/.github/workflows/release.yml); see: [`.github/workflows/fetch_latest_release_info.yml`](/.github/workflows/fetch_latest_release_info.yml))

## Building for Production

Production builds are handled automatically by the Netlify configuration. For reference:

To properly build for production, the `HUGO_ENV` environment variable must be set to `"production"`.
This configures various important options - such as a `robots.txt` file that actually allows search engines.

Example:
```sh
HUGO_ENV="production" hugo --gc --minify --i18n-warnings
```

For more, see the [`netlify.toml`](/netlify.toml) file.
