# Node.js TypeScript Backend Template for Vercel

This repository contains a minimal Node.js backend template written in TypeScript. It is professionally structured and ideal for future expansion. The project is organized into separate folders for configurations, middleware, routes, controllers, services, and shared utilities. The template is preconfigured to be deployed as a Serverless Function on Vercel, while still allowing you to run and test it locally (for example, via a health-check route).

## Features

- **Modular Structure**: Clear separation into folders for configurations, middleware, routes, controllers, services, and shared types/utilities.
- **Health Check Route**: A simple `/health/ping` route to test the system.
- **User Management**: An example route for deleting a user account (using Supabase Service Role).
- **Environment Variables**: Configurable via a `.env` file.
- **Optional Supabase Integration**: Use the `USE_SUPABASE` environment variable to enable or disable Supabase features. If disabled, the project will run without Supabase integration.
- **Local & Vercel Operation**: A conditional listener in `api/index.ts` (or a separate wrapper in `api/server.ts`) enables local testing, while Vercel uses the exported app handler.

## Folder Structure

```
.
├── api
│   ├── configs/           # Configuration files (e.g., CORS, Database)
│   ├── controllers/       # Controllers for handling HTTP requests
│   ├── middleware/        # Custom middleware (e.g., Auth, Logger)
│   ├── routes/            # Express routes (e.g., Health, User)
│   ├── services/          # Business logic and services
│   ├── shared/            # Shared types and utilities
│   │   └── types/         # Type definitions (including Express.Request extensions)
│   ├── utils/             # Utility functions (e.g., HTTP helpers, Logger)
│   ├── index.ts           # Main Express app (exported for Vercel)
│   └── server.ts          # (Optional) Local server wrapper for development
├── .env                   # Environment variables (do not commit; see .gitignore)
├── .gitignore
├── package.json
├── tsconfig.json
└── vercel.json            # Vercel deployment configuration
```

## Getting Started

### Prerequisites

- Node.js (version 14 or higher recommended)
- npm

### Installation

1. **Clone the Repository:**

   ```bash
   git clone <repository-url>
   cd node-js-ts-vercel-backend-template
   ```

2. **Install Dependencies:**

   ```bash
   npm install
   ```

### Setting Up Environment Variables

Create a `.env` file in the root directory (do not commit this file) with the following content (adjust the values as needed):

```dotenv
PORT=3000
USE_SUPABASE=true
SUPABASE_URL=https://your-supabase-url.supabase.co
SUPABASE_SERVICE_KEY=your-supabase-service-key
JWT_SECRET=your-jwt-secret
CRON_SECRET=your-cron-secret
```

**Optional Supabase Integration:**  
- To run the project without Supabase, set `USE_SUPABASE=false` in the `.env` file.  
- When `USE_SUPABASE` is set to `false`, the Supabase client will not be initialized, and any functionality relying on Supabase (e.g., user deletion) will be bypassed or handled gracefully.

### Local Operation

#### Option 1: Separate Server Wrapper

If you prefer to have a separate file for starting the local server, create a file named `api/server.ts`:

```typescript
// api/server.ts
import app from './index';

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server running on http://localhost:${PORT}`);
});
```

Then adjust your `package.json` scripts accordingly:

```json
"scripts": {
  "dev": "ts-node-dev --respawn --transpile-only api/server.ts",
  "build": "tsc",
  "start": "node dist/server.js"
}
```

#### Option 2: Conditional Listener in the Main File

Alternatively, add a conditional listener in `api/index.ts` so that the server starts only when running locally:

```typescript
// api/index.ts
import express from 'express';
import cors from 'cors';
import { corsOptions } from './configs/cors';
import healthRouter from './routes/health';
import userRouter from './routes/user.routes';

const app = express();

// Global Middleware
app.use(cors(corsOptions));
app.use(express.json());

// Mount Routes
app.use('/api/health', healthRouter);
app.use('/api/user', userRouter);

// Start the listener only if this module is executed directly (i.e., locally)
if (require.main === module) {
  const PORT = process.env.PORT || 3000;
  app.listen(PORT, () => {
    console.log(`Server running on http://localhost:${PORT}`);
  });
}

export default app;
```

When you run the app locally (e.g., via `npm run dev`), the listener will start and display the URL. When deployed on Vercel, the conditional check prevents the listener from starting so that Vercel can handle it as a Serverless Function.

### Starting the Project Locally

Run the following command in your terminal:

```bash
npm run dev
```

You should see an output similar to:

```
Server running on http://localhost:3000
```

### Testing the Health Route

Open your browser or use a tool like Postman and visit:

```
http://localhost:3000/health/ping
```

You should receive a JSON response similar to:

```json
{
  "status": "OK",
  "memoryUsage": { "rss": "XXMB", "heapTotal": "YYMB", "heapUsed": "ZZMB", "external": "WWMB" },
  "timestamp": "2025-02-11T12:34:56.789Z"
}
```

## Deployment to Vercel

The template is configured so that Vercel treats `api/index.ts` as a Serverless Function. The `vercel.json` file contains the necessary instructions, so you can simply import the repository via GitHub or deploy using the Vercel CLI.

During deployment, the conditional listener in `api/index.ts` will not run (since `require.main !== module`), and Vercel will use the exported Express app handler.

## Extending and Customizing

- **Declaration Merging:**  
  The file `api/shared/types/express/index.d.ts` extends the Express Request interface to include `clientInfo` and `user`.
- **Utility Modules:**  
  If not already present, create simple implementations in `api/utils/http.utils.ts` and `api/utils/logger.ts`.
- **Additional Routes/Controllers:**  
  Add more routes and controllers in the respective folders to implement your business logic.

## License

This project is licensed under the [MIT License](LICENSE).
````markdown

Simply copy the entire block above (including the triple backticks) into your `README.md` file.
