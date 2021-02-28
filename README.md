# nestjs-vercel-example

[![GitHub Workflow Status (branch)](https://img.shields.io/github/workflow/status/robingenz/nestjs-vercel-example/CI/main)](https://github.com/robingenz/nestjs-vercel-example/actions?query=workflow%3ACI)

Example of how to deploy a [NestJS](https://nestjs.com/) app to [Vercel](https://vercel.com/). 

## Setup

1. Create a `.vercelignore` file:

```
# Ignore everything (folders and files) on root only
/*
!dist
!vercel.json
!package.json
!package-lock.json
```

2. Create a `vercel.json` file:

```json
{
  "version": 2,
  "github": {
    "enabled": false
  },
  "builds": [{ "src": "dist/main.js", "use": "@vercel/node" }],
  "routes": [{ "src": "/(.*)", "dest": "dist/main.js" }]
}
```

3. Run `vercel` to deploy your app. 
Once set up, a new `.vercel` directory will be added to your directory. 
The `.vercel` directory contains both the organization and project id of your project.

4. Optionally, you can also set up an automated deployment with GitHub Actions.
For this you can use the [vercel-action](https://github.com/amondnet/vercel-action).

Example workflow:

```yml
name: Deploy

on:
  push:
    branches:
      - main

jobs:
  build-web:
    name: Build web assets
    runs-on: ubuntu-20.04
    steps:
      - name: Checkout
        uses: actions/checkout@v2
      - name: Set up Node.js 12
        uses: actions/setup-node@v1
        with:
          node-version: '12'
      - name: Install dependencies
        run: npm ci
      - name: Build web assets
        run: npm run build
      - name: Upload artifacts
        uses: actions/upload-artifact@v2
        with:
          name: dist
          path: dist
  deploy:
    name: Deploy
    needs: [ build-web ]
    runs-on: ubuntu-20.04
    steps:
      - name: Checkout
        uses: actions/checkout@v2
      - name: Download
        uses: actions/download-artifact@v2
        with:
          name: dist
          path: dist
      - name: Vercel Action
        uses: amondnet/vercel-action@v20.0.0
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          github-comment: false
          vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}
          vercel-org-id: ${{ secrets.VERCEL_ORG_ID }}
          vercel-args: '--prod'
```

GitHub Actions secrets:
- `VERCEL_TOKEN`: Create a token in the [Vercel Tokens settings](https://vercel.com/account/tokens).
- `VERCEL_PROJECT_ID`: You find the project id in the `.vercel/project.json` file.
- `VERCEL_ORG_ID`: You find the organization id in the `.vercel/project.json` file.
