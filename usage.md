# Hugo Conference Template Customization and Usage Guide

This document details how to configure the site metadata, manage key database features, and use custom shortcodes to edit page contents.

---

## 1. Central Configuration (hugo.toml)

Manage all core database items and layout styles in `hugo.toml` located in the project root.

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
  registration_link = "/register/register-conf/"
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
  announcement_link = "/about/about-welcome/"
```

### Important Dates Database

Configures the milestones listed in the dynamic timeline page.

* **type options**: `"info"` (blue bubble), `"open"` (green bubble), `"close"` (red bubble for deadlines).
* **season** (optional): If configured as a string (e.g. `"Late Summer, 2027"`), the template displays this value in place of the specific date. The `date` parameter must still be present to govern sorting and automatic expiration styling.
* Past dates are marked automatically as outdated based on the build timestamp.

```toml
[[params.important_dates]]
  date = "2027-12-30T23:59:59+09:00"
  season = "Late Summer, 2027" # optional
  title = "Abstract Submission Deadline"
  type = "close"
  description = "Abstract submission closes strictly at 23:59 JST. No late submissions accepted."
```

### News and Announcements Feed

The three newest entries will display featured preview cards on the homepage:

```toml
[[params.news]]
  date = "2026-05-15T00:00:00+09:00"
  title = "Exhibitor Prospectus Now Available"
  summary = "The exhibitor and sponsorship prospectus for IPAC'28 is now available..."
  link = "/exhibitor/exhibitor-info/"
```

### Tiered Sponsors

Renders sponsor graphics grouped by hierarchy tiers:

* **tier options**: `"platinum"`, `"gold"`, `"silver"`, `"bronze"`, `"supporter"`.

```toml
[[params.sponsors]]
  name = "Quantum Dynamics Corp."
  logo = "/img/ipac28_600x304.png"
  url = "https://example.com/quantum-dynamics"
  tier = "platinum"
  tagline = "Pioneering the next generation of particle accelerator power systems."
```

### Organizing Committees

Configures rosters for different committee groups:

* Set `chair = true` to highlight the member at the top of the roster.
* For `committee_sab` members, group them by setting `region` to `"America"`, `"Asia"`, or `"Europe"`. The template handles tab categorization automatically.

```toml
[[params.committee_ioc]]
  name = "Tadashi"
  surname = "Koseki"
  institution = "KEK"
  location = "Japan"
  desc = "IOC Chair"
  chair = true
```

---

## 2. Interactive Markdown Shortcodes

Include these components directly inside any page content Markdown file.

### Dynamic Content Shortcodes

#### timelines (`full-dates-timeline`)

Renders the timeline compiled dynamically from `[[params.important_dates]]`.

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
