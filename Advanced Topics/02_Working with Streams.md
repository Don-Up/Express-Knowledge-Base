# Working with Streams
## Handling File Streams
### 1. Streaming Files for Download

**Stream files** in Express to handle large files efficiently.

**Example: Streaming File Download**

1. **Setup Express App**:

   ```javascript
   // server.js
   const express = require('express');
   const fs = require('fs');
   const path = require('path');
   const app = express();
   const PORT = process.env.PORT || 3000;

   // Route to stream a file for download
   app.get('/download', (req, res) => {
     const filePath = path.join(__dirname, 'large-file.zip');
     const stat = fs.statSync(filePath);

     res.setHeader('Content-Disposition', 'attachment; filename="large-file.zip"');
     res.setHeader('Content-Length', stat.size);

     // Create a read stream and pipe it to the response
     const readStream = fs.createReadStream(filePath);
     readStream.pipe(res);

     // Handle errors
     readStream.on('error', (err) => {
       res.status(500).send('Error reading file');
     });
   });

   app.listen(PORT, () => {
     console.log(`Server running on port ${PORT}`);
   });
   ```

2. **How It Works**:

   - **Create a Read Stream**: Efficiently reads the file in chunks.
   - **Pipe to Response**: Streams file data directly to the client.
   - **Set Headers**: Include `Content-Disposition` and `Content-Length` for proper file handling.

- **Use streams** to handle large files and improve performance.
- **Handle errors** to ensure a smooth user experience.

### 2. Processing Large Data Sets

**Process large data sets** efficiently using streams in Express.

**Example: Streaming Data Processing**

1. **Setup Express App**:

   ```javascript
   // server.js
   const express = require('express');
   const fs = require('fs');
   const csv = require('csv-parser'); // npm install csv-parser
   const app = express();
   const PORT = process.env.PORT || 3000;

   // Route to process a large CSV file
   app.get('/process-data', (req, res) => {
     const results = [];
     const filePath = 'large-data.csv';

     // Create a read stream for the CSV file
     const readStream = fs.createReadStream(filePath)
       .pipe(csv()) // Parse CSV data
       .on('data', (row) => {
         results.push(row); // Process each row
       })
       .on('end', () => {
         res.json(results); // Send processed data as JSON
       })
       .on('error', (err) => {
         res.status(500).send('Error processing file');
       });
   });

   app.listen(PORT, () => {
     console.log(`Server running on port ${PORT}`);
   });
   ```

2. **How It Works**:

   - **Create Read Stream**: Efficiently reads large CSV files.
   - **Pipe and Parse**: Use `csv-parser` to process data in chunks.
   - **Collect and Respond**: Gather results and respond once processing is complete.

- **Streams** allow handling large data sets without loading the entire file into memory.
- **Error handling** ensures robustness in data processing.