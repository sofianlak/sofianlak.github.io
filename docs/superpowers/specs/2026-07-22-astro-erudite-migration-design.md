# Astro Erudite Migration Design

## Objective

Replace the current Jekyll/Chirpy site with Astro Erudite v2 while preserving the site's published content, identity, custom domain, and existing post URLs.

The migration must keep every current article available at its existing canonical `/posts/<slug>/` URL because those URLs are referenced by external sites. The result remains a statically generated site deployed to GitHub Pages.

## Scope

The migrated site includes:

- Astro Erudite's editorial homepage, rewritten for Sofian Lakhdar.
- A post index at `/posts/`.
- The five existing posts at their current `/posts/<slug>/` routes.
- Project, about, tags, author, RSS, sitemap, robots, and 404 pages.
- Giscus comments using the existing repository, category, and `pathname` mapping.
- Light and dark themes, responsive layout, syntax highlighting, table of contents, heading anchors, SEO metadata, favicons, and the custom domain.
- Per-post language and explicit last-modified metadata.
- Existing public images and social/contact links.

The migration intentionally removes:

- Category pages and category metadata.
- The archives page.
- Google Analytics.
- Chirpy's search UI and post pagination.
- Share buttons.
- The Jekyll service worker and offline cache. The web manifest and favicons remain.

## Migration Strategy

Use Astro Erudite v2 as the new application foundation rather than trying to incrementally convert Chirpy layouts. Remove the starter's demo content and branding, then migrate only the site's content and required custom behavior.

This keeps the upstream component and CSS structure recognizable while avoiding a permanent dual Jekyll/Astro setup. The repository will own the copied starter code, so future upstream updates will be reviewed and applied manually rather than received through a theme package.

## Routes and URL Compatibility

The canonical post route is `/posts/<slug>/`, not Erudite's default `/blog/<slug>`.

The Erudite post page, cards, navigation, adjacent-post links, tag pages, RSS feed, sitemap filters, canonical metadata, and series reader logic will all use `/posts`. Astro will generate directory-style output so GitHub Pages serves the existing trailing-slash URLs.

No redirect is required for a currently published post. Optional `/blog/...` compatibility redirects are excluded because no production URLs use that prefix and GitHub Pages would only serve client-side HTML redirects.

Required public routes are:

- `/`
- `/posts/`
- `/posts/la-pair-de-rayons-au-pays-du-soleil-levant/`
- `/posts/text2vector-converter-using-azure-terraform-and-github-actions/`
- `/posts/how-to-replace-flannel-by-cilium-for-kubernetes-on-talos/`
- `/posts/cloudflare-tunnel-vs-tailscale-secure-front-door/`
- `/posts/la-chamade/`
- `/projects/`
- `/about/`
- `/authors/` and `/authors/sofianlak/`
- `/tags/` and one page per tag
- `/rss.xml`
- `/robots.txt`
- `/404.html`

## Site Structure and Navigation

The main navigation contains `Posts`, `Projects`, and `About`. Tags remain discoverable from post metadata and the tags page but do not occupy a primary navigation slot. The authors collection remains because Erudite requires typed author references, but the single-author page does not need a primary navigation item.

The homepage keeps Erudite's editorial dictionary-entry layout and visual language. Its content changes from template documentation to a concise introduction to Sofian Lakhdar, DevOps, Platform Engineering, open-source projects, and personal writing. It links readers to posts and projects without duplicating either listing.

The footer exposes GitHub, LinkedIn, YouTube, status, email, and RSS links. Dedicated local SVG icons are added for the missing services without introducing an icon dependency.

## Content Model

### Posts

Each Jekyll post moves into `src/content/posts/<slug>/index.md`. Its banner is colocated under that post's `assets/` directory for Astro image processing. Inline article images retain their public `/assets/img/...` paths.

The post schema contains:

- `title: string`
- `description: string`
- `date: Date`
- `updatedAt?: Date`
- `lang?: string`, defaulting to the site locale
- `authors: AuthorReference[]`
- `tags?: string[]`
- `image?: ImageMetadata`
- `draft?: boolean`
- `order?: number` for Erudite's series ordering

The old `categories` property is discarded. Existing `tags` remain unchanged rather than implicitly converting categories into tags.

The current Git-derived modification dates are written explicitly as `updatedAt` values. This makes builds reproducible even when GitHub Actions performs a shallow checkout.

### Author

`src/content/authors/sofianlak.md` contains the current author name, avatar, biography, email, website, and social links. Every post references `sofianlak` through the typed content relationship.

### Projects

Each entry in `_data/projects.yml` becomes a file in `src/content/projects/`. The schema supports a primary link plus an optional list of additional links so the migration does not silently discard project destinations. The old Font Awesome icon field is discarded because Erudite's project cards do not use project icons.

### About

The existing about copy is rendered through a dedicated `/about/` Astro page. Jekyll-only image positioning and inline styles are replaced with scoped Astro CSS.

## Markdown Conversion

The content conversion removes all Kramdown inline attribute lists.

- External-link `target` annotations are removed because Erudite adds safe external-link attributes during Markdown processing.
- Chirpy prompt blocks become Erudite callout directives.
- Code-block file annotations become Expressive Code titles.
- Image dimensions and border-radius annotations become native image markup and shared prose styles.
- Footnote class annotations are removed while standard footnote syntax remains.
- Necessary raw HTML remains supported, but redundant `<br>` tags and inline presentational styles are simplified where they affect rendering.

All migrated Markdown must build without exposing `{:` annotations as visible text.

## Images

Post banners use Astro-managed copies so Erudite can generate optimized output. Existing banner images retain their natural aspect ratio; components must not stretch them to 1200×630. Open Graph metadata uses the same images even when their dimensions differ from Erudite's recommendation.

The complete current `assets/img` tree moves unchanged to `public/assets/img`. This preserves existing direct image URLs and retains unused or duplicate assets rather than risking an externally referenced URL. Inline article images keep their paths, meaningful alt text, and captions.

## Comments

A focused `Giscus.astro` component is rendered after the post body. It uses the current public configuration:

- Repository: `sofianlak/sofianlak.github.io`
- Repository ID: `R_kgDOLsWVGA`
- Category: `General`
- Category ID: `DIC_kwDOLsWVGM4CzeUO`
- Mapping: `pathname`
- Input position: `bottom`
- Reactions enabled
- Lazy loading

The component synchronizes Giscus with Erudite's light/dark theme. Its interface language is `fr` for French posts and `en` otherwise. Preserving `/posts/.../` means existing pathname-based discussions remain associated with their articles.

## SEO and Language

Global site metadata uses `https://sofianlak.fr`, the current description, and the existing default social image. Post metadata includes canonical URL, publication date, optional modification date, author, Open Graph data, and Twitter card data.

The page layout accepts a locale. English is the site default, while French posts pass `fr-FR` to the document language and Open Graph locale. RSS and sitemap links use the custom production domain.

Tag and author detail pages remain `noindex`, following Erudite's current behavior. The sitemap contains canonical posts and primary pages, not duplicate author/tag detail routes.

## Build and Deployment

The Ruby/Jekyll runtime, plugins, theme configuration, and development scripts are removed after Astro equivalents are in place.

The project uses the package manager and lockfile supplied by Astro Erudite. `astro.config.ts` sets:

- `site: "https://sofianlak.fr"`
- static output
- directory-style build output and trailing-slash-compatible routes
- the post-based sitemap filter
- Erudite's Markdown processing plugins

GitHub Actions uses the official Astro deployment action. `CNAME` moves to `public/CNAME`, and the workflow keeps the existing Pages permissions and deployment concurrency controls.

## Validation

Automated checks cover observable migration behavior rather than implementation details:

- All five legacy post URLs are generated under `dist/posts/<slug>/index.html`.
- Each generated post has the expected canonical `/posts/.../` URL.
- Post listings and RSS point to `/posts`, never `/blog`.
- Giscus is present on posts and retains pathname mapping.
- All five posts, their titles, tags, authors, languages, and modification dates pass content-schema validation.
- No migrated Markdown contains unsupported Kramdown attribute lists.
- Primary routes, sitemap, RSS, robots, CNAME, favicons, and the 404 page are generated.
- Formatting and production builds pass.

After automated checks, the site is inspected in a local browser at desktop and mobile widths. The review covers the homepage, post index, a long technical article, a French article, projects, about, theme switching, navigation, table of contents, code blocks, images, and Giscus placement.

## Acceptance Criteria

The migration is complete when:

1. The Astro production build succeeds from a clean dependency installation.
2. Every existing `/posts/<slug>/` URL renders its original article without a redirect.
3. No required content or referenced image is missing.
4. The editorial homepage, posts, projects, about page, tags, author pages, RSS, sitemap, robots, and 404 page render correctly.
5. Giscus loads with the current repository and pathname configuration.
6. English and French post metadata use the correct locale.
7. Jekyll-only markup is absent from generated pages.
8. GitHub Pages deployment is configured for `sofianlak.fr`.
9. Automated checks and browser-based visual verification pass.
