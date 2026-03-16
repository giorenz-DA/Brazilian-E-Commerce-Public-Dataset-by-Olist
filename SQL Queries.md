# Data Exploration

1. Checking for null values
- customer:
     
```SQL
SELECT
  * 
FROM `casestudy2-489003.brazil_ecommerce.customer`
WHERE customer_id IS NULL OR
  customer_unique_id IS NULL OR
  customer_zip_code_prefix IS NULL OR
  customer_city IS NULL OR
  customer_state IS NULL
```

- geolocation:
  
```SQL
SELECT
  * 
FROM `casestudy2-489003.brazil_ecommerce.geolocation`
WHERE geolocation_zip_code_prefix IS NULL OR
  geolocation_lat IS NULL OR
  geolocation_lng IS NULL OR
  geolocation_city IS NULL OR
  geolocation_state IS NULL
```

- order_items:
```SQL
SELECT
  * 
FROM `casestudy2-489003.brazil_ecommerce.order_items`
WHERE order_id IS NULL OR
  order_item_id IS NULL OR
  price IS NULL OR
  product_id IS NULL OR
  seller_id IS NULL OR
  shipping_limit_date IS NULL OR
  price IS NULL OR
  freight_value IS NULL
```

- order_payments:
```SQL
SELECT
  *
FROM `casestudy2-489003.brazil_ecommerce.order_payments`
WHERE order_id IS NULL OR
  payment_sequential IS NULL OR
  payment_type IS NULL OR
  payment_installments IS NULL OR
  payment_value IS NULL
```

- orders:
```SQL
SELECT
  *
FROM `casestudy2-489003.brazil_ecommerce.orders`
WHERE order_id IS NULL OR
  order_id IS NULL OR
  customer_id IS NULL OR
  order_status IS NULL OR
  order_purchase_timestamp IS NULL OR
  order_approved_at IS NULL OR
  order_delivered_carrier_date IS NULL OR
  order_delivered_customer_date IS NULL OR
  order_estimated_delivery_date IS NULL
```


- product_category_name:
```SQL
SELECT
  *
FROM `casestudy2-489003.brazil_ecommerce.product_category_name_translation`
WHERE product_category_name IS NULL OR
  product_category_name_english IS NULL 
```


- products:
```SQL
SELECT
  *
FROM `casestudy2-489003.brazil_ecommerce.products`
WHERE product_id IS NULL OR
  product_category_name IS NULL OR
  product_name_lenght IS NULL OR
  product_description_lenght IS NULL OR
  product_photos_qty IS NULL OR
  product_weight_g IS NULL OR
  product_length_cm IS NULL OR
  product_height_cm IS NULL OR
  product_width_cm IS NULL
```


- reviews:
```SQL
SELECT
  *
FROM `casestudy2-489003.brazil_ecommerce.reviews`
WHERE review_id IS NULL OR
  order_id IS NULL OR
  review_score IS NULL OR
  review_creation_date IS NULL OR
  review_answer_timestamp IS NULL 
```

- sellers:
```SQL
SELECT
  *
FROM `casestudy2-489003.brazil_ecommerce.sellers`
WHERE seller_id IS NULL OR
  seller_zip_code_prefix IS NULL OR
  seller_city IS NULL OR
  seller_state IS NULL 
```
2. Checking for ambiguous values on the tables

- customer:
```SQL
SELECT 
  LENGTH(customer_id) AS customer_id_char_len,
  LENGTH(customer_unique_id) AS customer_unique_id_len
FROM `casestudy2-489003.brazil_ecommerce.customer`
GROUP BY customer_id_char_len, customer_unique_id_len
```

- order_items
```SQL
SELECT 
  LENGTH(order_id) AS order_id_len,
  LENGTH(product_id) AS product_id_len,
  LENGTH(seller_id) AS seller_id_len
FROM `casestudy2-489003.brazil_ecommerce.order_items`
GROUP BY order_id_len, product_id_len, seller_id_len
```


- order_payments
```SQL
SELECT 
  LENGTH(order_id) AS order_id_char_len
FROM `casestudy2-489003.brazil_ecommerce.order_payments`
GROUP BY order_id_char_len
```
checking for payment_value
```SQL
SELECT 
  MIN(payment_value) AS min_payment_value,
  MAX(payment_value) AS max_payment_value
FROM `casestudy2-489003.brazil_ecommerce.order_payments` 
```

- orders:
```SQL
SELECT 
  LENGTH(order_id) AS order_id_len,
  LENGTH(customer_id) AS customer_id_len
FROM `casestudy2-489003.brazil_ecommerce.orders`
GROUP BY order_id_len, customer_id_len
```

- product_category_name:
```SQL
-products
SELECT 
  LENGTH(product_id) AS order_id_len
FROM `casestudy2-489003.brazil_ecommerce.products`
GROUP BY order_id_len
```

- reviews:
```SQL
SELECT 
  LENGTH(review_id) AS review_id_len,
  LENGTH(order_id) AS order_id_len
FROM `casestudy2-489003.brazil_ecommerce.reviews`
GROUP BY review_id_len, order_id_len
```

- seller:
```SQL
SELECT 
  LENGTH(seller_id) AS seller_id_len
FROM `casestudy2-489003.brazil_ecommerce.sellers`
GROUP BY seller_id_len
```

##Data Cleaning

1. Creation of products table with product_id and English product names:
```SQL
DROP TABLE IF EXISTS `casestudy2-489003.brazil_ecommerce.products_cleaned`;

CREATE TABLE IF NOT EXISTS `casestudy2-489003.brazil_ecommerce.products_cleaned` AS (

SELECT
  a.product_id,
  a.product_category_name,
  b.product_category_name_english
FROM `casestudy2-489003.brazil_ecommerce.products` AS a
LEFT OUTER JOIN `casestudy2-489003.brazil_ecommerce.product_category_name_translation` AS b
ON a.product_category_name = b.product_category_name
WHERE a.product_category_name IS NOT NULL
);
```

```SQL
INSERT INTO `casestudy2-489003.brazil_ecommerce.product_category_name_translation` (product_category_name, product_category_name_english)
VALUES ('pc_gamer', 'gaming_pc'),
       ('portateis_cozinha_e_preparadores_de_alimentos', 'portable_kitchen_and_food_preparators')
```
Some data cleaning steps are added to aggregation during analysis

## Analysis

Top 10 product categories qty sold and revenue
```SQL
SELECT 
  b.product_category_name_english,
  COUNT(a.product_id) AS qty_sold,
  ROUND(SUM(a.price),2) AS total_revenue
FROM `casestudy2-489003.brazil_ecommerce.order_items` AS a
LEFT JOIN `casestudy2-489003.brazil_ecommerce.products_cleaned` AS b
ON a.product_id = b.product_id
GROUP BY b.product_category_name_english
ORDER BY total_revenue DESC
LIMIT 10
```
Monthly Revenue trend

```SQL
SELECT
  FORMAT_DATE('%B', DATE(b.order_purchase_timestamp)) AS sales_month,
  ROUND (SUM(
    CASE
      WHEN EXTRACT(YEAR FROM b.order_purchase_timestamp) = 2016 THEN a.price
      ELSE 0
  END
    ),2) AS `2016`,
  ROUND (SUM(
    CASE
      WHEN EXTRACT(YEAR FROM b.order_purchase_timestamp) = 2017 THEN a.price
      ELSE 0
  END
    ),2) AS `2017`,
  ROUND (SUM(
    CASE
      WHEN EXTRACT(YEAR FROM b.order_purchase_timestamp) = 2018 THEN a.price
      ELSE 0
  END
    ),2) AS `2018`
FROM `casestudy2-489003.brazil_ecommerce.order_items` AS a
LEFT JOIN `casestudy2-489003.brazil_ecommerce.orders` AS b
ON a.order_id = b.order_id
WHERE b.order_status NOT IN ('canceled','unavailable')
GROUP BY
  sales_month,
  EXTRACT(MONTH
  FROM
    b.order_purchase_timestamp)
ORDER BY
  EXTRACT(MONTH
  FROM
    b.order_purchase_timestamp);
```

Reapeat and New customer count

```SQL
WITH
  CustomerOrderCounts AS (
  SELECT
    customer.customer_unique_id,
    COUNT(customer.customer_unique_id) AS customer_order_count
  FROM
    `casestudy2-489003`.`brazil_ecommerce`.`customer` AS customer
  GROUP BY
    customer.customer_unique_id ),
  CustomerTypeCounts AS (
  SELECT
    SUM(CASE
        WHEN t0.customer_order_count > 1 THEN 1
        ELSE 0
    END
      ) AS repeat_customers_count,
    SUM(CASE
        WHEN t0.customer_order_count = 1 THEN 1
        ELSE 0
    END
      ) AS new_customers_count
  FROM
    `CustomerOrderCounts` AS t0 )
SELECT
  'repeat_customers' AS customer_type,
  repeat_customers_count AS customer_count
FROM
  `CustomerTypeCounts`
UNION ALL
SELECT
  'new_customers' AS customer_type,
  new_customers_count AS customer_count
FROM
  `CustomerTypeCounts`;
```
Repeat and New customer revenue
```SQL
WITH customer_revenue AS (
  SELECT
    a.customer_unique_id,
    ROUND(SUM(c.price),2) AS revenue
    FROM 
    `casestudy2-489003.brazil_ecommerce.customer` AS a
    LEFT JOIN 
    `casestudy2-489003.brazil_ecommerce.orders` AS b ON a.customer_id = b.customer_id
    LEFT JOIN
    `casestudy2-489003.brazil_ecommerce.order_items` AS c on b.order_id = c.order_id
    GROUP BY a.customer_unique_id),
    customer_order_count AS (
    SELECT
      a.customer_unique_id,
      COUNT (b.order_id) AS total_orders
    FROM `casestudy2-489003.brazil_ecommerce.customer` AS a
    LEFT JOIN `casestudy2-489003.brazil_ecommerce.orders` AS b
    ON a.customer_id = b.customer_id
    GROUP BY a.customer_unique_id
)
SELECT 
  CASE WHEN a.total_orders = 1 THEN 'New Customer' ELSE 'Repeating Customer'
  END AS customer_type,
  ROUND (SUM (b.revenue),2) AS total_revenue
  FROM customer_order_count AS a
  LEFT JOIN customer_revenue AS b
  ON a.customer_unique_id = b.customer_unique_id
  GROUP BY customer_type
```

Revenue by city
```SQL
SELECT 
  a.customer_city,
  ROUND (SUM(c.price),2) AS total_revenue,
  COUNT (b.order_id) AS order_count
FROM `casestudy2-489003.brazil_ecommerce.customer` AS a
LEFT JOIN `casestudy2-489003.brazil_ecommerce.orders` as b
ON a.customer_id = b.customer_id
LEFT JOIN `casestudy2-489003.brazil_ecommerce.order_items` AS c
ON b.order_id = c.order_id
GROUP BY a.customer_city
ORDER BY total_revenue DESC
LIMIT 10
```

