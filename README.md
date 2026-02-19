# Backblaze B2 Documentation

This repository contains the source for the Backblaze B2 API documentation site, powered by [Fern](https://buildwithfern.com).

The docs site includes guides and API references for three APIs:

- **B2 Native API** — Core Backblaze B2 Cloud Storage API
- **S3-Compatible API** — AWS S3-compatible interface
- **Partner API** — Partner account and group management

## Prerequisites

- [Node.js](https://nodejs.org/) v18 or later
- npm (comes with Node.js)

## Installation

Install the Fern CLI globally:

```bash
npm install -g fern-api
```

Verify the installation:

```bash
fern --version
```

## Project Structure

```
fern/
├── fern.config.json          # Fern project configuration
├── docs.yml                  # Documentation site layout, branding, and navigation
├── assets/                   # Logo, favicon, and other static assets
├── pages/                    # MDX guide pages
│   ├── welcome.mdx
│   └── guides/
│       ├── quickstart.mdx
│       ├── authentication.mdx
│       ├── error-handling.mdx
│       ├── native-api.mdx
│       ├── s3-compatible-api.mdx
│       └── partner-api.mdx
└── apis/                     # OpenAPI specs for each API
    ├── b2-native-api/
    │   └── openapi/openapi.yaml
    ├── s3-compatible-api/
    │   └── openapi/openapi.yaml
    └── partner-api/
        └── openapi/openapi.yaml
```

### Key Files

| File | Purpose |
|------|---------|
| `fern/fern.config.json` | Organization name and Fern CLI version |
| `fern/docs.yml` | Site URL, branding, tabs, navigation structure |
| `fern/apis/*/openapi/openapi.yaml` | OpenAPI specs that generate the API reference |
| `fern/pages/**/*.mdx` | Written guides and documentation pages |

## Development

### Validate Configuration

Check that all configuration files and OpenAPI specs are valid:

```bash
fern check
```

### Preview Locally

Start a local dev server with hot-reloading:

```bash
fern docs dev
```

This opens your docs at `http://localhost:3000`. Edits to MDX pages, `docs.yml`, and OpenAPI specs are reflected automatically.

To use a different port:

```bash
fern docs dev --port 3001
```

### Generate a Shareable Preview

Create a temporary preview URL you can share with your team before publishing:

```bash
fern generate --docs --preview
```

This outputs a unique URL that anyone can view without needing the repo.

## Publishing

When you're ready to deploy to production:

```bash
fern generate --docs
```

This publishes the documentation site to the URL configured in `fern/docs.yml` (currently `backblaze.docs.buildwithfern.com`).

## Making Changes

### Editing Guides

Guide pages live in `fern/pages/` as `.mdx` files. MDX supports standard Markdown plus JSX components. Edit these files directly and preview with `fern docs dev`.

### Updating API References

API references are generated from the OpenAPI specs in `fern/apis/*/openapi/openapi.yaml`. To update the API reference:

1. Edit the relevant OpenAPI spec
2. Run `fern check` to validate
3. Preview with `fern docs dev`

### Changing Navigation and Layout

The site structure — tabs, sections, page order, branding, and colors — is all controlled by `fern/docs.yml`.

## Common Commands

| Command | Description |
|---------|-------------|
| `fern check` | Validate configuration and OpenAPI specs |
| `fern docs dev` | Preview docs locally with hot-reload |
| `fern generate --docs --preview` | Generate a shareable preview link |
| `fern generate --docs` | Publish docs to production |

## Resources

- [Fern Documentation](https://buildwithfern.com/learn)
- [Fern CLI Reference](https://buildwithfern.com/learn/cli-api-reference/cli-reference/commands)
- [Writing Content in Fern](https://buildwithfern.com/learn/docs/writing-content)
# backblaze
# backblaze
