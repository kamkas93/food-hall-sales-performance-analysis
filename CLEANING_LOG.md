# Data Cleaning Log

This document records the data cleaning and preparation steps applied
to the raw datasets prior to analysis.

## Dataset: sales_aperitivo_RAW

### Initial data review

The raw dataset contains aggregated daily sales per product.
Each row represents total quantity sold for a given product on a given date.

### Step 1: Sale date standardization

Issue:
The `sale_date` column contained multiple date formats
(e.g. YYYY-MM-DD and DD/MM/YYYY).

Action:
Google Sheets correctly recognized all values as date objects.
The column was standardized by applying a uniform date format
(YYYY-MM-DD).

Result:
All dates are stored consistently and are ready for time-based analysis.

### Step 2: Text normalization – trimming whitespace

Issue:
Potential leading and trailing whitespace detected in text fields
(`product_name`, `category`), which could affect joins and aggregations.

Action:
Applied TRIM (and CLEAN where necessary) to remove extra whitespace
from key text columns.

Result:
Text fields are standardized and safe for grouping and joining.

### Step 3: Logical duplicate detection

Issue:
Duplicate business records could inflate aggregated sales metrics.

Action:
Created a composite logical key combining:
sale_date, product_name, category, quantity, and product_price.
Used COUNTIF to identify repeated composite keys.

Result:
Each record represents a unique daily product-level sales aggregate.
No logical duplicates detected.

### Step 4: Text normalization

- Trimmed whitespace in product_name and category fields
- Standardized category naming (lowercase, consistent labels)
- Ensured sale_date stored as DATE type

### Step 5: Numeric validation

- Validated quantity > 0
- Validated product_price_gross > 0
- Verified total_price_gross = quantity * product_price_gross

### Step 6: Data completeness & range checks

- Verified no missing critical fields
- Confirmed date range limited to Q1 2025
- Checked price ranges for business plausibility

## Sales_Aperitivo – Data Cleaning Summary

**Dataset:** sales_aperitivo_RAW  
**Output:** sales_aperitivo_CLEAN  

### Performed cleaning steps:
- Standardized date format to ISO (YYYY-MM-DD)
- Removed empty rows and blank values in `sale_date`
- Normalized product names and categories (trimmed whitespace, unified casing)
- Validated numeric fields (`quantity`, `product_price_gross`)
- Identified and removed logical duplicates using composite row keys
- Verified absence of NULL values after cleaning
- Performed basic range validation on quantity and prices

### Result:
Dataset is consistent, normalized, and ready for transformation and aggregation.


## Dataset: Warehouse_Products_Tasty_Town_2025_RAW

The raw dataset contains warehouse purchase records for products used in cocktail
preparation and bar operations.

Each row represents a single purchase entry of a specific product from a supplier,
including purchase date, purchased quantity, unit of measure, net purchase cost,
and invoice reference number.

The dataset serves as a cost reference layer for ingredients and products and is
intended to support later beverage cost and profitability calculations rather than
sales performance analysis on its own.

The data is recorded at purchase-event level and may contain multiple entries
for the same product purchased on different dates or under different pricing conditions.

### Step 1 – Standardization

To ensure consistency across datasets and enable reliable joins, text-based
columns were standardized.

Applied transformations:
- Product names were converted to uppercase and trimmed to remove extra spaces.
- Product categories were converted to uppercase and trimmed.
- Supplier names were standardized to uppercase for consistency.

Standardization aligns this dataset with the sales data structure and reduces
the risk of join mismatches caused by inconsistent text formatting.

### Step 2 – Date normalization

Date columns were reviewed to ensure consistent formatting across datasets.

- Purchase dates in the warehouse dataset were validated and standardized to ISO format (YYYY-MM-DD).
- This format was selected to ensure compatibility with SQL-based analysis (BigQuery)
  and to avoid ambiguity between regional date formats.

Consistent date formatting enables reliable time-based joins, aggregations,
and filtering during analysis.

### Step 3 Unit normalization

Measurement units were standardized to ensure consistency across purchase records.
A standardized purchase quantity was calculated using unit multipliers to convert
all quantities into base units (grams or milliliters).

No cost calculations or analytical metrics were derived at this stage.
This step prepares the dataset for accurate cost analysis in later transformations.

#### Step 4 Handling non-convertible units and missing cost data

Some warehouse products (e.g. foams, egg whites, vanilla pods, ice cubes, fresh preparations)
are purchased in non-linear, count-based, or batch-based units that cannot be reliably
converted into standard mass or volume units (grams or milliliters).

Additionally, for certain components (e.g. foams, modifiers, fresh prep ingredients),
direct unit-level cost information is not available in the source data.

For these items:
- `unit_standardized` and `unit_multiplier` remain NULL
- standardized purchase quantity is not calculated
- unit-level cost is intentionally left as NULL

This approach reflects real-world bar operations, where such components are typically
managed and costed at a batch or operational level rather than per unit.

No cost imputation or estimation was applied at the cleaning stage.
These items will be handled explicitly during later transformation or analysis phases,
or excluded from unit-based beverage cost calculations where appropriate.


