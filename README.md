<div align="center">

# ⚡ Charge Master

### Vehicle Fuel & Energy Consumption Tracker

**A premium Progressive Web App (PWA) for tracking fuel and energy consumption of any vehicle — electric or combustion. Supports multiple vehicle profiles, each with independent records and unit preferences.**

![Version](https://img.shields.io/badge/version-1.1.0-blue?style=flat-square)
![PWA](https://img.shields.io/badge/PWA-ready-brightgreen?style=flat-square)
![License](https://img.shields.io/badge/license-MIT-orange?style=flat-square)
![Offline](https://img.shields.io/badge/offline-supported-success?style=flat-square)
![No dependencies](https://img.shields.io/badge/backend-none-lightgrey?style=flat-square)

</div>

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Features](#-features)
- [Interface Overview](#-interface-overview)
- [Tech Stack](#-tech-stack)
- [Getting Started](#-getting-started)
- [Usage Guide](#-usage-guide)
- [Data Model](#-data-model)
- [PWA & Offline Support](#-pwa--offline-support)
- [Settings & Customization](#-settings--customization)
- [Data Management](#-data-management)
- [About Modal](#ℹ️-about-modal)
- [Efficiency Progress Bar](#-efficiency-progress-bar)
- [Charts & Visualizations](#-charts--visualizations)
- [Visual Design System](#-visual-design-system)
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

### 🚗 Vehicle Profile Management

- **🏎️ Multiple vehicle profiles** — register as many vehicles as needed, each with a custom name and preferred measurement unit.
- **➕ Add a new vehicle** via the **"+ Add Vehicle"** button in the Vehicles tab header, or via the empty-state call-to-action when no vehicles exist yet.
- **✏️ Edit any vehicle** — tap the edit (pencil) icon on a vehicle card to update its name or switch its unit preference between kWh and Liters.
- **✅ Select the active vehicle** — tap the checkmark button on any vehicle card to make it the active profile. The active vehicle card is highlighted with a blue border accent. All refueling records, statistics, and charts shown throughout the app are scoped to the active vehicle.
- **🗑️ Delete a vehicle** — tap the trash icon on a vehicle card. An inline confirmation prompt appears, warning how many associated records will also be deleted. Deletion removes both the vehicle profile and all its linked records.
- **📊 Record count badge** — each vehicle card displays the number of refueling/charging records associated with it (e.g. `🔋 BEV · 12 records`).
- **📡 Empty state** — when no vehicles have been added yet, a full illustrated placeholder is shown with a "Add Vehicle" call-to-action button.
- **🔔 Toast notifications** — every vehicle action (add, update, select, delete) fires a non-intrusive toast notification.

#### Vehicle Profile Form

The vehicle form is presented as a bottom sheet modal with the following fields:

| Field | Description |
|---|---|
| **Name** | Free-text name for the vehicle (max 60 characters). Required. |
| **Preferred Unit** | Toggle pill: **🔋 kWh** (electric) or **⛽ Liters** (combustion). Determines labels across records, statistics, and charts for this vehicle. |
| **🔋 Battery Capacity (kWh)** | *(BEV only, optional)* — the total usable battery capacity in kWh. Used to compute absolute **effective energy consumed** and **Final SoC %** from the refueled quantity. If left empty, a full refuel is assumed. Shown only when the unit is `kWh`. |
| **⛽ Tank Capacity (L)** | *(ICE only, optional)* — the total fuel tank capacity in liters. Used to compute absolute **effective fuel consumed** and **Final Fuel Level %** from the refueled quantity. If left empty, a full refuel is assumed. Shown only when the unit is `L`. |

#### Data Isolation per Vehicle

Every refueling/charging record is linked to a specific vehicle via a `vehicleId` field. When a vehicle is selected as active:

- The **Refueling tab** shows only records belonging to that vehicle.
- The **Summary ribbon** (total records, total spent, total Km) reflects only that vehicle's data.
- The **Statistics tab** computes all metrics (min/avg/max price, total distance, average consumption, etc.) using only that vehicle's records.
- The **Charts tab** renders all 12 charts using only that vehicle's records.

Switching the active vehicle instantly updates all tabs and charts.

#### Migration from Pre-Profile Versions

When loading a data file exported from Charge Master v1.0.0 (before vehicle profiles were introduced), the app automatically detects that no `vehicles` array is present and performs a one-time migration:

- A `"Default"` vehicle profile is created with `unit: 'kWh'`.
- All existing records are assigned to this default profile via `vehicleId`.
- No data is lost.

The same migration logic applies when importing a legacy JSON export file.

---

### 🗂️ Record Management

- **➕ Add refueling/charging records** via a floating action button (FAB) that is always accessible. If no vehicle is selected, the app redirects to the Vehicles tab with a warning toast.
  - **🔵 FAB Pulse Animation** — When no records exist for the active vehicle, the FAB pulses with an infinite `fabPulse` animation (glowing box-shadow oscillation) to draw attention. The pulse is removed once records are added.
  - **👁️ FAB Visibility Toggle** — The FAB is **hidden** on the Vehicles tab (`display: none`) and **visible** on all other tabs (Refueling, Statistics, Charts).
- **✏️ Edit any existing record** by tapping on it in the list — the same form is reused for both creation and editing.
- **📋 Duplicate any existing record** via the **Duplicate Record** button available in the edit form. See [Duplicating a Record](#-duplicating-a-record) for full details.
- **🗑️ Delete individual records** with an inline two-step confirmation to prevent accidental deletion.
- **📄 Paginated record list** — records are displayed 20 per page, sorted by date (newest first). When two records share the same date, they are further sorted by their `createdAt` timestamp (newest first), ensuring stable and deterministic ordering. Pagination controls are shown at the bottom.
- **🏷️ Each record stores:**
  - 📅 Date of the refueling/charging session
  - 📏 Distance traveled since the last session (in Km)
  - 🔋 / ⛽ Energy or fuel quantity (kWh or Liters, per the active vehicle's unit)
  - 💶 Unit price (€ per kWh or € per Liter)
  - 💰 Total cost (auto-computed in real time)
  - 📊 Cost per Km (auto-computed in real time)
  - 🔌 Initial SoC / Fuel Level % *(optional)*
  - 🔋 Final SoC / Fuel Level % *(auto-computed when capacity is set)*
  - ⚡ Transfer Efficiency % *(BEV only, editable; ICE fixed at 100%)*
  - ⚡ Effective Energy / Fuel Consumed *(derived from SoC delta and vehicle capacity)*
  - 📍 Location / station name (optional free-text field, max 100 characters)
  - 📝 Notes (optional free-text field, max 500 characters with live character counter)
- **⚙️ Auto-computed fields:** As the user types quantity and unit price, the **Total Cost** and **Cost per Km** are calculated and displayed live inside the form, before saving.
- **✅ Form validation:** All required fields (date, distance, quantity, price) are validated on submission. Errors are shown inline with a shake animation on invalid fields. An additional validation prevents the **Final SoC exceeding 100%** when battery capacity is set.

---

### 🔌 State of Charge (SoC) & Fuel Level Tracking

This feature lets users record the battery or tank level before and after each session, unlocking more accurate energy consumption metrics.

#### 📋 Form Fields

When adding or editing a record, the following fields are available (shown for all vehicle types, with labels adjusted per unit):

| Field | Label (BEV) | Label (ICE) | Description |
|---|---|---|---|
| **Initial Level** | Initial SoC (%) | Initial Fuel Level (%) | The battery or tank charge level **before** refueling/charging, expressed as a percentage (0–100). Optional. |
| **Final Level** | Final SoC (%) | Final Fuel Level (%) | Auto-computed from the initial level, quantity, transfer efficiency, and vehicle capacity. Read-only. |
| **Transfer Efficiency** | Transfer Efficiency (%) | Transfer Efficiency (%) | For **BEV**: the fraction of the charged energy that is actually stored in the battery (default 100%, editable). For **ICE**: always fixed at 100% (read-only, cursor `not-allowed`). |

#### ⚡ Transfer Efficiency (BEV)

The Transfer Efficiency field accounts for charging losses in electric vehicles. It is:
- **Editable** for BEV vehicles (default 100%).
- **Fixed at 100%** and read-only for ICE vehicles (no charging losses apply to fuel).
- Persisted per record in the `transferEfficiency` field.
- Restored correctly when editing or duplicating a record.

The effective energy actually delivered to the battery is computed as:

```
effectiveQuantity = quantity × (transferEfficiency / 100)
```

#### 🔢 Final SoC / Fuel Level Auto-Computation

When the user provides an **Initial Level %** and the vehicle has a **Battery/Tank Capacity** set:

```
effectiveQtyAbsolute = quantity × (transferEfficiency / 100)
addedPct             = (effectiveQtyAbsolute / capacity) × 100
finalSoC             = clamp(initialSoC + addedPct, 0, 100)
```

The computed **Final SoC %** is displayed in the read-only `Final SoC (%)` field and updates live as the user types. If capacity is not set, the field is left blank.

#### 🔗 Previous Final SoC Chaining (`getPreviousFinalSoC`)

The app automatically looks up the **most recent previous session** (for the same vehicle) to determine the starting state before the current refueling began. This enables tracking how much energy was consumed *between* sessions:

- Records are sorted chronologically (ascending by date, then by `createdAt`).
- The previous record is the one immediately before the current one by date/time.
- If no previous record exists, the app assumes **100% (full)** as the baseline.
- This value is displayed in the form as **"Previous Final SoC"** (or "Previous Final Fuel Level" for ICE).

#### ⚡ Effective Energy / Fuel Consumed

The effective energy consumed between the previous session and the current one is computed from the SoC delta:

```
consumedPct      = previousFinalSoC% − initialSoC%
effectiveConsumed = (consumedPct / 100) × capacity    // in kWh or L
```

- If vehicle capacity is set, the result is shown in absolute units (e.g. `18.40 kWh` or `12.30 L`).
- If capacity is not set, the result is shown as a percentage delta (e.g. `15.0 %`).
- A `⚠` warning is appended if the result is negative (indicating an inconsistency in the entered SoC values).
- The computed value is displayed in the form as **"Effective Energy Used"** (BEV) or **"Effective Fuel Consumed"** (ICE), and is stored in the record as `effectiveEnergyConsumed`.

#### 📊 Charge Progress Bar (in form)

A visual progress bar is shown inside the form whenever both **Initial SoC %** and **Final SoC %** are known:

```
[■■■■■■■■░░░░░░░░░░░░] Initial: 20% ▶ Final: 75%  |  Charged: +55%
```

- The bar fill starts at `initialSoC%` and extends to `finalSoC%` within a full-width track.
- The fill uses a blue-to-cyan-to-green gradient (`#3B82F6 → #06B6D4 → #10B981`) with a glow shadow.
- Labels show the initial and final SoC percentages and the delta (`+N%`).
- The bar is hidden when either SoC value is unavailable or the SoC row is hidden.
- Updates live as the user types quantity, initial SoC, or transfer efficiency.

#### 🧩 Record Card: "Consumed" Chip

When a record has `effectiveEnergyConsumed` stored, an additional stat chip is shown on the record card in the Refueling list:

```
⚡ Consumed: 18.40 kWh    (BEV)
⛽ Consumed: 12.30 L      (ICE)
```

This chip is displayed **in addition to** the standard Refueled chip, giving a clear side-by-side view of what was put in vs. what was actually consumed between sessions.

#### 🔢 Impact on Statistics & Charts

When `effectiveEnergyConsumed` is available for a record, it is used **in place of `quantity`** for the following computed metrics:

- **Average Consumption** (Stats tab) — uses `effectiveEnergyConsumed / distanceKm × 100` instead of `quantity / distanceKm × 100`.
- **Efficiency** (Stats tab) — uses `distanceKm / effectiveEnergyConsumed` instead of `distanceKm / quantity`.
- **Efficiency Progress Bar** (record card) — uses `distanceKm / effectiveEnergyConsumed` as the `kmpu` value.
- **Consumption Over Time chart** — uses `effectiveEnergyConsumed` when available.
- **Efficiency Over Time chart** — uses `effectiveEnergyConsumed` when available.

If `effectiveEnergyConsumed` is `null` (SoC fields not filled or capacity not set), all computations fall back to `effectiveQuantity` (i.e. `quantity × transferEfficiency / 100`), and then to `quantity` as the ultimate fallback.

#### ✅ SoC Overflow Validation

A dedicated validation rule prevents saving a record that would cause the **Final SoC to exceed 100%** given the entered quantity and battery capacity:

```
projectedFinalSoC = initialSoC% + (quantity × transferEfficiency/100 / capacity × 100)

if projectedFinalSoC > 100:
  → error: "Refuel value too high: 'Final SoC' would reach X%. Max allowed: Y"
```

The **Quantity** field is highlighted in red with a shake animation, and the error message includes both the projected overflow value and the maximum allowed quantity. The save is blocked until the quantity is corrected.

---

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
- **📊 Efficiency progress bar:** Each record card shows a thin colored bar along its bottom edge that visually encodes the Km/kWh (or Km/L) efficiency of that session relative to all sessions of the active vehicle. See [Efficiency Progress Bar](#-efficiency-progress-bar) for the full specification.
- **📊 Inline stat chips on record cards:** Each record card displays stat chips in a row:
  - 🚩 **Distance** — kilometers traveled for that session.
  - 🔋 / ⛽ **Refueled** — energy (kWh) or fuel (Liters) put in.
  - ♻️ **Efficiency** — Km per unit (Km/kWh or Km/L), computed using `effectiveEnergyConsumed` when available.
  - ⚡ / ⛽ **Consumed** *(conditional)* — effective energy or fuel consumed between sessions, shown only when `effectiveEnergyConsumed` is stored on the record.
- **💡 Summary ribbon:** A persistent banner at the top of the Refueling tab always shows the **total number of records**, **total money spent (€)**, and **total kilometers** for the active vehicle.
- **📡 Empty state:** When no records exist for the active vehicle, an illustrated placeholder is shown. If no vehicle is selected at all, the empty state instead prompts the user to navigate to the Vehicles tab.
- **🔔 Toast notifications:** Non-intrusive, auto-dismissing toast messages (max 3 at once) appear after every user action (save, delete, export, import, vehicle changes), with ✓ / ✕ / ⚠ icons.
- **⚡ Ripple effect:** All interactive buttons and tappable elements feature a touch-responsive radial ripple animation for tactile feedback.
- **🃏 Card hover animations:** All cards lift slightly (`translateY(-2px)`) on hover with a shadow effect for depth.

### 📊 Efficiency Progress Bar

Each record card displays a **thin colored horizontal bar** anchored to the bottom edge of the card. This bar encodes the **Km/kWh** (electric vehicles) or **Km/L** (combustion vehicles) efficiency of that individual session, positioned and colored relative to the full efficiency range across **all records of the active vehicle**.

#### How It Works

1. **Efficiency value** — For each record, the per-session efficiency is computed as:
   ```
   effectiveE = effectiveEnergyConsumed ?? effectiveQuantity ?? quantity
   kmpu = distanceKm / effectiveE          // e.g. 320 Km / 55 kWh = 5.82 Km/kWh
   ```
   If the effective energy is zero (edge case), `kmpu` defaults to `0`.

2. **Global range** — Before rendering any card, the function scans all sorted records of the active vehicle and computes:
   ```
   globalMinKpu = Math.min(...allKmPerUnit)
   globalMaxKpu = Math.max(...allKmPerUnit)
   kpuRange     = globalMaxKpu - globalMinKpu
   ```

3. **Bar width percentage** — The bar width is a normalized 0–100% position of the record's efficiency within the global range:
   ```
   effPct = kpuRange > 0
     ? Math.round(((kmpu - globalMinKpu) / kpuRange) * 100)
     : 50    // fallback when all records have identical efficiency
   ```
   - `effPct = 0` → lowest efficiency session (bar is barely visible).
   - `effPct = 100` → best efficiency session (bar spans the full card width).
   - `effPct = 50` → default when there is no variance (single record or all identical).

4. **Color interpolation** — The bar color transitions smoothly across a **red → orange → green** gradient via two linear interpolations:
   - **Lower half** (`effPct` 0–50): interpolates between **red** (`rgb(239, 68, 68)`) and **orange-yellow** (`rgb(245, 158, 11)`).
     ```
     t      = effPct / 50
     r      = 239 + (245 - 239) * t   →  239 … 245
     g      =  68 + (158 -  68) * t   →   68 … 158
     b      =  68 + ( 11 -  68) * t   →   68 …  11
     ```
   - **Upper half** (`effPct` 51–100): interpolates between **orange-yellow** (`rgb(245, 158, 11)`) and **green** (`rgb(16, 185, 129)`).
     ```
     t      = (effPct - 50) / 50
     r      = 245 + ( 16 - 245) * t   →  245 …  16
     g      = 158 + (185 - 158) * t   →  158 … 185
     b      =  11 + (129 -  11) * t   →   11 … 129
     ```

   The resulting color spectrum at a glance:

   | `effPct` | Color | Meaning |
   |---|---|---|
   | 0 | 🔴 Red (`#EF4444`) | Lowest efficiency in the dataset |
   | 25 | 🔴🟠 Red-Orange | Below-average efficiency |
   | 50 | 🟡 Yellow (`#F59E0B`) | Mid-range efficiency |
   | 75 | 🟡🟢 Yellow-Green | Above-average efficiency |
   | 100 | 🟢 Green (`#10B981`) | Best efficiency in the dataset |

5. **Tooltip** — Hovering over (or long-pressing on mobile) the bar shows a native HTML `title` tooltip:
   ```
   Efficiency: 5.82 Km/kWh (73% of range)
   ```

#### Interaction With Filtering and Pagination

- The efficiency range (`globalMinKpu` / `globalMaxKpu`) is computed over **all records of the active vehicle** that match the current sort order, **not just the current page**. This ensures bar widths are consistent as the user pages through records.
- When the active vehicle is switched, the range is fully recomputed against the new vehicle's record set.
- When only a single record exists (or all records share the same efficiency value), `kpuRange` equals `0` and every bar defaults to `50%` width and amber color to avoid a division-by-zero rendering artifact.

---

### 📊 Statistics Tab

The Statistics tab provides an analytics view filtered by a user-selectable time period, scoped to the **active vehicle profile**.

#### 🔍 Filter Bar

The filter can be set to one of three modes using a **segmented control**:
- **📅 Month** — select a specific month and year from dropdowns.
- **📆 Year** — select an entire year.
- **🗓️ Custom Range** — select any arbitrary start and end date via date pickers.

A badge displays the number of records matching the current filter in real time.

#### 📈 Key Statistics Cards

The statistics content is organized in a structured layout:

- **3-column price grid** showing:
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
- 📈 **Average Consumption** — kWh or L per 100 Km, using `effectiveEnergyConsumed` when available.
- 💹 **Efficiency** — average Km per unit (Km/kWh or Km/L), using `effectiveEnergyConsumed` when available.

### 📉 Charts Tab

Twelve interactive charts powered by **Chart.js 4.4.1**, all themed to match the active dark/light mode and scoped to the **active vehicle profile**. Charts update whenever the theme, filter, or active vehicle changes.

| # | Chart | Type | Description |
|---|-------|------|-------------|
| 1 | **Distance Over Time** | Line | Km per session plotted over time, with a dashed **flat average** reference line. |
| 2 | **Unit Price Over Time** | Line | Price per unit plotted chronologically, with a dashed **5-point moving average** overlay. |
| 3 | **Cost per Km Over Time** | Line | Cost/Km per session plotted over time, with a dashed **flat average** reference line. |
| 4 | **Consumption Over Time** | Line | Energy or fuel consumption per session (kWh/100Km or L/100Km) plotted over time, with a dashed **flat average** reference line. Color: amber (`#FB923C`). Uses `effectiveEnergyConsumed` when available. |
| 5 | **Efficiency Over Time** | Line | Per-session efficiency (Km/kWh or Km/L) plotted over time, with a dashed **flat average** reference line. Color: purple (`#A855F7`). Uses `effectiveEnergyConsumed` when available. |
| 6 | **Monthly Total Expenditure** | Horizontal Bar | Total € spent per calendar month (uses all vehicle records, not the filter), with bars color-coded by season. |
| 7 | **Average Price by Season** | Grouped Horizontal Bar | Side-by-side Min / Avg / Max price bars for each of the four seasons (uses all vehicle records). |
| 8 | **Average Consumption by Season** | Grouped Horizontal Bar | Side-by-side Min / Avg / Max consumption (kWh/100Km or L/100Km) per season (uses all vehicle records). |
| 9 | **Average Efficiency by Season** | Grouped Horizontal Bar | Side-by-side Min / Avg / Max efficiency (Km/kWh or Km/L) per season (uses all vehicle records). |
| 10 | **Average Cost per Km by Season** | Grouped Horizontal Bar | Side-by-side Min / Avg / Max €/Km per season (uses all vehicle records). |
| 11 | **Spending by Season** | Doughnut | Share of total expenditure per season (%), with legend and tooltips showing exact amounts and percentages. |
| 12 | **Refueling Frequency by Month** | Doughnut | Number of sessions per calendar month across all vehicle records, with a gradient color scheme. |

All charts feature:
- 800ms entrance animation with `easeInOutQuart` easing.
- Themed tooltips (dark/light backgrounds matching the app theme).
- Rajdhani and JetBrains Mono fonts for labels and tooltips.
- Gradient area fills on line charts.

---

## 🖥️ Interface Overview

```
┌──────────────────────────────────────────────┐
│  ⚡ CHARGE MASTER               ⚙️  🌙  ℹ️ │  ← Fixed header with Settings, Theme toggle, About
├──────────────────────────────────────────────┤
...                                          ...
│  🚗 My Vehicles                         [+] │  ← Vehicles tab header with Add button
│                                              │
│  ┌──────────────────────────────────────┐    │
│  │ 🚗  Tesla Model S                   │    │  ← Vehicle card (active, blue border)
│  │     🔋 kWh · 24 records              │    │
│  │                        [✓] [✏] [🗑] │    │
│  └──────────────────────────────────────┘    │
│  ┌──────────────────────────────────────┐    │
│  │ 🚗  Fiat Punto                      │    │  ← Inactive vehicle card
│  │     ⛽ Liters · 8 records            │    │
│  │                         [ ] [✏] [🗑] │    │
│  └──────────────────────────────────────┘    │
...                                          ...
│             Tesla Model S (BEV)              │
│     [ Records | Total Spent | Total Km ]     │  ← Summary ribbon (Refueling tab)
│                                              │
│  ┌──────────────────────────────────────┐    │
│  │ 08/03/2026               🟢 €0.14/Km │    │  ← Record card (season-colored border)
│  │ 🚩 320Km | ♻️ 5.82Km/kWh             │    │
│  │ 🔋 Refueled: 55kWh | ⚡ Consumed: 18kWh│  │  ← Refueled + Consumed chips
│  │ € 13.75 💵  Shell Station 📍     📋  │    │
│  │                                      │    │
│  │▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓░░░░░░░░░░░░     │    │  ← Efficiency bar (green ~73%)
│  └──────────────────────────────────────┘    │
│         [← Prev]  1  2  3  [Next →]          │  ← Pagination
...                                          ...
│                                  [ + ]       │  ← Floating Action Button
├──────────────────────────────────────────────┤
│ 🚗 Veh. │ ⛽ Refuel. │ 📊 Stats │ 📈 Charts │  ← Bottom tab bar (4 tabs)
└──────────────────────────────────────────────┘
```

---

## 🛠️ Tech Stack

| Technology | Role |
|---|---|
| 🌐 **Vanilla HTML/CSS/JS** | The entire app is a single `index.html` file — no build step, no framework. |
| 📦 **Chart.js 4.4.1** | All charts (loaded from cdnjs CDN). |
| 💾 **localStorage** | Persistent client-side storage for records, vehicle profiles, and settings. |
| 🔧 **Service Worker** | Enables offline functionality and PWA installability. |
| 🎨 **CSS Custom Properties** | Full theming system (dark/light) using CSS variables. |
| 🖋️ **Google Fonts** | Rajdhani (display), DM Sans (body), JetBrains Mono (data/mono). |
| 🔐 **Web Crypto API** | UUID v4 generation via `crypto.getRandomValues`. |

---

## 🚀 Getting Started

### Option A — Run locally (zero setup)

```bash
# Clone or download the repository
git clone https://github.com/GianfrancoPiazzolla/Charge-Master.git

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

### 🚗 Setting Up Your First Vehicle

Before adding any refueling records, you need at least one vehicle profile. On first launch, the app opens on the **Vehicles tab** with an empty state.

1. Tap **+ Add Vehicle** (empty-state button or the `+` icon in the section header).
2. Enter a **Name** for the vehicle (e.g. "Tesla Model S", "Fiat Punto").
3. Select the **Preferred Unit**: 🔋 **kWh** for electric vehicles, ⛽ **Liters** for combustion vehicles.
4. *(Optional)* Enter the **Battery Capacity (kWh)** (BEV) or **Tank Capacity (L)** (ICE) to enable SoC-based consumption tracking. See [SoC & Fuel Level Tracking](#-state-of-charge-soc--fuel-level-tracking).
5. Tap **Save Vehicle**. The vehicle is created and automatically set as the active profile.

### ✅ Switching the Active Vehicle

1. Open the **Vehicles tab** from the bottom tab bar.
2. Tap the **checkmark button** on any vehicle card.
3. A success toast confirms the selection. All tabs (Refueling, Statistics, Charts) immediately update to show data for the newly selected vehicle.

### ✏️ Editing a Vehicle

1. Open the **Vehicles tab**.
2. Tap the **pencil icon** on the vehicle card you want to edit.
3. Modify the name, preferred unit, or vehicle capacity, then tap **Save Vehicle**.

### 🗑️ Deleting a Vehicle

1. Open the **Vehicles tab**.
2. Tap the **trash icon** on the vehicle card.
3. An inline confirmation prompt appears, indicating how many associated records will also be permanently deleted.
4. Tap **Delete** to confirm, or **Cancel** to abort.

> ⚠️ Deleting a vehicle removes all its linked refueling records. This action is irreversible.

### ➕ Adding a Record

1. Make sure the desired vehicle is set as active (see [Switching the Active Vehicle](#-switching-the-active-vehicle)).
2. Tap the **blue `+` floating button** (bottom-right corner). If no vehicle is active, the app redirects to the Vehicles tab with a warning.
3. A bottom sheet slides up with the entry form.
4. Fill in:
   - **Date** — the date of the refueling/charging session.
   - **Distance (Km)** — the kilometers traveled since the last session.
   - **Quantity** — energy (kWh) or fuel (Liters) consumed, based on the active vehicle's unit.
   - **Price per unit** — the price you paid per kWh or per Liter.
5. The **Total Cost** and **Cost per Km** fields update automatically as you type.
6. *(Optional)* Fill in the **SoC / Fuel Level** section:
   - Enter **Initial SoC %** (or Initial Fuel Level % for ICE) to record the state before charging/refueling.
   - The **Final SoC %** is auto-computed if the vehicle has a capacity set.
   - For BEV vehicles, adjust the **Transfer Efficiency %** if needed (default 100%).
   - The **Charge Progress Bar** appears and updates live.
   - The **Effective Energy / Fuel Consumed** indicator shows how much energy was drawn from the battery between sessions.
7. Optionally add a **Location** (e.g. station name) and **Notes**.
8. Tap **Save Record**. A toast confirms success and haptic feedback fires on mobile devices (`navigator.vibrate`).

### ✏️ Editing a Record

1. Tap anywhere on an existing record card.
2. The form opens pre-filled with the record's data, including all SoC/fuel level fields.
3. Modify any field and tap **Save Record** to update.

### 📋 Duplicating a Record

The **Duplicate Record** feature allows quickly creating a new entry pre-filled from an existing one, without altering the original.

1. Tap an existing record card to open the edit form.
2. Tap the **Duplicate Record** button (visible only in edit mode).
3. The form transitions in place — without closing and reopening the modal — into a **new-record state**:
   - The modal title changes to **"Duplicate Record"**.
   - The record **`id`** field is cleared so that saving will create a brand-new entry.
   - **Date** is reset to today's date.
   - **Distance** and **Quantity** are cleared — the user must fill these in.
   - **Unit Price**, **Location**, and **Notes** are carried over from the original record (all editable).
   - **Transfer Efficiency** is carried over from the original record for BEV vehicles (editable); fixed at 100% for ICE.
   - A hidden `createdAt` field is set to the current timestamp so the new record is correctly timestamped.
4. Edit any fields as needed and tap **Save Record** to persist the new entry.
5. A `warning` toast ("Record duplicated — edit the fields and save") is shown immediately after the duplication is initiated.

> 💡 The duplicate workflow intentionally leaves Distance and Quantity empty to force the user to enter the values specific to the new session, while preserving contextual data (price, location, notes, transfer efficiency) that is likely to repeat.

### 🗑️ Deleting a Record

- **Single record:** Open the edit form → tap **Delete Record** → confirm in the inline confirmation prompt.
- **Via context menu:** Tap the **⋮** (three-dot) button on a record card → select **Delete** from the dropdown.
- **All records at once:** Open Settings → **Delete All Records** → confirm.

### 🔄 Switching Between Tabs

- Tap the **bottom tab bar** icons: Vehicles 🚗 | Refueling ⛽ | Statistics 📊 | Charts 📈.
- Or **swipe left/right** anywhere in the content area (horizontal swipe > 50px, vertical displacement < 60px).

---

## 🗄️ Data Model

### Vehicle Profile

Each vehicle profile is stored as a JSON object under the `chargemaster_v1_vehicles` localStorage key:

```json
{
  "id": "uuid-v4-string",
  "name": "Tesla Model S",
  "unit": "kWh",
  "batteryCapacityKwh": 75.0,
  "tankCapacityL": null
}
```

| Field | Type | Description |
|---|---|---|
| `id` | string | UUID v4 — unique identifier for the vehicle. |
| `name` | string | Display name of the vehicle (max 60 characters). |
| `unit` | `"kWh"` \| `"L"` | Preferred measurement unit. Drives all labels, statistics, and chart axes for this vehicle. |
| `batteryCapacityKwh` | number \| null | 🔋 Total usable battery capacity in kWh (BEV only). Used to compute SoC percentages and effective energy consumed. `null` if not set. |
| `tankCapacityL` | number \| null | ⛽ Total fuel tank capacity in liters (ICE only). Used to compute fuel level percentages and effective fuel consumed. `null` if not set. |

### Refueling Record

Each record is stored as a JSON object under the `chargemaster_v1_records` localStorage key:

```json
{
  "id": "uuid-v4-string",
  "vehicleId": "uuid-v4-string",
  "date": "YYYY-MM-DD",
  "distanceKm": 320.0,
  "quantity": 55.20,
  "unitPrice": 0.2500,
  "totalCost": 13.80,
  "costPerKm": 0.0431,
  "initialSoC": 20.0,
  "finalSoC": 75.0,
  "initialFuelLevel": null,
  "finalFuelLevel": null,
  "transferEfficiency": 95.0,
  "effectiveQuantity": 52.44,
  "effectiveEnergyConsumed": 33.75,
  "location": "EnelX Anagnina",
  "notes": "Optional free-text notes",
  "createdAt": "ISO-8601 timestamp",
  "updatedAt": "ISO-8601 timestamp"
}
```

| Field | Type | Description |
|---|---|---|
| `id` | string | UUID v4 — unique identifier for the record. |
| `vehicleId` | string | UUID v4 — links the record to its vehicle profile. |
| `date` | string | Date of the session (`YYYY-MM-DD`). |
| `distanceKm` | number | Kilometers traveled since the last session. |
| `quantity` | number | Energy (kWh) or fuel (L) put into the vehicle. |
| `unitPrice` | number | Price per unit (€/kWh or €/L). |
| `totalCost` | number | `quantity × unitPrice`. |
| `costPerKm` | number | `totalCost / distanceKm`. |
| `initialSoC` | number \| null | 🔋 Battery charge level (%) **before** the charging session (BEV). `null` if not entered. |
| `finalSoC` | number \| null | 🔋 Battery charge level (%) **after** the charging session (BEV, auto-computed). `null` if not available. |
| `initialFuelLevel` | number \| null | ⛽ Tank fuel level (%) **before** refueling (ICE). `null` if not entered. |
| `finalFuelLevel` | number \| null | ⛽ Tank fuel level (%) **after** refueling (ICE, auto-computed). `null` if not available. |
| `transferEfficiency` | number | ⚡ Fraction of charged energy stored in the battery (%). Always `100` for ICE. |
| `effectiveQuantity` | number | `quantity × (transferEfficiency / 100)` — energy actually delivered to the battery. |
| `effectiveEnergyConsumed` | number \| null | ⚡ / ⛽ Absolute energy or fuel consumed between sessions, derived from the SoC delta and vehicle capacity. `null` if capacity or initial SoC is not set. |
| `location` | string \| null | Optional station or location name (max 100 characters). |
| `notes` | string \| null | Optional free-text notes (max 500 characters). |
| `createdAt` | string | ISO 8601 timestamp — when the record was first saved. |
| `updatedAt` | string | ISO 8601 timestamp — when the record was last modified. |

The `vehicleId` field links each record to its vehicle profile. Records whose `vehicleId` does not match any existing vehicle are automatically reassigned to the first available vehicle on load (migration safeguard).

### localStorage Keys

| Key | Contents |
|---|---|
| `chargemaster_v1_vehicles` | JSON array of vehicle profile objects. |
| `chargemaster_v1_records` | JSON array of refueling/charging record objects. |
| `chargemaster_v1_settings` | JSON object with `{ theme, schemaVersion }`. |

---

## 📡 PWA & Offline Support

Charge Master is a fully installable **Progressive Web App**:

- 📲 **Installable** — on mobile (iOS/Android) and desktop (Chrome/Edge), users can install it to the home screen or app launcher via the browser's "Add to Home Screen" / "Install" prompt.
- 🔌 **Offline-first** — an external Service Worker (`/sw.js`) is registered via a standard `navigator.serviceWorker.register('/sw.js')` call on the `window load` event, for environments where a physical `sw.js` file is deployed alongside the app.
- 🎨 **Themed status bar** — on iOS (via `apple-mobile-web-app-status-bar-style`) and Android (via `theme-color` meta tag), the status bar matches the app's dark/light theme.
- 🖼️ **Dynamic icons** — app icons (192×192 and 512×512) are generated programmatically on a `<canvas>` with the branded blue-to-cyan gradient and a lightning bolt symbol.
- 📄 **Web App Manifest** — dynamically injected at runtime with name, short name, description, orientation, colors, and icons.

---

## ⚙️ Settings & Customization

Access Settings via the **⚙ gear icon** in the top-right header.

### 🌙 / ☀️ Display Mode

Toggle between **Dark Mode** (default) and **Light Mode**. The toggle is available both in the Settings sheet and via the moon/sun icon in the header. Theme changes are saved to `localStorage` and re-applied on every subsequent load.

---

## ℹ️ About Modal

A polished information panel accessible from the header.

**📍 Access:**
- Click the **ℹ️ info icon** in the top-right header (rightmost button)

**📋 Contents:**
- **⚡ App Logo** — Branded lightning bolt icon on a blue-to-cyan gradient rounded square
- **🏷️ App Name** — "Charge Master" in gradient display font
- **🔢 Version** — Dynamically rendered app version (e.g. `v1.1.0`)
- **👤 Author Card** — Avatar with initials ("GP"), author name (Gianfranco Piazzolla), and tagline
- **📝 Synopsis** — Brief description of the app's purpose
- **🔗 GitHub Repository** — Direct link to the Charge Master repository
- **💻 More Apps** — Link to the author's GitHub profile
- **📜 License** — MIT License summary

**🎭 UI Behavior:**
- Modal dialog (centered, max-width 360px) with backdrop blur (`4px`) and dark semi-transparent background
- Scale-in entrance animation (`scale(0.92) → scale(1)`) with blue glow shadow (`0 0 60px rgba(59,130,246,0.15)`)
- Close via the **Close** button at the bottom

---

## 💾 Data Management

### 📤 Export

- Go to **Settings → Export All Records (JSON)**.
- A `.json` file named `chargemaster_export_YYYY-MM-DD.json` is automatically downloaded.
- The export envelope includes metadata: `exportedAt`, `appVersion`, `vehicles` (array of vehicle profiles with record counts **including `batteryCapacityKwh` and `tankCapacityL`**), and the full `records` array.

**🔄 Three-Tier Export Strategy:**
1. **📥 Blob Download** — Standard browsers receive an automatic file download via `URL.createObjectURL()` and a temporary `<a>` element.
2. **📱 Web Share API** — On mobile devices that support `navigator.share()`, the app offers to share the export file directly (e.g. via WhatsApp, email, or file manager).
3. **📋 Modal Fallback** — In WebView environments (detected via `/wv/` UA test or `window.Website2APK` check) or when blob download fails, a centered modal dialog appears with:
   - The export filename displayed in monospace font
   - A readonly `<textarea>` preview of the full JSON content
   - A **📋 Copy to Clipboard** button that copies the entire JSON to the clipboard
   - A **Close** button to dismiss the modal

```json
{
  "exportedAt": "ISO-8601 timestamp",
  "appVersion": "1.1.0",
  "vehicles": [
    { "vehicleId": "uuid", "vehicleName": "Tesla Model S", "unit": "kWh", "recordCount": 24, "batteryCapacityKwh": 75.0, "tankCapacityL": null }
  ],
  "records": [ ... ]
}
```

### 📥 Import

- Go to **Settings → Import Records from File**.
- Select a `.json` file previously exported from Charge Master (or a raw array of valid record objects).
- A modal dialog appears showing the **number of records** to import, with two options:
  - **⚠ Overwrite All** — replaces all existing records and vehicles with the imported ones (irreversible). Styled with a red border for visual warning.
  - **✓ Merge** — adds only records whose `id` is not already present in the local database (deduplication by UUID). Vehicle profiles present in the import file but not in the local database are also added.
- **Import Validation:** Each imported record must have the following required fields to be considered valid: `id`, `date`, `distanceKm`, `quantity`, and `unitPrice`. Records missing any of these are silently skipped.
- **Orphaned Record Handling:** During import, records with an invalid or missing `vehicleId` are automatically reassigned to the active vehicle (or the first available vehicle if none is active).
- **Legacy file handling:** If the imported file does not contain a `vehicles` array (exported from v1.0.0), a `"Default"` vehicle profile is automatically created and all imported records are assigned to it.
- If the file is malformed or contains no valid records, an error toast is shown.

### 🗑️ Delete All Records

- Go to **Settings → Delete All Records**.
- An inline confirmation prompt appears. Confirm to permanently erase all refueling/charging records from `localStorage`. Vehicle profiles are not deleted by this action.

---

## 📉 Charts & Visualizations

All charts are rendered with **Chart.js 4.4.1**, scoped to the **active vehicle**, and re-rendered whenever the active filter, theme, or active vehicle changes.

### Line Charts (Charts 1–5)

Five line charts are rendered at the top of the Charts tab:

1. **Distance Over Time** (`chart-distance`) — Km per session over time.
2. **Unit Price Over Time** (`chart-price`) — price per unit over time.
3. **Cost per Km Over Time** (`chart-cpkm`) — cost/Km per session over time.
4. **Consumption Over Time** (`chart-consumption`) — energy or fuel consumption per session expressed as kWh/100Km or L/100Km, plotted chronologically. Color: amber (`#FB923C`).
5. **Efficiency Over Time** (`chart-efficiency`) — per-session vehicle efficiency expressed as Km/kWh or Km/L, plotted chronologically. Color: purple (`#A855F7`).

Each line chart includes:
- A **primary dataset** with gradient area fill and data-point dots.
- A **secondary reference line** (dashed), which is a rolling 5-point moving average for the Price chart (Chart 2), or a flat mean for all other line charts.
- Responsive layout with `maintainAspectRatio: false` inside a 220px-tall container.

#### 📊 Consumption Over Time (Chart 4)

- **Y-axis:** consumption rate in `kWh/100Km` (electric) or `L/100Km` (combustion), computed as `(quantity / distanceKm) × 100`.
- **Primary line color:** amber (`#FB923C`) with a matching gradient fill.
- **Reference line:** flat average of all session consumption values in the current filter period, rendered as a dashed amber-yellow line (`#F59E0B`).
- **Tooltip:** shows the value formatted as `X.XX kWh/100Km` or `X.XX L/100Km`.

#### 💹 Efficiency Over Time (Chart 5)

- **Y-axis:** efficiency in `Km/kWh` (electric) or `Km/L` (combustion), computed as `distanceKm / quantity`.
- **Primary line color:** purple (`#A855F7`) with a matching gradient fill.
- **Reference line:** flat average of all session efficiency values in the current filter period, rendered as a dashed amber-yellow line (`#F59E0B`).
- **Tooltip:** shows the value formatted as `X.XX Km/kWh` or `X.XX Km/L`.

### Bar Charts (Charts 6–10)

- **Chart 6 (Monthly Expenditure):** Horizontal bars, one per month-year combination, colored by the season of that month. Uses **all active vehicle records** regardless of the active filter.
- **Chart 7 (Seasonal Price):** Grouped horizontal bar chart showing Min / Avg / Max unit prices per season.
- **Chart 8 (Seasonal Consumption):** Grouped horizontal bar chart showing Min / Avg / Max consumption (kWh/100Km or L/100Km) per season.
- **Chart 9 (Seasonal Efficiency):** Grouped horizontal bar chart showing Min / Avg / Max efficiency (Km/kWh or Km/L) per season.
- **Chart 10 (Seasonal Cost per Km):** Grouped horizontal bar chart showing Min / Avg / Max €/Km per season.

### Doughnut Charts (Charts 11–12)

- **Chart 11 (Season Spending):** Cutout doughnut (65% inner radius) showing the percentage of total expenditure per season. Tooltips show absolute € amounts and percentages.
- **Chart 12 (Monthly Frequency):** Doughnut showing how many sessions occurred in each calendar month. Colors use an HSL gradient from cool blue to warm blue.

All charts are properly destroyed (`chart.destroy()`) before being re-created to prevent memory leaks and canvas conflicts.

---

## 🎨 Visual Design System

Charge Master features a premium, meticulously crafted visual design with a comprehensive token-based system.

### 🖋️ Typography

Three Google Fonts are loaded with specific weight ranges:

| Font | Role | Weights | CSS Variable |
|---|---|---|---|
| **Rajdhani** | Display/headings | 400, 500, 600, 700 | `--font-display` |
| **DM Sans** | Body text | 300, 400, 500, 600 | `--font-body` |
| **JetBrains Mono** | Data/numbers/code | 400, 500, 600 | `--font-mono` |

**Font Size Scale:**
- `--text-xs`: 12px — labels, badges, tooltips
- `--text-sm`: 12px — secondary text, chips
- `--text-base`: 15px — body text, form inputs
- `--text-lg`: 18px — section titles, card values
- `--text-xl`: 22px — header title, modal titles
- `--text-2xl`: 28px — about app name
- `--text-3xl`: 36px — hero values

**Border Radius Scale:**
- `--radius-sm`: 8px — small buttons, chips
- `--radius-md`: 12px — form inputs, modals
- `--radius-lg`: 16px — cards, record cards
- `--radius-xl`: 24px — modal sheet top corners

### 🎭 Theme System

- **🌙 Dark Mode** (default): Deep navy/charcoal palette (`#0A0E1A` background, `#111827` cards, `#1E2435` tertiary).
- **☀️ Light Mode**: Clean slate-gray palette (`#F1F5F9` background, `#FFFFFF` cards, `#E2E8F0` tertiary).
- Global transitions (`300ms ease`) on `color` and `background-color` for smooth theme switching.
- Form inputs use `color-scheme: dark/light` to adapt native date pickers and number spinners to the active theme.
- Status bar color (`theme-color` meta tag) updates dynamically to match the active theme.

### ✨ Animations & Transitions

- **🃏 Card Entrance Animation** — All cards (records, vehicles, stat cards) animate in with `opacity: 0 → 1` and `translateY(16px) → 0` using `cubic-bezier(0.4, 0, 0.2, 1)` easing. Staggered delays are applied (`i × 40ms` for records, `i × 50ms` for vehicles).
- **🃏 Card Hover Effects** — Cards lift (`translateY(-2px)`) with an enhanced shadow on hover. Record cards additionally change their border color to `var(--border-active)`.
- **⚡ Ripple Effect** — All interactive elements with the `.ripple-host` class feature a radial gradient ripple via `::after` pseudo-element, positioned using CSS custom properties (`--rx`, `--ry`), with a scale-down press effect (`scale(0.97)`, 80ms transition).
- **📑 Tab Panel Transitions** — Incoming panels slide in from the right (`translateX(30px)`), outgoing panels slide left (`translateX(-30px)`), both with `280ms cubic-bezier(0.4, 0, 0.2, 1)` easing. Panels use `will-change: opacity, transform` for GPU-accelerated rendering.
- **🔘 Toggle Switch** — Features a spring animation (`cubic-bezier(0.34, 1.56, 0.64, 1)`) for the knob, with a 22px white circle that translates 20px when activated.
- **📊 Tab Bar Active Indicator** — The active tab displays a glowing top-bar indicator (`::before` pseudo-element) with a `scaleX` animation and `box-shadow` glow effect in the accent primary color.
- **🔘 Icon Button Hover Glow** — Header icon buttons gain a `0 0 12px var(--accent-glow)` box-shadow on hover.

### 🎨 Component Styling

- **🪟 Glass Morphism** — `.card-glass` class with `backdrop-filter: blur(12px)` and semi-transparent background for layered visual depth.
- **📜 Custom Scrollbar** — 4px-wide scrollbar with transparent track and subtle thumb (`var(--border-subtle)`), styled via `::-webkit-scrollbar`.
- **🔘 Button Variants (4 types):**
  - **Primary** — Blue gradient (`var(--accent-primary)` → `#2563EB`) with glow shadow
  - **Secondary** — Tertiary background with subtle border
  - **Danger** — Red-tinted background (`rgba(239,68,68,0.12)`) with red border
  - **Success** — Green-tinted background (`rgba(16,185,129,0.12)`) with green border
- **📊 Computed Fields** — Blue-tinted background (`rgba(59,130,246,0.07)`) with blue border, displaying label/value pairs in monospace font.
- **🚗 Vehicle Card Icons** — Blue-to-cyan gradient background on the icon container.
- **📊 Stat Chips** — Flex-wrap container with tertiary background, monospace font for values, and 3px gap between chips.

### 📱 Mobile Optimizations

- **Safe Area Padding** — Tab bar includes `padding-bottom: env(safe-area-inset-bottom)` for devices with home indicators (iPhone X+).
- **Viewport Fit** — `viewport-fit=cover` meta tag enables full-screen rendering on notched devices.
- **Overflow Management** — `html, body` use `overflow: hidden` with scrollable tab panels for controlled scrolling.
- **Font Smoothing** — `-webkit-font-smoothing: antialiased` for crisp text rendering on WebKit browsers.

---

## 🖐️ Keyboard & Gesture Navigation

| Interaction | Action |
|---|---|
| 👆 Tap record card | Open edit form |
| 👆 Tap `+` FAB | Open add record form (redirects to Vehicles tab if no vehicle active) |
| 👆 Tap tab bar buttons | Switch active tab |
| 👆 Tap header logo | Scroll active panel to top |
| 👆 Tap modal backdrop | Close the modal |
| 👆 Tap vehicle checkmark button | Set vehicle as active profile |
| 👆 Tap vehicle pencil button | Open vehicle edit form |
| 👆 Tap vehicle trash button | Trigger inline delete confirmation |
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
- Vehicle action buttons (select, edit, delete) each carry descriptive `aria-label` and `title` attributes.
- The SoC fields (Initial SoC, Final SoC, Transfer Efficiency) include `aria-label` attributes.
- Color is never the sole indicator of meaning (badges also differ in value context; seasons also have icon labels; active vehicle is indicated by both border color and button state).

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
