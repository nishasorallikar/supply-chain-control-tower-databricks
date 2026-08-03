# 📐 Star Schema Modeling & Design

To enable high-performance BI queries, aggregations, and business intelligence reporting, the Gold Layer is modeled into a clean, optimized **Star Schema**. This architectural pattern decouples transactional facts from descriptive dimensions, ensuring fast joins and query-performance optimizations (such as partition pruning).

### 📐 Entity-Relationship Diagram (ERD)

```mermaid
erDiagram
    dim_customer ||--o{ fact_orders : "has"
    dim_product ||--o{ fact_orders : "includes"
    dim_date ||--o{ fact_orders : "placed_on"
    dim_shipping ||--o{ fact_orders : "shipped_with"

    dim_customer {
        bigint customer_id PK
        string customer_fname
        string customer_lname
        string customer_segment
        string customer_city
        string customer_state
        string customer_country
        string customer_zipcode
    }

    dim_product {
        bigint product_card_id PK
        bigint product_category_id
        string product_name
        double product_price
        string category_name
        string department_name
    }

    dim_date {
        date date PK
        int year
        int quarter
        int month
        string month_name
        int day
        string day_name
    }

    dim_shipping {
        string shipping_mode PK
        string delivery_status PK
    }

    fact_orders {
        bigint order_item_id PK
        bigint order_id
        bigint customer_id FK
        bigint product_card_id FK
        date order_date FK
        string shipping_mode FK
        string delivery_status FK
        double sales
        int order_item_quantity
        double order_item_total
        double order_profit_per_order
        int late_delivery_risk
    }
```

### 📊 Schema Components & Table Specifications

The database model is structured around a central **Fact Table** surrounded by four descriptive **Dimension Tables**:

#### 🛒 1. Central Fact Table: `fact_orders`
* **Grain**: One record per order item.
* **Keys (Foreign Keys)**: 
  * `customer_key` (FK to `dim_customer`)
  * `product_key` (FK to `dim_product`)
  * `date_key` (FK to `dim_date`)
  * `shipping_key` (FK to `dim_shipping`)
* **Identifiers**: `order_id` (Unique Order String)
* **Measures (Metrics)**:
  * `order_quantity` (Quantity ordered)
  * `order_amount` / `total_amount` (Transactional sale and revenue metrics)
  * `discount_amount` (Applied discount deductions)
  * `shipping_cost` (Logistics delivery cost)
  * `tax_amount` (Calculated government taxes)
  * `profit_amount` (Calculated margin benefits)

#### 👥 2. Customer Dimension: `dim_customer`
* **Key**: `customer_key` (Surrogate Key - PK)
* **Attributes**: `customer_id` (Source ID), `customer_name`, `customer_segment` (Consumer, Corporate, etc.), and complete address details (`country`, `state`, `city`, `postal_code`, `created_date`).

#### 📦 3. Product Dimension: `dim_product`
* **Key**: `product_key` (Surrogate Key - PK)
* **Attributes**: `product_id` (Source ID), `product_name`, `product_category`, `product_subcategory` (Categories and departments), `brand`, and pricing details (`unit_price`, `created_date`).

#### 🚚 4. Shipping Dimension: `dim_shipping`
* **Key**: `shipping_key` (Surrogate Key - PK)
* **Attributes**: `shipping_mode` (First Class, Second Class, etc.), `carrier`, `service_level`, and transit duration (`delivery_time_days`, `created_date`).

#### 📅 5. Date Dimension: `dim_date`
* **Key**: `date_key` (Surrogate Key - PK)
* **Attributes**: Complete calendar tracking (`full_date`, `day`, `day_name`, `week`, `month`, `month_name`, `quarter`, `year`) and operational flags (`is_weekend`, `is_holiday`).
