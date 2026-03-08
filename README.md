<div align="center">

# ⚡ Charge Master

### Vehicle Fuel & Energy Consumption Tracker

**A premium Progressive Web App (PWA) for tracking fuel and energy consumption of any vehicle — electric or combustion.**

![Version](https://img.shields.io/badge/version-1.0.0-blue?style=flat-square)
![PWA](https://img.shields.io/badge/PWA-ready-brightgreen?style=flat-square)
![License](https://img.shields.io/badge/license-MIT-orange?style=flat-square)
![Offline](https://img.shields.io/badge/offline-supported-success?style=flat-square)
![No dependencies](https://img.shields.io/badge/backend-none-lightgrey?style=flat-square)

</div>

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Features](#-features)
- [Screenshots & Interface](#-interface-overview)
- [Tech Stack](#-tech-stack)
- [Getting Started](#-getting-started)
- [Usage Guide](#-usage-guide)
- [Data Model](#-data-model)
- [PWA & Offline Support](#-pwa--offline-support)
- [Settings & Customization](#-settings--customization)
- [Data Management](#-data-management)
- [Charts & Visualizations](#-charts--visualizations)
- [Statistics & Analytics](#-statistics--analytics)
- [Keyboard & Gesture Navigation](#-keyboard--gesture-navigation)
- [Accessibility](#-accessibility)
- [Contributing](#-contributing)

---

## 🌟 Overview

**Charge Master** is a self-contained, single-file web application designed to help drivers of both **electric vehicles (BEV)** and **internal combustion engine (ICE)** vehicles track every refueling or charging session. It requires no server, no account, and no internet connection after the first load. All data is stored locally in the browser via `localStorage`.

> 💡 Whether you want to monitor electricity costs per kWh or gasoline costs per liter, Charge Master provides detailed insights, seasonal breakdowns, and beautiful charts — all in one lightweight HTML file.

---

## ✨ Features

### 🗂️ Record Management

- **➕ Add refueling/charging records** via a floating action button (FAB) that is always accessible.
- **✏️ Edit any existing record** by tapping on it in the list — the same form is reused for both creation and editing.
- **🗑️ Delete individual records** with an inline two-step confirmation to prevent accidental deletion.
- **📄 Paginated record list** — records are displayed 20 per page, sorted by date (newest first), with pagination controls at the bottom.
- **🏷️ Each record stores:**
  - 📅 Date of the refueling/charging session
  - 📏 Distance traveled since the last session (in Km)
  - 🔋 / ⛽ Energy or fuel quantity (kWh or Liters, switchable globally)
  - 💶 Unit price (€ per kWh or € per Liter)
  - 💰 Total cost (auto-computed in real time)
  - 📊 Cost per Km (auto-computed in real time)
  - 📍 Location / station name (optional free-text field, max 100 characters)
  - 📝 Notes (optional free-text field, max 500 characters with live character counter)
- **⚙️ Auto-computed fields:** As the user types quantity and unit price, the **Total Cost** and **Cost per Km** are calculated and displayed live inside the form, before saving.
- **✅ Form validation:** All required fields (date, distance, quantity, price) are validated on submission. Errors are shown inline with a shake animation on invalid fields.

### 🎨 Visual Design & UX

- **🌙 Dark Mode** (default) with a deep navy/charcoal palette (`#0A0E1A` background).
- **☀️ Light Mode** with a clean slate-gray palette, togglable at any time from the header or the Settings panel.
- **🎨 Season-colored record cards:** Each record card displays a colored left-border accent based on the season of its date:
  - ❄️ **Winter** (Dec, Jan, Feb) → Blue (`#60A5FA`)
  - 🌱 **Spring** (Mar, Apr, May) → Green (`#4ADE80`)
  - ☀️ **Summer** (Jun, Jul, Aug) → Yellow-green (`#D8FA3F`)
  - 🍂 **Autumn** (Sep, Oct, Nov) → Amber (`#FB923C`)
- **Cost-per-Km badge:** Each record card shows a color-coded efficiency badge:
  - 🟢 Green (`badge-good`) — cost/Km is below or equal to average
  - 🟡 Yellow (`badge-mid`) — cost/Km is between average and 120% of average
  - 🔴 Red (`badge-bad`) — cost/Km exceeds 120% of average
- **💡 Summary ribbon:** A persistent banner at the top of the Refueling tab always shows the **total number of records**, **total money spent (€)**, and **total kilometers** across all records.
- **📡 Empty state:** When no records exist, an illustrated placeholder is shown with a call-to-action button to add the first record, and the FAB pulses with a glow animation to draw attention.
- **🔔 Toast notifications:** Non-intrusive, auto-dismissing toast messages (max 3 at once) appear after every user action (save, delete, export, import), with ✓ / ✕ / ⚠ icons.
- **⚡ Ripple effect:** All interactive buttons and tappable elements feature a touch-responsive radial ripple animation for tactile feedback.
- **🃏 Card hover animations:** All cards lift slightly (`translateY(-2px)`) on hover with a shadow effect for depth.

### 📊 Statistics Tab

The Statistics tab provides an analytics view filtered by a user-selectable time period.

#### 🔍 Filter Bar

The filter can be set to one of three modes using a **segmented control**:
- **📅 Month** — select a specific month and year from dropdowns.
- **📆 Year** — select an entire year.
- **🗓️ Custom Range** — select any arbitrary start and end date via date pickers.

A badge displays the number of records matching the current filter in real time.

#### 📈 Key Statistics Cards

The statistics content is organized in a structured layout:

- **Total Records** — displayed alone in a full-width single-column card at the top of the section.
- **3-column price grid** immediately below, showing:
  - **▼ Min Price** — the lowest unit price recorded in the period.
  - **≈ Avg Price** — the average unit price.
  - **▲ Max Price** — the highest unit price.

These price values animate from zero to their final value using an **easeOutCubic** animation when the tab is opened or the filter changes.

#### ⛅ Seasonal Breakdown (collapsible)

An accordion-style section groups statistics by season. Each season card (Winter ❄️, Spring 🌱, Summer ☀️, Autumn 🍂) is individually collapsible and shows, if data exists for that season in the selected period:
- Minimum price
- Average price
- Maximum price

#### 📋 Additional Stats (2-column grid)

A secondary grid provides:
- 🛣️ **Total Distance** — sum of all Km covered.
- 🔋 / ⛽ **Total Energy / Fuel** — total kWh or Liters consumed.
- 💸 **Total Expense** — total euros spent.
- 💰 **Average Cost per Km** — average €/Km across the period.
- 📈 **Average Consumption** — kWh or L per 100 Km.
- 💹 **Efficiency** — average Km per unit (Km/kWh or Km/L).

### 📉 Charts Tab

Ten interactive charts powered by **Chart.js 4.4.1**, all themed to match the active dark/light mode and updated whenever the theme or filter changes.

| # | Chart | Type | Description |
|---|-------|------|-------------|
| 1 | **Distance Over Time** | Line | Km per session plotted over time, with a dashed **flat average** reference line. |
| 2 | **Unit Price Over Time** | Line | Price per unit plotted chronologically, with a dashed **5-point moving average** overlay. |
| 3 | **Cost per Km Over Time** | Line | Cost/Km per session plotted over time, with a dashed **flat average** reference line. |
| 4 | **Monthly Total Expenditure** | Horizontal Bar | Total € spent per calendar month (uses all records, not the filter), with bars color-coded by season. |
| 5 | **Average Price by Season** | Grouped Horizontal Bar | Side-by-side Min / Avg / Max price bars for each of the four seasons (uses all records). |
| 6 | **Average Consumption by Season** | Grouped Horizontal Bar | Side-by-side Min / Avg / Max consumption (kWh/100Km or L/100Km) per season (uses all records). |
| 7 | **Average Efficiency by Season** | Grouped Horizontal Bar | Side-by-side Min / Avg / Max efficiency (Km/kWh or Km/L) per season (uses all records). |
| 8 | **Average Cost per Km by Season** | Grouped Horizontal Bar | Side-by-side Min / Avg / Max €/Km per season (uses all records). |
| 9 | **Spending by Season** | Doughnut | Share of total expenditure per season (%), with legend and tooltips showing exact amounts and percentages. |
| 10 | **Refueling Frequency by Month** | Doughnut | Number of sessions per calendar month across all records, with a gradient color scheme. |

All charts feature:
- 800ms entrance animation with `easeInOutQuart` easing.
- Themed tooltips (dark/light backgrounds matching the app theme).
- Rajdhani and JetBrains Mono fonts for labels and tooltips.
- Gradient area fills on line charts.

---

## 🖥️ Interface Overview

```
┌──────────────────────────────────────────────┐
│  ⚡ CHARGE MASTER          ⚙  🌙  ℹ         │  ← Fixed header with Settings, Theme toggle, About
├──────────────────────────────────────────────┤
│                                              │
│  Records │ Total Spent │ Total Km            │  ← Summary ribbon (always visible)
│                                              │
│  ┌──────────────────────────────────────┐    │
│  │ ❄ 08/03/2026          🟢 €0.14/Km   │    │  ← Record card (season-colored border)
│  │ 🚩 320 Km | 🔋 55 kWh |  €0.25/kWh  │    │
│  │ € 13.75 💵     Shell Station A1 📍  │    │
│  └──────────────────────────────────────┘    │
│                                              │
│         [← Prev]  1  2  3  [Next →]          │  ← Pagination
│                                              │
│                                  [ + ]       │  ← Floating Action Button
├──────────────────────────────────────────────┤
│  🏠 Refueling │ 📊 Statistics │ 📈 Charts   │  ← Bottom tab bar
└──────────────────────────────────────────────┘
```

---

## 🛠️ Tech Stack

| Technology | Role |
|---|---|
| 🌐 **Vanilla HTML/CSS/JS** | The entire app is a single `index.html` file — no build step, no framework. |
| 📦 **Chart.js 4.4.1** | All charts (loaded from cdnjs CDN). |
| 💾 **localStorage** | Persistent client-side storage for records and settings. |
| 🔧 **Service Worker** | Enables offline functionality and PWA installability. |
| 🎨 **CSS Custom Properties** | Full theming system (dark/light) using CSS variables. |
| 🖋️ **Google Fonts** | Rajdhani (display), DM Sans (body), JetBrains Mono (data/mono). |
| 🔐 **Web Crypto API** | UUID v4 generation via `crypto.getRandomValues`. |

---

## 🚀 Getting Started

### Option A — Run locally (zero setup)

```bash
# Clone or download the repository
git clone https://github.com/your-username/charge-master.git

# Open the file directly in your browser
open index.html
# or on Windows:
start index.html
```

No npm install, no build step, no server required.

### Option B — Serve via a local HTTP server (recommended for PWA features)

```bash
# Using Python 3
python3 -m http.server 8080

# Using Node.js (npx)
npx serve .

# Using PHP
php -S localhost:8080
```

Then open `http://localhost:8080` in your browser.

### Option C — Deploy to a static host

Upload `index.html` to any static hosting service (GitHub Pages, Netlify, Vercel, Cloudflare Pages, etc.). No server-side configuration is needed.

---

## 📖 Usage Guide

### ➕ Adding a Record

1. Tap the **blue `+` floating button** (bottom-right corner).
2. A bottom sheet slides up with the entry form.
3. Fill in:
   - **Date** — the date of the refueling/charging session.
   - **Distance (Km)** — the kilometers traveled since the last session.
   - **Quantity** — energy (kWh) or fuel (Liters) consumed.
   - **Price per unit** — the price you paid per kWh or per Liter.
4. The **Total Cost** and **Cost per Km** fields update automatically as you type.
5. Optionally add a **Location** (e.g. station name) and **Notes**.
6. Tap **Save Record**. A toast confirms success and haptic feedback fires on mobile devices (`navigator.vibrate`).

### ✏️ Editing a Record

1. Tap anywhere on an existing record card.
2. The form opens pre-filled with the record's data.
3. Modify any field and tap **Save Record** to update.

### 🗑️ Deleting a Record

- **Single record:** Open the edit form → tap **Delete Record** → confirm in the inline confirmation prompt.
- **Via context menu:** Tap the **⋮** (three-dot) button on a record card → select **Delete** from the dropdown.
- **All records at once:** Open Settings → **Delete All Records** → confirm.

### 🔄 Switching Between Tabs

- Tap the **bottom tab bar** icons: Refueling 🏠 | Statistics 📊 | Charts 📈.
- Or **swipe left/right** anywhere in the content area (horizontal swipe > 50px, vertical displacement < 60px).

---

## 🗄️ Data Model

Each record is stored as a JSON object with the following fields:

```json
{
  "id": "uuid-v4-string",
  "date": "YYYY-MM-DD",
  "distanceKm": 320.0,
  "quantity": 55.20,
  "unitPrice": 0.2500,
  "totalCost": 13.80,
  "costPerKm": 0.0431,
  "location": "Shell Station A1",
  "notes": "Optional free-text notes",
  "createdAt": "ISO-8601 timestamp",
  "updatedAt": "ISO-8601 timestamp"
}
```

Records are stored in `localStorage` under the key `chargemaster_v1_records` as a JSON array. Settings are stored under `chargemaster_v1_settings`.

---

## 📡 PWA & Offline Support

Charge Master is a fully installable **Progressive Web App**:

- 📲 **Installable** — on mobile (iOS/Android) and desktop (Chrome/Edge), users can install it to the home screen or app launcher via the browser's "Add to Home Screen" / "Install" prompt.
- 🔌 **Offline-first** — two Service Worker registrations are active:
  - An **inline blob-based Service Worker** generated at runtime (`initPWA()`), which caches the app shell immediately on first load using a `chargemaster-v1` cache.
  - An **external Service Worker** (`/sw.js`) registered via a standard `navigator.serviceWorker.register('/sw.js')` call on the `window load` event, for environments where a physical `sw.js` file is deployed alongside the app.
- 🎨 **Themed status bar** — on iOS (via `apple-mobile-web-app-status-bar-style`) and Android (via `theme-color` meta tag), the status bar matches the app's dark/light theme.
- 🖼️ **Dynamic icons** — app icons (192×192 and 512×512) are generated programmatically on a `<canvas>` with the branded blue-to-cyan gradient and a lightning bolt symbol.
- 📄 **Web App Manifest** — dynamically injected at runtime with name, short name, description, orientation, colors, and icons.

---

## ⚙️ Settings & Customization

Access Settings via the **⚙ gear icon** in the top-right header.

### ⚡ / ⛽ Preferred Unit

Toggle between two tracking modes:
- **⚡ kWh** — for electric vehicles (BEV). Labels throughout the app update to show kWh, Km/kWh, and kWh/100Km.
- **⛽ Liters** — for ICE vehicles. Labels update to show L, Km/L, and L/100Km.

Changing the unit is instant and affects all labels, forms, statistics, and chart tooltips throughout the app.

### 🌙 / ☀️ Display Mode

Toggle between **Dark Mode** (default) and **Light Mode**. The toggle is available both in the Settings sheet and via the moon/sun icon in the header. Theme changes are saved to `localStorage` and re-applied on every subsequent load.

---

## 💾 Data Management

### 📤 Export

- Go to **Settings → Export All Records (JSON)**.
- A `.json` file named `chargemaster_export_YYYY-MM-DD.json` is automatically downloaded.
- The export envelope includes metadata: `exportedAt`, `appVersion`, `recordCount`, `unit`, and the full `records` array.

### 📥 Import

- Go to **Settings → Import Records from File**.
- Select a `.json` file previously exported from Charge Master (or a raw array of valid record objects).
- A dialog asks how to handle the import:
  - **⚠ Overwrite All** — replaces all existing records with the imported ones (irreversible).
  - **✓ Merge** — adds only records whose `id` is not already present in the local database (deduplication by UUID).
- If the file is malformed or contains no valid records, an error toast is shown.

### 🗑️ Delete All Records

- Go to **Settings → Delete All Records**.
- An inline confirmation prompt appears. Confirm to permanently erase all data from `localStorage`.

---

## 📉 Charts & Visualizations

All charts are rendered with **Chart.js 4.4.1** and re-rendered whenever the active filter changes or the theme is switched.

### Line Charts (Charts 1–3)

Each line chart includes:
- A **primary dataset** with gradient area fill and data-point dots.
- A **secondary reference line** (dashed), which is either a rolling moving average (Chart 2) or a flat mean (Charts 1 and 3).
- Responsive layout with `maintainAspectRatio: false` inside a 220px-tall container.

### Bar Charts (Charts 4–8)

- **Chart 4 (Monthly Expenditure):** Horizontal bars, one per month-year combination, colored by the season of that month. Uses **all records** regardless of the active filter to provide a complete spending history.
- **Chart 5 (Seasonal Price):** Grouped horizontal bar chart showing Min / Avg / Max unit prices per season, using all records.
- **Chart 6 (Seasonal Consumption):** Grouped horizontal bar chart showing Min / Avg / Max consumption (kWh/100Km or L/100Km) per season, using all records.
- **Chart 7 (Seasonal Efficiency):** Grouped horizontal bar chart showing Min / Avg / Max efficiency (Km/kWh or Km/L) per season, using all records.
- **Chart 8 (Seasonal Cost per Km):** Grouped horizontal bar chart showing Min / Avg / Max €/Km per season, using all records.

### Doughnut Charts (Charts 9–10)

- **Chart 9 (Season Spending):** Cutout doughnut (65% inner radius) showing the percentage of total expenditure per season. Tooltips show absolute € amounts and percentages.
- **Chart 10 (Monthly Frequency):** Doughnut showing how many sessions occurred in each calendar month across all records. Colors use an HSL gradient from cool blue to warm blue.

All charts are properly destroyed (`chart.destroy()`) before being re-created to prevent memory leaks and canvas conflicts.

---

## 🖐️ Keyboard & Gesture Navigation

| Interaction | Action |
|---|---|
| 👆 Tap record card | Open edit form |
| 👆 Tap `+` FAB | Open add record form |
| 👆 Tap tab bar buttons | Switch active tab |
| 👆 Tap header logo | Scroll active panel to top |
| 👆 Tap modal backdrop | Close the modal |
| 👉 Swipe left (content area) | Navigate to the next tab |
| 👈 Swipe right (content area) | Navigate to the previous tab |
| 📳 Haptic feedback | Vibration on save (`50ms`) and delete (`50–30–50ms`) |

Tab switching uses smooth CSS transitions: the outgoing panel slides to the left (`translateX(-30px)`) while the incoming panel slides in from the right (`translateX(30px)`), with 280ms cubic-bezier easing.

---

## ♿ Accessibility

- All interactive elements include `aria-label` attributes for screen readers.
- Bottom tab bar buttons use `role="tab"` and `aria-selected` to communicate the active tab.
- Modal overlays use `role="dialog"` and `aria-modal="true"`.
- The toast container uses `aria-live="polite"` for screen reader announcements.
- Season accordion headers use `aria-expanded` to communicate open/closed state.
- Form inputs include explicit `<label>` associations and `aria-label` attributes.
- Color is never the sole indicator of meaning (badges also differ in value context; seasons also have icon labels).

---

## 🤝 Contributing

Contributions, issues, and feature requests are welcome!

1. Fork the repository.
2. Create a feature branch: `git checkout -b feature/my-feature`
3. Commit your changes: `git commit -m 'Add my feature'`
4. Push to the branch: `git push origin feature/my-feature`
5. Open a Pull Request.

Since the entire app is a single `index.html`, there is no build system to configure.

---

## 📜 License

Distributed under the **MIT License**. Feel free to use, modify, and share.

---

> 🌱 *Developed for the sustainable mobility community. If you find this tool helpful, please leave a ⭐ on GitHub!*
