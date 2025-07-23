+++ 
title = "Adding a Search Bar to the Blog With Pagefind.app"
description = "How to integrate pagefind into your Hugo site hosted on Github for offline blog search capabilities"
date = 2025-07-23T17:36:07-05:00
series = []
categories = ["blog"]
tags = []
+++
# Adding a Search Bar to Tobybase with Pagefind

After letting Tobybase sit idle for a while, I'm kicking things back into gear—and what better way to start than by implementing one of the most requested features: **search**.

I decided to use [Pagefind](https://pagefind.app/), a modern, privacy-friendly search solution built for static sites. It's easy to set up and works well with Hugo, the static site generator that powers this blog. However, integrating it into a GitHub Pages deployment workflow took a little trial and error. Here's how I got it working.

## Where the Search Bar Goes

The first step was to decide where to place the search bar. For Tobybase, I added it just above the page title on list pages—specifically, the `/posts/` and `/links/` sections.

Here's how it looks in context:

![Search Content on Tobybase](https://res.cloudinary.com/tobyblog/image/upload/v1753310525/Screenshot_2025-07-23_at_5.11.51_PM_yjdoni.jpg)

To insert the search bar, I had to override the following layout templates:

```
/layouts/posts/list.html
/layouts/links/list.html
```

I placed the snippet provided in Pagefind’s “Getting Started” docs just above the header but within the `container list` section. Here's what the modified layout looks like:

```go-html-template
{{ define "title" }}
  {{ title (i18n (lower .Title)) | default .Title }} · {{ .Site.Title }}
{{ end }}

{{ define "content" }}
  <section class="container list">

    <!-- search bar -->
    <link href="/pagefind/pagefind-ui.css" rel="stylesheet">
    <script src="/pagefind/pagefind-ui.js"></script>
    <div id="search"></div>
    <script>
      window.addEventListener('DOMContentLoaded', (event) => {
        new PagefindUI({ element: "#search", showSubResults: true });
      });
    </script>
    <!-- search bar -->

    <header>
      <h1 class="title">
        <a class="title-link" href="{{ .Permalink | safeURL }}">
          {{ title (i18n (lower .Title)) | default .Title }}
        </a>
      </h1>
    </header>

    {{ .Content }}
    <ul>
      {{- range .Paginator.Pages -}}
        {{- .Render "li" -}}
      {{- end -}}
    </ul>

    {{ partial "pagination.html" . }}
  </section>
{{ end }}
```

With the search bar in place, I turned my attention to the deployment side of things.

## Running Pagefind in GitHub Actions

The Pagefind docs mention that it should be run *after* the site is built, but they don't provide much guidance on how to do this in an automated CI/CD setup. Since Tobybase is hosted on GitHub Pages and deployed via GitHub Actions, I needed to modify the existing workflow to include a step for indexing the site.

Here's the workflow file I updated:

```
.github/workflows/hugo.yml
```

I added a new step under the `build` job, right after the `Build with Hugo` step. This runs the Pagefind CLI to index the generated static site:

```yaml
- name: Index site with pagefind
  run: npx -y pagefind --site public
```

The complete `build` job now looks like this (excerpt):

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.147.7
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb           && sudo dpkg -i ${{ runner.temp }}/hugo.deb
      - name: Install Dart Sass
        run: sudo snap install dart-sass
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5
      - name: Install Node.js dependencies
        run: "[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true"
      - name: Build with Hugo
        env:
          HUGO_CACHEDIR: ${{ runner.temp }}/hugo_cache
          HUGO_ENVIRONMENT: production
        run: |
          hugo             --minify             --baseURL "${{ steps.pages.outputs.base_url }}/"
      - name: Index site with pagefind
        run: npx -y pagefind --site public
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public
```

## That’s It!

Once this step was in place, I committed the changes and pushed them to the `master` branch. The next time the GitHub Action ran, Pagefind created the `pagefind/` folder inside the `public/` directory, and everything worked as expected. Now Tobybase has a working search bar that’s fast, privacy-respecting, and easy to maintain.

If you're building a Hugo site and deploying it via GitHub Actions, I hope this helps fill in the blanks left by the official Pagefind docs.

Stay tuned—more updates and technical deep dives are coming soon.

— Toby
