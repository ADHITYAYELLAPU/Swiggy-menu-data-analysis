# 🍔 Swiggy Menu Data Analysis — Nested JSON to Relational Tables
### Food-Tech Pricing Intelligence | Excel · Power Query (M) · JSON Modeling · Dynamic Pricing Deconstruction

> **24.3 MB of raw nested JSON. 300+ menu records. 250+ add-on price components.** — Reverse-engineered the dynamic pricing architecture behind one of India's largest food delivery platforms — transforming deeply nested, production-grade API data into clean, analysis-ready relational datasets using nothing but Excel and Power Query.

---

## 📌 Project at a Glance

| Dimension | Value |
|---|---|
| 📦 Source Data | **24.3 MB** raw nested JSON — Swiggy menu API structure |
| 🍽️ Menu Records Processed | **300+ nested menu items** extracted and normalized |
| ➕ Add-on Price Components | **250+ incremental price components** cleaned and standardized |
| 📊 Output Tables Produced | **3 relational tables** — Items · Add-ons · Variants |
| 🔢 Nesting Depth | **Multi-level** — categories → items → groups → options → price deltas |
| ⚠️ Core Problem Solved | **Row explosion prevention** — controlled expansion of nested Lists & Records |
| 💡 Pricing Discovery | **UI price ≠ backend price** — delta-based runtime pricing model reverse-engineered |
| 🛠️ Tools | **Excel · Power Query (M Language) · JSON Data Modeling** |

---

## 🎯 The Problem — Why This Is Hard

Most analysts treat JSON as a file format. This project treats it as a **system architecture problem**.

Swiggy's menu API doesn't store final prices. It stores **components of prices** — and the actual price the customer sees is assembled dynamically at runtime by combining:

```
Final Item Price = Base Item Price
                 + Selected Variant Price Delta
                 + Selected Add-on Price Deltas
```

This means:
- A ₹199 burger on the Swiggy app **does not exist as ₹199 anywhere in the backend**
- The ₹199 is computed — base price + variant upgrade + add-on selection
- Raw data extracted naively will show wrong prices, broken rows, or silent errors

**This project solves that — correctly.**

---

## 🔍 Key Insights & Technical Discoveries

### 📐 Scale of the Raw Data Problem
- Source JSON weighs **24.3 MB** — a production-scale file, not a tutorial dataset
- Contains **300+ menu item records** embedded inside multiple layers of nested Lists and Records
- Each item can have multiple **variant groups**, each with multiple **options**, each with its own **price delta**
- Each item can also have multiple **add-on groups**, each with multiple **add-on items**, each also with a price delta
- Naively expanding this structure in Power Query **explodes rows** — a ₹199 item with 4 variants and 5 add-ons produces 20 duplicated rows before any filtering. This is the **cartesian product problem**, and it was solved using controlled, step-by-step expansion strategy

### 🧩 The 3-Table Relational Model — What Was Built

**Table 1 — Items (items.xlsx)**
- **1 row per menu item** — the atomic unit of analysis
- Columns: `Category` | `Food Type (Veg/Non-Veg)` | `Item Name` | `Base Item Price`
- Extracted cleanly from the top-level nested structure
- Base price only — no variant or add-on inflation

**Table 2 — Add-ons (Add ons.xlsx)**
- **1 row per add-on option** across all items
- Columns: `Item Name` | `Add-on Group Name` | `Add-on Item Name` | `Add-on Price (Delta)`
- **250+ rows** of incremental pricing components, each independently validated
- Critical insight: every price here is a **delta**, not a final price — adding without understanding this would produce completely wrong revenue analysis

**Table 3 — Variants (Variants.xlsx)**
- **1 row per variant option** — demonstrated as proof-of-concept for 1 category
- Columns: `Item Name` | `Variant Group` | `Variant Option` | `Variant Price Delta`
- Proves the methodology scales — the same M query logic extends to all categories
- Intentionally scoped: demonstrates engineering judgment (build, validate, then scale) over brute-force extraction

### 💥 The Row Explosion Problem — Quantified

| Scenario | Without Controlled Expansion | With This Approach |
|---|---|---|
| Item with 4 variants + 5 add-ons | **20 duplicate rows** per item (cartesian product) | **1 clean row** in Items table |
| 300 items × avg 3 variants × avg 4 add-ons | **3,600+ bloated rows** | **300 items + 250 add-ons normalized separately** |
| Price accuracy | **Silent errors** — deltas summed as base prices | **Correct** — each layer modeled independently |

This is not a theoretical concern — uncontrolled JSON expansion in Power Query is one of the most common sources of **silent data corruption** in food-tech and e-commerce analytics pipelines.

### 🔍 UI Price vs Backend Price — The Discrepancy

One of the most important findings of this project: **what the customer sees is not what is stored.**

- The Swiggy app displays a computed final price at runtime
- The backend JSON stores only **base price + delta components**
- Manual cross-validation against the live Swiggy UI confirmed that the pricing model was correctly reverse-engineered
- Any analyst who pulls raw price fields from the JSON without understanding this architecture will report wrong numbers — 100% of the time

This insight has direct implications for **revenue analysis, GMV reporting, pricing strategy audits, and menu performance tracking** at any food delivery company.

---

## 🏗️ Power Query Transformation Pipeline

```
📦 Swiggy Menu JSON — Transformation Architecture
│
├── INPUT: swiggy_menu.json (24.3 MB, multi-level nested structure)
│
├── STAGE 1 → Load & Parse JSON
│   └── Identify root structure: List of category objects
│       Each category → List of item objects
│       Each item → fields + nested add-on groups + nested variant groups
│
├── STAGE 2 → Items Extraction (Controlled)
│   ├── Expand category list → one row per category
│   ├── Expand item list → one row per item (within category)
│   ├── Select only flat fields: name, base_price, food_type, category
│   └── ✅ OUTPUT: items.xlsx — 1 clean row per menu item, no explosion
│
├── STAGE 3 → Add-ons Extraction (Nested List Navigation)
│   ├── From items, expand add-on group list ONLY (not variants)
│   ├── Within each add-on group, expand add-on item list
│   ├── Extract: item_name, group_name, addon_name, addon_price_delta
│   ├── Handle nulls, missing groups, and zero-delta add-ons
│   └── ✅ OUTPUT: Add ons.xlsx — 250+ rows, 1 per add-on option
│
├── STAGE 4 → Variants Extraction (Proof-of-Concept)
│   ├── Navigate variant group nesting (deeper than add-ons)
│   ├── Extract: item_name, variant_group, variant_option, variant_delta
│   ├── Demonstrated for 1 category to validate logic before scaling
│   └── ✅ OUTPUT: Variants.xlsx — methodology proven, ready to extend
│
└── STAGE 5 → Cross-Validation
    └── Manual spot-check: reconstructed prices vs live Swiggy UI
        Base price + variant delta + add-on delta = UI displayed price ✅
```

---

## ⚠️ Technical Challenges Solved

| Challenge | Why It's Hard | How It Was Solved |
|---|---|---|
| **Deeply nested Lists & Records** | Standard expand flattens wrong levels | Step-by-step level-aware expansion in M |
| **Cartesian row explosion** | Expanding multiple list columns simultaneously multiplies rows | Extracted each entity type (items, add-ons, variants) in **separate queries** |
| **UI price ≠ backend price** | Silent — no error thrown, just wrong numbers | Identified delta pricing model; validated against live UI |
| **Null add-on groups** | Items with no add-ons cause null expansion errors | Null checks added before list expansion steps |
| **Multi-level variant nesting** | Variants nest deeper than add-ons — different M path required | Separate extraction query with correct navigation path |

---

## 📊 Process Screenshots

### Raw JSON Structure & Power Query Editor
![Power Query View 1](Screenshot%202026-02-04%20142045.png)
![Power Query View 2](Screenshot%202026-02-04%20191341.png)
![Power Query View 3](Screenshot%202026-02-04%20191516.png)
![Extraction Progress 1](Screenshot%202026-02-04%20200812.png)
![Extraction Progress 2](Screenshot%202026-02-04%20230247.png)

### Output Tables & Validation
![Items Table Output](Screenshot%202026-02-05%20124100.png)
![Add-ons Table Output](Screenshot%202026-02-05%20164147.png)
![Variants Table Output](Screenshot%202026-02-05%20164959.png)
![Final Validation](Screenshot%202026-02-05%20233516.png)

---

## 💼 Skills Demonstrated (Recruiter Checklist ✅)

- ✅ **Production-Scale JSON Handling** — 24.3 MB real-world API data, not synthetic tutorial files
- ✅ **Nested Data Architecture Understanding** — navigated multi-level Lists & Records without flattening errors
- ✅ **Row Explosion Prevention** — controlled cartesian product problem through entity-separated query design
- ✅ **Dynamic Pricing Model Deconstruction** — reverse-engineered food-tech delta pricing logic from raw data
- ✅ **3-Table Relational Output Design** — Items, Add-ons, Variants modeled as a normalized schema
- ✅ **UI vs Backend Price Discrepancy** — identified and documented the gap between displayed and stored prices
- ✅ **Power Query (M Language)** — wrote step-level M transformations for controlled nested expansion
- ✅ **Data Validation Against Live System** — cross-checked reconstructed prices against Swiggy UI
- ✅ **Engineering Judgment** — scoped Variants as proof-of-concept rather than rushing incomplete output

---

## 🧩 Tools & Technologies

![Excel](https://img.shields.io/badge/Excel-217346?style=for-the-badge&logo=microsoft-excel&logoColor=white)
![Power Query](https://img.shields.io/badge/Power_Query_(M)-0078D4?style=for-the-badge&logo=microsoft&logoColor=white)
![JSON](https://img.shields.io/badge/JSON_Modeling-000000?style=for-the-badge&logo=json&logoColor=white)

| Tool | Role |
|---|---|
| **Microsoft Excel** | Output tables, data validation, final structured storage |
| **Power Query (M Language)** | All extraction, transformation, and normalization logic |
| **JSON** | Source format — 24.3 MB nested production menu data |
| **Manual UI Cross-Validation** | Spot-checking reconstructed prices against live Swiggy app |

---

## 📁 Repository Structure

```
📂 Swiggy-menu-data-analysis/
│
├── 📄 swiggy_menu.json                    # Source: 24.3 MB raw nested JSON
│
├── 📊 items.xlsx                          # Output Table 1: 1 row per menu item
├── 📊 Add ons.xlsx                        # Output Table 2: 250+ add-on components
├── 📊 Variants.xlsx                       # Output Table 3: Variant proof-of-concept
│
├── 🖼️ Screenshot 2026-02-04 142045.png    # Raw JSON structure in Power Query
├── 🖼️ Screenshot 2026-02-04 191341.png    # Add-on group expansion step
├── 🖼️ Screenshot 2026-02-04 191516.png    # Nested list navigation
├── 🖼️ Screenshot 2026-02-04 200812.png    # Mid-extraction progress
├── 🖼️ Screenshot 2026-02-04 230247.png    # Controlled expansion output
├── 🖼️ Screenshot 2026-02-05 124100.png    # Items table final output
├── 🖼️ Screenshot 2026-02-05 164147.png    # Add-ons table final output
├── 🖼️ Screenshot 2026-02-05 164959.png    # Variants table output
├── 🖼️ Screenshot 2026-02-05 233516.png    # Cross-validation against UI
│
└── 📄 README.md                           # This file
```

---

## 🚀 Future Extensions

| Enhancement | Business Value |
|---|---|
| Complete variant extraction for all categories | Full pricing simulation across entire menu |
| Dynamic price calculator (Base + Variant + Add-on) | Audit tool to validate UI prices against backend at scale |
| Extend to multiple restaurants | Cross-restaurant pricing comparison and benchmarking |
| Power BI pricing dashboard | Visual menu performance and pricing gap reporting |

---

## 👤 About the Author

**Adhitya Yellapu** — Data & Business Operations Analyst

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/adhitya-yellapu)
[![GitHub](https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/ADHITYAYELLAPU)
[![Portfolio](https://img.shields.io/badge/Portfolio-FF5722?style=for-the-badge&logo=google-chrome&logoColor=white)](https://adhityayellapu.github.io/Portfolio/)

---

*The JSON parsing, nested data normalization, and delta pricing logic demonstrated here directly mirrors the data engineering work performed by analytics teams at Swiggy, Zomato, and every food delivery platform that runs a configurable menu system — making this one of the most industry-relevant projects in this portfolio.*
