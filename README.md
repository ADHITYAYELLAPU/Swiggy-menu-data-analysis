# Swiggy-menu-data-analysis
"Self-Initiated Project"  
Analysing menu items, variants, and add-on pricing logic from a food delivery platform using Excel &amp; Power Query

🔍 Project Snapshot

Domain: Food-tech / E-commerce
Tools: Excel, Power Query (M), JSON
Focus Areas:
 > Nested data handling
 > Dynamic pricing logic
 > Variants & add-ons modeling
 > Real-world data normalization

This project demonstrates how complex, real-world menu data can be transformed into clean, analysis-ready datasets.

📌 Project Overview

Food delivery platforms like Swiggy do not store final menu prices directly.
Instead, they rely on a dynamic pricing model where:

Items have a base price

Variants and add-ons contribute incremental price deltas

Final price is calculated at runtime

This project explores how such menu data is structured and shows how to extract, normalize, and analyze it using Excel and Power Query.

🎯 Objectives

 ✔ Extract item-level menu data from nested structures
 
 ✔ Normalize add-ons and variants into separate tables
 
 ✔ Understand and document pricing delta logic
 
 ✔ Handle common real-world issues like:
 
    > Deeply nested Lists & Records
    
    > Row explosion during expansion
    
    > UI price ≠ backend stored price

🛠 Tools & Technologies Used
   > Microsoft Excel
   > Power Query (M Language)
   > JSON Data Modeling
   > Manual validation against UI behavior

📂 Project Structure

swiggy-menu-data-analysis/
│
├── README.md
│
├── data/
│   ├── items.xlsx
│   ├── addons.xlsx
│   └── variants.xlsx   (Work in Progress)
│
├── power-query/
│   ├── item_extraction_steps.md
│   ├── addon_extraction_steps.md
│   └── pricing_logic_notes.md
│
├── screenshots/
│   ├── power_query_flow.png
│   ├── item_table_output.png
│   ├── addon_table_output.png
│   └── price_mismatch_example.png
│
└── insights/
    └── business_insights.md

📊 Datasets Prepared
1️⃣ Items Table (items.xlsx)

One row per menu item

Columns:

Category

Food Type (Veg / Non-Veg)

Item Name

Base Item Price

2️⃣ Add-ons Table (addons.xlsx)

One row per add-on option

Columns:

Item Name

Add-on Group Name

Add-on Item Name

Add-on Price (Delta)

⚠ Add-on prices represent incremental price changes, not final item prices.

3️⃣ Variants Table (variants.xlsx) 🚧

" Variants extracted for one category as a proof of concept "

Demonstrates:

Variant groups

Option-level price deltas

Relationship to base item price

Status: 🚧 Work in Progress
(The same approach can be extended to all categories.)

⚠️ Key Challenges Solved

 > Navigated deeply nested JSON structures

 > Prevented cartesian row explosion in Power Query

 > Identified why UI prices differ from stored prices

 > Modeled configurable product pricing

 > Built analytics-ready relational tables

💡 Pricing Logic (Key Learning)

Food-tech platforms use dynamic pricing models:

Final Item Price =
Base Item Price
+ Selected Variant Price Deltas
+ Selected Add-on Price Deltas


The backend stores only components, not final prices.

📈 Business Insights Gained

Raw menu data is not analysis-ready

Add-ons and variants use incremental pricing

UI pricing is computed dynamically at runtime

Proper normalization is critical for:

Revenue analysis

Pricing strategy

Product analytics

🚧 Project Status

Item extraction: ✅ Completed

Add-on extraction: ✅ Completed

Variant extraction: 🚧 In Progress

Variants are intentionally demonstrated for one category to show logic and scalability.

⚖️ Disclaimer

This is a self-initiated, educational project.
All data is derived from publicly accessible menu structures.
No private, internal, or proprietary data is included.

🧠 What I Learned

 > Real-world data is messy and deeply nested

 > Pricing systems rely on deltas, not flat values

 > Power Query requires controlled expansion strategies

 > Data modeling matters more than just extraction

🚀 Future Enhancements

Complete variant extraction for all categories

Build a dynamic price calculator

Extend to multiple restaurants

Create a Power BI pricing dashboard

📬 Contact

Feel free to connect if you’d like to discuss:

Data modeling

E-commerce analytics

Pricing logic

Power Query best practices
