# mongodb8th
a.	Demonstrate creation of different types of indexes on collection (unique, sparse, compound and multikey indexes)
Unique Indexes

A unique index ensures that the indexed fields do not store duplicate values. A unique index on a single field ensures that a value appears at most once for a given field. A unique compound index ensures that any given combination of the index key values only appears at most once. By default, MongoDB creates a unique index on the _id field during the creation of a collection.
1. Unique Index
A unique index ensures that the indexed field does not have duplicate values.
db.Users.createIndex(
  { email: 1 },
  { unique: true }
)

db.Users.insertOne({ username: "alice", email: "alice@example.com" }) // OK
db.Users.insertOne({ username: "bob", email: "alice@example.com" })   // Error!


2. Sparse Index
A sparse index only indexes documents where the field exists.
Documents without the field are ignored.
{ "_id": 1, "name": "Alice", "email": "alice@example.com" }
{ "_id": 2, "name": "Bob" }
{ "_id": 3, "name": "Charlie", "email": "charlie@example.com" }

Create a Sparse Index
db.Employees.createIndex(
  { email: 1 },
  { sparse: true }
)
Behavior of Sparse Index
•	Alice and Charlie are indexed because they have an email.
•	Bob is ignored by the index.

db.Employees.find({ email: "alice@example.com" })

db.Employees.find({ email: null })

3. Compound Index
A compound index includes multiple fields in a single index.
It helps queries that involve both fields.
{ "_id": 1, "category": "Electronics", "price": 1200, "name": "Smartphone" }
{ "_id": 2, "category": "Electronics", "price": 800, "name": "Headphones" }
{ "_id": 3, "category": "Clothing", "price": 50, "name": "T-Shirt" }

db.Products.find({ category: "Electronics", price: { $lt: 1000 } })

Create a Compound Index
db.Products.createIndex(
  { category: 1, price: -1 }
)
{ category: 1, price: -1 } means:
•	category is indexed in ascending order,
•	price is indexed in descending order.

4. Multikey Index
A multikey index is created when you index a field that is an array.
MongoDB automatically creates a multikey index.
{ "_id": 1, "title": "MongoDB Basics", "tags": ["database", "nosql", "mongodb"] }
{ "_id": 2, "title": "Advanced Databases", "tags": ["database", "sql", "optimization"] }
{ "_id": 3, "title": "Web Development", "tags": ["javascript", "web", "frontend"] }

Create an Index on the Array Field
db.Books.createIndex({ tags: 1 })
•	MongoDB automatically creates a multikey index because tags is an array.
You don't need to specify anything special — it detects it.

b.	Demonstrate optimization of queries using indexes.
1. Setup a Sample Collection
Suppose we have a students collection with many documents:
db.students.insertMany([
  { name: "Alice", age: 22, major: "CS" },
  { name: "Bob", age: 24, major: "Math" },
  { name: "Charlie", age: 22, major: "Physics" },
  { name: "David", age: 23, major: "CS" },
  // ... imagine thousands more
]);

2. Query Without Index
Let’s run a query to find students with age 22:
db.students.find({ age: 22 }).explain("executionStats");
Now check how MongoDB is executing the query using explain():
db.students.find({ age: 22 }).explain("executionStats");

Important fields in executionStats:
•	"totalDocsExamined" = All documents (full collection scan!)
•	"executionTimeMillis" = Higher time
➡️ Problem: Without an index, MongoDB scans every document!

3. Create an Index
Now, create an index on the age field:
db.students.createIndex({ age: 1 });
Index is now created on age.
4. Query With Index
Run the same query again:
db.students.find({ age: 22 }).explain("executionStats");
Now, observe:
•	"totalDocsExamined" = Only matched documents (very few!)
•	"executionTimeMillis" = Much lower
➡️ Result: MongoDB directly jumps to the correct documents using the index!
 
Real Optimization Techniques
•	Create indexes for fields frequently used in search, sort, or join.
•	Compound indexes for queries involving multiple fields.
•	Use explain() regularly to analyze your query plans.
•	Avoid unnecessary indexes (they consume disk/memory and slow writes).
