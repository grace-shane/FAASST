# FAASST — Machining Speed & Feed API (SaaS)

🚀 **FAASST** is a modern SaaS API that generates optimized cutting parameters for milling and turning tools. It turns messy manufacturer catalog data into reliable, production-ready recommendations.

---

## 1. Overview

This service provides a single source of truth for:

- **Cutting speed** (`V_c`)
- **Feed per tooth** (`f_z`)
- **RPM** and **feed rate**
- **Surface speed** and **chip load** adjustments

Built on:

- ISO 13399 tool geometry
- ISO 513 material groups
- Manufacturer catalogs as the authoritative data source
- Physics-based machining logic

---

## 2. Core Data Architecture — Golden Record

All scraped data is normalized into a standardized schema for interoperability with CAM/PLM systems.

### A. Tool Geometry (ISO 13399)

| Key | Description | Standard Field |
|---|---|---|
| `DC` | Cutting diameter | `hand_tool_diameter` |
| `ZEFP` | Effective cutting edges | `flute_count` |
| `RE` | Corner radius | `tip_radius` |
| `FHA` | Flute helix angle | `helix_angle` |
| `LU` | Usable length | `reach_length` |

### B. Material Groups (ISO 513)

We map proprietary material codes (e.g. `P2.1`) to universal groups:

- `P` — Steel
- `M` — Stainless Steel
- `K` — Cast Iron
- `N` — Non-ferrous / Aluminum
- `S` — HRSA / Titanium
- `H` — Hardened Steel

---

## 3. Scraping Pipeline

Manufacturer catalogs are often unstructured and dynamic, so the pipeline uses multiple stages to ensure accuracy.

- **Orchestration**: Node.js with Playwright/Puppeteer for JS-heavy pages
- **Extraction**: Vision-capable LLM (Gemini 1.5 Pro / GPT-4o) to parse images and tables into structured JSON
- **Validation**: Cross-check extracted values against manufacturer SKU data

---

## 4. Backend Strategy — Supabase + Logic

The backend uses Supabase/PostgreSQL with `pgvector` for advanced material matching.

### Primary tables

- `dim_tools` — static tool geometry per SKU
- `fact_cutting_data` — raw catalog ranges for `V_c` and `f_z`
- `ref_materials` — vector-enabled mapping for user materials to catalog groups

### Physics-driven logic

The API calculates more than raw numbers:

- **Radial chip thinning** adjustments when `a_e < 50%`
- **Swiss turning support** for guide bushing / reverse-turning constraints
- **Unit-agnostic outputs**: metric and imperial support (`m/min`, `mm/rev`, `SFM`, `IPT`)

---

## 5. API Draft

### POST `/v1/calculateInput`

Request example:

```json
{
  "sku": "ABC-123",
  "material": "4140 Steel",
  "hardness_hrc": 28,
  "strategy": {
    "type": "side_milling",
    "ae": 1.5,
    "ap": 12.0
  }
}
```

Response example:

```json
{
  "recommendation": {
    "speed_rpm": 4500,
    "feed_ipm": 62.5,
    "surface_speed_sfm": 589,
    "feed_per_tooth": 0.0035,
    "chip_load_adjusted": true,
    "logic_notes": "Increased feed by 15% due to radial chip thinning."
  }
}
```

---

## 6. Project Roadmap

Read the roadmap and actionable TODO list in [TODO.md](TODO.md).

---

## 7. Team & Standards

- **Lead Engineer**: Shane V Waid
- **Standards**: ISO 13399 / GTC compliant

---

## 8. Why FAASST?

FAASST makes tooling data searchable, consistent, and ready for automated machining decision-making.

---

## 9. Getting Started

1. **Clone the repo**

```bash
git clone https://github.com/your-org/FAASST.git
cd FAASST
```

2. **Install dependencies**

```bash
npm install
```

3. **Configure environment**

Create a `.env` file with your Supabase URL, service key, and any scraping credentials.

4. **Run the API locally**

```bash
npm run dev
```

5. **Test the endpoint**

Send a POST request to `/v1/calculateInput` with the example payload in the API Draft section.

6. **Extend the pipeline**

Start by adding one manufacturer scraper, then connect the extracted JSON into the Supabase schema.

