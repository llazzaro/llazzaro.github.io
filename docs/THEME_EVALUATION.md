# Theme Evaluation: PaperMod Customization vs Theme Switch

**Issue:** #622 - Consider switching Hugo theme or heavy customization  
**Date:** 2026-02-13  
**Conclusion:** ✅ **Keep PaperMod** - All requirements achievable through customization

---

## Executive Summary

After reviewing issues #617-621 and the target design (rdegges.com-inspired minimalist style), **PaperMod can fully support all requirements** through layout overrides and CSS customization. A theme switch is **not recommended** because:

1. PaperMod already has extensive customization infrastructure in place
2. Custom layouts are already created (`layouts/partials/`)
3. Theme switching would lose existing SEO, analytics, and styling work
4. PaperMod's architecture explicitly supports override patterns

---

## Requirements Analysis

### Issues #617-621 vs rdegges.com Design

| Issue | Requirement | rdegges.com Style | PaperMod Solution |
|-------|-------------|-------------------|-------------------|
| #617 | Simplify header to name + tagline | `RANDALL DEGGES` + `Random Thoughts...` | Override `header.html`, disable nav |
| #618 | Inline comma-separated nav | `HOME, BLUESKY, GITHUB, TALKS, EMAIL` | Custom `nav.html` partial |
| #619 | Avatar + short intro | Circle avatar + paragraph intro | Use `homeInfoParams` or custom partial |
| #620 | Full post list with dates | `DATE - TITLE` list format | Override `index.html`, custom list template |
| #621 | Simplify footer | Minimal: `© Name (social)` | Override `footer.html` |

---

## PaperMod Capabilities

### Current Theme Modes
PaperMod supports 3 homepage modes:
1. **Regular Mode** - Post list (default)
2. **Home-Info Mode** - Title + content + social icons
3. **Profile Mode** - Image + title + subtitle + buttons (currently active)

### Override System
PaperMod follows Hugo's lookup order:
```
layouts/partials/header.html     ← Project override (takes priority)
themes/PaperMod/layouts/...      ← Theme default (fallback)
```

### Existing Customizations
Already in place:
- `layouts/partials/extend_head.html` - Analytics hooks
- `layouts/partials/post_meta.html` - Custom post metadata
- `layouts/partials/templates/twitter_cards.html` - Social sharing
- `layouts/partials/templates/opengraph.html` - SEO
- `assets/css/extended/` - Custom styling (~300 lines)

---

## Implementation Plan

### Phase 1: Header Simplification (#617, #618)

Create `layouts/partials/header.html`:
```html
<header class="header minimal">
  <div class="header-inner">
    <h1 class="site-title">
      <a href="{{ "/" | absURL }}">LEONARDO LAZZARO</a>
    </h1>
    <p class="site-tagline">Software engineer writing about Python, Django, and building things</p>
    <nav class="nav-inline">
      {{ range .Site.Menus.main }}
      <a href="{{ .URL }}">{{ .Name }}</a>{{ if not (eq .Name (index (last 1 $.Site.Menus.main) 0).Name) }}, {{ end }}
      {{ end }}
    </nav>
  </div>
</header>
```

CSS additions:
```css
.header.minimal {
  text-align: center;
  padding: 60px 20px 40px;
  border-bottom: 1px solid var(--border);
}
.site-title { font-size: 2rem; letter-spacing: 0.1em; margin: 0; }
.site-tagline { color: var(--secondary); margin: 10px 0 20px; }
.nav-inline a { color: var(--secondary); text-decoration: none; }
.nav-inline a:hover { color: var(--primary); }
```

### Phase 2: Homepage with Avatar + Post List (#619, #620)

Create `layouts/index.html`:
```html
{{ define "main" }}
<main class="main minimal-home">
  <!-- Intro Section -->
  <section class="intro">
    <img src="/images/avatar.jpg" alt="Leonardo Lazzaro" class="avatar">
    <p class="bio">{{ .Site.Params.author.bio }}</p>
  </section>

  <!-- Full Post List -->
  <section class="post-list">
    {{ range .Site.RegularPages }}
    {{ if eq .Section "posts" }}
    <article class="post-item">
      <time datetime="{{ .Date.Format "2006-01-02" }}">{{ .Date.Format "02 Jan 2006" }}</time>
      <a href="{{ .Permalink }}">{{ .Title }}</a>
    </article>
    {{ end }}
    {{ end }}
  </section>
</main>
{{ end }}
```

CSS:
```css
.avatar { width: 120px; height: 120px; border-radius: 50%; }
.post-item { display: flex; gap: 20px; padding: 8px 0; }
.post-item time { color: var(--secondary); min-width: 100px; }
```

### Phase 3: Footer Simplification (#621)

Create `layouts/partials/footer.html`:
```html
<footer class="footer minimal">
  <p>© {{ now.Year }} <a href="mailto:contact@lazzaro.com.ar">Leonardo Lazzaro</a>
    ({{ range $i, $icon := .Site.Params.socialIcons }}{{ if $i }}, {{ end }}<a href="{{ $icon.url }}">{{ $icon.name }}</a>{{ end }})
  </p>
</footer>
```

---

## Alternative Themes Considered

If a full redesign were needed, these alternatives were evaluated:

| Theme | Pros | Cons |
|-------|------|------|
| **[Blowfish](https://blowfish.page/)** | Modern, feature-rich, excellent docs | Heavier, overkill for minimalist design |
| **[Congo](https://jpanther.github.io/congo/)** | Clean, fast, good typography | Less flexible than PaperMod |
| **[Coder](https://github.com/luizdepra/hugo-coder)** | Minimalist, dev-focused | Fewer features than PaperMod |
| **Custom from scratch** | Full control | Significant effort, lose existing work |

**Verdict:** None offer significant advantages over customized PaperMod for this use case.

---

## Recommendation

### ✅ Keep PaperMod + Customize

**Rationale:**
1. **Lower risk** - Preserve SEO, analytics, existing customizations
2. **Faster implementation** - Incremental changes vs full migration
3. **Maintainability** - PaperMod is actively maintained
4. **Flexibility** - Override only what needs changing

### Implementation Order
1. Create `layouts/partials/header.html` (#617, #618)
2. Create `layouts/index.html` (#619, #620)
3. Create `layouts/partials/footer.html` (#621)
4. Add CSS in `assets/css/extended/minimal.css`
5. Update `config.toml` to disable Profile Mode

### Estimated Effort
- Layout overrides: ~2-3 hours
- CSS refinement: ~1-2 hours
- Testing & adjustments: ~1 hour
- **Total: ~4-6 hours**

---

## Files to Create/Modify

```
layouts/
├── index.html                    # NEW: Custom homepage
├── partials/
│   ├── header.html               # NEW: Minimal header
│   └── footer.html               # NEW: Minimal footer
assets/
└── css/
    └── extended/
        └── minimal.css           # NEW: Minimalist styles
config.toml                       # MODIFY: Disable profileMode
```

---

## Appendix: rdegges.com Structure Reference

```
┌─────────────────────────────────────┐
│         RANDALL DEGGES              │  ← Name (uppercase, centered)
│  Random Thoughts of a Happy...      │  ← Tagline
│  ─────────────────────────────      │  ← Separator
│  HOME, BLUESKY, GITHUB, TALKS, EMAIL│  ← Inline nav (comma-separated)
├─────────────────────────────────────┤
│         [Avatar Image]              │  ← Circular photo
│  Hey, I'm Randall. I'm just a...    │  ← Short intro
├─────────────────────────────────────┤
│  03 Aug 2024  I'M WRITING AGAIN     │  ← Post list
│  04 Apr 2022  DOES MUSIC HELP...    │     (date + title)
│  04 Apr 2022  REAL ESTATE VS...     │
│  ...                                │
├─────────────────────────────────────┤
│  © Randall Degges (@rdegges.com)    │  ← Minimal footer
└─────────────────────────────────────┘
```

---

**Decision:** Proceed with PaperMod customization. No theme change needed.
