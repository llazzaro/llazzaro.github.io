# Leonardo Lazzaro's Homepage

This is a Hugo-based personal website deployed on GitHub Pages with a custom domain.

## 🚀 Quick Start

### Local Development
```bash
# Install Hugo (macOS)
brew install hugo

# Clone with submodules
git clone --recursive https://github.com/llazzaro/llazzaro.github.io.git

# Or if already cloned, initialize submodules
git submodule update --init --recursive

# Start development server
hugo server -D
```

### Deployment

The site automatically deploys to GitHub Pages when you push to the `master` branch.

## 🌐 Domain Configuration

### Current Setup
- **Custom Domain**: `www.lazzaro.com.ar`
- **CNAME File**: Properly configured
- **DNS Requirements**: Ensure your DNS provider has:
  - CNAME record: `www.lazzaro.com.ar` → `llazzaro.github.io`
  - A records for apex domain (optional): 
    - `185.199.108.153`
    - `185.199.109.153`
    - `185.199.110.153`
    - `185.199.111.153`

### Troubleshooting Domain Issues

1. **Check DNS propagation**: Use `dig www.lazzaro.com.ar` or online tools
2. **GitHub Pages settings**: Ensure custom domain is set in repository settings
3. **HTTPS**: GitHub Pages automatically provisions SSL for custom domains
4. **Cache issues**: Clear browser cache and wait for DNS propagation (up to 24h)

### GitHub Pages Settings Checklist
- [ ] Repository Settings → Pages → Source: "Deploy from a branch"
- [ ] Branch: `gh-pages` (created by GitHub Actions)
- [ ] Custom domain: `www.lazzaro.com.ar`
- [ ] Enforce HTTPS: ✓ (after DNS propagation)

## 🔧 Technical Details

- **Framework**: Hugo
- **Theme**: PaperMod (as git submodule)
- **Deployment**: GitHub Actions
- **Hosting**: GitHub Pages

## 📝 Content Management

- Posts: `content/posts/`
- Pages: `content/`
- Configuration: `config.toml`

## 🛠 Maintenance

### Update Theme
```bash
git submodule update --remote themes/PaperMod
git add themes/PaperMod
git commit -m "Update PaperMod theme"
```

### Update Hugo Version
The GitHub Actions workflow automatically uses the latest Hugo version.