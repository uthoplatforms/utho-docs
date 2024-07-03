# utho Docs
[🧑🏻‍💻Website 🔗](https://utho.com/docs/)

## Local Development

Pre-requisites: [Hugo](https://gohugo.io/getting-started/installing/), [Go](https://golang.org/doc/install) and [Git](https://git-scm.com)

```shell
# Start server for development
hugo mod tidy
hugo server --logLevel debug --disableFastRender -p 1313
```

## Custom Features

### Section Tabs:
These are sub-folders with there own **_index.md** and `tab: true` in the front-matter.

![Section Tabs](static/section-tabs.png)

- Link to **Section Tabs** automatically appear on Parent Section. There can be as many section tabs as needed.
- **icon** is required for page layout and recommended. icon should be mentioned with filename `icon: icon-name` in the front-matter.
- **icon size** should be standard **128 x 128, 256 x 256 or 512 x 512**
- **icon filename** should be alphabets/numbers or hyphens only, no spaces, no special symbols.
- **Section tab articles** should be created inside respective section tab folder to appear categorically.


### Featured Articles
Files with `featured: true` in the front matter appear categorically and exclusively under sections/sub-sections.

![featured article](static/featured-articles.png)

### Article Cards on Home page
Files with `homecard: true` in the front matter appear categorically and exclusively on home page.

### Featured Articles on Home page
Files with `home: true` in the front matter appear categorically and exclusively on home page.


### Images and Thumbnails
Article list thumbnails are automatically created from the first image of the article A standard aspect ratio of 720p, 1080p should be good enough for almost all display sizes.

Thumbnail specific images should always be in proper aspect ratio to maintain documentation standards and uniform layout.

Example image that also shows up as thumbnail:

![Sample image](static/sample-image.jpg)

It is highly recommended to optimise all images for faster build and smaller payload, use tools like [imageoptim](https://imageoptim.com/mac), [Pinga](https://css-ig.net/pinga), [Trimage](https://trimage.org) or any cli based utility  before uploading.
Standard settings could be `JPEG 80%, PNG 40%, SVG 40%` or as per your organisation standards.

![Imageoptim](static/imageoptim.png)

### Custom Template Layouts

Page layouts can be overridden by placing a custom front-matter `layout: some-layout-name` at `_index.md` of list type page or at `index.md` of single pages.

The layout template goes into respective folder with filename exactly same as above so it will be `some-layout-name.html`

**For Example:**
- Category "Web-servers" uses `/layouts/docs/list.html`.
- Copy `list.html` to  `/layouts/docs/some-customlayout.html`
- Add filename to the front-matter `/content/Web-Servers/_index.md` as below:

```yaml
---
weight: 20
title: "Web-Servers Page"
layout: some-customlayout
---
```
 Now the `some-customlayout.html` is used exclusively on pages where `layout: some-customlayout` is set.

Related Hugo Docs:
[# Template lookup order](https://gohugo.io/templates/lookup-order/) •
[# Layout](https://gohugo.io/methods/page/layout/) •
[# Type](https://gohugo.io/methods/page/type/)

### Things to keep in mind:
Hugo has insane/quirky caching for HTML outputs while building, 
- Refresh browser page manually or navigate away and come back.
- Make sure Hugo has finished working in the terminal or deployed completely.
- Try with restarting Hugo server when things are done correct but don't seem to be working, you would be surprised. 😉
