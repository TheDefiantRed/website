# Performance & Codebase Improvements (TODO)

This document outlines a roadmap to achieve a 100/100 score on PageSpeed Insights and improve overall codebase best practices. The design and structure of the website remain unchanged.

## 1. PageSpeed Insights (Performance)

### Optimize Images (Largest Contentful Paint - LCP)
* [x] **Convert to Next-Gen Formats**: Replaced `hero-app-screens` images with `the-defiant-red-book-mockup.webp` (24KB). Converted `hero-bg.jpg` to `hero-bg.webp`.
* [x] **Compress Images**: The hero mockup and background images have been significantly optimized and converted to WebP.
* [x] **Preload LCP Image**: Add `<link rel="preload" as="image" href="images/the-defiant-red-book-mockup.webp">` in the `<head>` for the hero image so the browser discovers it immediately.
* [x] **Fix JS-Dependent Background**: The parallax background (`hero-bg.jpg`) is loaded via `data-image-src` in JS. This delays the download. Move this to an inline `style="background-image: url(...)"` or a CSS class to ensure the browser fetches it early.

### Prevent Layout Shifts (Cumulative Layout Shift - CLS)
* [x] **Explicit Dimensions**: Add `width` and `height` attributes to the hero image (`<img alt="..." src="images/the-defiant-red-book-mockup.webp" width="420" height="705">`). This allows the browser to allocate space before the image loads, preventing content from jumping.

### Reduce Render-Blocking Resources (First Contentful Paint - FCP)
* [ ] **Defer JavaScript**: Add the `defer` attribute to all `<script>` tags in the `<head>` (`modernizr.js`, `pace.min.js`, `sweetalert2.min.js`). Alternatively, move them to the bottom of the `<body>`.
* [ ] **Remove Preloader**: Remove the `div#preloader` and the `pace.min.js` library. Artificial loading screens actively hurt FCP and LCP scores and negatively impact user experience.
* [ ] **Non-Critical CSS**: Load non-critical CSS (like `sweetalert2.min.css`) asynchronously using `media="print" onload="this.media='all'"`, as it's not needed for the initial render.

### Asset Minification
* [ ] **Minify CSS & JS**: Minify `main.css`, `base.css`, `vendor.css`, and `main.js`. Currently, they are served unminified which wastes bytes.

## 2. Netlify Headers & Caching (`_headers`)

* [ ] **Optimize Cache-Control**: The current rule `Cache-Control: public, max-age=14400` (4 hours) is applied globally. This is too short for static assets and too long for `index.html`.
* [ ] **Split Cache Rules**:
  * Set `index.html` to `Cache-Control: public, max-age=0, must-revalidate` so users always get the latest version.
  * Set assets (`/css/*`, `/js/*`, `/images/*`, `/favicon/*`) to `Cache-Control: public, max-age=31536000, immutable` (1 year). Note: This requires cache-busting (e.g., appending `?v=2` or file hashes) when you update these files.

## 3. Codebase Best Practices

* [ ] **Dependency Management**: Currently, vendor libraries (jQuery, Pace, SweetAlert2) are manually downloaded and placed in the `/js/` folder. Introduce a simple `package.json` and a bundler (like Vite, Parcel, or at least npm scripts) to manage, update, bundle, and minify dependencies automatically.
* [ ] **Semantic HTML**: Review the use of `<article class="chem">Fe</article>`. An `<article>` should make sense on its own (like a blog post). A `<div>` or `<span aria-hidden="true">` would be more semantically correct for an icon/chemical symbol.
* [ ] **Accessibility (A11y)**:
  * Check color contrast ratios, especially text sitting on top of the parallax background image (`hero-bg.jpg`).
  * Ensure the "Back to Top" link (`<a class="smoothscroll" title="Back to Top" href="#top"></a>`) has visible text or an `aria-label` for screen readers.
* [ ] **Modern JavaScript (Future Rewrite)**: The codebase relies on jQuery 3.5.1. While functional, modern Vanilla JS (`document.querySelector`, `IntersectionObserver` for parallax/scroll events) is more than capable and would allow you to remove jQuery entirely, significantly reducing the JS payload.
