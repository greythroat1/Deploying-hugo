# Deploying hugo website on GitHub for the first time

1.Create an empty folder on local folder where hugo website is.

```
.github/workflows
```

2. On that folder, create **YAML** file name `hugo.yaml` file and put the following **YAML** below.

```yaml
# Sample workflow for building and deploying a Hugo site to GitHub Pages
name: Deploy Hugo site to Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches:
      - main

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow only one concurrent deployment, skipping runs queued between the run in-progress and latest queued.
# However, do NOT cancel in-progress runs as we want to allow these production deployments to complete.
concurrency:
  group: "pages"
  cancel-in-progress: false

# Default to bash
defaults:
  run:
    shell: bash

jobs:
  # Build job
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.111.3
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
          && sudo dpkg -i ${{ runner.temp }}/hugo.deb          
      - name: Install Dart Sass Embedded
        run: sudo snap install dart-sass-embedded
      - name: Checkout
        uses: actions/checkout@v3
        with:
          submodules: recursive
          fetch-depth: 0
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v3
      - name: Install Node.js dependencies
        run: "[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true"
      - name: Build with Hugo
        env:
          # For maximum backward compatibility with Hugo modules
          HUGO_ENVIRONMENT: production
          HUGO_ENV: production
        run: |
          hugo \
            --gc \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/"          
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v1
        with:
          path: ./public

  # Deployment job
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v2
```

3. Go to github and create new repo
4. Open settings on that repo, then navigate to *Pages*, under *Build and deployment* change source to **GitHub Actions**
5. Open terminal then navigate to local folder where hugo website is.
6. Input following command:

```shell
git init                        # initialize github on the folder
git status                      # to check folder or file that want to push to github
git add .                       # add folder or file to github
git commit -m "commit text"     # add text (summary) to github
git branch -M main              # change the branch from master to main
git remote add origin https://github.com/<USERNAME>/<REPONAME>.git    # add github repo that we make before
git push -u origin main         # push change to github
```
Wait for a minute and check the website, to know the url, navigate to Settings > Pages


nb: *for adding theme to hugo, can use the submodule instead clone*
```shell
git submodule add <HUGO THEME REPO LINK> themes/<THEME NAME>   
```

# Deploying after first time

if we want to deploy after make the post on hugo or change the hugo setting 
(basically add or change the files):

```shell
git status      # to see the changes
git add .
git commit -m "commit text"
git push -u origin main
```
