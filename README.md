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

<img width="1916" height="976" alt="executive overview " src="https://github.com/user-attachments/assets/e6fd6b7a-500d-4fd0-b2d3-b62f577450ad" />
[Executive Overview]

---

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

## 💡 Key insights
 
| Metric | Value |
|---|---|
| Total Revenue | R$13.59M |
| Total Orders | 99.4K |
| Avg Order Value | R$136.68 |
| Avg Review Score | 4.09 / 5 |
| Low (1–2★) Reviews | 14.68% |
 
**1. Late delivery is the single strongest driver of customer dissatisfaction.**
On-time orders average ~4 stars; late orders fall to ~2 — satisfaction is halved. → Logistics reliability protects ratings more than any product-side fix. → Prioritize carrier performance in the states with the highest late-delivery rates.
 
**2. Nearly 15% of orders earn 1–2 stars, and they cluster.**
Low reviews concentrate in slow deliveries and a handful of weak categories. → Dissatisfaction is a focused fix-list, not a broad problem. → Target the bottom categories and slowest delivery lanes first.
 
**3. Both demand and supply concentrate heavily in São Paulo.**
SP leads with ~42K orders, and sellers are even more SP-concentrated than customers. Remote northern states pay the highest freight and wait the longest. → Growth outside the southeast is a logistics problem before it is a marketing problem.
 
**4. Most orders actually arrive *early*.**
The delivery-vs-estimate distribution sits firmly in negative territory — Olist over-estimates delivery dates. → The late-delivery problem is about the tail, not the average.

---

### What I Built :
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

- Star schema: Orders + Order Items at the center; Products, Customers, Sellers, Geolocation, and a dedicated marked Date table as dimensions
- Used `customer_unique_id` (not `customer_id`) for retention analysis — Olist issues a new customer_id per order

## 🧹 ETL — Power Query

- Cleaned and typed 9 raw CSVs; explicit datetime casting on all order timestamps
- Derived `delivery_days`, `delivery_delta` (actual vs estimated), and `is_late` flag — with null-safe logic so cancelled/undelivered orders don't error
- Merged the Portuguese→English category translation into Products; labeled missing categories as "unknown" instead of dropping rows (preserves revenue accuracy)
- Built a grouped **Payments by Order** query (via Reference) to prevent double-counting orders paid across multiple payment rows
- Deduplicated the geolocation table to one lat/lng per zip prefix to avoid relationship fan-out

## 🧮 DAX measures
 
~30 measures organised into folders. A few headline ones:
 
```dax
Total Revenue   = SUM ( 'Order Items'[price] )
Avg Order Value = DIVIDE ( [Total Revenue], [Total Orders] )
% Late Deliveries =
    DIVIDE (
        CALCULATE ( [Total Orders], Orders[is_late] = TRUE() ),
        CALCULATE ( [Total Orders], NOT ISBLANK ( Orders[delivery_days] ) )
    )
Avg Review Score = AVERAGE ( Reviews[review_score] )
Revenue YoY %    = DIVIDE ( [Total Revenue] - [Revenue LY], [Revenue LY] )
```
 
Full set in [`Olist_DAX_Measures.txt`](Olist_DAX_Measures.txt).



