---
author: Borislav Galabov
pubDatetime: 2026-03-13T15:50:00Z
modDatetime: 2026-03-13T16:12:47.400Z
title: MongoDB Queries
slug: mongo-queries
featured: false
image: /assets/images/mongoQueryBoy.webp
ogImage: /assets/images/mongoQueryBoy.webp
tags:
  - mongodb
  - tutorial
  - exercise
description: MongoDB Queries Excercise for the MongoDB Course
---

![MongoDB on Docker](/assets/images/mongoQueryBoy.webp)

MongoDB Queries excercise
=========================

Table of Contents
-----------------

*   [Introduction](#introduction)
*   [Queries with Operators](#queries-with-operators)
*   [University Students Example](#university-students-example)
*   [Music Catalog Example](#music-catalog-example)

Introduction
------------

In this lab we will try out different Queries against MongoDB with different operators involved. 

### Prerequisites

*   **MongoDB installation**: Ensure you have MongoDB Community Server installed. 
    
1.  **Access the MongoDB Shell**: Open a new terminal window and execute:
    
    ```bash
    mongosh
    ```
    
    This command opens the MongoDB shell, allowing you to interact with your databases.

2. **Cleaning the leftovers**: In this lab we will create databases with the following names: `university` and `music`. Hence, we have to ensure there are no leftovers from previous students that had run through the excercise. To do so: 

    Run the following command: 
    ```bash
    show dbs;
    ```
    
    If you find databases, named `university` or `music`, we have to delete them with the following sequence of commands: 
    
    To select the `university` database

    ```bash
    use university;
    ```
    To delete the selected (i.e. `univerity` database)

    ```bash
    db.dropDatabase();
    ```
    
    Same commands apply for the `music` database. 
    

Queries with Operators
-----------------------

### Operators in MongoDB

Ooperators in MongoDB allow us to build more expressive and powerful queries.
They extend the basic query functionality by enabling comparisons, logical conditions, and operations on fields.

In MongoDB, operators always start with the `$` symbol and are used inside query expressions.

For example:

```javascript
db.songs.find({ year: { $gt: 2010 } })
```

This query returns all songs where the value of year is greater than 2010.

### Common types of Operators

Some commonly used operators include:

* *Comparison operators*: `$gt`, `$lt`, `$gte`, `$lte`, `$eq`, `$ne`
* *Logical operators*: `$and`, `$or`, `$not`
* *Array operators*: `$in`, `$all`, `$size`



University Students Example
---------------------------

### 1\. Create University Database

To create the University database

```javascript
use university;
```

### 2\. Insert sample data

Insert the below sample data, into `students` collection. 

```json
[
  {
    "name": "Alice Johnson",
    "age": 21,
    "grades": {
      "math": 92,
      "science": 88
    },
    "activities": ["Debate Club", "Chess Club"]
  },
  {
    "name": "Mark Petrov",
    "age": 19,
    "grades": {
      "math": 75,
      "science": 81
    },
    "activities": ["Chess Club", "Robotics"]
  },
  {
    "name": "Sofia Dimitrova",
    "age": 22,
    "grades": {
      "math": 89,
      "science": 94
    },
    "activities": ["Debate Club", "Photography"]
  },
  {
    "name": "Liam Carter",
    "age": 20,
    "grades": {
      "math": 68,
      "science": 73
    },
    "activities": ["Music Band", "Debate Club"]
  },
  {
    "name": "Emma Novak",
    "age": 23,
    "grades": {
      "math": 95,
      "science": 91
    },
    "activities": ["Volunteering", "Robotics", "Chess Club"]
  },
  {
    "name": "Andreas Kraiss",
    "age": 28,
    "grades": {
      "math": 65,
      "science": 71
    },
    "activities": ["Debate Club", "Music Band"]
  }, 
  {
    "name": "John Willson",
    "age": 25,
    "grades": {
      "math": 65,
      "science": 71
    },
    "activities": ["Debate Club", "Music Band"]
  }
]
```

### 3\. Tasks

1. **Find students**: Find students whose math grade is greater than 90 and who participate into the "Chess Club". 

    ```javascript
    {
      "grades.math" : {"$gt" : 90}, 
      "activities" : "Chess Club"
    }
    ```

2. **Create compound index**: Create a compound index on the `grades.math` and `activities` field, in order to accelerate the above query: 

    Use the `db.students.createIndex(...)` command to create the compound index. 

    ```javascript
    {
      "grades.math" : 1, 
      "activities" : 1
    }
    ```

2.  **Update a student**: Update the math grade of the student with name `Mark Petrov` to `93`. 

    Sample filter document: 

    ```javascript
    {
      "name" : "Mark Petrov"
    }
    ```

    Sample update document: 

    ```javascript
    {
      $set: {"grades.math" : 93}
    }
    ```

3.  **Check the update**: Check the student with name `Mark Petrov` has math grade with value `93`. 

    Sample filter document for the `find(...)` method.

    ```json
    {
      "name" : "Mark Petrov"
    }
    ```

4. **Delete students**: Delete the students, who are 25 years or older.

    Sample filter document for `deleteMany(...)` query: 

    ```json
    {
      "age" : {"$gte" : 25}
    }
    ```

Music Catalog Example
---------------------------

### 1\. Create Music Catalog Database

To create the Music Catalog database:

```javascript
use music;
```

### 2\. Insert sample data

Insert the below sample data, into `songs` collection. 

```json
[
  {
    "title": "If You Had My Love",
    "artists": ["Jennifer Lopez"],
    "year": 1999,
    "genres": ["Pop", "R&B"]
  },
  {
    "title": "Problems",
    "artists": ["A R I Z O N A"],
    "year": 2019,
    "genres": ["Pop", "Indie"]
  },
  {
    "title": "Love Me Now",
    "artists": ["John Legend"],
    "year": 2016,
    "genres": ["Pop"]
  },
  {
    "title": "Learn To Fly",
    "artists": ["Foo Fighters"],
    "year": 1999,
    "genres": ["Alternative"]
  },
  {
    "title": "Lose Yourself",
    "artists": ["Eminem"],
    "year": 2002,
    "genres": ["Rap"]
  },
  {
    "title": "Daughter",
    "artists": ["Pearl Jam"],
    "year": 1993,
    "genres": ["Alternative"]
  },
  {
    "title": "Bohemian Rhapsody",
    "artists": ["Queen"],
    "year": 1975,
    "genres": ["Rock"]
  },
  {
    "title": "Blinding Lights",
    "artists": ["The Weeknd"],
    "year": 2019,
    "genres": ["Pop", "R&B"]
  },
  {
    "title": "Rolling in the Deep",
    "artists": ["Adele"],
    "year": 2010,
    "genres": ["Soul", "Pop"]
  },
  {
    "title": "Bonnie & Clyde",
    "artists": ["Beyonce", "Jay-Z"],
    "year": 2002,
    "genres": ["Pop", "R&B"]
  },
  {
    "title": "Love Me Again",
    "artists": ["John Newman"],
    "year": 2013,
    "genres": ["Pop"]
  },
  {
    "title": "Drunk in Love",
    "artists": ["Beyonce", "Jay-Z"],
    "year": 2013,
    "genres": ["Pop", "R&B"]
  }
]
```

### 3\. Tasks

1. **Find songs by genre**: Find songs of genre `Pop`.  

    You can use the following sample query: 

    ```json
    {
      "genres" : "Pop"
    }
    ```

2. **Find songs by genre and year period**: Find songs of genre `Pop`, between `2010` and `2016`, inclusive. 

    You can use the following sample query: 

    ```json
    {
      "genres" : {"$eq" : "Pop"},
      "year" : {"$gte" : 2010, "$lte" : 2016}
    }
    ```

    Please note, that in the results, there are songs of not only `Pop` genre. 

3. **Find songs by exact genres**: Find songs of genres `Pop` and `R&B`, exact. No other genres. 

    You can use the following sample query: 

    ```json
    {
      "genres" : {"$all" : ["Pop", "R&B"], "$size" : 2},
    }
    ```

4. **Implementing a text search feature**: We want to implement a text search feature, so we can search for songs based on `title` and `artists`, like in Spotify, for example. 

    To achieve this, we will have to create a Text index for the fields we want to search into, by using the `db.songs.createIndex(...)` command: 

    ```json
    {
      "title" : "text", 
      "artists" : "text"
    }
    ```

    Then we can use the following query, to search for songs: 

    ```javascript
    db.songs.find({
        "$text" : {"$search" : "love legend"}
    });
    ```


5. **Find the songs count for each year**: Find the count of the `Pop` songs in the songs collection for all of the years. 

    We'll use an aggregation pipeline, in order to aggregate the songs count per year. 

    ```javascript
    db.songs.aggregate(
      [
        {"$match" : {"genres" : "Pop"}}, 
        {"$group" : {"_id" : "$year", "songs_per_year" : {"$sum" : 1 }}}, 
        {"$sort" : {"_id" : 1}}
      ]
    );
    ```

    The Aggregation pipeline has three stages: 
    * `$match` – Filters the documents in the collection based on a given condition. In this example, it selects only the songs whose genres field contains "Pop". All other documents are excluded from further processing in the pipeline.

    * `$group` – Groups the remaining documents by a specified field and performs an aggregation. Here we define _id: "$year", which means the songs are grouped by their year. For each group (each year), the expression { $sum: 1 } counts how many songs belong to that year.

    * `$sort` – Sorts the aggregated results. In this example, the results are sorted by `_id`, which represents the year used for grouping, in ascending order. 
    
    Each stage in the aggregation pipeline takes the output of the previous stage as its input and transforms it further.








