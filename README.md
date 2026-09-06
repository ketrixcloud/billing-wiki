<h1 align="center">Ketrix Wiki</h1>

<p align="center">
  The content behind <a href="https://ketrix.cloud/wiki"><strong>ketrix.cloud/wiki</strong></a> — hosting guides, account walkthroughs and reference pages, written in MDX and published straight from this repository.
</p>

<p align="center">
  <img alt="Languages: English, Ukrainian, Russian" src="https://img.shields.io/badge/languages-en%20%C2%B7%20ua%20%C2%B7%20ru-1f1f1f?style=flat-square">
  <img alt="Format: MDX" src="https://img.shields.io/badge/format-MDX-1f1f1f?style=flat-square">
  <img alt="Publishes on push to main" src="https://img.shields.io/badge/publishes-on%20push%20to%20main-1f1f1f?style=flat-square">
</p>

---

## How it reaches the site

There is no build step here and nothing to deploy. The billing frontend reads this
repository **live** over `raw.githubusercontent.com`, so a page is public as soon as it
lands on `main`.

```
push to main  ─▶  raw.githubusercontent.com/ketrixcloud/billing-wiki/main/pages/…  ─▶  ketrix.cloud/wiki/<slug>
```

Responses are cached for a short window (10 s by default), so give it a moment before
assuming an edit did not go through.

## Repository layout

```
pages/
├── en/                          ← English content
│   └── how-to-enable-2fa/
│       └── index.mdx
├── ua/                          ← Ukrainian content
├── ru/                          ← Russian content
│
└── how-to-enable-2fa/           ← images, shared by all three languages
    ├── enable-2fa-button.png
    └── scan-qr-code.png
```

Two rules follow from this shape:

- **One page = one directory.** A page lives at `pages/<language>/<slug>/index.mdx`.
  The directory name *is* the slug, and it must be identical across languages.
- **Images are language-neutral.** Screenshots live in `pages/<slug>/`, outside any
  language folder, so a screenshot is uploaded once and reused by all three
  translations.

## Frontmatter

Every `index.mdx` opens with a YAML block:

```yaml
---
title: How to enable 2FA?
description: Step-by-step guide to enabling two-factor authentication on your Ketrix account.
category: main
disabledin: false
---
```

| Field | Purpose |
| --- | --- |
| `title` | Page heading and `<title>`. The sidebar label comes from the frontend's translations, not from here. |
| `description` | Meta description used for search engines and link previews. |
| `category` | Legacy field, kept on every page as `main`. Real grouping lives in the frontend (see below). |
| `disabledin` | Hides the page in specific languages. `false` publishes everywhere. |

### `disabledin`

Use it when a translation is not ready yet, instead of shipping an empty page:

```yaml
disabledin: false        # visible in every language
disabledin: en           # hidden in English only
disabledin: en, ru       # hidden in English and Russian
disabledin: true         # hidden everywhere
```

The value is read per language, so the same flag is written into all three files of a
page — the reader's language decides whether it applies.

## Writing a page

1. Create the directory in **all three** languages:

   ```
   pages/en/<slug>/index.mdx
   pages/ua/<slug>/index.mdx
   pages/ru/<slug>/index.mdx
   ```

   If a translation is not ready, still create the file and set `disabledin` for that
   language rather than leaving the path missing.

2. Put screenshots in `pages/<slug>/` and reference them **without** the language
   prefix — the path is resolved relative to `pages/`:

   ```markdown
   ![Enable 2FA button in account settings](how-to-enable-2fa/enable-2fa-button.png)
   ```

   Images from another page's folder are fair game when the step is genuinely the same
   screen; several guides reuse `how-to-connect-socials/account-settings-button.png`
   this way instead of duplicating the file.

3. Register the page in the frontend — see the next section — otherwise nobody can
   reach it.

### Supported content

Standard Markdown, plus a few extras the renderer understands:

| Element | Notes |
| --- | --- |
| Headings, lists, tables, code | Standard Markdown. `##` and `###` headings build the on-page table of contents. |
| Images | Resolved against `pages/`. Always write meaningful alt text. |
| Blockquotes | Rendered as callouts. Used for warnings, e.g. the 2FA recovery notice. |
| Links | Plain Markdown links; external ones open in a new tab. |
| YouTube `<iframe>` | Recognised and re-rendered as a responsive embed. |

## Navigation lives in the frontend

> **Adding a file here is not enough to make a page appear.**

The sidebar — its categories, their order and the page titles shown in each language —
is defined in the billing frontend, not in this repository:

| What | Where |
| --- | --- |
| Category and page order | `billing-frontend/src/config/wiki-config.ts` |
| Sidebar titles per language | `billing-frontend/src/i18n/translations/{en,ua,ru}.ts` |

A new page therefore takes two pull requests: the content here, and its registration
in the frontend.

### Current pages

| Category | Slug | Page |
| --- | --- | --- |
| Getting started | `why-our-hosting` | Why our hosting? |
| | `dictionary` | Glossary |
| | `useful-links` | Useful links |
| Payments | `how-to-buy` | How to purchase a service |
| | `how-to-pay-in-another-currency` | How to pay in another currency? |
| | `discounts` | Are there any discounts? When? |
| | `refund-policy` | Can I get a refund for a product? |
| Services | `how-to-change-core` | How to change the base? |
| | `how-to-connect-cloudflare-domain` | How to connect a domain to CloudFlare? |
| Account | `how-to-enable-2fa` | How to enable 2FA? |
| | `how-to-connect-socials` | How to link Discord/Telegram? |
| | `forgot-password` | What to do if I forgot my account password? |
| Support | `ticket-response-time` | How long to wait for a ticket reply? |
| | `join-our-team` | Can I join your team? |

`index` holds the wiki's introduction and is served at the section root rather than
from the sidebar. The **Legal** category points at PDF documents served by the
frontend, so `privacy-policy` and `terms-of-use` have no pages here.

## Editing

Every rendered page carries an **Edit on GitHub** link that opens the exact file for the
language you are reading:

```
https://github.com/ketrixcloud/billing-wiki/edit/main/pages/<language>/<slug>/index.mdx
```

For a small correction that link is the whole workflow. For anything larger — a new
page, restructuring, or a change that spans all three languages — open a pull request so
the translations can be reviewed together.

## Previewing locally

Point the frontend at a checkout of this repository to preview edits before pushing.
`WIKI_LOCAL_ROOT` takes the path to the **`pages` directory**:

```bash
WIKI_LOCAL_ROOT=/path/to/billing-wiki/pages npm run dev
```

MDX is then read from disk. Images still fall back to `raw.githubusercontent.com`, so a
screenshot that has not been pushed yet will not show up in the local preview.

| Variable | Default | Purpose |
| --- | --- | --- |
| `WIKI_LOCAL_ROOT` | — | Read MDX from this `pages` directory instead of GitHub. |
| `WIKI_NO_CACHE` | `0` | Set to `1` to bypass caching entirely. |
| `WIKI_REVALIDATE_SECONDS` | `10` | How long a fetched page is cached. |
| `NEXT_PUBLIC_WIKI_GITHUB_BRANCH` | `main` | Branch the content is read from. |

## House style

- Write for someone who has not used the panel before: name the button, then show it.
- Number the steps a reader has to perform; keep prose out of numbered lists.
- Screenshot exactly the region under discussion, and crop out unrelated chrome.
- Never leave real account data in an image — user IDs, emails and tokens get redacted
  before the file is committed.
- Keep the three languages in step. A change to one page should update its siblings in
  the same pull request.
