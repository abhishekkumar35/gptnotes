# Building MCP Servers with TypeScript: A Comprehensive Guide

This advanced tutorial will guide you through creating a Model Context Protocol (MCP) server using TypeScript, including best practices, type safety, and modern development patterns.

## Table of Contents
- [Understanding MCP Servers](#understanding-mcp-servers)
- [TypeScript Setup and Configuration](#typescript-setup-and-configuration)
- [Project Structure and Architecture](#project-structure-and-architecture)
- [Implementation Guide](#implementation-guide)
- [Testing with TypeScript](#testing-with-typescript)
- [Deployment and Publishing](#deployment-and-publishing)
- [Advanced Features](#advanced-features)
- [Troubleshooting and Best Practices](#troubleshooting-and-best-practices)

## Understanding MCP Servers

### What is an MCP Server?
An MCP (Model Context Protocol) server provides a standardized interface for AI tools like Cursor to interact with external services. Using TypeScript adds type safety, better IDE support, and improved maintainability.

### Architecture Overview
```
Client (Cursor) <-> MCP Server <-> External Services
     |               |               |
     |-- HTTP/WS ----               |
     |                              |
     |------ Standardized API ------| 
```

## TypeScript Setup and Configuration

### 1. Project Initialization
```bash
# Create project directory
mkdir mcp-typescript
cd mcp-typescript

# Initialize npm
npm init -y

# Install TypeScript and essential dependencies
npm install typescript @types/node ts-node nodemon --save-dev

# Initialize TypeScript configuration
npx tsc --init

# Install core dependencies
npm install express cors axios winston dotenv
npm install @types/express @types/cors @types/winston --save-dev

# Development tools
npm install jest ts-jest @types/jest supertest @types/supertest eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin prettier --save-dev
```

### 2. TypeScript Configuration (tsconfig.json)
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "lib": ["ES2020"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "sourceMap": true,
    "declaration": true,
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    }
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist", "**/*.test.ts"]
}
```

### 3. Project Structure
```
mcp-typescript/
├── src/
│   ├── types/           # Type definitions
│   │   ├── mcp.ts
│   │   └── config.ts
│   ├── services/        # Business logic
│   │   └── feature.service.ts
│   ├── middleware/      # Express middleware
│   │   ├── error.middleware.ts
│   │   └── validation.middleware.ts
│   ├── controllers/     # Route handlers
│   │   └── feature.controller.ts
│   ├── utils/          # Utility functions
│   │   ├── logger.ts
│   │   └── response.ts
│   ├── routes/         # Express routes
│   │   └── index.ts
│   ├── config/         # Configuration
│   │   └── index.ts
│   └── server.ts       # Main application file
├── test/
│   ├── integration/
│   └── unit/
├── dist/               # Compiled JavaScript
├── docs/
│   └── API.md
├── tsconfig.json
├── jest.config.js
├── .eslintrc.js
├── .prettierrc
├── .env
└── package.json
```

## Implementation Guide

### 1. Type Definitions (src/types/mcp.ts)
```typescript
export interface MCPConfig {
  name: string;
  version: string;
  description: string;
  capabilities: Record<string, boolean>;
  type: 'local' | 'remote';
}

export interface MCPResponse<T> {
  success: boolean;
  timestamp: string;
  data?: T;
  error?: string;
}

export interface HealthStatus {
  status: 'healthy' | 'unhealthy';
  version: string;
  ready: boolean;
}

export interface ConfigResponse {
  baseUrl: string;
  status: {
    ready: boolean;
    message: string;
  };
}
```

### 2. Logger Configuration (src/utils/logger.ts)
```typescript
import winston from 'winston';

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json()
  ),
  defaultMeta: { service: 'mcp-server' },
  transports: [
    new winston.transports.Console({
      format: winston.format.combine(
        winston.format.colorize(),
        winston.format.simple()
      )
    }),
    new winston.transports.File({ 
      filename: 'error.log',
      level: 'error',
      maxsize: 5242880, // 5MB
      maxFiles: 5
    }),
    new winston.transports.File({ 
      filename: 'combined.log',
      maxsize: 5242880,
      maxFiles: 5
    })
  ]
});

export default logger;
```

### 3. Response Utility (src/utils/response.ts)
```typescript
import { Response } from 'express';
import { MCPResponse } from '../types/mcp';

export class ResponseUtil {
  static success<T>(res: Response, data: T): void {
    const response: MCPResponse<T> = {
      success: true,
      timestamp: new Date().toISOString(),
      data
    };
    res.json(response);
  }

  static error(res: Response, error: Error, status: number = 500): void {
    const response: MCPResponse<never> = {
      success: false,
      timestamp: new Date().toISOString(),
      error: error.message
    };
    res.status(status).json(response);
  }
}
```

### 4. Error Middleware (src/middleware/error.middleware.ts)
```typescript
import { Request, Response, NextFunction } from 'express';
import logger from '../utils/logger';
import { ResponseUtil } from '../utils/response';

export class HttpError extends Error {
  constructor(public status: number, message: string) {
    super(message);
    this.name = 'HttpError';
  }
}

export function errorMiddleware(
  error: Error,
  req: Request,
  res: Response,
  next: NextFunction
): void {
  logger.error('Error:', {
    error: error.message,
    stack: error.stack,
    path: req.path,
    method: req.method
  });

  if (error instanceof HttpError) {
    ResponseUtil.error(res, error, error.status);
  } else {
    ResponseUtil.error(res, error);
  }
}
```

### 5. Main Server (src/server.ts)
```typescript
#!/usr/bin/env node
import 'dotenv/config';
import express from 'express';
import cors from 'cors';
import { MCPConfig, HealthStatus, ConfigResponse } from './types/mcp';
import { ResponseUtil } from './utils/response';
import { errorMiddleware } from './middleware/error.middleware';
import logger from './utils/logger';

const app = express();
const port = process.env.PORT || 3000;

// MCP Configuration
const MCP_CONFIG: MCPConfig = {
  name: "TypeScript MCP Server",
  version: "1.0.0",
  description: "A type-safe MCP server implementation",
  type: "local",
  capabilities: {
    myFeature: true
  }
};

// Middleware
app.use(cors());
app.use(express.json());

// Health Check
app.get('/health', (req, res) => {
  const health: HealthStatus = {
    status: 'healthy',
    version: MCP_CONFIG.version,
    ready: true
  };
  ResponseUtil.success(res, health);
});

// Configuration
app.get('/config', (req, res) => {
  const config: ConfigResponse = {
    baseUrl: `http://localhost:${port}`,
    status: {
      ready: true,
      message: "MCP Server is ready"
    }
  };
  ResponseUtil.success(res, { ...MCP_CONFIG, ...config });
});

// Feature endpoint
app.post('/my-feature', async (req, res, next) => {
  try {
    // Implementation
    const result = { message: "Feature executed successfully" };
    ResponseUtil.success(res, result);
  } catch (error) {
    next(error);
  }
});

// Error handling
app.use(errorMiddleware);

// Start server
app.listen(port, () => {
  logger.info(`MCP Server running on port ${port}`);
});

export default app;
```

### 6. Tests (test/integration/server.test.ts)
```typescript
import request from 'supertest';
import app from '../../src/server';

describe('MCP Server Integration Tests', () => {
  describe('Health Check', () => {
    it('should return healthy status', async () => {
      const response = await request(app).get('/health');
      expect(response.status).toBe(200);
      expect(response.body).toMatchObject({
        success: true,
        data: {
          status: 'healthy',
          ready: true
        }
      });
    });
  });

  describe('Configuration', () => {
    it('should return server configuration', async () => {
      const response = await request(app).get('/config');
      expect(response.status).toBe(200);
      expect(response.body).toMatchObject({
        success: true,
        data: {
          name: expect.any(String),
          version: expect.any(String),
          baseUrl: expect.stringContaining('http://'),
          status: {
            ready: true
          }
        }
      });
    });
  });
});
```

### 7. Jest Configuration (jest.config.js)
```javascript
module.exports = {
  preset: 'ts-jest',
  testEnvironment: 'node',
  roots: ['<rootDir>/src', '<rootDir>/test'],
  testMatch: ['**/*.test.ts'],
  moduleNameMapper: {
    '^@/(.*)$': '<rootDir>/src/$1'
  },
  collectCoverage: true,
  coverageDirectory: 'coverage',
  coveragePathIgnorePatterns: ['/node_modules/'],
  setupFiles: ['dotenv/config']
};
```

## Building and Publishing

### 1. Build Script (package.json)
```json
{
  "scripts": {
    "build": "tsc",
    "start": "node dist/server.js",
    "dev": "nodemon --exec ts-node src/server.ts",
    "test": "jest",
    "lint": "eslint . --ext .ts",
    "format": "prettier --write \"src/**/*.ts\"",
    "prepare": "husky install && npm run build",
    "prepublishOnly": "npm test && npm run lint"
  }
}
```

### 2. Publishing Configuration
```json
{
  "name": "@your-org/mcp-typescript",
  "version": "1.0.0",
  "main": "dist/server.js",
  "types": "dist/server.d.ts",
  "bin": {
    "mcp-typescript": "dist/server.js"
  },
  "files": [
    "dist",
    "mcp.json",
    "README.md",
    "LICENSE"
  ],
  "publishConfig": {
    "access": "public"
  }
}
```

### 3. Build and Publish
```bash
# Build the project
npm run build

# Run tests
npm test

# Publish to npm
npm publish --access public
```

## Advanced Features

### 1. Rate Limiting
```typescript
import rateLimit from 'express-rate-limit';

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100 // limit each IP to 100 requests per windowMs
});

app.use(limiter);
```

### 2. Request Validation
```typescript
import { body, validationResult } from 'express-validator';

const validateFeatureRequest = [
  body('param').isString().trim().notEmpty(),
  (req: Request, res: Response, next: NextFunction) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      throw new HttpError(400, 'Invalid request parameters');
    }
    next();
  }
];

app.post('/my-feature', validateFeatureRequest, async (req, res, next) => {
  // Implementation
});
```

### 3. WebSocket Support
```typescript
import WebSocket from 'ws';
import { Server } from 'http';

export function setupWebSocket(server: Server): void {
  const wss = new WebSocket.Server({ server });

  wss.on('connection', (ws) => {
    ws.on('message', (message) => {
      // Handle messages
    });
  });
}
```

## Integration with Cursor

### Configuration (mcp.json)
```json
{
  "mcpServers": {
    "typescript-mcp": {
      "command": "npx",
      "args": ["@your-org/mcp-typescript"],
      "env": {
        "PORT": "3000",
        "NODE_ENV": "development"
      },
      "autostart": true,
      "type": "local",
      "baseUrl": "http://localhost:3000",
      "configEndpoint": "/config",
      "healthEndpoint": "/health"
    }
  }
}
```

## Best Practices

1. **Type Safety**
   - Use strict TypeScript configuration
   - Define interfaces for all data structures
   - Avoid `any` type
   - Use type guards when necessary

2. **Error Handling**
   - Create custom error classes
   - Use middleware for consistent error handling
   - Provide detailed error messages
   - Implement proper logging

3. **Security**
   - Implement rate limiting
   - Validate all inputs
   - Use HTTPS in production
   - Keep dependencies updated
   - Implement proper authentication if needed

4. **Testing**
   - Write unit tests for utilities and services
   - Write integration tests for endpoints
   - Use test coverage reports
   - Implement CI/CD pipelines

## Troubleshooting

1. **Type Issues**
   - Check TypeScript configuration
   - Verify type definitions
   - Use type assertions carefully

2. **Build Issues**
   - Clear dist directory before building
   - Check module resolution
   - Verify tsconfig.json settings

3. **Runtime Issues**
   - Check environment variables
   - Verify port availability
   - Monitor memory usage
   - Check logs for errors

## Resources

- [TypeScript Documentation](https://www.typescriptlang.org/docs)
- [Express.js with TypeScript](https://expressjs.com/en/guide/typescript.html)
- [Jest with TypeScript](https://jestjs.io/docs/getting-started#using-typescript)
- [TypeScript Best Practices](https://github.com/typescript-eslint/typescript-eslint)

Remember: TypeScript adds type safety and better development experience to your MCP server, making it more maintainable and reliable. 