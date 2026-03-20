# Spectra Viewer

A self-contained, browser-based tool for loading, overlaying, and formatting multiple spectra files for visual inspection and publication-quality export. No installation, no backend, no dependencies to manage — open the HTML file directly in any modern browser, or host it as a static page (e.g. GitHub Pages).

Built with [Plotly.js](https://plotly.com/javascript/).

---

## Getting started

Download `spectra_viewer.html` and open it in your browser. That's it. No server required.

Alternatively, host it on GitHub Pages by placing the file in your repository and enabling Pages in the repository settings.

---

## Loading files

Spectra files can be loaded in two ways:

- **Click the drop zone** in the Files tab to open a file picker. Multiple files can be selected at once.
- **Drag and drop** one or more files directly onto the drop zone.

Accepted formats are plain-text tabular files: `.txt`, `.csv`, `.tsv`, `.dat`, or any file containing whitespace- or delimiter-separated numeric columns. The viewer auto-detects the delimiter and the row at which data begins, so most files will load correctly without any manual configuration.

---

## Interface overview

The interface has three main areas:

- **Top bar** — app title, status messages, and global action buttons (theme toggle, replot, save session, load session, save PNG)
- **Sidebar** — all controls, split into two tabs: **Files** and **Plot options**. The sidebar can be resized by dragging its right edge, or hidden entirely using the **‹** collapse button on its border.
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

## Top bar actions

| Button | Description |
|---|---|
| **☀ Light / 🌙 Dark** | Toggles between light and dark themes for both the UI and the plot. Light mode is the default. |
| **↺ Replot** | Forces a full redraw of the plot, useful if something looks out of sync. |
| **↓ Save session** | Downloads a `.json` file capturing the complete state: all loaded file contents, all per-file settings (trace names, colours, column assignments, X trims, axis assignments, visibility), all plot options, toggle states, and theme. The file is named after the current plot title. |
| **↑ Load session** | Opens a file picker to select a previously saved session `.json`. The entire state — files, settings, and plot — is restored exactly as it was saved. |
| **↓ Save PNG** | Exports the current plot as a PNG at the resolution set by the Export scale option. The file is named after the current plot title. |

---

## Tips

**File ordering** — the order of traces in the file list determines the order in the legend. Drag cards by their grip handle to reorder. Plotly's default colour cycle is applied in list order, so reordering also cycles colours unless you have set them manually.

**Trace colours** — by default, Plotly assigns colours automatically. To override, click the colour swatch on any file card and choose a colour from the picker. The swatch reflects Plotly's auto-assigned colour for traces that haven't been manually coloured, so you always know what each trace looks like.

**Dual Y axes** — when one or more traces are assigned to the right Y axis, the right margin expands automatically to accommodate the axis ticks and label. The right axis shares the same log/linear state as the left axis. The right axis grid is hidden to avoid visual clutter; only the left axis draws horizontal grid lines.

**Session files** — session `.json` files embed the full text of all loaded spectra, so they are self-contained and can be shared. File sizes scale with the size and number of the spectra loaded.

**Normalisation and dual axes** — normalisation is global: when enabled, every trace (regardless of which Y axis it is on) is scaled to 0–1 independently. Both Y-axis labels update to reflect this.

**Parsing problems** — if a file doesn't load as expected, check the Data preview panel. Adjust Skip N lines to skip over header blocks, select the correct Delimiter if Auto doesn't detect it correctly, and tick First remaining line is header if the first data row contains column names.

---

## Technical notes

- Runs entirely in the browser. No data is sent to any server.
- Requires an internet connection on first load to fetch Plotly.js and the IBM Plex fonts from CDNs. Once loaded, the page functions offline until refreshed.
- Tested in current versions of Chrome, Firefox, Edge, and Safari.
- The HTML file is self-contained apart from the two CDN dependencies and can be committed as a single file to a repository.

---

*Georgios Tsiminis, 2026*
