# Popular Mail — Nigerian Online Newspaper

News, Facts and Perspectives. A fast static newspaper site built with **Eleventy**, deployed on **Netlify**, edited through a no-code newsroom dashboard (**Decap CMS**).

## Why not WordPress?

WordPress needs a PHP server and a MySQL database — Netlify only hosts static files, so it can't run WordPress directly. This build gets you the same outcome — journalists publishing without touching code — using tools built for static hosting:

- **Decap CMS** (`/admin`) gives journalists a form-based dashboard: log in, fill in a headline/photo/body, click Publish.
- **Eleventy** turns what they publish into real HTML pages automatically on every build. This is the piece that was missing before — now the homepage, category pages, and article pages regenerate themselves whenever new content is published.

If you specifically need the real WordPress dashboard (e.g. handing off to a team that only knows WP), that needs PHP hosting instead of Netlify — a different, heavier stack. Happy to scope that separately if it turns out to be a hard requirement.

## How publishing actually works now

1. A journalist logs into `/admin` (see setup below).
2. They fill out the Article form (headline, category, author, image, body, etc.) and click **Publish**.
3. Decap CMS commits a new markdown file into `src/content/articles/`.
4. That commit triggers a new Netlify build.
5. Eleventy reads every file in `src/content/articles/`, regenerates the homepage, the relevant category page, the author's profile page, the sitemap, the RSS feed, and the search index — then Netlify deploys the result.
6. A minute or two later, the new article is live on the homepage, its category page, and the author's page, with no one touching code.

## One-time setup on Netlify

**1. Connect a Git repo (required for `/admin` to work)**
Decap CMS commits content changes to a Git repository — it can't do that against a Netlify Drop deploy. Push this project to GitHub (or GitLab/Bitbucket), then in Netlify: **Add new site → Import an existing project** and connect that repo.
Build command: `npm run build` · Publish directory: `_site` (already set in `netlify.toml`).

**2. Enable Identity**
Site configuration → **Identity** → **Enable Identity**.

**3. Enable Git Gateway**
Still under Identity → **Services** → **Enable Git Gateway**. This is what lets logged-in journalists save content without needing their own GitHub account.

**4. Set registration to invite-only**
Identity → **Registration** → **Invite only**, then save. (Otherwise anyone could sign up and edit the site.)

**5. Invite your journalists**
Identity → **Invite users** → enter their email. They'll get an email to set a password.

**6. Make the site public**
If the project is still marked Private in Netlify, click **Make public** so readers (not just you) can see it.

## Logging in and posting

1. Go to `yoursite.netlify.app/admin` (or `yourdomain.com/admin` once your custom domain is connected).
2. Log in with the email + password from the invite email.
3. Pick a content type from the sidebar:
   - **Articles** — the news stories themselves
   - **Journalists** — author profiles (photo, bio, social links)
   - **Breaking News Ticker** — the scrolling headlines strip
   - **Site Settings** — navigation menu, footer/social links, ad slot codes, newsletter text
4. For a new article: click **New Article**, fill in the fields, click **Publish now** (or save as a draft first if editorial workflow is on).
5. Wait roughly 1–3 minutes for Netlify to rebuild, then refresh the site — the article appears on the homepage and its category page automatically.

## Project structure

- `src/index.njk` — homepage, pulls real articles from the content collection
- `src/_includes/layouts/article.njk` — article template (byline, related stories, NewsArticle schema)
- `src/category.njk` — generates one page per category automatically from `src/_data/categories.yml`
- `src/_includes/layouts/author.njk` — journalist profile template
- `src/search.njk` + `src/search-index.njk` — live client-side search over a build-time index
- `src/sitemap.njk`, `src/feed.njk` — sitemap and RSS feed, regenerated on every build
- `src/content/articles/`, `src/content/authors/` — the actual content files Decap CMS writes to
- `src/_data/` — site-wide settings (navigation, footer, ads, newsletter, breaking news, categories)
- `admin/config.yml`, `src/admin/index.html` — the Decap CMS dashboard and its content schema

## A note on this build

This was assembled and reviewed carefully, but the sandbox this was built in has no network access, so the Eleventy build itself could not be run and confirmed end-to-end before delivery. Netlify's build (which does have full npm access) is the first real test. If the deploy log shows an error, paste it back and it can be fixed quickly — Eleventy/Nunjucks errors are usually a one-line fix.

## Replacing placeholder content

- Swap the Unsplash placeholder photos for licensed/owned images before going live.
- Replace `.ad-slot` placeholders with real AdSense or direct-sales ad tags (editable from Site Settings → Advertisement Slots in `/admin`).
- Update social links, WhatsApp number, and legal pages (About, Privacy Policy, Terms, Editorial Policy) with real copy.
