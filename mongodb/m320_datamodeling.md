---

layout: default
title: m320_datamodeling
parent: mongodb

---

# Mongo DB Data Modeling course summary

[Course](https://learn.mongodb.com/courses/m320-mongodb-data-modeling)

## Some docs

[BSON Data types](https://www.mongodb.com/docs/manual/reference/bson-types/?_ga=2.251732681.114112862.1667323576-1663210444.1666003266)

## Introduction, let's go

### Mongodb document model

![data\_hierarchy](/vault/mongodb/m320_img/mongodb_data_hierarchy.png)

Each deployment can have multiple databases\
Each database can have multiple collections\
Each collection can have multiple documents

### Understanding schema

Different documents within the same collection can have different fields\
Datatype of fields may vary between different documents in the same collection\
Monogdb collection can have schema for document:

*   document may have no rules for fields
*   document may have rules for certain fields
*   docuemnt may have rules for all fields

### Data Modeling constraints connected with Computers

#### Hardware

Which type of storage use?

*   RAM
*   SSD
*   HDD

#### Data

*   Size
*   Security

#### Application

*   Network latency

#### Database Server(Mongodb in this case)

*   max size of document is 16MB
*   reading information from document, required full document to be loaded in RAM

> **MongoDB Terms**\
> Working set - the total body of data that the application uses in the course of normal operations\
> In ohter words - frequently accessed documents and indexes

> **Couple tips**\
> Keep frequently used Documents in RAM\
> Keep indexes in RAM\
> Prefer SSD over HDD
> Infrequently used data can use HDD

### Data Modeling Methodology

![mongodb\_methodology](/vault/mongodb/m320_img/mongodb_methodology.png)

Methodology consists of 3 phases

1.  Define workload

Based on Scenarious, Production logs & stats, knowledge of business domain expert need to define:

*   size of data in a month
*   size of data in a year
*   non functional requirements for data(e.g. max response time)
*   entities

2.  Define relationships

On this phase need to define relationships between data

*   identify type of relationships (1-to-1, 1-to-N, M-to-N)
*   define should we link entity or embed

3.  Apply schema design patterns

*   will be covered later

### Simplicity vs. Performance

#### Simplicity criteria

For this type of projects usually have limited expectations in small reqirements in terms of resources

Start by identifying the most important operations for system

Identify entities and relations. Very common in that case is have a fewer collections with documents that contains mode information and relations expressed by sub-documents or arrays.

All these will lead to fewer operations to retrieve data from db to an application

#### Performance criteria

Very likely that you need to model your database in this way if you

*   have sharding
*   need very fasr R\&W operations
*   need to support a lof of operations

In this case it's better to start by identifying operations and metrics for them, like:
operations per second, reqiured latency, and other operations property(is this operation a part of other larger operation etc.)

> **General advice for searching best approach**\
> It's easier to and optimization later instead pulling of complexity from the application

![flexible\_methodology.png](/vault/mongodb/m320_img/flexible_methodology.png)

### Identifying the workload

List of operations

![example\_identifying\_ops.png](/vault/mongodb/m320_img/example_identifying_ops.png)

Operation details

![example\_op\_details.png](/vault/mongodb/m320_img/example_op_details.png)
![example\_op\_read\_details.png](/vault/mongodb/m320_img/example_op_read_details.png)

Operation importance

![example\_op\_importace.png](/vault/mongodb/m320_img/example_op_importace.png)

Summary

![example\_op\_info\_summary.png](/vault/mongodb/m320_img/example_op_info_summary.png)

## Relationships

> One of the main question in modeling document storage is embed or link relationships?

### One-to-many

With this type of relation we have several options how to model schema in terms of embedding or linking

1.  Embed

*   embed one-side entity to many-side entities
    ![1\_t\_N\_embed\_many\_side.png](/vault/mongodb/m320_img/1_t_N_embed_many_side.png)

*   embed many-side entities to one-side entities
    ![1\_t\_N\_embed\_one\_side.png](/vault/mongodb/m320_img/1_t_N_embed_one_side.png)

Usualy embedding in the entities the most queried

2.  Link

*   link in the one-side entity
    ![1\_t\_N\_link\_one\_side.png](/vault/mongodb/m320_img/1_t_N_link_one_side.png)

*   link in the many-side entities
    ![1\_t\_N\_link\_many\_side.png](/vault/mongodb/m320_img/1_t_N_link_many_side.png)

Usually linking in the main side entities

### Many-to-many

![N\_t\_M\_exmp.png](/vault/mongodb/m320_img/N_t_M_exmp.png)

1.  Embed

*   array of subdocuments in the many-side
    ![N\_t\_M\_embed.png](/vault/mongodb/m320_img/N_t_M_embed.png)

*   array of subdocuments in the other many-side

Usually the most quired side is considered

2.  Link

*   array of references in the many-side
    ![N\_t\_M\_link\_main\_side.png](/vault/mongodb/m320_img/N_t_M_link_main_side.png)

*   array of references in the other many-side
    ![N\_t\_M\_link\_secondary\_side.png](/vault/mongodb/m320_img/N_t_M_link_secondary_side.png)

#### Tips

*   Ensure that shouldn't be simplified
*   Prefer embedding on the most quired side
*   Prefer embedding for information that is primarly static
*   Prefer linkink over embedding to avoid managind duplication

### One-to-one

1.  Embed

*   fields at the same level
*   grouping in the sub-documents

2.  Link

*   same id in both documents
*   in the main one-side
*   in the secondary one-side

#### Tips

*   Prefer embedding over linking for simplicity
*   Use subdocuments to organise fields
*   Use links for optimization purposes

### One-to-zillions

> What is zillions?
> This type of relationships introduce to define relationships when we have really big number of entities on the many-side(So actually it's not a new type of relationships but just a specific case of ont-to-many). And also it has specific designation in Crow's Foot notation:
> ![zillions\_notation.png](/vault/mongodb/m320_img/zillions_notation.png)
> Numbers in squared brackets designate information about number of links in format: **\[min, likely, max]**

So for this type of relationships we have only one option to model it: **link with link on the main side**

## Patterns

Applying patterns may lead to duplications, data staleness, writing extra app logic to ensure reference integrity

### Handling duplications

**Why duplication may need** - embedding information in a given document for faster access

**Concern of having duplications** - challenge for correctness and consistency

Cases with duplications

1.  **Need to save snapshot of certain object in a specific state**\
    Consider order history where each order should have an information about customer. If we will linking to customer by id in ther order document it may lead to cases where order referencing to wrong customer because customer info by this ID was updated after this order was delivered.

![duplication\_is\_solution.png](/vault/mongodb/m320_img/duplication_is_solution.png)

2.  **Duplication has minimal effect**\
    Consider a movie and an actors. We need to store information about which actor were filming in a movie. It's were unlikily that these information will changed after movie release, so it's ok to store list of actors inside movie document.

![dupl\_min\_effect.png](/vault/mongodb/m320_img/dupl_min_effect.png)

3.  **Duplication should be handled**\
    Consider a movie collection and it's revenue collections. Total revenue the movie should be calculated as sum of revenues in different countries and different periods of time. So do we want to save total revenue field in the movie document(duplicate information) or we want to calculate it each time when we need it? To answer these question we need to know more about scenarios of using application but it's were likely that having precomputed value will be good idea. But in these case we need to be ready to add logic in our application which will update these total revenue field or configure offline job which will be update this field. This things head us to possible data staleness.

### Handling data staleness

**Why data staleness** - new events come along at such rate that updating data constantly causes performance issues

**Concern** - data quality and relability

#### Ways to resolve staleness

*   Batch updates - can keep data staleness from 0 to any suitable time range, depending on how oftern this update is starting.

*   Find changes through mongo [change stream](https://www.mongodb.com/docs/manual/changeStreams/?_ga=2.52154217.114112862.1667323576-1663210444.1666003266) which let to subscribe on certaing changes and configure how to react on them

### Handling referential integrity

**Whe referential integrity** - documents may have links to each other, but mongodb doesn't support cascading deletes. So it's were likely that document may have links to unexisted documents.

**Concern** - data correctness and consistency

### Ways to resolve referential integrity

*   Mongo [change stream](https://www.mongodb.com/docs/manual/changeStreams/?_ga=2.52154217.114112862.1667323576-1663210444.1666003266)
*   Single document
*   Multidocument transactions

### Attribute pattern

Pattern description
![attr\_patter\_summary.png](/vault/mongodb/m320_img/attr_patter_summary.png)

Examples

Let's consider store that sales different types of product. Each of product will have fields that a common for all product like title or price. But some fields that contains characteristic of product may vary between differenet product category. It's ok, mongo support flexible schema for cases like this, so it's not a problem to store all these product in the same collection. But problem appers when we need to have indexes by these category specific fields. It may lead to huge number of indexes which is really hard to maintain. To solve this we can use attribute pattern like this:

![attr\_pattern\_product\_example.png](/vault/mongodb/m320_img/attr_pattern_product_example.png)

Or another example when we have film and releases date in different countries. If we want to able to search or filter by these fields it's better to apply attribute patern instead making indexes on every feild:

![attr\_pattern\_film\_releases\_example.png](/vault/mongodb/m320_img/attr_pattern_film_releases_example.png)

### Extended reference pattern

Pattern description
![ext\_ref\_pattern\_summary.png](/vault/mongodb/m320_img/ext_ref_pattern_summary.png)

Examples

Consider we have 2 collections: customer and order. These collections have relationships one-to-many, where each order can have strictly one customer, and every customer may or may not have orders. In these case we need again to uderstand our use cases, but most likely that we will load orders more often than customers. Assume that every time we show order we also need to show shipping addres which actually is a customer address on time whan order was placed. So in this case it's much correct to store shipping address inside order next to the link to the customer
![ext\_ref\_example.png](/vault/mongodb/m320_img/ext_ref_example.png)

### Subset pattern

Pattern description
![ubset\_pattern\_summary.png](/vault/mongodb/m320_img/subset_pattern_summary.png)

If you read a document from collections, this all documents fields should be read to a RAM. Also mongodb is trying to keep in RAM frequently used documents. So it head us to situtation where our document may have a lot of fields but in some frequently used queries we only need a subset of these fields. To avoid filing RAM with information we don't need we can divide collection into 2 collection with relationships one-to-one or one-to-many and access to second collection only when we really need it.

### Computed pattern

![compute\_pattern\_summary.png](/vault/mongodb/m320_img/compute_pattern_summary.png)

### Bucket pattern

![bucket\_pattern\_summary.png](/vault/mongodb/m320_img/bucket_pattern_summary.png)

Bucket pattern can be very usefull if trying to avoid really big amount of small documents or in opposite way when
we trying to avoid really big document, which aggregate a lot of repeatable data. It can be usefull in case if we dealing
with some metrics(different kind of metrics) or any type of data where we have a lot of informattion associated with one
object.

Let's consider we're collecting data from tempreture sensors which arrive every minute. Instead of having different
document for every mesurement, we can group them by device by day
![bucket\_by\_day\_exmpl.png](/vault/mongodb/m320_img/bucket_by_day_exmpl.png)
Or\
By device by houre
![bucket\_by\_hour\_example.png](/vault/mongodb/m320_img/bucket_by_hour_example.png)

When don't need to use this pattern

*   Random insertion/deletions inside a bucket
*   Difficult to sort accros the bucket, if you need to sort no by parameter you've bucketed
*   Adhoc queries might be more complex, because you need to understand how data is stored

### Schema versioning pattern

![schema\_version\_pattern\_summary.png](/vault/mongodb/m320_img/schema_version_pattern_summary.png)

### Tree patterns

[Docs](https://www.mongodb.com/docs/manual/applications/data-models-tree-structures/?_ga=2.14388215.114112862.1667323576-1663210444.1666003266)

![tree\_pattern\_summary.png](/vault/mongodb/m320_img/tree_pattern_summary.png)

Tree paterns need to help organise data module if we have operations which doing following things:

1.  Who are the ancestors of node X?
2.  Who reports to Y?
3.  Find all nodes that are under Z?
4.  Change all categories under N to under P

#### Parent references

In this model document looks like:

```json
{
    "id": 1234,
    "name": "doc_name",
    "parent": 4321
}
```

How this model is good for operations:

1.  Who are the ancestors of node X? - \[!] this type of operations possible but requires joins
2.  Who reports to Y? - \[+] this type of operations work well for this model
3.  Find all nodes that are under Z? - \[!] this type of operations possible, but you will need to iterate over a lot of documents
4.  Change all categories under N to under P - \[+] this type of operations work well for this model

#### Child references

Document example:

```json
{
    "id": 1234,
    "name": "parent",
    "children": [11, 22, 33]
}
```

How this model is good for operations:

1.  Who are the ancestors of node X? - \[!] complicated
2.  Who reports to Y? - \[!] complicated
3.  Find all nodes that are under Z? - \[+] this type of operations works well
4.  Change all categories under N to under P - \[!] complicated

#### Array of ancestors

Document example:

```json
{
    "id": 33,
    "name": "node",
    "ancestors": [11,22]
}
```

How this model is good for operations:

1.  Who are the ancestors of node X? - \[+] works well
2.  Who reports to Y? - \[+] works well
3.  Find all nodes that are under Z? - \[!] complicated
4.  Change all categories under N to under P - \[!] complicated

#### Materialized path

Document example:

```json
{
    "id": 33,
    "name": "node",
    "ancestors": ".11.22"
}
```

Looks like the same as array of ancestors, but get ability to filtering by the ancestors fields.

1.  Who are the ancestors of node X? - \[+] works well
2.  Who reports to Y? - \[!] not really efficient
3.  Find all nodes that are under Z? - \[!] complicated
4.  Change all categories under N to under P - \[!] complicated

But of course noone can stop you from combine these techniques

### Polymoprphic patterns

![polymorph\_pattern\_summary.png](/vault/mongodb/m320_img/polymorph_pattern_summary.png)

### Approxymation pattern

![aprox\_pattern\_summary.png](/vault/mongodb/m320_img/aprox_pattern_summary.png)

### Outliers pattern

![outlires\_pattern\_summary.png](/vault/mongodb/m320_img/outlires_pattern_summary.png)

### Patterns summary

![patterns\_summary.png](/vault/mongodb/m320_img/patterns_summary.png)
