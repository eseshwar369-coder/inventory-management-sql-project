# Inventory Management System — MySQL

A relational database project for managing product categories, product inventory, sales records, and practical business reports using MySQL.

## Project goals

- Model categories, products, and sales using relational tables.
- Enforce primary-key and foreign-key relationships.
- Apply validation rules to prices, stock quantities, and quantities sold.
- Produce business reports for sales performance and inventory management.
- Document repeatable setup, report logic, and testing considerations.

## Technology

- MySQL 8.0.16+ (recommended, for enforced `CHECK` constraints)
- MySQL Workbench
- SQL, Git, and GitHub

## Database schema

The project uses three tables:

| Table | Purpose | Main columns |
|---|---|---|
| `categories` | Product category lookup | `category_id` (PK), `category_name` |
| `product` | Product details and current inventory | `product_id` (PK), `product_name`, `category_Id` (FK), `Unit_price`, `Unitin_stock` |
| `sales` | Sales transaction records | `sales_Id` (PK), `product_id` (FK), `sales_date`, `quantity_sold` |

Relationships:
- `product.category_Id` references `categories.category_id`.
- `sales.product_id` references `product.product_id`.
- One category can have many products.
- One product can have many sales records.

See [`docs/schema.md`](docs/schema.md) for more details.

## Repository structure

```text
inventory-management-sql-project/
├── README.md
├── sql/
│   ├── 01_create_tables.sql
│   ├── 02_insert_sample_data.sql
│   ├── 03_constraints_and_validation.sql
│   ├── 04_sales_reports.sql
│   ├── 05_inventory_reports.sql
│   └── 06_testing.sql
├── docs/
│   ├── schema.md
│   └── testing-results.md
└── results/
    └── .gitkeep
```

## Setup instructions

### Option A — Create a fresh demo database

1. Open MySQL Workbench and connect to your local MySQL instance.
2. Open and run `sql/01_create_tables.sql`.
3. Run `sql/02_insert_sample_data.sql`.
4. Run `sql/03_constraints_and_validation.sql` only after reviewing it. It is designed for the demo schema and uses named constraints.
5. Run the report queries from `sql/04_sales_reports.sql` and `sql/05_inventory_reports.sql`.

**Warning:** `01_create_tables.sql` drops and recreates the three tables in the `inventory_management_demo` database. Do not run it against a database containing data you need to preserve.

### Option B — Run reports on your existing database

Your existing Workbench schema is named `inventrydb` and uses the tables `categories`, `product`, and `sales`. Open the report files and change the `USE` statement to `USE inventryDB;` if required by your environment. Do not run the fresh-demo setup script against your existing database.

The report queries use the column names established during the project:
- `categories.category_id`, `categories.category_name`
- `product.product_id`, `product.product_name`, `product.category_Id`, `product.Unit_price`, `product.Unitin_stock`
- `sales.sales_Id`, `sales.product_id`, `sales.sales_date`, `sales.quantity_sold`

MySQL table-name case sensitivity can vary by operating system and configuration. Use the exact table names shown in your Workbench schema.

## Business reports

1. **Total units sold by product** — sums recorded quantities for each product, including products with no matching sales.
2. **Total units sold by category** — sums recorded sales quantities across products in each category.
3. **Current inventory and stock status** — classifies products as out of stock, low stock (1–10 units), or in stock (above 10 units).
4. **Inventory value by category and grand total** — sums current unit price multiplied by current stock. The grand total is returned as a separate row.
5. **Monthly sales trends** — sums units sold per year-month.
6. **Products without recorded sales** — lists products with no matching rows in the sales table.

## Validation and testing

The intended rules are:
- `Unit_price > 0`
- `Unitin_stock >= 0`
- `quantity_sold >= 0`

The exact constraints in a database should be checked with `SHOW CREATE TABLE` or the Workbench table editor. `CHECK` constraints are enforced by MySQL 8.0.16 and later.

The test script includes read-only integrity checks and a guarded stock-update demonstration that is rolled back. Invalid-value rejection tests are documented in `docs/testing-results.md`; execute them individually in a disposable test database because an expected constraint error may stop a multi-statement script.

A foreign key does not prevent selling more units than are in stock. Preventing overselling requires a transaction-based sale workflow or another explicit stock-control mechanism.

## Important assumptions and limitations

- `sales` stores quantity and date but not the transaction sale price. The project therefore reports **units sold**, not historical sales revenue.
- Inventory value is calculated using the current `Unit_price` and `Unitin_stock`; it is not a historical valuation.
- The low-stock threshold of 10 units is an example business rule and can be changed.
- A product with no matching row in `sales` has no recorded sales in this dataset; this does not prove it was never sold historically.
- The demo data is synthetic and is provided for reproducible testing. It should not be presented as the user's original data.

## Next improvements

- Add a transaction-based sale procedure that checks and deducts stock atomically.
- Store sale price per transaction to support historical revenue reports.
- Add product-specific reorder thresholds.
- Add automated tests and save verified output screenshots or CSVs.

## Author

Add your name and GitHub profile here before publishing.
