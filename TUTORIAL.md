# Building and Publishing MCP Servers: A Complete Guide

This tutorial will guide you through creating a Model Context Protocol (MCP) server from scratch and publishing it for others to use.

## Table of Contents
- [Understanding MCP Servers](#understanding-mcp-servers)
- [Setting Up Your Development Environment](#setting-up-your-development-environment)
- [Creating Your First MCP Server](#creating-your-first-mcp-server)
- [Testing Your MCP Server](#testing-your-mcp-server)
- [Publishing Your MCP Server](#publishing-your-mcp-server)
- [Best Practices and Tips](#best-practices-and-tips)

## Understanding MCP Servers

### What is an MCP Server?
An MCP (Model Context Protocol) server is a standardized interface that allows AI tools like Cursor to interact with external services and data sources. It acts as a bridge between AI models and the outside world, providing consistent APIs for various capabilities.

### Key Components
1. **Health Check Endpoint**: Tells Cursor if the server is running
2. **Configuration Endpoint**: Provides server capabilities and settings
3. **Custom Endpoints**: Your specific functionality
4. **Standard Response Format**: Consistent JSON responses

## Setting Up Your Development Environment

### 1. Prerequisites
```bash
# Install Node.js (v14 or higher)
# Install npm (comes with Node.js)
# Install Git

# Create a new directory for your project
mkdir my-mcp-server
cd my-mcp-server

# Initialize git
git init

# Initialize npm
npm init -y
```

### 2. Essential Dependencies
```bash
# Core dependencies
npm install express cors axios winston dotenv

# Development dependencies
npm install --save-dev nodemon eslint jest prettier husky
```

### 3. Project Structure
```
my-mcp-server/
├── src/
│   ├── server.js        # Main server file
│   ├── config.js        # Configuration
│   └── handlers/        # Request handlers
├── test/
│   └── server.test.js   # Tests
├── docs/
│   └── API.md          # API documentation
├── .env                # Environment variables
├── .gitignore         # Git ignore file
├── .npmignore         # npm ignore file
├── package.json       # Package configuration
├── mcp.json           # Cursor MCP configuration
└── README.md          # Documentation
```

## Creating Your First MCP Server

### 1. Basic Server Setup (src/server.js)
```javascript
#!/usr/bin/env node

require('dotenv').config();
const express = require('express');
const cors = require('cors');
const winston = require('winston');

// Logger setup
const logger = winston.createLogger({
  level: 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.json()
  ),
  transports: [
    new winston.transports.Console(),
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' })
  ]
});

const app = express();
const port = process.env.PORT || 3000;

// Middleware
app.use(cors());
app.use(express.json());

// MCP Configuration
const MCP_CONFIG = {
  name: "My MCP Server",
  version: "1.0.0",
  description: "My first MCP server",
  capabilities: {
    // List your capabilities here
    myFeature: true
  }
};

// Required MCP endpoints
app.get('/health', (req, res) => {
  res.json({
    success: true,
    status: 'healthy',
    timestamp: new Date().toISOString(),
    version: MCP_CONFIG.version,
    ready: true
  });
});

app.get('/config', (req, res) => {
  res.json({
    success: true,
    timestamp: new Date().toISOString(),
    config: {
      ...MCP_CONFIG,
      baseUrl: `http://localhost:${port}`,
      status: {
        ready: true,
        message: "MCP Server is ready"
      }
    }
  });
});

// Custom endpoint example
app.post('/my-feature', async (req, res) => {
  try {
    // Your feature implementation
    const result = { message: "Feature executed successfully" };
    
    res.json({
      success: true,
      timestamp: new Date().toISOString(),
      data: result
    });
  } catch (error) {
    logger.error('Error:', error);
    res.status(500).json({
      success: false,
      error: error.message,
      timestamp: new Date().toISOString()
    });
  }
});

// Start server
app.listen(port, () => {
  logger.info(`MCP Server running on port ${port}`);
});
```

### 2. Configuration (mcp.json)
```json
{
  "mcpServers": {
    "my-mcp-server": {
      "command": "npx",
      "args": ["my-mcp-server"],
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

### 3. Package Configuration (package.json)
```json
{
  "name": "@your-org/my-mcp-server",
  "version": "1.0.0",
  "description": "My first MCP server",
  "main": "src/server.js",
  "bin": {
    "my-mcp-server": "src/server.js"
  },
  "scripts": {
    "start": "node src/server.js",
    "dev": "nodemon src/server.js",
    "test": "jest",
    "lint": "eslint .",
    "prepare": "husky install"
  },
  "files": [
    "src/",
    "mcp.json",
    "README.md",
    "LICENSE"
  ],
  "publishConfig": {
    "access": "public"
  }
}
```

## Testing Your MCP Server

### 1. Manual Testing
```bash
# Start the server
npm run dev

# Test health endpoint
curl http://localhost:3000/health

# Test config endpoint
curl http://localhost:3000/config

# Test custom endpoint
curl -X POST http://localhost:3000/my-feature \
  -H "Content-Type: application/json" \
  -d '{"param": "value"}'
```

### 2. Automated Testing (test/server.test.js)
```javascript
const request = require('supertest');
const app = require('../src/server');

describe('MCP Server', () => {
  test('Health check returns correct format', async () => {
    const response = await request(app).get('/health');
    expect(response.status).toBe(200);
    expect(response.body).toHaveProperty('success', true);
    expect(response.body).toHaveProperty('status', 'healthy');
  });

  // Add more tests...
});
```

## Publishing Your MCP Server

### 1. Prepare for Publishing
```bash
# Update version
npm version patch/minor/major

# Build (if needed)
npm run build

# Test
npm test
```

### 2. Publishing to npm
```bash
# Login to npm
npm login

# Publish package
npm publish --access public
```

### 3. Publishing to GitHub Packages
```bash
# Configure GitHub registry
npm config set @YOUR_GITHUB_USERNAME:registry https://npm.pkg.github.com

# Login to GitHub Packages
npm login --registry=https://npm.pkg.github.com

# Publish
npm publish
```

## Best Practices and Tips

### 1. Response Format
Always use consistent response format:
```javascript
// Success response
{
  success: true,
  timestamp: new Date().toISOString(),
  data: {
    // Your response data
  }
}

// Error response
{
  success: false,
  error: "Error message",
  timestamp: new Date().toISOString()
}
```

### 2. Error Handling
```javascript
app.use((err, req, res, next) => {
  logger.error('Error:', err);
  res.status(500).json({
    success: false,
    error: err.message,
    timestamp: new Date().toISOString()
  });
});
```

### 3. Security Best Practices
- Use environment variables for sensitive data
- Implement rate limiting
- Validate input
- Use CORS properly
- Keep dependencies updated

### 4. Documentation
- Clear installation instructions
- API documentation
- Examples
- Troubleshooting guide

## Integration with Cursor

### 1. Installation in Cursor
1. Install your MCP globally:
```bash
npm install -g @your-org/my-mcp-server
```

2. Add to Cursor's config directory:
- Windows: `%APPDATA%\Cursor\mcp.json`
- macOS: `~/Library/Application Support/Cursor/mcp.json`
- Linux: `~/.config/Cursor/mcp.json`

3. Restart Cursor

### 2. Verification
1. Open Cursor
2. Check MCP list in settings
3. Your MCP should show as "Connected"

## Common Issues and Solutions

1. **"Client Closed" Error**
   - Check if server is running
   - Verify port configuration
   - Check health endpoint format

2. **Installation Issues**
   - Verify global npm permissions
   - Check package.json configuration
   - Verify mcp.json location

3. **Connection Issues**
   - Check port availability
   - Verify CORS settings
   - Check network connectivity

## Resources

- [Cursor Documentation](https://cursor.sh/docs)
- [npm Documentation](https://docs.npmjs.com)
- [Express.js Guide](https://expressjs.com/guide)
- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)

## Need Help?

- Create an issue on GitHub
- Check the FAQ in README
- Contact the maintainers

Remember: A good MCP server is reliable, well-documented, and follows standard practices for response formats and error handling. 