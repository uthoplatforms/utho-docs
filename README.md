# Hugo on utho Docs

[🌐 Demo ↗](https://uthoplatforms.github.io/utho-docs/)

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

##

