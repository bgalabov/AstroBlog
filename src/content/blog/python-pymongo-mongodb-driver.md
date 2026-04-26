---
author: Borislav Galabov
pubDatetime: 2026-04-25T15:50:00Z
modDatetime: 2026-04-25T09:12:47.400Z
title: Build a simple Python Console App that connects to MongoDB
slug: python-console-app
featured: false
image: /assets/images/mongodb_python_boy.webp
ogImage: /assets/images/mongodb_python_boy.webp
tags:
  - python
  - mongodb
  - tutorial
  - exercise
description: Learn how to create a simple Python-based Console App that uses MongoDB driver
---
# Build a simple Python Console App that uses MongoDB driver

![Python and MongoDB based App](/assets/images/mongodb_python_boy.webp)

## Introduction
In this article we'll build a very simple Menu-like Console Application in Python, that connects and operates MongoDB via PyMongo driver. 

Table of Contents
-----------------

*   [Prerequisites](#prerequisites)
*   [Environment setup](#environment-setup)
*   [Connecting to MongoDB](#connecting-to-mongodb)
*   [Database and Collection access](#database-and-collection-access)
*   [CRUD Operations](#crud-operations)
*   [Aggregation Pipeline](#aggregation-pipeline)
*   [The Simple Application](#the-simple-application)



## Prerequisites

Before starting, make sure you have: 

* **Python** runtime installed
* **MongoDB** server instance running

For this tutorial we assume that MongoDB is available locally on: 

```javascript
mongodb://127.0.0.1:27017
```

## Environment setup

Open **Command Prompt** and run: 

```shell
python -m venv myonlinestore
```

This will create a Python virtual environment directory for your sample console app project, in our case this is `myonlinestore`. 

At this point your `myonlinestore` project directory will have the following structure: 

```text
myonlinestore/
  include/
  Lib/
  Scripts/
  .gitignore
  pyenv.cfg
```

Before we use the Virtual Environment, we need to **activate** it by running:

```text
myonlinestore\Scripts\activate
```

As a result, in the **Command Prompt**, you should see the following: 

```text
(myonlinestore) C:\Users\<your_user_name>>
```

Next step is to install the `PyMongo` driver, in order to be able to connect to MongoDB: 

```shell
pip install pymongo
```

Now, let's verify the installation of `PyMongo`. To do so: 

1. Create a `test.py` file in the `myonlinestore` project directory with the following contents: 

```python
import pymongo
print(pymongo.__version__)
```

2. Run the `test.py` file with the Python runtime: 

```shell
python myonlinestore\test.py
```

It should print the version of the `pymongo` driver, here is the output from me: 

```shell
(myonlinestore) C:\Users\borislav>python myonlinestore\test.py
4.17.0
```


## Connecting to MongoDB

PyMongo uses `MongoClient` as the primary entry point. The default local connection string is `mongodb://localhost:27017/`. 

It is a good practice to store your connection connection data in as Environment Variables or an `.env` file. Create `.env` file in your project directory with the following contents: 

```text
MONGO_URI = "mongodb://localhost:27017/"
```
If you are using MongoDB Atlas, you can use your Atlas connection string instead.

In order to be able to read the `.env` file contents from your Python app, you need to install the `python-dotenv` library. 

```shell
pip install python-dotenv
```

To connect to MongoDB from your Python script, by using the `MONGO_URI`, defined in your `.env` file: 

```python
import os
from dotenv import load_dotenv

load_dotenv()

#in case MONGO_URI is not present, we provide a default value: "mongodb://localhost:27017/"
MONGO_URI = os.getenv("MONGO_URI", "mongodb://localhost:27017/")
client = MongoClient(MONGO_URI)
```

## Database and Collection access

MongoDB lazily creates databases and collections — they are not created until a document is inserted.

```python
# Access (or create) a database
db = client["onlinestore_db"]

# Access (or create) a collection
products = db["products"]

# Introspect existing databases and collections
print(client.list_database_names())
print(db.list_collection_names())

```
## CRUD Operations

### 1. Create (Insert)

```python
#Insert single document
result = products.insert_one({
   "name" : "Apple iPhone 17 Pro Max", 
   "qty" : 10, 
   "category" : "Mobile Phones",
   "price" : 1400
})

print(f"Inserted ID: {result.inserted_id}")

#Insert multiple documents
new_products = [{
   "name" : "Samsung Galaxy S25", 
   "qty" : 8, 
   "category" : "Mobile Phones",
   "price" : 700
},
{
   "name" : "Apple iPhone 17 Air", 
   "qty" : 5, 
   "category" : "Mobile Phones",
   "price" : 1000
}, 
{
   "name" : "Apple MacBook Pro M5 Pro 14", 
   "qty" : 5, 
   "category" : "Laptops",
   "price" : 2500
}, 
{
   "name" : "Lenovo Thinkpad T16 Gen 2", 
   "qty" : 12, 
   "category" : "Laptops",
   "price" : 1800
}
]

result = products.insert_many(new_products)

print(f"Inserted IDs: {result.inserted_ids}")

```

### 2. Read (Find)

```python
# Find one document, whose `name` contains `Lenovo`
doc = products.find_one({"name": {"$regex" : "Lenovo", "$options" : "i"}})
print(doc)

```

```python
# Find all documents matching a filter and print their name and price only:
mobile_phones = products.find({"category": "Mobile Phones"})
for mp in mobile_phones: 
   print(f"Name: {mp["name"]} , Price: {mp["price"]}")

```

### 3. Update

```python
# Update one document
products.update_one(
    {"name": "Apple iPhone 17 Air"},
    {"$set" : {"price" : 900} }
)

#Update all documents, whose name contains Apple (case insensitive)
products.updateMany(
    {"name": {"$regex": "Apple", "$options" : "i"}}
    {"$set" : {"tags" : ["apple"]} }
)
```


### 4. Delete

```python

# Delete one (by Name)
products.delete_one({"name": "Apple MacBook Pro M5 Pro 14"})

# Delete one (by ObjectID)
id = "69eda8ccc864579625134a73"
oid = ObjectId(id)
products.delete_one({"_id": oid})

# Delete many
products.delete_many({"tags": "apple"})
```

## Aggregation Pipeline

```python
agg_pipeline = [
   #Stage 1: Filter
   {"$match" : {
                  "price" : {"$gt" : 500} 
               }   
   }, 

   #Stage 2: Group all products by "category" and count the quantity for all items per "category"
   {"$group" : {
      "_id" : "$category", 
      "total_items_qty_per_category" : {"$sum" : "$qty"}
   }}, 

   #Stage 3: Sort by category name, i.e. after $group stage, this is the "_id"
   {"$sort" : {"_id" : 1} }
]

products_count_per_cat = products.aggregate(pipeline)
for p in products_count_per_cat:
   print(p)
```

## The Simple Application

Below is the sample **Menu**-style Python Application for the `myonlinestore` project. The Python app contains CRUD operations and one Aggregation over the `products` collection of the `onlinestore_db`. 

In the `main` method, we create an instance of the `MyOnlineStore` class. From within the constructor (i.e. `__init__()`), we create a `MongoClient`, and then use it to get a reference to the `onlinestore_db` and the `products` collection and store them into member-variables. Thus, we create a single `MongoClient` per client (a.k.a Console App Instance) and not per separate DB operation which is an expensive operation. We don't need to worry about connection pooling - the `PyMongo` driver has internal connection pooling mechanism. 

You can run the app, from the **Command Prompt** by executing the following command: 

```shell
python my_online_store.py
```
However, keep in mind that the Virtual Environment for the project must be activated for your **Command Prompt** instance, otherwise, you will get an error. 

To activate the Virtual Environment, you must run the `activate` script from your **Command Prompt** instance: 

```shell
myonlinestore\Scripts\activate
```

Also, you can use `VSCode` + [the Official Python Extension from Microsoft](https://marketplace.visualstudio.com/items?itemName=ms-python.python) for easier development. 

```python
# my_online_store.py

import os
from dotenv import load_dotenv
from pymongo import MongoClient
from pymongo.errors import DuplicateKeyError, PyMongoError
from bson import ObjectId

load_dotenv()

#in case MONGO_URI is not present, we provide a default value: "mongodb://localhost:27017/"
MONGO_URI = os.getenv("MONGO_URI", "mongodb://localhost:27017/")
DB_NAME   = "onlinestore_db"

def get_client():
    client = MongoClient(MONGO_URI, serverSelectionTimeoutMS=8000)
    return client

class MyOnlineStore:
    
    def __init__(self):
        self.client = get_client()
        self.database = self.client[DB_NAME]
        self.col = self.database["products"]
        # Ensure unique index on product's name
        self.col.create_index("name", unique=True)
    
    def close_connection(self): 
        self.client.close()
      
    def add_product(self, name: str, category: str, price: int, qty: int) -> str:
        doc = {"name": name, "category": category, "price": price, "qty": qty}
        try:
            result = self.col.insert_one(doc)
            return str(result.inserted_id)
        except DuplicateKeyError:
            raise ValueError(f"Product with Name: '{name}' already exists.")
        
    def find_product_by_id(self, id: str) -> dict:
        try: 
            oid = ObjectId(id)
            query = {"_id": oid}
            return self.col.find_one(query)
        except:
            #in case the provided "id" is not valid ObjectId string
            print("Invalid ID format")
            return None
    
    def print_product_details(self, product: dict):
        if not product:
            print("No data for product")
            return
        
        print(f"Product ID: {product["_id"]}")
        print(f"Product Name: {product["name"]}")
        print(f"Product Category: {product["category"]}")
        print(f"Product Price: {product["price"]}")
        print(f"Product Quantity: {product["qty"]}")

    def update_product(self, id: str, new_name: str, new_category: str, new_price: int, new_quantity: int):
        try: 
            oid = ObjectId(id)
            query = {"_id": oid}
            prod_to_update = self.col.find_one(query)
        except:
            #in case the provided "id" is not valid ObjectId string
            print("Invalid ID format.")
            return None
        
        if not prod_to_update:
            print("Product not found.")
            return
        
        update_data = {}

        #update only when new_name is different than current name, and new name is not null or empty
        if new_name != prod_to_update.get("name"): update_data["name"] = new_name
        if new_category != prod_to_update.get("category"): update_data["category"] = new_category
        if new_price != prod_to_update.get("price"): update_data["price"] = new_price
        if new_quantity != prod_to_update.get("qty"): update_data["qty"] = new_quantity
            
        if update_data:
            result = self.col.update_one({"_id": oid}, {"$set": update_data})
            print(f"Updated {result.modified_count} product. New values for {list(update_data.keys())}")
        else:
            print("Product was not updated.")
        
    def delete_product_by_id(self, id: str):
        try: 
            oid = ObjectId(id)
        except:
            #in case the provided "id" is not valid ObjectId string
            print("Invalid ID format.")
            return None
        
        query = {"_id": oid}
        delete_data = self.col.delete_one(query)
        if delete_data.deleted_count > 0: 
            print(f"{delete_data.deleted_count} product(s) were deleted")
        else:
            print(f"Unable to delete product with ID {id}")
    
        
    def find_products_by_name(self, name: str) -> list[dict]:
        query = {"name": {"$regex": name, "$options" : "i"}}
        return self.col.find(query)
                
    def get_all_products(self) -> list[dict]:
        return self.col.find()
    
    def aggregate_product_items_per_cat(self) -> list[dict]:
        agg_pipeline = [
            #Stage 1: Filter
            {
                "$match" : {
                    "price" : {"$gt" : 500} 
                }
            }, 
            
            #Stage 2: Group all products by "category" and count the quantity for all items per "category"
            {
                "$group" : {
                    "_id" : "$category", 
                    "total_items_qty_per_category" : {"$sum" : "$qty"}
                    }
            }, 
            
            #Stage 3: Sort by category name, i.e. after $group stage, this is the "_id"
            {
                "$sort" : {"_id" : 1} 
            }
        ]
        return self.col.aggregate(agg_pipeline)

# ── Main ──────────────────────────────────────────────────────────────────────
if __name__ == "__main__":
    store = MyOnlineStore()
    while True:
        print("\n1. Add product " \
        "\n2. Find Product by Name " \
        "\n3. Find all Products " \
        "\n4. Update product "
        "\n5. Delete product by ID"
        "\n6. Get all product items count per category, with price greater than 500. " \
        "\n7. Exit")
        
        choice = input("Enter your choice: ")
        
        if choice == '1':
            name = input("Product Name: ")
            category = input("Category: ")
            price = int(input("Price: "))
            quantity = int(input("Quantity: "))
            new_prod_id = store.add_product(name, category, price, quantity)
            print(f"Successfully added product with ID: {new_prod_id}")
        elif choice == '2':
            name = input("Type a Product Name or part of it: ")
            products = store.find_products_by_name(name)
            for p in products:
                print(p)
        elif choice == '3':
            products = store.get_all_products()
            for p in products:
                print(p)
        elif choice == '4':
            id = input("Type a Product ID to update: ")
            product_to_update = store.find_product_by_id(id)
            if not product_to_update:
                continue
            store.print_product_details(product_to_update)
            name = input("New Name: (press Enter to keep old): ") or product_to_update['name']
            category  = input("New Category: (press Enter to keep old): ") or product_to_update['category']
            try:
                price_in = input("New Price: (press Enter to keep old): ")
                price = int(price_in) if price_in else product_to_update['price']
                qty_in = input("New Quantity: (press Enter to keep old): ")
                qty = int(qty_in) if qty_in else product_to_update['qty']
                
                store.update_product(id, name, category, price, qty)
            except ValueError:
                print("Price and Quantity must be Integer numbers.")
        elif choice == '5':
            id = input("Type a Product ID to delete: ")
            store.delete_product_by_id(id)
        elif choice == '6':
            agg_result = store.aggregate_product_items_per_cat()
            for r in agg_result:
                print(r)
        elif choice == '7':
            store.close_connection()
            print("Bye!")
            break
        else:
            print("Invalid choice. Please try again.")
```