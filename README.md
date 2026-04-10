Technical Requirements: Machining Speed \& Feed API (SaaS)1. Project OverviewThe goal is to build a centralized SaaS API that provides optimized cutting parameters ($V\_c$, $f\_z$) based on ISO 13399 tool geometry and ISO 513 material groups. The system will leverage scraped manufacturer data as a "Source of Truth" and apply a physics-based logic layer for real-world machining environments.2. Core Data Architecture (The "Golden Record")All scraped data must be normalized into a standard schema. We will utilize ISO 13399 keys for interoperability with modern CAM/PLM systems.A. Tool Geometry (ISO 13399 Mapping)KeyDescriptionStandard MappingDCCutting Diameterhand\_tool\_diameterZEFPEffective Cutting Edgesflute\_countRECorner Radiustip\_radiusFHAFlute Helix Anglehelix\_angleLUUsable Lengthreach\_lengthB. Material Groups (ISO 513)To ensure the API is "searchable," we must map proprietary brand material names (e.g., "P2.1") to a universal standard:P (Steel)M (Stainless Steel)K (Cast Iron)N (Non-ferrous/Aluminium)S (HRSA/Titanium)H (Hardened Steel)3. The Scraping PipelineSince manufacturer data is unstructured (PDFs, JS-heavy SPAs), the scraper needs a multi-stage approach.Orchestration: Node.js with Playwright/Puppeteer to navigate catalogs and handle dynamic content.Extraction: Vision-capable LLM (Gemini 1.5 Pro / GPT-4o) to ingest table screenshots and output structured JSON.Validation: Cross-reference extracted DC and ZEFP against the manufacturer's master SKU list to ensure data integrity.4. Backend Strategy (Supabase \& Logic)We will utilize Supabase (PostgreSQL) for data storage and pgvector for material matching.Database Tables:dim\_tools: Static geometry based on SKU.fact\_cutting\_data: Raw $V\_c$ and $f\_z$ ranges from catalogs.ref\_materials: A vector-enabled table for mapping "User Input Material" to "Catalog Material Group."The Physics Engine (The "Value Add"):The API shouldn't just return raw numbers. It must calculate Adjusted Parameters:Radial Chip Thinning: If $a\_e < 50\\%$, the API must increase the programmed feed rate to maintain the target chip thickness ($h\_x$).Swiss-specific Logic: Support for Reverse Turning (pulling away from guide bushing) and small-diameter high-RPM limiters.Unit Agnostic: Input/Output must support both Metric ($m/min$, $mm/rev$) and Imperial ($SFM$, $IPT$).5. API Endpoints (Draft)POST /v1/calculateInput:JSON{

&#x20; "sku": "ABC-123",

&#x20; "material": "4140 Steel",

&#x20; "hardness\_hrc": 28,

&#x20; "strategy": {

&#x20;   "type": "side\_milling",

&#x20;   "ae": 1.5,

&#x20;   "ap": 12.0

&#x20; }

}

Output:JSON{

&#x20; "recommendation": {

&#x20;   "speed\_rpm": 4500,

&#x20;   "feed\_ipm": 62.5,

&#x20;   "surface\_speed\_sfm": 589,

&#x20;   "feed\_per\_tooth": 0.0035,

&#x20;   "chip\_load\_adjusted": true,

&#x20;   "logic\_notes": "Increased feed by 15% due to radial chip thinning."

&#x20; }

}

6\. Next Steps for DevelopmentData Audit: Review existing Supabase records and map them to the new ISO 13399 schema.Scraper MVP: Build a Playwright script for one major manufacturer (e.g., Sandvik or Kennametal) to test the Vision-to-JSON extraction reliability.Material Rosetta Stone: Create the initial pgvector material library to handle the "dirty" material names found in catalogs.Lead Engineer: Shane V WaidStandard: ISO 13399 / GTC Compliant

