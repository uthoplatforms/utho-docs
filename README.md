# utho Docs
[🧑🏻‍💻Website 🔗](https://utho.com/docs/)

## Local Development

Pre-requisites: [Hugo](https://gohugo.io/getting-started/installing/), [Go](https://golang.org/doc/install) and [Git](https://git-scm.com)

```shell
# Start server for development
hugo mod tidy
hugo server --logLevel debug --disableFastRender -p 1313

# Start server for production
hugo mod tidy
hugo server -p 1313
```

## Site Config

•  `config > _default > hugo.yaml` for development settings
•  `config > production > hugo.yaml` for production settings
Google analytics, disqus etc. go into production, do not have them in _default.
Refer to official [Hugo docs](https://gohugo.io/documentation/) for better undertanding.

Any theme specific settings can be found here: [Hextra Docs](https://imfing.github.io/hextra/docs/)
(Not all settings are implemented and hence should be checked locally for function. Example: gisqus has been ommited and instead diqus has been implemented )

### Required settings for theme functions to work.
<pre>
module:
  imports:
  - path: hextra

markup:
&emsp; goldmark:
&emsp;&emsp;  renderer:
&emsp; &emsp; &emsp; unsafe: true
&emsp; highlight:
&emsp; &emsp; &emsp; noClasses: false
</pre>

**Theme supports built-in inline shortcodes**
<pre>
params:
  enableInlineShortcodes: true
</pre>
*Reference: https://imfing.github.io/hextra/docs/guide/shortcodes/*

## Custom Theme Features

### Section Tabs:
These are sub-folders with there own **_index.md** and `tab: true` in the front-matter.

![img (Section Tabs)](https://utho.com/docs/images/section-tabs.png)

- Link to **Section Tabs** automatically appear on Parent Section. There can be as many section tabs as needed.
- **icon** is required for page layout and recommended. icon should be mentioned with filename `icon: icon-name` in the front-matter.
- **icon size** should be standard **128 x 128, 256 x 256 or 512 x 512**
- **icon filename** should be alphabets/numbers or hyphens only, no spaces, no special symbols.
- **Section tab articles** should be created inside respective section tab folder to appear categorically.

### Featured Articles
Files with `featured: true` in the front matter appear categorically and exclusively under sections/sub-sections.
![featured article](https://utho.com/docs/images/featured-articles.png)

### Images and Thumbnails
Article list thumbnails are automatically created from the first image of the article A standard aspect ratio of 720p, 1080p should be good enough for almost all display sizes.

Thumbnail specific images should always be in proper aspect ratio to maintain documentation standards and uniform layout.

Example image that also shows up as thumbnail:

![Sample image](https://utho.com/docs/images/sample-image.jpg)

It is highly recommended to optimise all images for faster build and smaller payload, use tools like [imageoptim](https://imageoptim.com/mac), [Pinga](https://css-ig.net/pinga), [Trimage](https://trimage.org) or any cli based utility  before uploading.
Standard settings could be `JPEG 80%, PNG 40%, SVG 40%` or as per your organisation standards.