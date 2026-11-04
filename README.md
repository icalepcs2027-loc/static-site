# IPAC Hugo Conference Template

A customizable Hugo Extended template tailored for large-scale academic and scientific conferences, styled with Tailwind CSS v4. It features a responsive layout, frosted glassmorphism navigation, dynamic timelines, and committee listings. 

Branding, colors, and display settings are configured in `hugo.toml`. News (`data/news.yaml`), sponsors (`data/sponsors.yaml`), important dates (`data/schedules/important_dates.yaml`), and committees (`data/committees/*.yaml`) are managed separately from the layout templates.

---

## Directory Structure

```text
web/
├── hugo.toml                 # Site metadata, branding, and appearance
├── data/                     # YAML data: menus, news, sponsors, schedules, and committees
│   ├── menus.yaml            # Nested header, sidebar, and footer navigation
├── tailwindcss               # Tailwind CSS CLI binary for template compilation
├── assets/
│   └── css/
│       └── input.css         # Tailwind source entry point and theme variables
├── content/                  # Markdown pages (about, authors, program, travel, registration)
├── layouts/                  # Hugo HTML templates
│   ├── _default/             # Base layouts for single pages and list collections
│   ├── partials/             # Reusable HTML sub-layouts (header, footer, head metadata)
│   └── shortcodes/           # Custom interactive shortcode components
├── static/                   # Static assets (compiled CSS, client JS, images, logos)
│   ├── css/
│   │   └── style.css         # Target compiled CSS output (loaded by the template)
│   ├── js/                   # Theme interactivity (lightboxes, timers, banners)
│   └── img/                  # Logos, banners, graphics
└── README.md                 # Project introduction
```

---

## Getting Started

### 1. Prerequisites
Ensure you have the Extended version of Hugo installed:
* **macOS** (Homebrew): `brew install hugo`
* **Windows** (Winget): `winget install Gohugo.Hugo.Extended`

### 2. Local Development
1. Run the local Hugo server from the project root:
   ```bash
   hugo server
   ```
2. Access the site at `http://localhost:1313/`. Hot reloading is enabled by default.

### 3. Compilation of Styles (Tailwind CSS)
The theme styles are managed via Tailwind CSS v4. If you modify any styles in `assets/css/input.css` or introduce new CSS utility classes in the templates, compile the production styles using the local binary:
```bash
./tailwindcss -i assets/css/input.css -o static/css/style.css --watch
```
Ensure the compiled output file is committed to git.

For a comprehensive guide on template configuration and interactive shortcodes, refer to [usage.md](usage.md).

