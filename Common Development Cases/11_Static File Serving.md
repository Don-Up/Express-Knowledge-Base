# Static File Serving

## Serving Static Assets
### 1. Using express.static

**Serve static files** such as images, CSS, and JavaScript using `express.static`.

**Example: Serving Static Files**

1. **Setup Express App**:

   ```javascript
   // server.js
   const express = require('express');
   const app = express();

   // Serve static files from 'public' directory
   app.use(express.static('public'));

   // Example route
   app.get('/', (req, res) => {
     res.send('<h1>Welcome to the static file server!</h1>');
   });

   const PORT = process.env.PORT || 3000;
   app.listen(PORT, () => {
     console.log(`Server running on port ${PORT}`);
   });
   ```

2. **Directory Structure**:

   ```
   project/
   ├── public/
   │   ├── images/
   │   │   └── logo.png
   │   ├── styles/
   │   │   └── style.css
   │   └── scripts/
   │       └── app.js
   └── server.js
   ```

- **Use `express.static`** to serve files.
- **Place assets** in a directory (e.g., `public`).
- **Access files** via URL paths corresponding to the directory structure.

### 2. Configuring Cache for Static Files

**Optimize static file serving** by configuring cache settings with `express.static`.

**Example: Configuring Cache**

1. **Setup Express App**:

   ```javascript
   // server.js
   const express = require('express');
   const app = express();

   // Serve static files with cache control
   app.use(express.static('public', {
     maxAge: '1d', // Cache static files for 1 day
     etag: false   // Disable ETag generation
   }));

   // Example route
   app.get('/', (req, res) => {
     res.send('<h1>Static files with cache control!</h1>');
   });

   const PORT = process.env.PORT || 3000;
   app.listen(PORT, () => {
     console.log(`Server running on port ${PORT}`);
   });
   ```

2. **Directory Structure**:

   ```
   project/
   ├── public/
   │   ├── images/
   │   │   └── logo.png
   │   ├── styles/
   │   │   └── style.css
   │   └── scripts/
   │       └── app.js
   └── server.js
   ```

- **Use `maxAge`** to set cache duration.
- **Disable `etag`** if not needed.
- **Cache static files** to improve performance.



## Building Single Page Applications
### Serving React or Angular Apps

**Serve a React SPA** with Express by serving the built static files.

**Example: Serving React App**

1. **Setup Express App**:

   ```javascript
   // server.js
   const express = require('express');
   const path = require('path');
   const app = express();

   // Serve static files from 'build' directory
   app.use(express.static(path.join(__dirname, 'build')));

   // Handle all requests by serving the React app
   app.get('*', (req, res) => {
     res.sendFile(path.join(__dirname, 'build', 'index.html'));
   });

   const PORT = process.env.PORT || 3000;
   app.listen(PORT, () => {
     console.log(`Server running on port ${PORT}`);
   });
   ```

2. **Build React App**:

   - Run `npm run build` in your React project.
   - Place the `build` folder in your Express project directory.

3. **Directory Structure**:

   ```
   project/
   ├── build/                // React build output
   │   ├── static/
   │   ├── index.html
   │   └── ...
   └── server.js
   ```

- **Use `express.static`** to serve static files from the `build` directory.
- **Route all requests** to `index.html` to handle client-side routing.