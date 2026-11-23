# FlowGrid Engineering Blog

A static technical series about building FlowGrid with FastAPI, PostgreSQL, Redis, and Celery. HTML, CSS, and images are ready to serve without a build step.

## Folder structure

```text
flowgrid-blog/
  index.html
  phase-1.html
  phase-2.html
  phase-3.html
  technical-review.html
  publishing-guide.html
  assets/
    style.css
    images/              PNG figures and SVG masters
  docs/                  Markdown article sources and author notes
  .nojekyll
  .gitignore
  README.md
```

HTML is the served content. Markdown files are editable sources; changing a Markdown file does not automatically rebuild its HTML copy. Relative links keep the site compatible with a repository subpath such as /flowgrid-blog/.

## Publish on GitHub Pages

1. Create a GitHub repository named `flowgrid-blog`. Use a public repository for GitHub Free.
2. Upload the contents of this folder to the repository root. `index.html` must be at the top level of the chosen publishing folder, not nested inside another `flowgrid-blog` folder. Include the `.nojekyll` file when pushing with Git.
3. In the repository, open **Settings > Pages**.
4. Set **Source** to **Deploy from a branch**, select the branch containing these files (usually `main`), choose **/(root)**, and save.
5. Wait for the Pages deployment to finish and use the URL displayed in Settings > Pages. For a typical project repository the URL pattern is `https://YOUR-USERNAME.github.io/flowgrid-blog/`.

Upload or push later edits to the same branch to update the site. This blog requires no Python API, Redis, PostgreSQL, Node build, or environment variables to be hosted.

GitHub documentation: [Configure a publishing source](https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site) and [Create a GitHub Pages site](https://docs.github.com/en/pages/getting-started-with-github-pages/creating-a-github-pages-site).

## Local preview

From this directory, run:

```bash
python3 -m http.server 8000
```

Open `http://localhost:8000/`. Stop the preview with Ctrl+C.

## Alternative hosts

- **Cloudflare Pages:** Connect the GitHub repository or use Direct Upload. For this buildless site, the content directory is the repository root. [Static HTML guide](https://developers.cloudflare.com/pages/framework-guides/deploy-anything/).
- **Netlify:** Deploy the folder manually or connect the GitHub repository. The publish directory is the repository root and no build command is needed. [Deployment guide](https://docs.netlify.com/deploy/create-deploys/).
