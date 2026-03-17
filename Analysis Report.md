# Brazilian E-Commerce Public Dataset by Olist



## Introduction
This is a Brazilian ecommerce public dataset of orders made at Olist Store. The dataset has information of 100k orders from 2016 to 2018 made at multiple marketplaces in Brazil. Its features allows viewing an order from multiple dimensions: from order status, price, payment and freight performance to customer location, product attributes and finally reviews written by customers. We also released a geolocation dataset that relates Brazilian zip codes to lat/lng coordinates.  

This is real commercial data, it has been anonymised, and references to the companies and partners in the review text have been replaced with the names of Game of Thrones great houses.

## About the Dataset
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

This dataset was generously provided by Olist, the largest department store in Brazilian marketplaces. Olist connects small businesses from all over Brazil to channels without hassle and with a single contract. Those merchants are able to sell their products through the Olist Store and ship them directly to the customers using Olist logistics partners. See more on their website: www.olist.com

After a customer purchases the product from Olist Store a seller gets notified to fulfill that order. Once the customer receives the product, or the estimated delivery date is due, the customer gets a satisfaction survey by email where he can give a note for the purchase experience and write down some comments.  

Dataset is downloaded from Kaggle from this link: https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce/data?select=olist_sellers_dataset.csv


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

1. Identify top selling products.  
   To identify top selling products, order_items and products_cleaned are joined to show english translation for product categories.  

 <p align="center">
    <img width="1230" height="565" alt="image" src="https://github.com/user-attachments/assets/45de4758-7781-4744-835c-40b5fcd42904" />
</p>

   The most profitable products are from health and beauty products which amounts to ~ R$1,26M. In this case, even if the most profitable purchases are from health and beauty products, the mostly sold products are from bed,bath, and table product category. It is also notable that the 2nd most profitable are the watches and gifts products as even if it only sold 5,991 in quantity, the total revenue for these products is R$1,21M. 

   This means customers using Olist prefers ordering bed and bath needs and willing to purchase watches and gifts even if they are quite expensive. 



2. Analyze monthly revenue trends.  
   Monthly Revenue trend is acquired by considering when the customer placed the order. So those orders that are canceled and unaivalable are NOT considered.
<p align="center">
<img width="1232" height="527" alt="image" src="https://github.com/user-attachments/assets/e9aea312-acaa-47a9-83d0-4dbe11b2c8f0" />
</p>
   
   The month with the highest sales is during May and followed by August. During May, Mother's Day is celebrated in Brazil and this could be a driving factor since the mostly sold products all year round are bed, bath and table products seconded by health and beauty products which are commonly gifted to mothers.


   
Upon further research Brazil celebrates Dia dos Namorados on June 12, their Valentines Day. Retailers often start promotional campaigns weeks in advance, sometimes beginning in late May, and could extend momentum from the Mother's Day campaigns.  

The sudden dip in September could be explained as the "recuperating" period of customers since September is in between Father's Day which is celebrated during August and Black Friday Sale which is on November. Also there is a lack of major retail holidays during September so customers chose this period to recover from their expenses during Mother's Day in May and Father's Day in August and prepare for their purchases during Black Friday.





3. Determine high-value customers.  
    Since the customer table does not contain any personal information and only customer_ids, customer_unique_ids are counted and those that have customer_unique_id appear only once are considered as New Customers while those with customer_unique_id appear more than once is considered as repeat customers.

<p align="center">
<img width="1230" height="559" alt="image" src="https://github.com/user-attachments/assets/8850b469-7261-4920-809b-cca32ae020aa" />
</p>

   New customers greatly outnumber repeat customers with Repeat Customer Conversion rate only at 3.12%. Most of the revenue also came from new customers. 


4. Evaluate regional revenue performance.  
   By joining the customers, orders, order_items table and aggregation, we get the total revenue and order count by each city to see which city generated the most revenue and most products sold.

<p align="center">
<img width="1226" height="521" alt="image" src="https://github.com/user-attachments/assets/97e9db4a-0ccb-4eba-bfa1-e22499786c14" />
</p>


   The city which generated the most revenue is Sao Paulo where 14% of the total revenue is generated . Since Sao Paulo is the largest city in Brazil, it also has the highest order count by customers. As seen on the visualization, the revenue generated is directly proportional to the order count on every cities.

   Campinas shows a little potential as it is only a municipality in Brazil and it still got a higher order count than Salvador even if Salvador has greater population. Also though Campinas generated lower revenue than Porto Alegre, Campinas has a higher order count.
   

All visualizations can be acceess on Tableau Public from this link: https://public.tableau.com/app/profile/gio.tamayo6569/viz/BrazilianE-commerce_17734758625520/Dashboard1
<p align="center">
<img width="1544" height="867" alt="image" src="https://github.com/user-attachments/assets/089e7978-a2f2-4914-948d-7308a90f09d7" />
</p>

## Key Insights

1. Customers usually purchase gifting items such as health, beauty, bed and bath products, furnitures and housewares.
2. Customers purchase more during major retail holidays like Mother's Day, Father's Day and Black Friday
3. Most of the revenue is generated by new customers.
4. Revenue is directly proportional to a city's population but some municipalities show potential having higher order count even with less generated revenue.



## Recommeendations

1. Creating product-focused marketing campaigns before and during retail holidays.
2. Increase marketing campaigns targeted to specific municipalities like a sale during their festivals or any other municipal holiday/event. 
3. Implementing a Tier-based membership where a customer's membership will upgrade upon completing a specific number of orders and have vouchers that are tier-locked to increase Repeat Customer Conversion Rate.
4. Introduce payday sale where you can get discounts when you purchase on usual pay days like first of the month and 15th of every month to normalize revenue every month. 
