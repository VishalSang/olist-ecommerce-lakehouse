# Data model and quality decisions

## Table grains

| Table | One row represents |
|---|---|
| bronze_orders / silver_orders | An order |
| bronze_order_items / silver_order_items | An item within an order |
| bronze_products / silver_products | A product |
| bronze_customers / silver_customers | A source customer record keyed by customer_id |
| bronze_payments / silver_payments | A payment sequence within an order |
| gold_order_summary | An order, with item/payment summaries and customer attributes |
| gold_monthly_sales | A purchase month, considering delivered orders |
| gold_category_sales | A product category, considering delivered items |
| gold_customer_metrics | A customer_unique_id with delivered orders |
| gold_delivery_metrics | A customer state, considering delivered orders |

Items and payments are aggregated independently before order joins. A direct items-to-payments join at order ID could multiply rows and money. Left joins preserve all orders, including those without item/payment records.

## Observed quality results

- Approval timestamp nulls: 160 before and after conversion (141 canceled, 14 delivered, five created).
- Carrier handoff timestamp nulls: 1,783 before and after conversion.
- Customer delivery timestamp nulls: 2,965 before and after conversion.
- Purchase and estimated-delivery timestamp nulls: zero.
- Customer delivery before carrier handoff: 23 rows, preserved with `shipping_sequence_issue`.
- Missing product categories: 610. Product name/description lengths and photo count also each have 610 nulls; weight and each dimension each have two. These per-column counts do not establish overlap of affected rows.
- Payment records: two zero installments, nine zero values, three undefined types. The three undefined types overlap zero-value rows; 11 distinct records met at least one of these conditions.
- Source relationship checks reported no unmatched order items to orders/products, orders to customers, or payments to orders.

## Financial reconciliation

All-status source-to-Gold totals: merchandise BRL 13,591,643.70; freight BRL 2,251,909.54; recorded payments BRL 16,008,872.12. Each is reconciled to its own source, not asserted equal to the other financial measures.

Delivered-item merchandise total: BRL 13,221,498.11 across 110,197 items. Delivered customer summaries contain 96,478 orders and 93,358 customers. Repeat customers number 2,801.

## Delivery definitions

`delivery_days` is calendar days from purchase to customer delivery. `carrier_transit_days` is calendar days from carrier handoff to delivery and is null for missing timestamps or flagged inconsistent sequences. `delivery_lateness_days` compares actual and estimated delivery dates; positive values are late. The overall late rate is 6,534 / 96,470 × 100, rounded to 6.77%. Do not average state percentages or state averages into overall metrics without weighting the correct eligible population.
