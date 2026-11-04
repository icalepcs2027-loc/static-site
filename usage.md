# Hugo Conference Template Customization and Usage Guide

This document details how to configure the site metadata, manage key database features, and use custom shortcodes to edit page contents.

---

## 1. Central Configuration (hugo.toml)

Manage site metadata and appearance in `hugo.toml` located in the project root. News, sponsors, schedules, committees, and navigation are stored under `data/`.

### Navigation Menus

Edit `data/menus.yaml` to change the header, footer, and page sidebar navigation. The templates read this custom data file automatically; it does not populate Hugo's native `Site.Menus`.

* `main`: Header navigation and page sidebar groups.
* `footer_quick`: Footer quick links.
* `footer_info`: Footer information links.
* Entries appear in file order; move list items to reorder them. No `weight` is required.
* `children`: Nested menu items; no `parent` or `identifier` is required. The current templates display up to three menu levels.
* `name`: Display name. `url`: Link target and, for local groups, the base path for children. Groups without a URL inherit their parent's base path.
* Child local URLs are joined to the parent path regardless of leading/trailing slashes. Repeated slashes are collapsed, and directory links receive a trailing slash. A child already containing the full parent path is not prefixed again. Query strings and fragments are preserved.
* Absolute URLs such as `https://...`, `mailto:...`, and `tel:...` ignore the parent path. Use an explicit scheme for external URLs; leading slashes always mean a local path in this format. Local links also include the build's `baseURL` subdirectory.
* `new_tab`: Set `true` to open the individual link in a new tab. `false` or omission opens it in the same tab. This works for internal and external links in the header, footer, and sidebar. The setting does not propagate to children. New-tab links include `rel="noopener noreferrer"`.

```yaml
main:
  - name: "About"
    url: "/about/"
    children:
      - name: "Welcome Address"
        url: "/welcome/"
      - name: "Committees"
        url: "/committees/"
      - name: "External Information"
        url: "https://example.org/"
        new_tab: true
  - name: "Contact"
    url: "/contact/"
  - name: "External Registration"
    url: "https://registration.example.org/"
    new_tab: true

footer_quick:
  - name: "Important Dates"
    url: "/key-dates/"

footer_info:
  - name: "FAQ"
    url: "/about/faq/"
```

### Basic Conference Information

Define the core metadata under the `[params]` namespace:

```toml
[params]
  conf_short_name = "IPAC'28"
  conf_full_name = "19th International Particle Accelerator Conference"
  conf_dates = "June 5-9, 2028"
  conf_start_date = "2028-06-05T08:00:00+09:00"  # Targets the homepage countdown timer
  conf_location = "Tokyo International Forum, Tokyo, Japan"
  email = "contact@ipac28.org"
  registration_link = "/register/conference/"
```

### Branding Logos and Toggles

* **Graphic Logo**: Set `logo_image = "/img/your-logo.png"` to use an image logo in the navbar.
* **Dynamic Text Logo fallback**: Comment out `logo_image` to automatically generate a styled text logo using `logo_prefix` and `logo_suffix`.
* **Interactivity Toggles**:
  * `enable_countdown = true` (Toggles the countdown timer on the homepage)
  * `enable_sponsors = true` (Toggles the scrolling sponsor ribbon)
  * `enable_stats = true` (Toggles the attendee, presentation, and exhibitor stats row on the homepage)

### Appearance, Fonts and Colors

Configure layout sizes, fonts, and base color tones under `[params.text_style]`:

```toml
[params.text_style]
  font_size = "0.95rem"               # Body font size
  line_height = "1.65"                # Body line height
  font_family = "Inter"               # Google Font for paragraph text
  heading_font_family = "Montserrat"  # Google Font for headings
  body_color = "#334155"              # Text color
  heading_color = "#1d4485"           # Title and heading color
  link_color = "#e52421"              # Primary highlight and button accent color
```

Note: If you customize primary CSS utilities or colors under `assets/css/input.css`, recompile the Tailwind stylesheet using:

```bash
./tailwindcss -i assets/css/input.css -o static/css/style.css
```

### Announcement Banner

Renders a dismissible banner at the top of the header:

```toml
  enable_announcement = true
  announcement_text = "Abstract submission will open on Oct 1, 2027!"
  announcement_link = "/about/welcome/"
```

### Important Dates Database

Edit the milestones in `data/schedules/important_dates.yaml`. The homepage and timeline shortcode both read this data; their layout remains in `layouts/`. Date display formatting is controlled by `params.date_fmt` in `hugo.toml`.

* **type options**: `"info"` (blue bubble), `"open"` (green bubble), `"close"` (red bubble for deadlines).
* **date** (optional): Omit it, leave it empty, or use `null` for an undecided date. The date label becomes "To be announced" and the badge becomes "TBD" regardless of `type`. A specified date must be a valid timestamp, not the string "TBD".
* **hidden** (optional): Set `hidden: true` to exclude an entry from both the homepage and full timeline. Omit it or set `hidden: false` to display it.
* **order** (optional): An integer specifying display priority; smaller numbers come first, including `0` and negative numbers. Entries without `order` (or with `order: null`) follow ordered entries, with dated entries sorted chronologically and undecided entries in file order. `hidden: true` still takes precedence.
* **season** (optional): If configured as a string (e.g. `"Late Summer, 2027"`), the template displays this value instead of the date label, including for undecided dates.
* Past dates are marked automatically as outdated based on the build timestamp.
* The homepage uses the same display order and shows the first three upcoming/undecided entries; if none exist, it shows the last three visible past entries.

```yaml
- date: "2027-12-30T23:59:59+09:00"
  order: 10
  season: "Late Summer, 2027" # optional
  title: "Abstract Submission Deadline"
  type: "close"
  description: "Abstract submission closes strictly at 23:59 JST. No late submissions accepted."

# Undecided date
- title: "Registration Opens"
  type: "open"
  description: "Details will be announced soon."

# Hidden entry
- date: "2027-06-01T09:00:00+09:00"
  title: "Technical Tour"
  type: "info"
  hidden: true
```

### News and Announcements Feed

Edit `data/news.yaml`. The three newest entries will display featured preview cards on the homepage:

```yaml
- date: "2026-05-15T00:00:00+09:00"
  title: "Exhibitor Prospectus Now Available"
  summary: "The exhibitor and sponsorship prospectus for IPAC'28 is now available..."
  link: "/exhibitor/info/"
```

### Tiered Sponsors

Edit `data/sponsors.yaml`. The `sponsors-grid` shortcode renders sponsor graphics grouped by hierarchy tiers. The homepage reads the same data; its sponsor section is controlled by `params.enable_sponsors` in `hugo.toml`.

* **tier options**: `"platinum"`, `"gold"`, `"silver"`, `"bronze"`, `"supporter"`.

```yaml
- name: "Quantum Dynamics Corp."
  logo: "/img/ipac28_600x304.png"
  url: "https://example.com/quantum-dynamics"
  tier: "platinum"
  tagline: "Pioneering the next generation of particle accelerator power systems."
```

### Organizing Committees

Edit the YAML files under `data/committees/`. The `committees-list` shortcode displays them in this order:

1. `conference_chair.yaml`: Conference Chairs
2. `iec.yaml`: ICALEPCS Executive Committee (IEC)
3. `isac.yaml`: International Scientific Advisory Committee (ISAC)
4. `ssc.yaml`: Site Selection Committee (SSC)
5. `loc.yaml`: Local Organizing Committee (LOC)

Add highlighted chairs under `chairs` and other members under `members`. Empty committees display "To Be Announced". Set `hide_members: true` to hide the regular member list while retaining any chairs. `location`, `desc`, and `avatar` are optional.

Both `chairs` and `members` may be omitted, left blank, or set to `null` or `[]`; all are treated as empty lists. If chairs are present and members are empty, only the chairs are displayed.

```yaml
id: "iec"
title: "ICALEPCS Executive Committee (IEC)"
hide_members: false
chairs: []
members:
  - name: "Given name"
    surname: "Family name"
    institution: "Institution"
    location: "Country"
    desc: "Role"
```

---

## 2. Interactive Markdown Shortcodes

Include these components directly inside any page content Markdown file.

### Dynamic Content Shortcodes

#### timelines (`full-dates-timeline`)

Renders the timeline from the YAML list in `data/schedules/important_dates.yaml` (`hugo.Data.schedules.important_dates`).

```markdown
{{< full-dates-timeline >}}
```

#### Committee Listing (`committees-list`)

RendersSPC, IOC, LOC, and SAB committees configured in `hugo.toml`, applying regional tab groupings automatically.

```markdown
{{< committees-list >}}
```

#### Sponsors Grid (`sponsors-grid`)

Generates the partner logo grid categorized by tier, and attaches a downloadable prospectus section.

```markdown
{{< sponsors-grid >}}
```

### Interactive Layout Shortcodes

#### Timetable Grid (`synoptic-table`)

Generates a structured, responsive multi-day timetable overview (Monday - Friday).

```markdown
{{< synoptic-table >}}
```

#### Collapsible Accordion

Renders an animated drop-down card suitable for FAQs:

```markdown
{{< accordion title="How do I request an invitation letter?" >}}
Invitation letters can be requested via the registration portal after payment.
{{< /accordion >}}
```

#### Tabs Layout

Creates tabbed panels to split dense information:

```markdown
{{< tab-container >}}
    {{< tab title="Haneda Airport" >}}
Take the Tokyo Monorail to Hamamatsucho Station.
    {{< /tab >}}
    {{< tab title="Narita Airport" >}}
Take the Narita Express to Tokyo Station.
    {{< /tab >}}
{{< /tab-container >}}
```

#### Poster Download Button (`download-button`)

Renders a clean, customizable download button. You can call it multiple times to display side-by-side buttons.
Parameters:

* **url**: Link to the target download asset.
* **label**: Custom text label.
* **filename**: Download filename.
* **icon** (optional): Custom FontAwesome icon class (defaults to `"fa-solid fa-download"`).
* **theme** (optional): Theme style class: `"primary"`, `"secondary"`, `"dark"`, `"green"`, `"blue"`.

Example:

```markdown
{{< download-button url="/img/poster_a0.png" label="Download A0 Poster" filename="poster_a0.png" theme="primary" >}}
{{< download-button url="/img/poster_a4.png" label="Download A4 Poster" filename="poster_a4.png" theme="secondary" >}}
```

#### Manual Timeline

Builds a custom timeline inside arbitrary pages:

```markdown
{{< timeline >}}
    {{< timeline-item date="Dec 5, 2027" badge="Deadline" badge_class="badge-danger" title="Abstracts" >}}
    Submissions close.
    {{< /timeline-item >}}
{{< /timeline >}}
```

*`badge_class` options: `badge-default` (gray), `badge-success` (green), `badge-danger` (red), `badge-info` (blue).*

#### Member Roster

Creates custom grid lists for contact pages or working groups:

```markdown
{{< member-list title="Support Contact" >}}
    {{< member name="Alice Green" role="Coordinator" inst="Local Lab" icon="envelope" >}}
{{< /member-list >}}
```

*`icon` values correspond to FontAwesome classes.*

### Embedded Media

#### Video Player

Embeds responsive videos from YouTube, Vimeo, or local files:

```markdown
{{< video provider="youtube" src="https://www.youtube.com/watch?v=dQw4w9WgXcQ" >}}
{{< video provider="local" src="/img/promo-video.mp4" >}}
```

#### Interactive Maps

Embeds dynamic Google maps iframe maps:

```markdown
{{< map src="https://www.google.com/maps/embed?..." title="Venue Map" >}}
```

#### Calendars

Embeds responsive public calendars (e.g. Google Calendar):

```markdown
{{< calendar src="https://calendar.google.com/calendar/embed?..." >}}
```
