# 🛒 Olist E-Commerce Analytics — Power BI Dashboard

End-to-end analysis of ~100,000 real orders from Olist, a Brazilian e-commerce marketplace (2016–2018). Nine raw CSVs cleaned in Power Query, modeled into a star schema, and presented as a five-page interactive report for a marketplace operations stakeholder.
 
## 🛠️ Tools & skills
`Power BI Desktop` · `Power Query (M)` · `DAX` · `Data modelling (star schema)` · `Data cleaning` · `Data visualisation`

---
 
## 📈 Report pages
 
| Page | Question it answers |
|------|--------------------|
| **Executive Overview** | How is the marketplace doing overall? |
| **Sales & Products** | What sells, and where's the money? |
| **Delivery Performance** | Are we delivering on time? |
| **Customer Reviews** | How happy are customers, and why? |
| **Geography & Sellers** | Where are our customers and sellers? |
 
---

<img width="1916" height="976" alt="executive overview " src="https://github.com/user-attachments/assets/e6fd6b7a-500d-4fd0-b2d3-b62f577450ad" />

## 🗂️ Dataset
 
**Source:** [Brazilian E-Commerce Public Dataset by Olist (Kaggle)](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) — CC BY-NC-SA 4.0
 
Nine CSVs spanning orders, items, payments, reviews, products, sellers, customers, geolocation, and a category-name translation lookup.
 
| File | Grain / contents | Role |
|------|------------------|------|
| `olist_orders_dataset` | One row per order; status + 5 timestamps | Fact (orders) |
| `olist_order_items_dataset` | One row per item per order; price, freight | Fact (line items) |
| `olist_order_payments_dataset` | Payment installments per order | Fact (payments) |
| `olist_order_reviews_dataset` | Review score + comments per order | Fact (reviews) |
| `olist_products_dataset` | Product attributes; category (Portuguese) | Dimension |
| `olist_sellers_dataset` | Seller ID, city, state | Dimension |
| `olist_customers_dataset` | Customer + unique customer ID, location | Dimension |
| `olist_geolocation_dataset` | Zip prefix → lat/lng | Dimension (geo) |
| `product_category_name_translation` | Portuguese → English categories | Lookup (merge) |
 
---

## 🧱 Data model
 
A **star schema** built around two fact tables at different grains (orders and order items), surrounded by dimensions and a dedicated date table.
 
<img width="1916" height="973" alt="star" src="https://github.com/user-attachments/assets/e9b4b95f-a997-437f-ae11-add900b04a4b" />

**Key relationships**
 
| From (many) | To (one) | Key |
|-------------|----------|-----|
| Order Items | Orders | `order_id` |
| Order Items | Products | `product_id` |
| Order Items | Sellers | `seller_id` |
| Orders | Customers | `customer_id` |
| Payments | Orders | `order_id` |
| Reviews | Orders | `order_id` |
| Orders | Date | `order_purchase_timestamp` |
 
---

## Key Findings

 
| Metric | Value |
|---|---|
| Total Revenue | $13.59M |
| Total Orders | 99.4K |
| Avg Order Value | $136.68 |
| Avg Review Score | 4.09 / 5 |
| Low (1–2★) Reviews | 14.68% |

---

