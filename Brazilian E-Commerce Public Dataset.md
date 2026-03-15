# Brazilian E-Commerce Public Dataset by Olist



## About Dataset
This is a Brazilian ecommerce public dataset of orders made at Olist Store. The dataset has information of 100k orders from 2016 to 2018 made at multiple marketplaces in Brazil. Its features allows viewing an order from multiple dimensions: from order status, price, payment and freight performance to customer location, product attributes and finally reviews written by customers. We also released a geolocation dataset that relates Brazilian zip codes to lat/lng coordinates.  

This is real commercial data, it has been anonymised, and references to the companies and partners in the review text have been replaced with the names of Game of Thrones great houses.

The data is consist of 9 tables:
1. Customers Dataset - This dataset has information about the customer and its location. At our system each order is assigned to a unique customer_id. This means that the same customer will get different ids for different orders. The purpose of having a customer_unique_id on the dataset is to allow you to identify customers that made repurchases at the store. Otherwise you would find that each order had a different customer associated with.
2. Geolocation Dataset - This dataset has information Brazilian zip codes and its lat/lng coordinates.
3. Order Items Dataset - This dataset includes data about the items purchased within each order.
4. Payments Dataset - This dataset includes data about the orders payment options.
5. Order Reviews Dataset - This dataset includes data about the reviews made by the customers. After a customer purchases the product from Olist Store a seller gets notified to fulfill that order. Once the customer receives the product, or the estimated delivery date is due, the customer gets a satisfaction survey by email where he can give a note for the purchase experience and write down some comments.
6. Order Dataset - This is the core dataset. From each order you might find all other information.
7. Products Dataset - This dataset includes data about the products sold by Olist.
8. Sellers Dataset - This dataset includes data about the sellers that fulfilled orders made at Olist. Use it to find the seller location and to identify which seller fulfilled each product.
9. Category Name Translation - Translates the product_category_name to english.


## Context

This dataset was generously provided by Olist, the largest department store in Brazilian marketplaces. Olist connects small businesses from all over Brazil to channels without hassle and with a single contract. Those merchants are able to sell their products through the Olist Store and ship them directly to the customers using Olist logistics partners. See more on their website: www.olist.com

After a customer purchases the product from Olist Store a seller gets notified to fulfill that order. Once the customer receives the product, or the estimated delivery date is due, the customer gets a satisfaction survey by email where he can give a note for the purchase experience and write down some comments.


## Business Task

1. Identify top selling products
2. Analyze monthly revenue trends
3. Determine high-value customers
4. Evaluate regional sales performance


## Tools Used
1. BiqQuery (Data Cleaning, aggregation)
2. Tableau Public (Visualization)


## Data Exploration

1. Checking for null values:
   - customers table: No null values
   - geolocation table: No null values
   - order_items table: No null values
   - order_payments table: No null values
   - orders table: Found 29890 rows with null values but nulls are due to canceled orders
   - product_category_name table: No null values
   - products table: Total of 611 rows with null values. No nulls on primary key
   - reviews table: No null values
   - sellers table: No null values

2. Checking for ambiguous values on the tables. Checking is only perfomed on tables that are needed for analysis
   - customers table: No ambiguous values on primary and foreign keys
   - order_items table: No ambiguous values on primary and foreign keys
   - order_payments table: There are payment_values that are between 0 and 1. A total of 166 rows are found to have this value. There are also not_defined values for payment type and all of these records have 0 payment_value
   - orders table: No ambiguous values on primary and foreign keys
   - product_category_name table: No ambiguous values on primary and foreign keys
   - products table: No ambiguous values on primary and foreign keys


## Data Cleaning

1. Creation of products table with product_id and English product names and named it products_cleaned. Total of 13 rows still have null values on product_category_name_english. adding translations for _pc_gamer_ and _portateis_cozinha_e_preparadores_de_alimentos_ to product_category_name.
2. For orders table, records with canceled and unavailable order_status are removed as this do not contribute to the sales of the E-commerece company.
3. order_payments with payment_values between 0 and 1 are removed and also those with not_defined values for payment_type.


## Analysis

1. Identify top selling products
   To identify top selling products, order_items and products_cleaned are joined to show english translation for product categories. 

