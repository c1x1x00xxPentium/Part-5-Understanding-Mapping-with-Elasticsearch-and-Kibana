# Beginner's Crash Course to Elastic Stack Series
## Part 5: Understanding Mapping with Elasticsearch and Kibana
Welcome to the Beginner's Crash Course to Elastic Stack!

This repo contains all resources shared during Part 5: Understanding Mapping with Elasticsearch and Kibana.

Have you ever encountered an error “Field type is not supported for [whatever you are trying to do with Elasticsearch]”?

The most likely culprit of this error is the mapping of your index!

Mapping is the process of defining how a document and its fields are indexed and stored. It defines the data type and format of the fields in the documents. As a result, mapping can significantly affect how Elasticsearch searches and stores data.

Understanding how mapping works will help you define mapping that best serves your use case and help you fix these pesky errors as they come up.

By the end of this workshop, you will be able to:
- what a mapping is
- identify areas to optimize mapping
- Define own mapping

define your own mappings to optimize the performance of your cluster
Defining your own mappings will make indexing and searching more efficient.

## Resources

[Table of Contents: Beginner's Crash Course to Elastic Stack](https://github.com/LisaHJung/Beginners-Crash-Course-to-the-Elastic-Stack-Series): 

This workshop is a part of the Beginner's Crash Course to Elastic Stack series. Check out this table contents to access all the workshops in the series thus far. This table will continue to get updated as more workshops in the series are released! 

[Free Elastic Cloud Trial](https://ela.st/elastic-beginners)

[Instructions](https://dev.to/lisahjung/beginner-s-guide-to-setting-up-elasticsearch-and-kibana-with-elastic-cloud-1joh) on how to access Elasticsearch and Kibana on Elastic Cloud

[Instructions](https://dev.to/elastic/downloading-elasticsearch-and-kibana-macos-linux-and-windows-1mmo) for downloading Elasticsearch and Kibana

[Presentation slides]()

[Elastic America Virtual Chapter](https://community.elastic.co/amer-virtual/): 

Want to attend live workshops? Join the Elastic Americal Virtual Chapter to get the deets!

## Review from previous workshops
![image](https://user-images.githubusercontent.com/60980933/118298775-23e4e280-b49d-11eb-955f-68b39102e1d4.png)

### Indexing a document
The following request will index the following document.  

Syntax: 
```
PUT Enter-name-of-the-index/_doc/id-you-want-to-assign-to-this-document
{
  "field": "value"
}
```
Example: 
```
PUT temp_index/_doc/1
{
  "name": "Pineapple",
  "quantity": 200,
  "unit_price": 1.3,
  "description": "a large juicy tropical fruit consisting of aromatic edible yellow flesh surrounded by a tough segmented skin and topped with a tuft of stiff leaves.",
  "vendor_details": {
    "vendor": "Diversificados De Costa Rica Dicori S.A.",
    "main_contact": "Mavis Luis Fallas",
    "phone_number": "011-506-2479-2000",
    "country": "Costa Rica",
    "date_received": "2020-06-02T12:15:35",
    "preferred vendor": true
  }
}
```
Expected response from Elasticsearch:

Elasticsearch will confirm that document 1 has been successfully indexed in the temp_index. 
![image](https://user-images.githubusercontent.com/60980933/118299194-abcaec80-b49d-11eb-943e-c122956976e7.png)

## View the mapping 
Syntax:
```
GET Enter_name_of_the_index_here/_mapping
```

Example:
```
GET temp_index/_mapping
```
Expected response from Elasticsearch:

Elasticsearch will return the mapping of the temp_index. It lists all the fields of the document we just indexed in alphabetical order and list the data type of each field. 

![image](https://user-images.githubusercontent.com/60980933/118303597-da979180-b4a2-11eb-83ae-bb7d10514fe2.png)
![image](https://user-images.githubusercontent.com/60980933/118303631-e7b48080-b4a2-11eb-98d7-ec3d724ea9dc.png)
![image](https://user-images.githubusercontent.com/60980933/118303655-eedb8e80-b4a2-11eb-8998-68b32bdbe4a6.png)

### Metric Aggregations 
`Metric`aggregations are used to compute numeric values based on your dataset. It can be used to calculate values of `sum`,`min`, `max`, `avg`, unique count(`cardinality`) and etc.  

#### Compute the `sum` of all unit prices in the index

Syntax:
```
GET Enter_name_of_the_index_here/_search
{
  "aggs": {
    "Name your aggregations here": {
      "sum": {
        "field": "Name the field you want to aggregate on here"
      }
    }
  }
}
```
Example:
```
GET ecommerce_data/_search
{
  "aggs": {
    "sum_unit_price": {
      "sum": {
        "field": "UnitPrice"
      }
    }
  }
}
```
Expected response from Elasticsearch:

By default, Elasticsearch will return top 10 hits(Lines 16+). 

![image](https://user-images.githubusercontent.com/60980933/114756805-5b366700-9d18-11eb-8ea4-1164821e715f.png)

When you minimize hits(red box- line 10), you will see the aggregations results we named sum_unit_price(image below). It displays the sum of all unit prices present in our index. 

![image](https://user-images.githubusercontent.com/60980933/114757167-c122ee80-9d18-11eb-9d7a-3856612c7de0.png)

If the purpose of running an aggregation is solely to get the aggregations results, you can add a size parameter and set it to 0 as shown below. This parameter will prevent Elasticsearch from fetching the top 10 hits and speed up the query. 

**Using a size parameter**

Example:
```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "sum_unit_price": {
      "sum": {
        "field": "UnitPrice"
      }
    }
  }
}
```
Expected response from Elasticsearch:

Now you do not need to minimize hits(line 10) to access the aggregations results! We will be setting the size parameter to 0 in all aggregations requests from this point on. 

![image](https://user-images.githubusercontent.com/60980933/114758361-1c091580-9d1a-11eb-94df-58afa67e20c4.png)

#### Compute the lowest(`min`) unit price of an item 

Syntax:
```
GET Enter_name_of_the_index_here/_search
{
  "size": 0,
  "aggs": {
    "Name your aggregations here": {
      "min": {
        "field": "Name the field you want to aggregate on here"
      }
    }
  }
}
```
Example: 
```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "lowest_unit_price": {
      "min": {
        "field": "UnitPrice"
      }
    }
  }
}
```
Expected response from Elasticsearch:

The lowest unit price of an item in our index is 1.01. 
![image](https://user-images.githubusercontent.com/60980933/112509885-869be680-8d56-11eb-9c2e-5935ff7437e8.png)

#### Compute the highest(`max`) unit price of an item 

Syntax:
```
GET Enter_name_of_the_index_here/_search
{
  "size": 0,
  "aggs": {
    "Name your aggregations here": {
      "max": {
        "field": "Name the field you want to aggregate on here"
      }
    }
  }
}
```
Example: 

```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "highest_unit_price": {
      "max": {
        "field": "UnitPrice"
      }
    }
  }
}
```
Expected response from Elasticsearch:

The highest unit price of an item in our index is 498.79. 

![image](https://user-images.githubusercontent.com/60980933/112511189-cca57a00-8d57-11eb-9ab3-809b2a410636.png)

#### Compute the `average` unit price of items 

Syntax:
```
GET Enter_name_of_the_index_here/_search
{
  "size": 0,
  "aggs": {
    "Name your aggregations here": {
      "avg": {
        "field": "Name the field you want to aggregate on here"
      }
    }
  }
}
```
Example: 
```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "average_unit_price": {
      "avg": {
        "field": "UnitPrice"
      }
    }
  }
}
```
Expected response from Elasticsearch: 

The average unit price of an item is ~4.39.

![image](https://user-images.githubusercontent.com/60980933/112511759-58b7a180-8d58-11eb-811f-8d6cb852c220.png)

#### `Stats` Aggregation: Compute the count, min, max, avg, sum in one go

Syntax:
```
GET Enter_name_of_the_index_here/_search
{
  "size": 0,
  "aggs": {
    "Name your aggregations here": {
      "stats": {
        "field": "Name the field you want to aggregate on here"
      }
    }
  }
}
```
Example:
```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "all_stats_unit_price": {
      "stats": {
        "field": "UnitPrice"
      }
    }
  }
}
```
Expected Response from Elasticsearch:

The `stats aggregation` will yield the values of `count`(the number of unit prices aggregation was performed on), `min`, `max`, `avg`, and `sum`(sum of all unit prices in the index). 

![image](https://user-images.githubusercontent.com/60980933/114769078-f20a2000-9d26-11eb-9827-e7672cbba158.png)

#### `Cardinality` Aggregation

The `cardinality aggregation` computes the count of unique values for a given field. 

Syntax:
```
GET Enter_name_of_the_index_here
{
  "size": 0,
  "aggs": {
    "Name your aggregations here": {
      "cardinality": {
        "field": "Name the field you want to aggregate on here"
      }
    }
  }
}
```
Example: 
```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "number_unique_customers": {
      "cardinality": {
        "field": "CustomerID"
      }
    }
  }
}
```
Expected response from Elasticsearch: 

Approximately, there are 4325 unique number of customers in our index.  
![image](https://user-images.githubusercontent.com/60980933/114774709-aeff7b00-9d2d-11eb-9da5-8faf0dc87292.png)

#### Limiting the scope of an aggregation

In the previous examples, aggregations were performed on all documents in the ecommerce_data index. What if you want to run an aggregation on a subset of the documents? 

For example, our index contains e-commerce data from multiple countries. Let's say you want to calculate the average unit price of items sold in Germany.

To limit the scope of the aggregation, you can add a query clause to the aggregations request. The query clause defines the subset of documents that aggregations should be performed on.  

The combined query and aggregations look like the following: 

Syntax:
```
GET Enter_name_of_the_index_here/_search
{
  "size": 0,
  "query": {
    "Enter match or match_phrase here": {
      "Enter the name of the field": "Enter the value you are looking for"
    }
  },
  "aggregations": {
    "Name your aggregations here": {
      "Specify aggregations type here": {
        "field": "Name the field you want to aggregate here"
      }
    }
  }
}
```
Example: 
```
GET ecommerce_data/_search
{
  "size": 0,
  "query": {
    "match": {
      "Country": "Germany"
    }
  },
  "aggs": {
    "germany_average_unit_price": {
      "avg": {
        "field": "UnitPrice"
      }
    }
  }
}
```
Expected response from Elasticsearch:

The average of unit price of items sold in Germany is ~4.58.
![image](https://user-images.githubusercontent.com/60980933/112534501-c1ab1380-8d70-11eb-9ce7-507953cc26d0.png)

The combination of query and aggregations request allowed us to perform aggregations on a subset of documents. What if we wanted to perform aggregations on several subsets of documents? 

This is where `Bucket Aggregations` come into play! 

###  Bucket Aggregations
When you want to aggregate on several subsets of documents, `bucket aggregations` will come in handy. `Bucket aggregations` group documents into several sets of documents called buckets. All documents in a bucket share a common criteria.  

![image](https://user-images.githubusercontent.com/60980933/115422110-d2a54400-a1b9-11eb-919c-15a88f9d148b.png)

The following are different types of `bucket aggregations`. 

1. Date Histogram Aggregation
2. Histogram Aggregation
3. Range Aggregation
4. Terms aggregation

#### 1. `Date Histogram` Aggregation
When you are looking to group data by time interval, the `date histogram` aggregation will become very useful! 

There are two ways to define time interval with `date histogram` aggregations.
1. `Fixed_interval`
2. `Calendar_interval`

`Fixed_interval`:The inverval is always constant.

Example: Create a bucket for every 8 hour interval. 

Syntax:
```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "Name your aggregations here": {
      "date_histogram": {
        "field":"Name the field you want to aggregate on here",
        "fixed_interval": "Specify the interval here"
      }
    }
  }
}
```

Example:
```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "transactions_by_8_hrs": {
      "date_histogram": {
        "field": "InvoiceDate",
        "fixed_interval": "8h"
      }
    }
  }
}
```
Expected response from Elasticsearch:

Elasticsearch creates a bucket for every 8 hours("key_as_string") and shows the number of documents("doc_count") grouped into each bucket. 

![image](https://user-images.githubusercontent.com/60980933/115752687-927bc800-a357-11eb-8ded-77038d9d11fa.png)

`Calendar_interval`: The interval may vary.

Daylight savings changes the length of specific days, months have different number of days, and leap seconds can be tacked onto a particular year. 

Ex. Split data into monthly buckets.

Syntax:
```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "Name your aggregations here": {
      "date_histogram": {
        "field":"Name the field you want to aggregate on here",
        "calendar_interval": "Specify the interval here"
      }
    }
  }
}
```
Example: 
```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "transactions_by_month": {
      "date_histogram": {
        "field": "InvoiceDate",
        "calendar_interval": "1M"
      }
    }
  }
}
```
Expected response from Elasticsearch:

Elasticsearch creates monthly buckets. Within each bucket, the date(monthly interval) is included in the field "key_as_string". The field "key" shows the same date represented as a timestamp. The number of documents that fall within the time interval is included in the field "doc_count".  

![image](https://user-images.githubusercontent.com/60980933/115707200-b9bca000-a32b-11eb-8d33-f089e5616b90.png)

**Bucket sorting for date histogram aggregation**

By default, `date histogram` aggregation sorts buckets based on the `_key`
values in ascending order. To reverse this order, you can add an order parameter to the aggregation. Then specify that you want to sort buckets based on the `_key` values in descending(desc) order! 

Example:
```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "transactions_by_month": {
      "date_histogram": {
        "field": "InvoiceDate",
        "calendar_interval": "1M",
        "order": {
          "_key": "desc"
        }
      }
    }
  }
}
```
Expected response from Elasticsearch:

You will see that buckets are now sorted to return the most recent interval first intead of last. 

![image](https://user-images.githubusercontent.com/60980933/115941140-72383000-a461-11eb-90c2-a86cc1ae8233.png)

#### Histogram Aggregation
The `histogram aggregation` creates buckets based on any numerical interval. 

Ex. Create a buckets based on price interval that increases in increments of 10. 

Syntax: 
```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "Name your aggregations here": {
      "histogram": {
        "field":"Name the field you want to aggregate on here",
        "interval": Specify the interval here
      }
    }
  }
}
```
Example: 
```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "transactions_per_price_interval": {
      "histogram": {
        "field": "UnitPrice",
        "interval": 10
      }
    }
  }
}
```

Expected response from Elasticsearch:

Elasticsearch returns a buckets array where each bucket represents a price interval("key"). Each interval increases in increments of 10 in unit price. It also includes the number of documents placed in each bucket. 

![image](https://user-images.githubusercontent.com/60980933/115754790-cf48be80-a359-11eb-82c8-128033e452cf.png)

**Bucket sorting for histogram aggregation**

By default, the `histogram` aggregation sorts buckets based on the `_key`
values in ascending order. To reverse this order, you can add an order parameter to the aggregation. Then specify that you want to sort buckets based on the `_key` values in descending(desc) order! 

Example:
```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "transactions_per_price_interval": {
      "histogram": {
        "field": "UnitPrice",
        "interval": 10,
        "order": {
          "_key": "desc"
        }
      }
    }
  }
}
```
Expected response from Elasticsearch:

You will see that buckets are now sorted to return the highest price interval first intead of last. 

![image](https://user-images.githubusercontent.com/60980933/116111564-01b52d00-a674-11eb-99a1-3891718d414c.png)g)

#### Range Aggregation

`Range aggregation` is similar to `histogram aggregation` in that it can create buckets based on any numerical interval. The difference is that `range aggregation` allows you to define intervals of varying sizes so you can customize it to your use case.  

For example, what if you wanted to know the number of transactions for items from varying price ranges between 0 and $50, between $50-$200, and between $200 and up? 

Syntax:
```
GET Enter_name_of_the_index_here/_search
{
  "size": 0,
  "aggs": {
   "Name your aggregations here": {
      "range": {
        "field": "Name the field you want to aggregate on here",
        "ranges": [
          {
            "to": x
          },
          {
            "from": x,
            "to": y
          },
          {
            "from": z
          }
        ]
      }
    }
  }
}
````
Example: 
```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "transactions_per_custom_price_ranges": {
      "range": {
        "field": "UnitPrice",
        "ranges": [
          {
            "to": 50
          },
          {
            "from": 50,
            "to": 200
          },
          {
            "from": 200
          }
        ]
      }
    }
  }
}
```
Expected response from Elasticsearch:

Elasticsearch returns a buckets array where each bucket represents a customized price interval("key"). It also includes the number of documents placed in each bucket. 

![image](https://user-images.githubusercontent.com/60980933/114792261-44f2d000-9d45-11eb-9298-6bae6dcf8f06.png)

**Bucket sorting for range aggregation**

The `range aggregation` is sorted based on the input ranges you specify and it cannot be sorted any other way! 

#### Terms Aggregation
The `terms aggregation` creates a new bucket for every unique term it encouters for the specified field. It is often used to find the most frequently found terms in a document. 

For example, let's say you want to identify 5 customers with highest number of transactions(documents). 

Syntax:
```
GET Enter_name_of_the_index_here/_search
{
  "aggs": {
    "Name your aggregations here": {
      "terms": {
        "field": "Name the field you want to aggregate on here",
        "size": State how many top results you want returned here
      }
    }
  }
}
```
Example:
```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "top_5_customers": {
      "terms": {
        "field": "CustomerID",
        "size": 5
      }
    }
  }
}
```
Expected response from Elasticsearch:

Elasticsearch will return top 5 customer IDs with the highest number of transactions(documents). 
![image](https://user-images.githubusercontent.com/60980933/114796514-6906df00-9d4e-11eb-862e-ac8eed4a10e2.png)

**Bucket sorting for terms aggregation**

By default, the terms aggregation sorts buckets based on the `_count`
values in descending order. To reverse this order, you can add an order parameter to the aggregation. Then specify that you want to sort buckets based on the `_count` values in ascending(asc) order! 

Example:
```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "5_customers_with_lowest_number_of_transactions": {
      "terms": {
        "field": "CustomerID",
        "size": 5,
        "order": {
          "_count": "asc"
        }
      }
    }
  }
}
```
Expected response from Elasticsearch:

You will see that buckets are now sorted in ascending order of doc_count, showing buckets with lowest doc_count first instead of last.  

![image](https://user-images.githubusercontent.com/60980933/116301597-77e18e80-a75d-11eb-835a-2fda2883bc5e.png)

### Combined Aggregations
Some questions require a combination of aggregations to be answered. 

For example, to answer the question "What is the sum of revenue per day?", we need to combine metric and buckets aggregations. 

#### Calculate the daily revenue
```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "transactions_per_day": {
      "date_histogram": {
        "field": "InvoiceDate",
        "calendar_interval": "day"
      },
      "aggs": {
        "daily_revenue": {
          "sum": {
            "script": {
              "source": "doc['UnitPrice'].value * doc['Quantity'].value"
            }
          }
        }
      }
    }
  }
}
```

Expected Response from Elasticsearch:

Elasticsearch returns an array of daily buckets. Within each bucket, it shows the number of documents("doc_count") within each bucket as well as the revenue generated from each day("daily_revenue). 

![image](https://user-images.githubusercontent.com/60980933/115085623-0df8f780-9ec8-11eb-81a5-a2da7d5759c1.png)

#### Calculating multiple metrics per bucket

You can also calculate multiple metrics per bucket. For example, if you wanted to calculate the daily revenue and the number of unique customers per day in one go, you can add multiple metrics per bucket as shown below!  

Example: 
```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "transactions_per_day": {
      "date_histogram": {
        "field": "InvoiceDate",
        "calendar_interval": "day"
      },
      "aggs": {
        "daily_revenue": {
          "sum": {
            "script": {
              "source": "doc['UnitPrice'].value * doc['Quantity'].value"
            }
          }
        },
        "number_of_unique_customers_per_day": {
          "cardinality": {
            "field": "CustomerID"
          }
        }
      }
    }
  }
}
```
Expected Response from Elasticsearch:

Elasticsearch returns an array of daily buckets. Within each bucket, you will see that the unique number of customers per day and daily revenue have been calculated for each day!

![image](https://user-images.githubusercontent.com/60980933/115086712-08041600-9eca-11eb-9afe-43923477b371.png)

**Sorting by metric value of a sub-aggregation**

You do not always need to sort by time interval, numerical interval, or by doc_count! You can also sort by metric value of a sub-aggregation. 

Let's take a look at the request below. Within the sub-aggregation, metric values daily_revenue and number_of_unique_customers_per_day are calculated. 

Let's say you wanted to find which day had the highest daily revenue to date! 

All you need to do is to add the order parameter and sort buckets based on the metric value of daily_revenue in descending order! 

```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "transactions_per_day": {
      "date_histogram": {
        "field": "InvoiceDate",
        "calendar_interval": "day",
        "order": {
          "daily_revenue": "desc"
        }
      },
      "aggs": {
        "daily_revenue": {
          "sum": {
            "script": {
              "source": "doc['UnitPrice'].value * doc['Quantity'].value"
            }
          }
        },
        "number_of_unique_customers_per_day": {
          "cardinality": {
            "field": "CustomerID"
          }
        }
      }
    }
  }
}
```

Expected response from Elasticsearch:

You will see that the response is no longer sorted by date. The buckets are now sorted to return the highest daily revenue first! 

![image](https://user-images.githubusercontent.com/60980933/116132063-5794cf80-a68a-11eb-9e85-f129054cacb2.png)


### Q and A from the live workshop

**1. Aggregated result is based on shard level correct?**

Yes, you are correct!

Aggregation is performed on every shard and the results from every shard are sent to the coordinator node. The coordinator node merges the shard results together into one final response which is sent to the user. 

**2.  Does this include bookends? So are there duplicates at the 50.0 and 200? I was just wondering if it went for example 0-50 and 50.+ to 200.0 and then 200.+**

What a great question! I assume you are referring to the range aggregation example where I set the ranges from 0-50, 50-200, and 200+.

Let's take 0-50 range for example. This range has a lower limit(0) and a upper limit(50). When it comes to range in Elasticsearch, the lower limit is included but the upper limit is excluded. So the range 0-50 grabs everything from 0 upto but not including 50.

**3. What about Time Weighted Average, is it possible?**

At present, Elasticsearch does not have a built in feature that automatically calculates the time weighted average. However, you can always write a script that represents the formula for calculating the time weighted average and include it in the aggregation. Here is a documentation on [how to write scripts](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-scripting-using.html)!  


