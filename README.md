# MongoDB Query Guide

## Table of Contents
- [Basic Queries](#basic-queries)
- [Intermediate Queries](#intermediate-queries)
- [Advanced Queries](#advanced-queries)
- [Aggregation Queries](#aggregation-queries)
- [Interview Questions](#interview-questions)

## Basic Queries

1. **Find All Documents in a Collection**

    ```javascript
    db.collection.find({})
    ```
    **Explanation**: This query retrieves all documents from the specified collection.

2. **Find Documents with a Specific Field Value**

    ```javascript
    db.collection.find({ field: value })
    ```
    **Explanation**: This query retrieves documents where the specified field matches the given value.

3. **Projection (Select Specific Fields)**

    ```javascript
    db.collection.find({ field: value }, { field1: 1, field2: 1 })
    ```
    **Explanation**: This query retrieves documents where the specified field matches the given value and only returns the specified fields.

4. **Query with Comparison Operators**

    ```javascript
    db.collection.find({ age: { $gt: 25 } })
    ```
    **Explanation**: This query retrieves documents where the age field is greater than 25. `$gt` is a comparison operator.

5. **Query with Logical Operators**

    ```javascript
    db.collection.find({ $and: [{ age: { $gt: 25 } }, { status: 'active' }] })
    ```
    **Explanation**: This query retrieves documents where the age is greater than 25 and the status is 'active'. `$and` is a logical operator.

## Intermediate Queries

6. **Insert a Document**

    ```javascript
    db.collection.insertOne({ name: "John", age: 30, status: "active" })
    ```
    **Explanation**: This query inserts a new document into the collection.

7. **Update a Document**

    ```javascript
    db.collection.updateOne({ name: "John" }, { $set: { age: 31 } })
    ```
    **Explanation**: This query finds a document with the name 'John' and updates the age field to 31.

8. **Delete a Document**

    ```javascript
    db.collection.deleteOne({ name: "John" })
    ```
    **Explanation**: This query deletes a document with the name 'John'.

9. **Aggregation Pipeline**

    ```javascript
    db.collection.aggregate([
        { $match: { status: "active" } },
        { $group: { _id: "$age", total: { $sum: 1 } } }
    ])
    ```
    **Explanation**: This query first matches documents with status 'active' and then groups them by age, counting the total number of documents for each age group.

10. **Sorting Results**

    ```javascript
    db.collection.find({}).sort({ age: 1 })
    ```
    **Explanation**: This query retrieves all documents and sorts them by the age field in ascending order.

## Advanced Queries

11. **Indexing**

    ```javascript
    db.collection.createIndex({ name: 1 })
    ```
    **Explanation**: This query creates an index on the name field to improve query performance.

12. **Text Search**

    ```javascript
    db.collection.createIndex({ description: "text" })
    db.collection.find({ $text: { $search: "keyword" } })
    ```
    **Explanation**: This query first creates a text index on the description field and then searches for documents containing the keyword.

13. **Geospatial Queries**

    ```javascript
    db.collection.createIndex({ location: "2dsphere" })
    db.collection.find({
        location: {
            $near: {
                $geometry: { type: "Point", coordinates: [ -73.9667, 40.78 ] },
                $maxDistance: 1000
            }
        }
    })
    ```
    **Explanation**: This query first creates a geospatial index on the location field and then finds documents near the specified coordinates within a 1000-meter radius.

14. **Update with Array Filters**

    ```javascript
    db.collection.updateOne(
        { name: "John" },
        { $set: { "grades.$[element].mean": 85 } },
        { arrayFilters: [ { "element.grade": { $gte: 80 } } ] }
    )
    ```
    **Explanation**: This query updates the mean field of grades array elements where the grade is greater than or equal to 80 for the document with the name 'John'.

15. **Lookup (Join) with Aggregation**

    ```javascript
    db.orders.aggregate([
        {
            $lookup: {
                from: "customers",
                localField: "customerId",
                foreignField: "_id",
                as: "customerDetails"
            }
        }
    ])
    ```
    **Explanation**: This query performs a left outer join to the customers collection, adding customer details to the orders collection based on the customerId field.

## Aggregation Queries

### Basics of Aggregation

Aggregation operations process data records and return computed results. MongoDB provides a powerful aggregation framework that includes a variety of stages and expressions for data transformation and analysis.

1. **$match**: Filters the documents to pass only those that match the specified condition(s).

    ```javascript
    db.collection.aggregate([
        { $match: { status: "active" } }
    ])
    ```
    **Explanation**: This stage filters the documents to include only those with status 'active'.

2. **$group**: Groups input documents by a specified identifier expression and applies the accumulator expressions.

    ```javascript
    db.collection.aggregate([
        { $group: { _id: "$status", count: { $sum: 1 } } }
    ])
    ```
    **Explanation**: This stage groups the documents by the status field and counts the number of documents in each group.

3. **$project**: Passes along the documents with only the specified fields.

    ```javascript
    db.collection.aggregate([
        { $project: { name: 1, age: 1, status: 1 } }
    ])
    ```
    **Explanation**: This stage includes only the name, age, and status fields in the output documents.

4. **$sort**: Sorts all input documents and returns them in sorted order.

    ```javascript
    db.collection.aggregate([
        { $sort: { age: -1 } }
    ])
    ```
    **Explanation**: This stage sorts the documents by the age field in descending order.

5. **$limit**: Limits the number of documents passed to the next stage in the pipeline.

    ```javascript
    db.collection.aggregate([
        { $limit: 5 }
    ])
    ```
    **Explanation**: This stage limits the output to the first 5 documents.

6. **$lookup**: Performs a left outer join to another collection in the same database.

    ```javascript
    db.orders.aggregate([
        {
            $lookup: {
                from: "customers",
                localField: "customerId",
                foreignField: "_id",
                as: "customerDetails"
            }
        }
    ])
    ```
    **Explanation**: This stage performs a join to the customers collection and adds customer details to the orders collection.

### Advanced Aggregation

1. **$unwind**: Deconstructs an array field from the input documents to output a document for each element.

    ```javascript
    db.collection.aggregate([
        { $unwind: "$tags" }
    ])
    ```
    **Explanation**: This stage deconstructs the tags array field, outputting a document for each element in the array.

2. **$addFields**: Adds new fields to documents.

    ```javascript
    db.collection.aggregate([
        { $addFields: { fullName: { $concat: ["$firstName", " ", "$lastName"] } } }
    ])
    ```
    **Explanation**: This stage adds a new field fullName by concatenating the firstName and lastName fields.

3. **$replaceRoot**: Replaces the input document with the specified document.

    ```javascript
    db.collection.aggregate([
        { $replaceRoot: { newRoot: "$customerDetails" } }
    ])
    ```
    **Explanation**: This stage replaces the input document with the customerDetails document.

4. **$facet**: Processes multiple aggregation pipelines within a single stage on the same set of input documents.

    ```javascript
    db.collection.aggregate([
        {
            $facet: {
                "ageBuckets": [
                    { $match: { age: { $gte: 0 } } },
                    { $bucket: { groupBy: "$age", boundaries: [0, 18, 30, 50, 100], default: "Other", output: { count: { $sum: 1 } } } }
                ],
                "statusCounts": [
                    { $group: { _id: "$status", count: { $sum: 1 } } }
                ]
            }
        }
    ])
    ```
    **Explanation**: This stage processes two pipelines - one to bucket documents by age and another to count documents by status.

## Interview Questions

1. **Explain the difference between `find` and `aggregate` in MongoDB.**

    **Answer**: `find` is used for simple queries and retrieval of documents from a collection, while `aggregate` is used for more complex data processing and transformation using an aggregation pipeline.

2. **How does MongoDB handle indexing, and why is it important?**

    **Answer**: MongoDB uses indexing to improve query performance by allowing the database to quickly locate and access the data. Indexes are created on fields that are frequently queried, reducing the need to scan the entire collection.

3. **What are the benefits and drawbacks of using embedded documents versus references in MongoDB?**

    **Answer**: Embedded documents provide faster read operations and atomic updates since related data is stored within a single document. However, they can lead to larger document sizes and duplication of data. References, on the other hand, normalize data and reduce duplication, but require additional queries to retrieve related data, potentially impacting performance.

4. **Describe a use case where you would use MongoDB's `text` index and `text` search.**

    **Answer**: A use case for `text` index and `text` search is implementing a search functionality in an e-commerce application, where users can search for products by keywords in the product descriptions.

5. **How would you optimize a MongoDB query that is performing slowly?**

    **Answer**: To optimize a slow MongoDB query, you can:
    - Ensure proper indexing on the fields being queried.
    - Analyze query performance using `explain` to identify bottlenecks.
    - Refactor the query to reduce complexity.
    - Use aggregation pipelines for complex data processing.
    - Optimize schema design for read-heavy or write-heavy workloads.

## Conclusion

This guide covers a range of MongoDB queries from basic to advanced, along with explanations and interview questions to help you prepare for senior-level positions. Understanding these concepts and practicing these queries will strengthen your MongoDB skills and help you excel in interviews.
