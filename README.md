# SECO Drawing Resources

Standardized ISO 2D engineering drawing resources for **Southern Engineering Co. Ltd. (SECO)** — covering both general mechanical fabrication drawings and shipbuilding / marine structural drawings.

## Contents

| File | Description |
| :--- | :--- |
| `Seco_master_ISO.dwt` | Clean, audited AutoCAD 2018+ metric drawing template (`A0`–`A4`) for direct manual drafting in AutoCAD. |
| `seco-engineering-drawings.zip` | Complete, portable **Antigravity skill** for automated, standards-verified 2D engineering drawing generation (`DWG`, `DXF`, and monochrome `PDF`) from plain engineering specs, reference CAD files, or 3D `.step` / `.stp` models. |

---

## 1. Using `Seco_master_ISO.dwt` in AutoCAD

Copy `Seco_master_ISO.dwt` into your AutoCAD Templates folder (e.g., `%LOCALAPPDATA%\Autodesk\AutoCAD 2027\R26.0\enu\Template\`) or open it directly via **File → New**.

### What Is Preconfigured
- **ISO Metric Database:** `$INSUNITS = 4` (mm), `$MEASUREMENT = 1` (metric `acadiso.lin` / `acadiso.pat`), `$PSLTSCALE = 1`, `$LTSCALE = 1.0`, clean empty Model Space, `0` AutoCAD `AUDIT` errors.
- **Sheets (`A0`–`A4` Landscape):** Full-bleed `DWG To PDF.pc3` + `monochrome.ctb` with *Plot Object Lineweights* enabled and normalized `(0, 0)` sheet origins.
- **Editable Attributed Title Blocks (`SECO_TBLOCK_A0`–`A4`):** Double-click any title-block attribute to fill `CLIENT`, `PROJECT`, `SITE`, `COUNTRY`, `PROJECT_NO`, `DRG_NO`, `SHEET`, `SCALE`, `TITLE`, `SUBTITLE`, `REF_DRG`, sign-offs, revision row, and `NOTE_1`–`NOTE_5`.
- **Always-Colored Corporate Logos:** Both the **Red & Green SECO emblem** (`SECO_LOGO`) and the **Orange & Gray SGS ISO 9001 badge** (`SECO_ISO9001_BADGE`) use 24-bit TrueColor vector fills so they always plot in full corporate color even under `monochrome.ctb`, while all engineering geometry plots strictly monochrome black-on-white.
- **Projection Symbol:** ISO 5456-2 First-Angle (`ISO_PROJ_FIRST`, default) and Third-Angle (`ISO_PROJ_THIRD`) blocks included.
- **Typography & Dimensioning:** ISO 3098 text style (`ISO_TEXT` using `isocpeur.ttf`) and ISO 129-1 associative dimension style (`SECO_ISO_MM`, `2.5 mm` plotted text/arrow height, trailing zeros suppressed, `BYLAYER`).

### Layer Standard (ISO 128 `0.5` Line Group — `2:1` Thick-to-Thin Ratio)

| Layer | Linetype | Plotted Weight | Purpose |
| :--- | :--- | :---: | :--- |
| `VISIBLE` | `Continuous` | `0.50 mm` | Visible outlines, cut section boundaries, profiles |
| `CUTTING` | `DASHDOT2` | `0.50 mm` | Section cutting-plane lines & arrows |
| `HIDDEN` | `HIDDEN2` | `0.35 mm` | Hidden edges and far-side (`FS`) ship stiffeners |
| `CENTER` | `CENTER2` | `0.25 mm` | Centerlines, axes of symmetry, pitch circles |
| `DIM` | `Continuous` | `0.25 mm` | Associative dimensions, extension lines, leaders |
| `HATCH` | `Continuous` | `0.25 mm` | Section hatching of cut solid material (`ANSI31`) |
| `VOID_OPENING` | `Continuous` | `0.25 mm` | Thin diagonal cross (`X`) for voids, openings, cutouts, flat surfaces, and break lines |
| `PHANTOM` | `PHANTOM2` | `0.25 mm` | Adjacent/reference structure, alternate positions |
| `TEXT` | `Continuous` | `0.25 mm` | View titles, callouts, notes, BOM text |
| `TITLEBLOCK_HEAVY` | `Continuous` | `0.50 mm` | Inner drawing frame & BOM outer perimeter |
| `TITLEBLOCK` | `Continuous` | `0.25 mm` | Sheet grid, title block lines/text, logos |
| `VIEWPORT` | `Continuous` | No-plot | Locked Paper Space viewports (`plot = 0`) |
| `MOLDED_LINE` | `PHANTOM2` | `0.25 mm` | Shipbuilding molded lines (`ML`) & throw arrows |
| `FRAME_GRID` | `CENTER2` | `0.18 mm` | Ship datum/frame grids (`FR`, `BL`, `CL`, `WL`) |
| `WELD_SYM` | `Continuous` | `0.25 mm` | ISO 2553 welding symbols & leaders |
| `REV_CLOUD` | `Continuous` | `0.35 mm` | Revision clouds & revision deltas |

---

## 2. Installing `seco-engineering-drawings` on Any Machine

### Prerequisites
- **Python 3.10+**
- **AutoCAD** with `accoreconsole.exe` (configured for AutoCAD 2027 by default)
- **Poppler** (`pdftoppm` on `PATH`) for rasterized visual inspection crops

### Setup
1. Unzip `seco-engineering-drawings.zip` into your Antigravity skills directory:
   - **Global (all projects):** `~/.gemini/config/skills/seco-engineering-drawings/`
   - **Project-local:** `<your-repo>/.agents/skills/seco-engineering-drawings/`
2. Install Python dependencies:
   ```powershell
   pip install -r ~/.gemini/config/skills/seco-engineering-drawings/requirements.txt
   ```
3. *(Only if using a different AutoCAD version than 2027)* Update `ACCORECONSOLE` in:
   - `scripts/cad_core/autocad_bridge.py`
   - `scripts/build_master_template.py`

### Running the Pipeline
Validate an engineering spec (`--intake-only`) or generate verified `DWG`, `DXF`, and `PDF` outputs:

```powershell
# Check schema & engineering completeness (flags missing dimensions, throw, toe, welds, etc.)
python ~/.gemini/config/skills/seco-engineering-drawings/scripts/generate_drawing.py spec.yaml --out ./out --intake-only

# Generate DWG, DXF, monochrome PDF, and run all 4 independent verification gates
python ~/.gemini/config/skills/seco-engineering-drawings/scripts/generate_drawing.py spec.yaml --out ./out
```

Three verified reference benchmarks are included inside `benchmarks/`:
- `mech_bearing_bracket.yaml` — General mechanical machined weldment (orthographic + section + BOM + ISO 2553 welds)
- `ship_bulkhead_subassy.yaml` — Watertight transverse bulkhead at `FR 24` (frame grid, molded-line throw, `HP`/`T` stiffeners with toe direction, `SYM ABOUT CL` + `STBD ONLY` manhole, welds, steel-weight BOM)
- `ship_leak_test_plan.yaml` — Double-bottom tank & leak testing plan per IACS UR S14 (monochrome `[HT]`/`[AT]` codes + ISO 128 thin continuous diagonal `X` void spaces)

---

## 3. Engineering Standards & Automated 4-Gate Verification

### Standards Encoded
- **General Drafting:** ISO 128 (`0.5` line group, precedence, section hatching rules, break lines, thin continuous `X` for voids/openings/flats), ISO 129-1 (associative dimensioning), ISO 5455 (preferred scales), ISO 5456-2 (First-Angle projection), ISO 5457 (sheet zones), ISO 3098 (technical lettering), ISO 2768-mK (general tolerances).
- **Shipbuilding & Marine:** ISO 128-15 (ship coordinate datums `BL`/`CL`/`WL`/`FR`, near-side solid vs. far-side dashed stiffeners), ISO 2553 (welding symbols by throat `a` or leg `z`), EN 10067 (`HP` bulb flats, `FB`, `L`, built-up `T`, `7.85 t/m³` steel mass BOM with `P`/`S`/`CL` handing), and IACS UR S14 (monochrome tank & leak testing conventions).
- **3D STEP Support (`cad_core/step_projector.py`):** Exact analytic B-Rep Hidden-Line Removal (`HLRBRep_Algo`) and planar cross-section slicing (`BRepAlgoAPI_Section`) via OpenCASCADE (`cadquery-ocp`), with strict anti-culling (`min_len_mm <= 1.0 mm`) so pipe end-caps, suction bells, flanges, and deck coamings remain intact.

### Independent 4-Gate Verification (`scripts/verify_drawing.py`)
Every generated drawing must pass all four gates before delivery:
1. **Gate 1 — Native CAD Integrity:** `0` errors in AutoCAD `AUDIT` and `0` errors in `ezdxf.audit()`.
2. **Gate 2 — Standards, Geometry & Marine Linter:** Metric header invariants, `100% BYLAYER`, zero duplicate/zero-length segments, `< 1e-6 mm` cross-view orthographic alignment, native non-overridden `DIMENSION` values matching geometry within `0.001 mm`, title-block completeness & exact `VIEWPORT` scale match, plus marine frame-grid, weld syntax, and BOM weight reconciliation (`≤ 1%`).
3. **Gate 3 — Plotted PDF Vector Check (PyMuPDF):** Exact ISO paper size, strictly monochrome drawing geometry + colored SECO/SGS corporate logos, ISO 128 `2:1` stroke-width hierarchy, zero clipped paths, rendered dashed linetypes, strict BOM table cell text enclosure, zero foreign lines crossing tables, and zero text-on-text overlaps.
4. **Gate 4 — Visual Inspection Crops:** High-DPI `pdftoppm` full-sheet, views, and title-block PNG crops for final review.
