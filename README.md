# Proyek Analisis Data: [E-Commerce Public Dataset]
- **Nama:** [Tri Wahyu Setiawan]
- **Email:** [tri.setiawan11@gmail.com]
- **ID Dicoding:** [tri.setiawan11]

## Menentukan Pertanyaan Bisnis

- Bagaimana review konsumen terhadap produk yang sudah dibeli
- Bagaimana tingkat nilai pembayaran dari konsumen di kota dengan konsumen terbesar

## Import Semua Packages/Library yang Digunakan

import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sbn

## Data Wrangling

### Gathering Data

customers_df = pd.read_csv("/content/customers_dataset.csv")
customers_df.head()
customers_df.info()

geolocation_df = pd.read_csv("/content/geolocation_dataset.csv")
geolocation_df.head()
geolocation_df.info()

reviews_df = pd.read_csv ("/content/order_reviews_dataset.csv")
reviews_df.head()
reviews_df.info()

orders_df = pd.read_csv("/content/orders_dataset.csv")
orders_df.head()
orders_df.info()

prodcat_df = pd.read_csv("/content/product_category_name_translation.csv")
prodcat_df.head()
prodcat_df.info()

products_df = pd.read_csv("/content/products_dataset.csv")
products_df.head()
products_df.info()

payments_df = pd.read_csv("/content/order_payments_dataset.csv")
payments_df.head()
payments_df.info()

items_df = pd.read_csv("/content/order_items_dataset.csv")
items_df.head()
payments_df.info()

### Assessing Data

customers_df.isna().sum()

geolocation_df.isna().sum()

reviews_df.isna().sum()

orders_df.isna().sum()

prodcat_df.isna().sum()

products_df.isna().sum()

payments_df.isna().sum()

items_df.isna().sum()

###Cleaning Data

**Cleaning data orders_df**

orders_df.isna().sum()

Cleaning data order_approved_at

orders_df[orders_df.order_approved_at.isna()]

Terlihat bahwa order_approved_at NaN ternyata order statusnya adalah canceled jadi data ini akan di drop

orders_df.dropna(axis=0, inplace= True, subset=["order_approved_at"])


orders_df.isna().sum()

Cleaning data order_delivered_carrier_date

orders_df[orders_df.order_delivered_carrier_date.isna()]

order_delivered_carrier_date kita samakan dengan order_approved_at

orders_df.order_delivered_carrier_date.fillna(value="order_approved_at", axis=0, inplace=True )

orders_df.isna().sum()

Cleaning data order_delivered_customer_date

orders_df[orders_df.order_delivered_customer_date.isna()]


tanggal order deliver customer date kita samakan dengan oirder approved at

orders_df.order_delivered_customer_date.fillna(value="order_approved_at", axis=0, inplace=True )

orders_df.isna().sum()

**Cleaning data products_df**

products_df.info()

products_df.duplicated().sum()

products_df.isna()

products_df.dropna(axis=0, inplace=True)

products_df.isna().sum()

## Exploratory Data Analysis (EDA)

**Eksplore data customers**

customers_df.sample(10)

customers_df.groupby(by="customer_city").customer_id.nunique().sort_values(ascending=False)

10 besar kota asal customer

customers_df.groupby(by="customer_city").customer_id.nunique().sort_values(ascending=False).head(10)

**Explore data geolocation**

geolocation_df.sample(5)

**Eksplore data review**

reviews_df.sample(5)

**Eksplore data orders**

orders_df.sample(5)

**Eksplore data payment**

payments_df.sample(5)

### Explore ...



## Visualization & Explanatory Analysis

### Pertanyaan 1: bagaimana hubungan review order di rangking customer city

review_order_df = pd.merge(
    left=orders_df,
    right=reviews_df,
    how="left",
    left_on="order_id",
    right_on="order_id"
)
review_order_df.head()

review_order_customer_df = pd.merge(
    left=review_order_df,
    right=customers_df,
    how="left",
    left_on="customer_id",
    right_on="customer_id"
)
review_order_customer_df.head()

review_order_customer_df.groupby(by=["customer_city"]).agg({
    "order_id":"nunique",
    "review_score":"sum",
}).sort_values(by="order_id", ascending=False).reset_index().head(10)

### Pertanyaan 2:payment value terhadap customers city

order_payment_type_df = pd.merge(
    left=review_order_customer_df,
    right=payments_df,
    how="left",
    left_on="order_id",
    right_on="order_id",
)
order_payment_type_df.head()

order_payment_type_df.groupby(by=["customer_city", "payment_type"]).agg({
    "order_id":"nunique",
    "payment_installments":"mean",
    "payment_value":"sum"
}).sort_values(by="order_id", ascending=False).reset_index().head(10)

## Conclusion

- Kota dengan konsumen terbesar mempunyai review produk tertinggi untuk produk yang telah dibeli (tingkat kepuasan yang tinggi)
- Kota dengan konsumen terbesar menyumbang nilai pembayaran tertinggi dengan nilai hampir dua kali lipat dari kota terbesar kedua (karena transaksi yang tinggi, menggunakan credit card dan cicilan yang paling rendah diantara kota yang lain)

order_payment_type_df.to_csv("order_payment_type.csv", index=False)
