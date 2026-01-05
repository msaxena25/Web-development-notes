# What is Vite?

**Vite** (pronounced "veet) is a next-generation frontend tooling. 
It was created by Evan You, the creator of Vue.js.

**Key Differences and Why Vite is often preferred:**


| **Feature**                | **CRA**                                                                 | **Vite**                                                                                   |
|----------------------------|-------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|
| **Development Approach**   | Uses Webpack, which builds the entire application before serving it.    | Utilizes native ES Modules, allowing the browser to handle module resolution.             |
| **Build Times**            | Large build times.                                                     | Faster builds, leveraging Rollup for production.                                          |
| **Hot Module Replacement** | Slow Hot Module Replacement (HMR).                                     | Fast HMR, with updates in milliseconds.                                                   |
| **Configuration**          | Complex configuration.                                                 | Simple configuration using a `vite.config.js` file.                                       |
| **Framework Support**      | Primarily focused on React.                                            | Framework-agnostic, supporting React, Vue, Svelte, Lit, Preact, and vanilla JavaScript.   |
| **Recommendation**         | Not recommended for new applications.                                  | Officially recommended as a modern tool.                                                  |

----

### Does Vite use Rollup?

Yes, Vite uses Rollup as its bundler for production builds. While Vite leverages ES modules for development, it relies on Rollup to optimize and bundle the application for deployment. This ensures smaller, faster, and more efficient production builds.

### Key Feature:

- Tree-shaking – removes unused code automatically  
- ES module–first – built specifically for import/export  
- Smaller bundle size than Webpack  
- Fast production builds  
- Used by Vite, Svelte, libraries  

Rollup is particularly renowned for its highly effective **"tree-shaking"** capabilities.

### What is  meaning of tree shaking?

- Tree shaking is a process used in JavaScript bundlers (like Rollup or Webpack) to remove unused code from the final bundle.
- This helps reduce the size of the production build, making it faster to load and more efficient.

-------------

### Is React use webpack?

When you create a React application using `npx create-react-app`, Webpack is included under the hood to handle tasks like bundling JavaScript, CSS, images, and other assets.

-----------

### CRA

Create React App (CRA) was created by Facebook.