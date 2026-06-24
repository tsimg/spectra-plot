# Spectra Viewer

A self-contained, browser-based tool for loading, overlaying, formatting, and processing multiple spectra files for visual inspection, background subtraction, reference correction, and publication-quality export. No installation, no backend, no dependencies to manage — open the HTML file directly in any modern browser, or host it as a static page (e.g. GitHub Pages).

Built with [Plotly.js](https://plotly.com/javascript/) by Georgios Tsiminis 2026.

---

## Getting started

Run it from your browser window by clicking on https://tsimg.github.io/spectra-plot/. 

OR, download `index.html` and open it in your browser.

---

## Loading files

Spectra files can be loaded in two ways:

- **Click the drop zone** in the Files tab to open a file picker. Multiple files can be selected at once.
- **Drag and drop** one or more files directly onto the drop zone.

Accepted formats are plain-text tabular files: `.txt`, `.csv`, `.tsv`, `.dat`, or any file containing whitespace- or delimiter-separated numeric columns. The viewer auto-detects the delimiter and the row at which data begins, so most files will load correctly without any manual configuration.

---

## Interface overview

The interface has three main areas:

- **Top bar** — app title, status messages, and global action buttons (theme toggle, replot, save/load session, save data CSV, save PNG)
- **Sidebar** — all controls, split into three tabs: **Files**, **Plot options**, and **Process**. The sidebar can be resized by dragging its right edge, or hidden entirely using the **‹** collapse button on its border.
- **Plot area** — the interactive Plotly chart, which fills all remaining space.

---

## Files tab

### File list

Loaded files appear as cards in the file list. Each card shows the filename and the current trace name (legend entry).

- **Click** a card to select it and load its settings into the File settings panel below.
- **Double-click the trace name** to rename it inline. Press **Enter** or click away to confirm; press **Escape** to cancel. The plot legend updates immediately.
- **Click the colour swatch** (small square on the left of each card) to open a colour picker and change the trace colour. The plot updates immediately.
- **Drag** a card by its grip handle (three horizontal lines, visible on hover) to reorder the traces. The legend order in the plot matches the list order.
- **Remove** — removes the currently selected file.
- **Clear all** — removes all loaded files.

### File settings

These settings apply to whichever file is currently selected in the list. After changing any setting, press **Apply** to update the plot. Use **Preview** to refresh the data preview without replotting.

| Setting | Description |
|---|---|
| **Trace name** | The label shown in the plot legend for this file. Can also be edited inline in the file list. |
| **Skip N lines** | Number of lines to discard from the top of the file before parsing begins. Useful for skipping instrument metadata or comment blocks. Auto-detected on load. |
| **Delimiter** | Column separator used in the file. **Auto** detects the most consistent delimiter from the first few rows. Options: Auto, Tab, Comma, Semicolon, Whitespace. |
| **X column** | Which parsed column to use as the X axis (e.g. wavelength). |
| **Y column** | Which parsed column to use as the Y axis (e.g. intensity). |
| **X min trim / X max trim** | Restrict the plotted range of this trace to a sub-range of its X data. Use **Full range** to reset both values to the data's actual extent. |
| **First remaining line is header** | Tick this if the first non-skipped line contains column names rather than data. The column selectors will then show those names instead of `col_0`, `col_1`, etc. |
| **Show this trace** | Untick to hide this trace from the plot without removing it from the file list. |
| **Plot on right Y-axis** | Assigns this trace to a second, independent Y axis on the right side of the plot. Useful for overlaying spectra with very different intensity scales without normalising. |

### Data preview

Shows the first 20 rows of the parsed table for the selected file, reflecting current Skip, Delimiter, and Header settings. Useful for diagnosing parsing problems before hitting Apply.

---

## Plot options tab

### Labels

| Field | Description |
|---|---|
| **Plot title** | Text shown above the plot. Also used as the base filename when saving a PNG or session. |
| **X-axis label** | Label on the horizontal axis. |
| **Left Y-axis label** | Label on the left vertical axis. Automatically replaced with "Normalised intensity (0–1)" when normalisation is on. |
| **Right Y-axis label** | Label on the right vertical axis, shown only when at least one trace is assigned to the right axis. Displayed rotated top-to-bottom. |

### Font sizes

Independent size controls (in points) for:

| Field | Affects |
|---|---|
| **Title** | The plot title |
| **Axis labels** | The X-axis, left Y-axis, and right Y-axis labels |
| **Tick labels** | The numbered tick marks on all axes |
| **Legend** | The legend entries |

### Trace style

| Setting | Description |
|---|---|
| **Line width** | Thickness of all trace lines in pixels. |
| **Normalise** | When on, every trace is independently scaled to the 0–1 range before plotting. This applies globally across all traces regardless of which Y axis they are on. The Y-axis label updates automatically. |
| **Markers** | When on, individual data points are shown as circles on top of each line. |

### Axes

| Toggle | Description |
|---|---|
| **Log X** | Switches the X axis to a logarithmic scale. |
| **Log Y** | Switches both Y axes to a logarithmic scale. |
| **Frame** | Draws a complete bounding box around the plot area (mirrors ticks on all four sides). Not available when a right Y axis is active. |

### Global X trim

Overrides per-file X trim settings and applies a single X range to all visible traces at once.

| Setting | Description |
|---|---|
| **Apply to all traces** | Toggle button. When on, the Min/Max values below are applied to every trace instead of each file's individual trim settings. When first enabled, the values are pre-filled from the currently selected file. |
| **Min / Max** | The global X trim boundaries. |

### Legend

**Position** — controls where the legend is placed relative to the plot. Options: top right, top left, bottom right, bottom left, right, left, middle right, middle left.

### Layout & export

| Setting | Description |
|---|---|
| **Aspect ratio** | Constrains the plot to a fixed width-to-height ratio using CSS. Options: free (fills available space), 1:1, 4:3, 16:9, 3:2, 21:9. |
| **Export scale** | Resolution multiplier for PNG export. 2× produces an image twice the screen pixel dimensions; 4× is suitable for print. |

---

## Process tab

The Process tab provides a sequential spectral correction pipeline. Each section operates on the data in order: linear background → exponential background → reference subtraction → region-of-interest integration. Corrections build on one another where explicitly requested (via the "Chain from…" options), but each section can also be used independently.

All corrections are applied to the displayed traces only and never modify the underlying loaded data. The original raw data is always preserved and can be recovered by toggling off the relevant correction.

### Linear background subtraction

Fits a straight line between two user-defined anchor wavelengths and subtracts it from each trace. Typically used to remove a flat detector floor or slowly varying baseline.

**Setting the anchor points**

- Enter λ₁ and λ₂ manually in the two input fields, or
- Click **⊕ Pick from plot** and then click two points on the plot in sequence. Press **Escape** to cancel.
- Click **Clear** to remove both anchors and disable the correction.

**Display toggles**

| Toggle | What it shows |
|---|---|
| **Region** | An amber shaded rectangle between the two anchor wavelengths |
| **Original** | The raw (uncorrected) trace |
| **Bg line** | The fitted straight line |
| **Subtracted** | The corrected trace (raw minus the background line) |

**Clip to zero** — when ticked, any negative values in the corrected trace are set to zero.

### Exponential background subtraction

Fits an exponential decay to a user-defined fitting window and subtracts the model from each trace. Useful for removing the steep tail from an LED emission peak or a rapidly decaying fluorescence background.

The model is `y = A · exp(b · (x − x₀))` where x₀ is the left edge of the fitting window and b < 0 for a decaying function.

**Fit mode**

| Mode | Description |
|---|---|
| **Free (A, b)** | Both amplitude and decay constant are fitted by ordinary least-squares regression on the log-transformed data within the fitting window. |
| **Anchored (A = y₀)** | The amplitude A is fixed to the interpolated data value at x₀. Only the decay constant b is fitted. Use this when the peak or step onset is known and you want to anchor the fit there. |

**Setting the fitting window** — enter the window start and end wavelengths manually, or use **⊕ Pick from plot** (two clicks). The fit is performed over the selected window and the exponential is then extrapolated and subtracted from x₀ to the end of the spectrum.

The status panel below the controls shows the fitted parameters (A, b, R², number of points used) for each visible trace after the fit is computed.

**Chain from linear background** — when ticked, the exponential fit is applied to the linear-background-corrected data rather than the raw data. Untick to fit the raw data directly regardless of whether a linear background is configured.

**Display toggles**

| Toggle | What it shows |
|---|---|
| **Region** | A purple shaded rectangle marking the fitting window |
| **Exp fit** | The fitted exponential curve |
| **Subtracted** | The corrected trace (base data minus the exponential) |

**Clip to zero** — sets negative corrected values to zero.

### Reference subtraction

Scales a reference spectrum to match each data trace at a chosen normalisation point (or over a fitting window) and subtracts it. This is the recommended approach for removing a physically measured background that shares the spectral shape of the background component in the data.

A typical use case is subtracting a second-LED reference spectrum (e.g. from a 630 nm excitation source that excites clay fluorescence but not the analyte signal) from a primary measurement spectrum (e.g. a 590 nm excitation spectrum that excites both). Each data trace gets its own independently computed scale factor k.

#### Reference spectrum

Choose the source using the toggle:

| Option | Description |
|---|---|
| **Loaded trace** | Select any trace already loaded in the Files tab from the dropdown. That trace is excluded from the subtraction results (a spectrum cannot meaningfully be subtracted from itself). |
| **Load file** | Load a separate file as the reference. It is not added to the Files tab and is used only for subtraction. |

#### Scale method

| Method | Description |
|---|---|
| **Single point** (default) | `k = data(λ_norm) / ref(λ_norm)`. The reference is scaled so it exactly matches the data at a single normalisation wavelength. Default λ_norm is **800 nm**. Recommended when the two spectra share the same background shape and a clean, signal-free normalisation point is available. One click on the plot sets the wavelength; **Reset** returns it to 800 nm. |
| **Window (OLS)** | `k = Σ(data · ref) / Σ(ref²)` over a user-defined wavelength window. This is ordinary least-squares regression through the origin and minimises the sum of squared residuals between the scaled reference and the data across the entire window. Use when no single reliable normalisation point exists. Two clicks on the plot set the window edges; **Clear** removes it. |

The scale factor k for each trace is displayed in the status panel and updated on every replot.

#### Subtraction range

| Option | Description |
|---|---|
| **Full range** | The scaled reference is subtracted across the entire wavelength range of each trace. |
| **Window** | The subtraction is restricted to a user-defined wavelength range. Data outside this range is left unchanged. |

#### Chain from linear background

When ticked, the reference subtraction operates on the linear-background-corrected data. Crucially, the **same floor correction is also applied to the reference spectrum** before k is computed, ensuring a consistent baseline for both spectra. This is necessary whenever both spectra contain a significant detector floor offset: without it, k = (floor-corrected data) / (raw reference), which gives a scale factor that is too small and produces an over-subtracted or under-subtracted result.

When unticked, raw values are used for both data and reference throughout.

#### Display toggles

| Toggle | What it shows |
|---|---|
| **Reference** | The reference spectrum at its original (unscaled) counts |
| **Scaled ref** | k × reference for each data trace, showing the reference as it will be subtracted |
| **Subtracted** | The corrected trace: data (or linear-corrected data) minus the scaled reference |

**Clip to zero** — sets negative corrected values to zero after subtraction.

### Regions of interest

Defines named wavelength windows over which integrals are computed. Use this to quantify peak areas or compare signal intensities across traces after correction.

**Adding and editing ROIs**

- Click **+ Add ROI** to add a new region. Each ROI gets an auto-incremented name and a colour-coded shaded rectangle on the plot.
- Set the start and end wavelengths for each ROI using the input fields in the ROI list, or click **⊕ Pick** to set them by clicking the plot.
- ROI names can be edited inline.
- Click the × button next to a ROI to remove it. **Clear all** removes all ROIs.

**Integration method**

| Method | Description |
|---|---|
| **Sum (Σy)** | Simple sum of all y values within the ROI: `Σ yᵢ`. Results are in counts and are integer-valued. This is the default and matches a manual pixel count. |
| **Area (trapezoid)** | Trapezoid-rule integral: `Σ ½(yᵢ + yᵢ₊₁)(xᵢ₊₁ − xᵢ)`. Results are in counts·nm. Use this for a physically meaningful spectral area when spectra have non-uniform wavelength spacing or when comparing across instruments with different dispersion. |

**Use subtracted data** — when ticked, integrals are computed on the fully processed data by applying corrections in pipeline order:

1. Linear background subtraction (if anchor points are set)
2. Reference subtraction (if a reference is configured and the scale is defined)

This ensures integrated values correspond to what is shown in the plot when the "Subtracted" display toggles are on.

When unticked, integrals are computed on the raw loaded data.

**Computing and exporting** — click **Compute integrals** to calculate results for all visible traces over all defined ROIs. Results appear in a table in the sidebar. Click **Export CSV** to download the table. The column header for each ROI uses the format `{start}-{end} nm`.

---

## Top bar actions

| Button | Description |
|---|---|
| **☀ Light / 🌙 Dark** | Toggles between light and dark themes for both the UI and the plot. Light mode is the default. |
| **↺ Replot** | Forces a full redraw of the plot, useful if something looks out of sync. |
| **↓ Save session** | Downloads a `.json` file capturing the complete state: all loaded file contents, all per-file settings (trace names, colours, column assignments, X trims, axis assignments, visibility), all plot options, all Process tab settings (background anchors, exponential fit parameters, reference subtraction configuration, ROI definitions), toggle states, and theme. The file is named after the current plot title. |
| **↑ Load session** | Opens a file picker to select a previously saved session `.json`. The entire state — files, settings, processing configuration, and plot — is restored exactly as it was saved. |
| **↓ Save data CSV** | Exports the processed data for all visible traces as a CSV file. Columns included depend on which corrections are configured: wavelength, raw counts, and (if set up) linear background values, linear-corrected counts, exponential background values, and exponential-corrected counts. |
| **↓ Save PNG** | Exports the current plot as a PNG at the resolution set by the Export scale option. The file is named after the current plot title. |

---

## Tips

**File ordering** — the order of traces in the file list determines the order in the legend. Drag cards by their grip handle to reorder. Plotly's default colour cycle is applied in list order, so reordering also cycles colours unless you have set them manually.

**Trace colours** — by default, Plotly assigns colours automatically. To override, click the colour swatch on any file card and choose a colour from the picker. The swatch reflects Plotly's auto-assigned colour for traces that haven't been manually coloured, so you always know what each trace looks like.

**Dual Y axes** — when one or more traces are assigned to the right Y axis, the right margin expands automatically to accommodate the axis ticks and label. The right axis shares the same log/linear state as the left axis. The right axis grid is hidden to avoid visual clutter; only the left axis draws horizontal grid lines.

**Session files** — session `.json` files embed the full text of all loaded spectra and all processing configuration, so they are self-contained and can be shared. File sizes scale with the size and number of the spectra loaded.

**Normalisation and dual axes** — normalisation is global: when enabled, every trace (regardless of which Y axis it is on) is scaled to 0–1 independently. Both Y-axis labels update to reflect this. Display normalisation is never applied during ROI integration — integrals always use raw or corrected counts.

**Parsing problems** — if a file doesn't load as expected, check the Data preview panel. Adjust Skip N lines to skip over header blocks, select the correct Delimiter if Auto doesn't detect it correctly, and tick First remaining line is header if the first data row contains column names.

**Processing pipeline order** — corrections are designed to be applied sequentially. Set up linear background first (to remove the detector floor), then exponential background if needed (chained from the linear correction), then reference subtraction (chained from linear background). ROI integrals can then be computed on the fully corrected data by ticking "Use subtracted data".

**Single-point vs. OLS reference scaling** — for physical reference subtraction where the two spectra share the same background shape, single-point normalisation (default, 800 nm) is recommended. Choose a wavelength in a region free of analyte signal where both spectra are stable. The OLS window method finds a compromise k that minimises residuals across the whole window rather than giving an exact match at any single wavelength.

**Consistent floor removal before reference subtraction** — when using reference subtraction with "Chain from linear background" ticked, the same linear correction is removed from both the data and the reference before k is computed. This is essential when both spectra contain a detector floor: without it the scale factor will be wrong, typically appearing much smaller than expected.

**Escape cancels pick mode** — whenever a **⊕ Pick from plot** button is active (highlighted), clicking the plot sets a wavelength. Press **Escape** at any time to cancel without making changes.

---

## Technical notes

- Runs entirely in the browser. No data is sent to any server.
- Requires an internet connection on first load to fetch Plotly.js and the IBM Plex fonts from CDNs. Once loaded, the page functions offline until refreshed.
- Tested in current versions of Chrome, Firefox, Edge, and Safari.
- The HTML file is self-contained apart from the two CDN dependencies and can be committed as a single file to a repository.

---

*Georgios Tsiminis, 2026*
