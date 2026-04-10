# FAASST Roadmap / TODO

## 🔧 Phase 1 — Foundation

- [ ] **Data audit**: map existing Supabase records into the ISO 13399 schema
- [ ] **Schema validation**: confirm `dim_tools`, `fact_cutting_data`, and `ref_materials` correctly align with the golden record
- [ ] **Environment setup**: define `.env` variables and Supabase connection workflow

## 🧩 Phase 2 — Scraper & Extraction

- [ ] **Scraper MVP**: build a Playwright scraper for one manufacturer catalog
- [ ] **Vision extraction**: integrate a vision-capable LLM to parse tables and images into JSON
- [ ] **Data validation**: cross-check extracted tool geometry, `DC`, and `ZEFP` against SKU records

## 🚀 Phase 3 — API & Logic

- [ ] **Calculate endpoint**: implement `/v1/calculateInput`
- [ ] **Physics engine**: add radial chip thinning, Swiss-turning, and unit-agnostic logic
- [ ] **Output formatting**: return both metric and imperial results with clear logic notes

## 📈 Phase 4 — Polish & Launch

- [ ] **Documentation**: finalize README and API usage docs
- [ ] **Testing**: add endpoint and validation tests
- [ ] **Launch prep**: prepare deployment and initial SaaS onboarding flow
