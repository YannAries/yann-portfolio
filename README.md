# Portfolio

Personal portfolio built with Hugo.

## Development
```bash
# Dev server
docker run --rm -p 1313:1313 -v $(pwd):/src hugomods/hugo:base-non-root hugo server --bind 0.0.0.0

# With drafts
docker run --rm -p 1313:1313 -v $(pwd):/src hugomods/hugo:base-non-root hugo server --bind 0.0.0.0 -D
```

## Build
```bash
# Production
docker run --rm -v $(pwd):/src hugomods/hugo:base-non-root hugo --minify

# Clean build artifacts
rm -rf public/ resources/
```

## Utilities
```bash
# Check Hugo version
docker run --rm hugomods/hugo:base-non-root hugo version

# Shell access
docker run --rm -it -v $(pwd):/src hugomods/hugo:base-non-root sh
```