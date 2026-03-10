# Performance & Codebase Improvements (TODO)

This document outlines a roadmap to achieve a 100/100 score on PageSpeed Insights and improve overall codebase best practices. The design and structure of the website remain unchanged.

## 1. PageSpeed Insights (Performance)

### Optimize Images (Largest Contentful Paint - LCP)
* [x] **Convert to Next-Gen Formats**: Replaced `hero-app-screens` images with `the-defiant-red-book-mockup.webp` (24KB). Converted `hero-bg.jpg` to `hero-bg.webp`.
* [x] **Compress Images**: The hero mockup and background images have been significantly optimized and converted to WebP.
* [x] **Preload LCP Image**: Added `<link rel="preload">` in the `<head>` for the hero image.
* [x] **Fix JS-Dependent Background**: Moved parallax background to an inline style.
* [ ] **Add `fetchpriority="high"`**: Add the `fetchpriority="high"` attribute to the LCP hero image (`images/the-defiant-red-book-mockup.webp`) to optimize LCP request discovery further.

### Reduce Render-Blocking Resources (First Contentful Paint - FCP)
* [ ] **Non-Critical CSS**: Load non-critical CSS asynchronously using `media="print" onload="this.media='all'"`. Render-blocking CSS (Google Fonts, `micons.css`, `base.css`, `main.css`, etc.) wastes ~1.47s.
* [ ] **Optimize Font Display**: Add `&display=swap` to the Google Fonts URL to ensure text remains visible during webfont load.
* [x] **Defer JavaScript**: Render-blocking scripts like `modernizr.js` and `pace.min.js` were removed from the `<head>`.
* [ ] **Remove Preloader**: Remove the `div#preloader` HTML structure. The `pace.min.js` library has been removed, but the artificial loading screen HTML remains and negatively impacts user experience.

### Asset Minification
* [ ] **Minify CSS & JS**: Minify `main.css`, `base.css`, `vendor.css`, and JS files. Currently, they are served unminified which wastes bytes (CSS minification could save ~3 KiB).

### Prevent Layout Shifts (Cumulative Layout Shift - CLS)
* [x] **Explicit Dimensions**: Add `width` and `height` attributes to the hero image.

## 2. Accessibility (A11y) - High Impact

* [x] **Replace Slick Slider (Critical ARIA Errors)**: The Slick slider library causes multiple critical ARIA failures (`aria-allowed-attr`, `aria-required-children`, `aria-input-field-name`). ~~Replacing it with a modern, accessible Vanilla JS slider or CSS scroll-snap is highly recommended to fix these issues.~~ *Mitigated via JS patch hooking into Slick's `init` event to replace invalid roles with `role="region"` and `role="group"`.*
* [x] **Fix Heading Order**: Heading elements are not in a sequentially-descending order.
  * In `.home-content__left`, an `<h1>` is followed directly by an `<h3>`.
  * Missing `<h2>` before `<h3>` elements like "History" and "Iron".
  * Footer uses `<h4>` directly.
* [x] **Improve Color Contrast**: Footer text (`#6e7071` on `#0e1113`) has an insufficient contrast ratio (3.8 vs expected 4.5). Lighten the text color.
* [ ] **Screen Reader Labels**: Ensure the "Back to Top" link (`<a class="smoothscroll" href="#top"></a>`) has visible text or an `aria-label` for screen readers.

## 3. Netlify Headers & Caching (`_headers`) - Medium Impact

* [ ] **Optimize Cache-Control**: The current rule `Cache-Control: public, max-age=14400` (4 hours) is applied globally. This causes a Lighthouse warning about inefficient cache lifetimes (est. savings 223 KiB).
* [ ] **Split Cache Rules**:
  * Set `index.html` to `Cache-Control: public, max-age=0, must-revalidate` so users always get the latest version.
  * Set assets (`/css/*`, `/js/*`, `/images/*`, `/favicon/*`) to `Cache-Control: public, max-age=31536000, immutable` (1 year). Note: This requires cache-busting (e.g., appending `?v=2` or file hashes) when you update these files.

## 4. Codebase Best Practices

* [ ] **Modern JavaScript (Future Rewrite)**: The codebase relies on jQuery 3.5.1 and Slick Slider. While functional, modern Vanilla JS (`document.querySelector`, `IntersectionObserver`, CSS scroll-snap) is more than capable and would allow you to remove jQuery entirely, significantly reducing the JS payload and resolving a11y issues.
* [ ] **Dependency Management**: Introduce a simple `package.json` and a bundler (like Vite, Parcel, or at least npm scripts) to manage, update, bundle, and minify dependencies automatically.
* [ ] **Semantic HTML**: Review the use of `<article class="chem">Fe</article>`. An `<article>` should make sense on its own (like a blog post). A `<div>` or `<span aria-hidden="true">` would be more semantically correct for an icon/chemical symbol.
