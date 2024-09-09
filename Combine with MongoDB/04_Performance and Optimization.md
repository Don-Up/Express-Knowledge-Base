# Performance and Optimization

## Indexing and Query Optimization
### 1. Creating Indexes in Mongoose

**Optimize queries** by creating indexes in Mongoose to improve performance.

**Example: Creating Indexes in Mongoose**

1. **Define Mongoose Model with Indexes**:

   ```javascript
   // models/User.js
   const mongoose = require('mongoose');
   const Schema = mongoose.Schema;

   const userSchema = new Schema({
     username: { type: String, required: true, unique: true },
     email: { type: String, required: true, unique: true },
     password: { type: String, required: true },
   });

   // Create an index on the 'username' field
   userSchema.index({ username: 1 });

   // Create a compound index on 'email' and 'password'
   userSchema.index({ email: 1, password: 1 });

   module.exports = mongoose.model('User', userSchema);
   ```

2. **Use Indexed Fields in Queries**:

   ```javascript
   // routes/userRoutes.js
   const express = require('express');
   const User = require('../models/User');
   const router = express.Router();
   
   // Optimized query using indexed field
   router.get('/user/:username', async (req, res) => {
     try {
       const user = await User.findOne({ username: req.params.username }).exec();
       if (user) {
         res.json(user);
       } else {
         res.status(404).send('User not found');
       }
     } catch (err) {
       res.status(500).send('Server error');
     }
   });
   
   module.exports = router;
   ```

- **Indexes**: Improve query performance by defining indexes on frequently queried fields in Mongoose models. Use `schema.index()` to create single or compound indexes.

### 2. Optimizing Query Performance

**Optimize query performance** by using indexes and efficient query techniques.

**Example: Optimizing Queries**

1. **Define Indexes in Mongoose**:

   ```javascript
   // models/Product.js
   const mongoose = require('mongoose');
   const Schema = mongoose.Schema;

   const productSchema = new Schema({
     name: { type: String, required: true },
     price: { type: Number, required: true },
     category: { type: String }
   });

   // Index on 'price' for fast sorting
   productSchema.index({ price: 1 });

   module.exports = mongoose.model('Product', productSchema);
   ```

2. **Efficient Query with Index Usage**:

   ```javascript
   // routes/productRoutes.js
   const express = require('express');
   const Product = require('../models/Product');
   const router = express.Router();
   
   // Optimized query using indexed field 'price'
   router.get('/products', async (req, res) => {
     try {
       // Sorting products by price
       const products = await Product.find()
                                     .sort({ price: 1 })
                                     .exec();
       res.json(products);
     } catch (err) {
       res.status(500).send('Server error');
     }
   });
   
   module.exports = router;
   ```

- **Indexing**: Improve query performance by adding indexes on fields frequently used in queries. Use `.sort()` and `.exec()` efficiently to leverage indexes.



## Handling Large Datasets
### 1. Pagination and Limitations

**Optimize performance** with pagination for large datasets to avoid overwhelming the server.

**Example: Implementing Pagination**

1. **Define Pagination in Query**:

   ```javascript
   // routes/productRoutes.js
   const express = require('express');
   const Product = require('../models/Product');
   const router = express.Router();

   // Pagination with query parameters 'page' and 'limit'
   router.get('/products', async (req, res) => {
     const { page = 1, limit = 10 } = req.query; // Default to page 1, limit 10
     try {
       const products = await Product.find()
                                     .skip((page - 1) * limit) // Skip documents
                                     .limit(parseInt(limit))    // Limit number of documents
                                     .exec();
       res.json(products);
     } catch (err) {
       res.status(500).send('Server error');
     }
   });

   module.exports = router;
   ```

2. **Example Request**:
   - `/products?page=2&limit=5` retrieves the second page of products with 5 items per page.

- **Pagination**: Use `.skip()` and `.limit()` to manage large datasets efficiently, reducing the amount of data processed and transmitted.

### 2. Using MongoDB Aggregations

**Optimize performance** by using MongoDB aggregations for complex queries and large datasets.

**Example: Aggregation Pipeline**

1. **Define Aggregation Pipeline**:

   ```javascript
   // routes/productRoutes.js
   const express = require('express');
   const Product = require('../models/Product');
   const router = express.Router();

   // Aggregation example: Group products by category and calculate average price
   router.get('/products/aggregate', async (req, res) => {
     try {
       const results = await Product.aggregate([
         { $group: { _id: "$category", averagePrice: { $avg: "$price" } } },
         { $sort: { averagePrice: -1 } } // Sort by average price descending
       ]).exec();
       res.json(results);
     } catch (err) {
       res.status(500).send('Server error');
     }
   });

   module.exports = router;
   ```

2. **Example Request**:
   - `/products/aggregate` aggregates products by category, calculates the average price, and sorts by price.

- **Aggregation Pipeline**: Use MongoDB’s aggregation framework for complex data processing like grouping, filtering, and sorting, reducing the need for application-level processing.