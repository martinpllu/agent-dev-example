# Developer Guide

This is Developer Guide designed for AI agents working in this codebase. It contains:

- **KEY AGENT RESPONSIBILITIES**: Important rules to follow when developing the application.
- **ARCHITECTURE**: application overview, components, layers, frameworks and libraries, key standards, code layout. 
- **DEVELOPMENT CAPABILITIES**: how to work with the application in a local test environment: interacting with servers, reading logs, running scripts, interactively accessing test data and debugging the app. 

The Developer Guide serves as an interface between the agent and the development environment. It aims to give the agent the knowledge, 'hands' and 'eyes' it needs to develop your application efficiently, autonomously and with a high degree of success. It also highlights any gaps in your architecture or missing development capabilities, helping the team identify opportunities for improvement. 

# KEY AGENT RESPONSIBILITIES

## Safety

- It is assumed that the application can run in a 'personal development environment', e.g. the user's local machine, or a namespaced cloud environment. 
- You must ensure that all actions you take will only affect the local development environment in a safe way. If in doubt, ask the user.
- Never take any action that affects a shared development environment without an explicit request from the user.
- Never access a production environment in any way.
- Ensure that any commands (e.g. aws cli) you run are configured to use the personal development environment.

## Continuous improvement

- You must help continuously improve this documentation. Correct any gaps or inaccuracies that you find. 
- Ensure that the document remains succinct. It will be read by all agents, so every token counts.

## Verification of changes

- You must verify every change that you make. 
- Don't guess or assume that a change is working unless it is confirmed by: 
  - An automated test, which must be written for new features, bugfixes
  - Or, for smaller changes or debugging: interactive verification using a Development Capability like log/database inspection or interactive browser debugging

## Use already-running servers

- For development environments that support hot reload of changes, use the existing development server/app instance if one is already running, instead of starting a new one or restarting. Use the "Application status" capability to determine if one is running before using "Run application".

# ARCHITECTURE

## Application Overview

### Purpose
A demo application for experimenting with AI agent development workflows, featuring a Kanban board for tracking work items across different stages (backlog, in-progress, done) with role-based access control.

### Type
Full-stack web application with SSR/SPA capabilities via React Router v7

### Domain
Demo application for AI agent development experimentation

### Key Features
- Kanban board with task management across backlog, in-progress, and done columns
- Role-based access control (Guest, User, Admin)
- Passwordless email-based authentication with verification codes
- Task creation, editing, and status management
- Admin user management panel
- Comprehensive E2E and unit testing infrastructure
- Interactive debugging tools
- See `README.md` for complete setup and usage

## Architecture Shape

### Pattern
Modular monolith with clear separation of concerns

### Deployment
Serverless on AWS using SST v3, Aurora Serverless v2 PostgreSQL with auto-pause

### Communication
Server-side rendering with React Router v7 loaders/actions, REST-style interactions

### Scalability
Serverless auto-scaling with Aurora Serverless v2 that scales to zero (0 ACU) when inactive (10 min dev, 60 min prod)

## Tech Stack

### Primary Language(s)
TypeScript

### Runtime/Platform
Node.js 18+, AWS Lambda for serverless functions, Vite for development

### Package Manager
pnpm

### Key Dependencies
- React 19
- React Router v7
- SST v3
- Drizzle ORM
- OpenAuth
- Hono (for auth Lambda)
- shadcn/ui components with Radix UI
- Tailwind CSS v4
- Playwright for E2E testing
- Vitest for unit testing
- Zod for validation

## Frontend

### Framework
React Router v7

### Rendering
SSR/SPA hybrid (React Router v7 with Vite)

### Routing
File-based routing with React Router v7

### Styling
Tailwind CSS v4 with shadcn/ui component library and Radix UI primitives

### State Management
React Router loaders/actions for server state, React hooks for local state

### Build Tool
Vite with React Router

### UI Components
shadcn/ui design system with Radix UI primitives

### API Communication
React Router actions and loaders for data fetching

## Backend

### Framework
React Router v7 with server-side capabilities, Hono for auth Lambda

### API Style
React Router actions/loaders pattern, REST-style endpoints

### Middleware
Custom auth middleware in app/auth/auth-middleware.ts

### Background Jobs
[Not implemented yet]

### File Handling
[Not implemented yet]

## Authentication & Authorization

### Authentication Method
Passwordless email-based verification codes (6-digit, 10-minute expiry)

### Auth Provider
OpenAuth with custom Lambda implementation

### Session Management
Base64 encoded JSON tokens (not JWT) with 24-hour expiry, stored in HttpOnly cookies

### Multi-Factor Auth
[Not implemented yet]

### Password Policy
Passwordless authentication via 6-digit email verification codes

### Authorization Model
Role-based access control (RBAC)

### Roles & Permissions
- Guest: Read-only access to tasks
- User: Create/edit own tasks, update task status
- Admin: Full task management, user management, delete any task

### API Security
Token-based authentication with validation middleware

### Token Management
24-hour access tokens (Base64 JSON), no refresh token strategy, automatic cleanup on expiry

### SSO Integration
[Not implemented yet]

## Data Layer

### Primary Database
Aurora Serverless v2 PostgreSQL with Data API

### ORM/ODM
Drizzle ORM

### Migrations
Drizzle Kit for migrations (drizzle/ directory)

### Caching
[Not implemented yet]

### Search
Database queries only

### Data Validation
Zod for schema validation

## Infrastructure & Deployment

### Hosting
AWS (eu-west-1 region) via SST v3

### Containerization
[Not applicable - serverless app]

### IaC
SST v3 (Infrastructure as Code) with Pulumi backend

### CI/CD
[Not implemented yet]

### Monitoring
[Not implemented yet]

### Logging
Console logging to .sst/log/ in development, CloudWatch in production

### Secrets Management
AWS Secrets Manager for Aurora database credentials (auto-generated)

## Development Patterns

### Code Style
ESLint with TypeScript ESLint plugin, Remix ESLint config

### Git Workflow
Feature branches with PR reviews

### Testing Strategy
Unit tests with Vitest (jsdom), E2E tests with Playwright (single worker), Page Object Model pattern, sequential execution to avoid auth conflicts

### Error Handling
Centralized error handler in app/utils/error-handler.ts

### Security Patterns
Input validation with Zod, parameterized queries via Drizzle ORM, HttpOnly cookies, SameSite protection

### Documentation
TypeScript types, comprehensive test documentation in docs/, inline JSDoc comments

### Configuration
Environment-based via SST stages, stage isolation with separate databases

## Codebase Structure

```
app/                        # React Router application
├── auth/                   # Authentication middleware and server
├── components/            # UI components
│   ├── ui/               # shadcn/ui base components
│   └── tasks/            # Task-specific components
├── db/                    # Database layer
│   ├── client.ts         # RDS Data API client
│   ├── repositories/     # Data access layer
│   └── schema.ts         # Drizzle schema definitions
├── model/                 # Domain models
├── routes/                # Page routes (file-based routing)
├── types/                 # TypeScript type definitions
├── utils/                 # Utility functions
├── root.tsx              # Root layout component
└── routes.ts             # Route configuration

auth/                      # Auth Lambda function
├── index.ts              # Lambda handler with Hono
├── file-storage.ts       # Dev mode storage
└── subjects.ts           # Auth subject types

scripts/                   # Development utilities
├── db.ts                 # Database CLI wrapper
├── sql-query.ts          # Direct SQL execution
├── migrate.ts            # Migration runner
├── create-user.ts        # User creation utility
├── debug-browser.ts      # Interactive Playwright debugger
└── get-dev-server-url.ts # Port detection utility

tests/                     # Test files
├── pages/                # Page Object Models
├── *.e2e.test.ts        # E2E test files
└── test-utils.ts        # Test utilities

drizzle/                  # Database migrations
├── *.sql                # Migration files
└── meta/                # Migration metadata

docs/                     # Documentation
├── test-*.md            # Testing documentation
└── typescript-guidelines.md

Configuration Files:
- sst.config.ts          # SST infrastructure config
- drizzle.config.ts      # Drizzle ORM config
- playwright.config.ts   # E2E test config
- vitest.config.ts       # Unit test config
- tailwind.config.ts     # Tailwind CSS config
- react-router.config.ts # React Router config
- vite.config.ts         # Vite bundler config
```

# DEVELOPMENT CAPABILITIES

## Setup & Initialization

### Install dependencies
`pnpm install`

### Start application
`npx sst dev`
- Runs with hot reload/watch mode for both frontend and backend
- Infrastructure changes are automatically deployed
- Web server logs are captured to `.sst/log/web.log`

### Stop application
`Ctrl+C` in the terminal running `npx sst dev`

### Application status
Run `find .sst -name "*.server" -type f` to check if a .server file exists in `.sst/`. If it exists, the app is running locally.

### Application deployment info
- Backend endpoints: Check `.sst/outputs.json`
- Web app URL: Check `.sst/log/web.log` for the local server URL

### Environment setup
All configuration is managed through SST. Database credentials are automatically configured.

### One-command setup
```bash
pnpm install && npx sst deploy --stage dev && pnpm migrate --stage dev && pnpm create-user --stage dev --email admin@test.com --admin
```

### Environment validation
SST will validate all required AWS configurations on startup

### Quick reset
`pnpm delete-all-data --stage <stage-name>` to clear all data

## Build & Development

### Build project
`pnpm build`

### Clean build
`rm -rf build .sst dist`

### Build for different environments
Deploy to different stages: `npx sst deploy --stage <stage-name>`

### Watch mode
Automatic via `npx sst dev` (includes hot reload)

### Bundle analysis
[Not implemented yet]

### Source maps
Generated automatically in development

## Code Quality & Validation

### Run linter
`pnpm lint`

### Run type checker
`pnpm typecheck`

### Format code
[Not implemented yet]

### Fix linting issues
`pnpm lint:fix`

### Pre-commit validation
[Not implemented yet]

### Security scan
[Not implemented yet]

### Check code style
`pnpm lint`

### Detect unused code
[Not implemented yet]

## Testing

### Run all tests
`pnpm test:all`

### Run specific test
`pnpm test path/to/test.ts` or `pnpm test:e2e tests/specific.e2e.test.ts`

### Run unit tests
`pnpm test`

### Test in watch mode
`pnpm test --watch`

### Test coverage report
`pnpm test --coverage`

### Run integration tests
[Not implemented yet]

### Run E2E tests
`pnpm test:e2e`

### Debug specific test
`pnpm test:e2e:ui` for Playwright UI mode

### Generate test
[Not implemented yet]

### Create test user
```bash
# Create test admin user
pnpm create-user --stage <stage-name> --email test@example.com --admin

# Create test regular user
pnpm create-user --stage <stage-name> --email user@example.com
```

### Log in test user
In development mode, verification codes are logged to the console:
1. Start the app with `npx sst dev`
2. Navigate to login page and enter email
3. Check SST logs > AuthFunction for: `Login code for test@example.com: XXXXXX`
4. Enter the 6-digit code to complete login

## Database Operations

### Run migrations
`pnpm migrate --stage <stage-name>`

### Connect to database
`pnpm db studio --stage <stage-name>` opens Drizzle Studio browser

### Reset database
`pnpm delete-all-data --stage <stage-name>`

### Seed database
Create users with `pnpm create-user --stage <stage-name> --email user@test.com [--admin]`

### Run arbitrary query
`pnpm sql "SELECT * FROM users;" --stage <stage-name>`

### View schema
`pnpm db studio --stage <stage-name>` or check `app/db/schema.ts`

### Rollback migration
[Not implemented yet]

### Backup database
[Not implemented yet]

### Restore database
[Not implemented yet]

### Query performance analysis
Use AWS RDS console for query insights

## Debugging & Inspection

### View logs
- SST/infrastructure logs: `.sst/log/pulumi.log`
- Lambda logs: `.sst/lambda/<FunctionName>/<id>.log`
- Web app logs: `.sst/log/web.log`
- Auth function logs: Check SST console Functions tab

### Tail logs
`tail -f .sst/log/web.log` or use SST console

### Interactively use the app
`pnpm tsx scripts/debug-browser.ts` - Interactive Playwright REPL with helper functions:
- `debug.goto(url)` - Navigate to URL
- `debug.loginAsAdmin()` - Login as admin
- `debug.inspect()` - Inspect page state
- `debug.screenshot()` - Take screenshot
- Full Playwright API available via `debug.page`

### Search logs
`grep "pattern" .sst/log/*.log`

### Filter logs by level
Use grep: `grep -E "ERROR|WARN" .sst/log/*.log`

### Connect debugger
Use VS Code debugger with Node.js configuration

### Inspect running process
[Not implemented yet]

### Profile performance
[Not implemented yet]

### Trace requests
Check Lambda invocation logs in `.sst/lambda/`

## Monitoring

### Health check
Check application status as described above

### View metrics
[Not implemented yet]

### Check service status
SST console shows Lambda function status

### Monitor dashboard
[Not implemented yet]

### View error rates
[Not implemented yet]

## Deployment & Release

### Build for production
`npx sst deploy --stage production`

### Run production mode locally
[Not implemented yet]

### View deployment status
SST console shows deployment progress

### Rollback deployment
[Not implemented yet]

### Run smoke tests
`pnpm test:quick` for basic test suite

### Generate release notes
[Not implemented yet]

## Further Documentation

### Project overview
See `README.md`

### Available commands
Run `pnpm run` to see all available scripts

### Environment variables
Managed via SST, see `sst.config.ts`

### API documentation
[Not implemented yet]

### Generate documentation
[Not implemented yet]

### View documentation locally
[Not implemented yet]

### Architecture overview
See this document and `README.md`

### Troubleshooting guide
[Not implemented yet]

## Utilities

### Install dependencies
`pnpm add <package-name>`

### Update dependencies
`pnpm update`

### Check outdated dependencies
`pnpm outdated`

### Dependency security audit
`pnpm audit`

### Clean cache
`pnpm store prune`

### Generate component/module
[Not implemented yet]

### Find unused dependencies
[Not implemented yet]

### Validate dependencies
`pnpm install --frozen-lockfile`

## API/Service Specific

### List endpoints
Check `app/routes.ts` for route definitions

### Test endpoint
Use `scripts/debug-browser.ts` or curl

### View API documentation
[Not implemented yet]

### Mock external services
[Not implemented yet]

### Validate request/response
TypeScript types in `app/types/`

### Load test endpoint
[Not implemented yet]

## Frontend Specific

### Build frontend
`pnpm build`

### Bundle size analysis
[Not implemented yet]

### Component explorer
[Not implemented yet]

### Run accessibility audit
[Not implemented yet]

### Lighthouse audit
[Not implemented yet]

### Browser testing
E2E tests run in Chromium via Playwright

## Configuration & Environment

### List configuration
Check `.sst/outputs.json` after running `npx sst dev`

### Switch environment
Use `--stage` parameter: `npx sst dev --stage <stage-name>`

### Validate configuration
SST validates on startup

### Diff configurations
[Not implemented yet]

### Encrypt secrets
Handled automatically by AWS Secrets Manager

### Export configuration
View `.sst/outputs.json`

## Infrastructure as code

SST v3. Note that there are significant differences between SSTv2 and v3. Please consult online docs at https://sst.dev/docs if you're not sure.

### Start application

`npx sst dev`

- In sst dev, all changes to backend/frontend code and infrastructure (in sst.config.ts) will be automatically reloaded. 
- Code reloading takes a few seconds. 
- Infrastructure changes can take longer so you might want to watch the logs.

### Application status

Run `find .sst -name "*.server" -type f` to check if a .server file exist in `.sst/`. If it exists, the app is running locally. If not, the app is not running. 

### View logs

Logs are written to `.sst/log`. The exact logs will depend on the type of application and infrastructure used, but they may include:

- `.sst/log/pulumi.log` - infrastructure logs
- `.sst/lambda/<MyFunction>/<id>.log` - lambda invocation logs
- `.sst/log/web.log` - web app logs (e.g. nextjs or React Router app). You can get the URL of the web app in this log.

### Application deployment info

- `.sst/outputs.json` contains infrastructure outputs, e.g. backend endpoints.

---
Built with https://github.com/martinpllu/agent-dev-guide v1.0.0