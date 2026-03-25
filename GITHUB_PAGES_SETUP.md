# GitHub Pages Setup Instructions

This document explains how to enable and configure GitHub Pages for the llm-d-benchmark repository.

## Prerequisites

- Repository: `https://github.com/oprince/llm-d-benchmark`
- GitHub account with admin access to the repository

## Setup Steps

### 1. Enable GitHub Pages

1. Go to your repository on GitHub: `https://github.com/oprince/llm-d-benchmark`
2. Click on **Settings** (top right)
3. Scroll down to **Pages** in the left sidebar
4. Under **Source**, select:
   - **Branch:** `simulator_benchmark`
   - **Folder:** `/docs`
5. Click **Save**

### 2. Configure the Theme (Optional)

The repository is already configured to use the Cayman theme via `_config.yml`. GitHub Pages will automatically apply it.

### 3. Wait for Deployment

- GitHub will automatically build and deploy your site
- This usually takes 1-2 minutes
- You'll see a green checkmark when it's ready
- Your site will be available at: `https://oprince.github.io/llm-d-benchmark/`

### 4. Verify the Site

Visit these URLs to verify everything is working:

- **Home page:** `https://oprince.github.io/llm-d-benchmark/`
- **Benchmark guide:** `https://oprince.github.io/llm-d-benchmark/RUN_BENCHMARK_AGAINST_EXISTING_DEPLOYMENT.html`

## File Structure

```
llm-d-benchmark/
├── GITHUB_PAGES_SETUP.md    # This file
└── docs/
    ├── _config.yml          # Jekyll configuration
    ├── Gemfile              # Ruby dependencies
    ├── index.md             # Main documentation index
    ├── RUN_BENCHMARK_AGAINST_EXISTING_DEPLOYMENT.md
    └── [other documentation files]
```

## Files Created

The following files were created to enable GitHub Pages:

1. **`docs/_config.yml`** - Jekyll configuration
   - Sets the theme (Cayman)
   - Configures site title and description
   - Defines build settings

2. **`docs/index.md`** - Main documentation index
   - Lists all documentation pages
   - Features the benchmark guide
   - Provides navigation

3. **`docs/Gemfile`** - Ruby dependencies
   - Specifies Jekyll and plugins
   - Required for local testing

4. **`docs/RUN_BENCHMARK_AGAINST_EXISTING_DEPLOYMENT.md`** - Updated with front matter
   - Added YAML front matter for Jekyll processing
   - Maintains all existing content

## Local Testing (Optional)

To test the site locally before pushing:

```bash
# Install Ruby and Bundler (if not already installed)
# On macOS:
brew install ruby
gem install bundler

# Navigate to docs directory
cd docs

# Install dependencies
bundle install

# Serve the site locally
bundle exec jekyll serve

# Visit http://localhost:4000/llm-d-benchmark/
```

## Updating Content

To add or update documentation:

1. Edit files in the `docs/` directory
2. Commit and push to GitHub
3. GitHub Pages will automatically rebuild (1-2 minutes)

### Adding New Pages

1. Create a new `.md` file in `docs/`
2. Add YAML front matter at the top:
   ```yaml
   ---
   layout: default
   title: Your Page Title
   ---
   ```
3. Add a link to `docs/index.md`
4. Commit and push

## Troubleshooting

### Site Not Updating

- Check the **Actions** tab in GitHub for build status
- Look for any error messages in the workflow logs
- Ensure the `docs/` folder is selected in Pages settings

### 404 Errors

- Verify the file exists in the `docs/` directory
- Check that the file has a `.md` extension
- Ensure front matter is present at the top of the file

### Theme Not Applied

- Verify `_config.yml` is in the `docs/` directory
- Check that `remote_theme` is correctly specified
- Wait a few minutes for GitHub to rebuild

## Current Configuration

- **Site URL:** `https://oprince.github.io/llm-d-benchmark/`
- **Theme:** Cayman (via `pages-themes/cayman@v0.2.0`)
- **Source:** `docs/` folder on `simulator_benchmark` branch
- **Build:** Automatic on push

## Next Steps

1. Push all changes to GitHub:
   ```bash
   git add docs/_config.yml docs/Gemfile docs/index.md docs/RUN_BENCHMARK_AGAINST_EXISTING_DEPLOYMENT.md GITHUB_PAGES_SETUP.md
   git commit -m "Add GitHub Pages configuration with Jekyll theme"
   git push origin simulator_benchmark
   ```

2. Enable GitHub Pages in repository settings (see Step 1 above):
   - Go to Settings → Pages
   - Select Branch: `simulator_benchmark`
   - Select Folder: `/docs`
   - Click Save

3. Wait for deployment (1-2 minutes) and visit your site at `https://oprince.github.io/llm-d-benchmark/`

**Note:** If the site still appears empty after deployment, check the GitHub Actions tab for build errors. The Jekyll build process should complete successfully within 1-2 minutes.

## Resources

- [GitHub Pages Documentation](https://docs.github.com/en/pages)
- [Jekyll Documentation](https://jekyllrb.com/docs/)
- [Cayman Theme](https://github.com/pages-themes/cayman)
- [Jekyll Themes](https://pages.github.com/themes/)
