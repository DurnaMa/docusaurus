# Docusaurus Blog

The Docusaurus Blog is sort of like a personal journal where I describe my projects and how I go about them.

import GithubLinkAdmonition from '@site/src/components/GithubLinkAdmonition';

<GithubLinkAdmonition
    link="https://github.com/DurnaMa/docusaurus"
    title="Github Tip"
    type="tip"
>
Checkout this repository to see the code/implementation
</GithubLinkAdmonition>

## Quickstart

### Prerequisites

- [Node.js](https://nodejs.org) (v16 or later recommended)
- [pnpm](https://pnpm.io/) (package manager for faster and more efficient dependency handling)

1. Installation
    ```
    $ pnpm install
    ```

2. Local Development

   ```
   $ pnpm start
   ```

   This command starts a local development server and opens up a browser window. Most changes are reflected live without having to restart the server.

3. Build

    ```
    $ pnpm build
    ```
    This command generates static content into the `build` directory and can be served using any static contents hosting service.

## Description

I customized the docusaurus.config.ts file to suit my needs.

### Configuration

1. docusaurus.config.ts

    I adjusted the site metadata in `docusaurus.config.ts` — title, tagline, and URL — so the site reflects my own project instead of the template defaults.

2. Navbar and Footer

    In the navigation bar, I added links to my GitHub repository in the “Title” and “Items” sections, and in the footer, I removed the ‘Community’ section. I also customized the “More” section to match my GitHub and template page.

### Problems & Solutions

1. After removing the Community column from the footer, the array index was off — it still pointed to `links[2]` instead of `links[1]`, which broke the build. Fixing the index resolved it.

2. An internal link pointed to a path that did not exist. This kind of broken link does not show up with `pnpm start` — only `pnpm build` reports it. After correcting the path, the build passed.

## Further References

- [Docusaurus Documentation](https://docusaurus.io/docs)
- [Configuring Docusaurus](https://docusaurus.io/docs/configuration)
- [Deployment](https://docusaurus.io/docs/deployment)

## TOC

- [Quickstart](#quickstart)
  - [Prerequisites](#prerequisites)
- [Description](#description)
  - [Configuration](#configuration)
  - [Problems & Solutions](#problems--solutions)
- [Further References](#further-references)
