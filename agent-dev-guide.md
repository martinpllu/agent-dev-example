# Developer Guide

This is a Developer Guide designed for AI agents working in this codebase. It contains:

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
A team task management application that enables teams to collaborate on tasks using a kanban board interface. Built as a demo app for experimenting with AI agent development workflows.

### Type
Full-stack web application with server-side rendering

### Domain
Team collaboration and project management

### Key Features
- Kanban board with Backlog, In Progress, and Done columns
- Create, edit, and move tasks between columns
- Role-based access control (Guest, User, Admin)
- User authentication via email verification codes
- Task assignment and ownership tracking
- See `docs/testing-strategy.md` for testing approach

## Architecture Shape

### Pattern
Modular monolith with React Router SSR

### Deployment
Serverless cloud-native on AWS

### Communication
REST API with server-side rendering and form actions

### Scalability
Stateless Lambda functions with auto-scaling Aurora database

## Tech Stack

### Primary Language(s)
TypeScript (ES2022 target)

### Runtime/Platform
Node.js 22 on AWS Lambda

### Package Manager
pnpm

### Key Dependencies
- React 19
- React Router 7
- Drizzle ORM
- SST 3
- Hono
- Tailwind CSS 4
- Zod

## Frontend

### Framework
React 19 with React Router 7

### Rendering
SSR (Server-Side Rendering) with hydration

### Routing
File-based routing with React Router v7 (see `app/routes/`)

### Styling
Tailwind CSS v4 with shadcn/ui components

### State Management
Component state with React hooks (see `app/components/kanban-board.tsx` for main state management)

### Build Tool
Vite (via React Router)

### UI Components
shadcn/ui component library (see `app/components/ui/`)

### API Communication
React Router loaders and actions for data fetching/mutations

## Backend

### Framework
React Router with Hono for auth endpoints (see `auth/index.ts`)

### API Style
REST with form actions and loaders

### Middleware
Authentication middleware in React Router (see `app/middleware.ts`)

### Background Jobs
[Not implemented]

### File Handling
Local file storage for dev auth codes (see `auth/index.ts:57-77`)

## Authentication & Authorization

### Authentication Method
Custom OpenAuth with email verification codes (see `auth/index.ts:sendCodeEmail()`)

### Auth Provider
Self-managed with SST OpenAuth

### Session Management
DynamoDB for production, file storage for development (see `auth/utils.ts:getStorage()`)

### Multi-Factor Auth
[Not implemented - uses email verification codes only]

### Password Policy
[Not applicable - passwordless authentication via email codes]

### Authorization Model
Role-based access control (RBAC)

### Roles & Permissions
- Guest: Can view public content
- User: Can manage own tasks
- Admin: Can manage all tasks and users (see `app/repository/user.repository.ts:getUserById()`)

### API Security
Session-based authentication with CSRF protection

### Token Management
Session cookies with server-side validation

### SSO Integration
[Not implemented]

## Data Layer

### Primary Database
Aurora Serverless v2 PostgreSQL 16

### ORM/ODM
Drizzle ORM with AWS Data API driver (see `app/db/schema.ts`)

### Migrations
Drizzle Kit with sequential SQL files in `drizzle/` directory

### Caching
[Not implemented]

### Search
Database queries with Drizzle ORM

### Data Validation
Zod schemas for runtime validation (see `app/db/schema.ts`)

## Infrastructure & Deployment

### Hosting
AWS (Lambda, Aurora, DynamoDB, VPC)

### Containerization
[Not applicable - serverless app]

### Infrastructure as Code
SST v3 (see `sst.config.ts`)

### CI/CD
[Not implemented yet]

### Monitoring
[Not implemented yet]

### Logging
CloudWatch Logs via Lambda

### Secrets Management
SST Secrets for database credentials

## Development Patterns

### Code Style
Biome for linting and formatting (see `biome.json`)

### Git Workflow
Feature branches with main branch protection

### Testing Strategy
Unit tests with Vitest, E2E with Playwright (see `docs/testing-strategy.md`)

### Error Handling
Try-catch blocks with user-friendly error messages

### Security Patterns
Input validation with Zod, parameterized queries via Drizzle

### Documentation
Inline JSDoc comments and markdown documentation

### Configuration
Environment-based via SST stage configuration

## Codebase Structure

```
app/
├── components/     # React UI components
│   ├── ui/        # shadcn/ui base components
│   └── *.tsx      # Application components
├── db/            # Database configuration
│   ├── client.ts  # Drizzle client setup
│   └── schema.ts  # Database schema definitions
├── repository/    # Data access layer
│   ├── task.repository.ts   # Task CRUD operations
│   └── user.repository.ts   # User CRUD operations
├── routes/        # React Router routes
│   ├── _index.tsx           # Home page with kanban board
│   ├── auth.callback.tsx    # Auth callback handler
│   ├── users.$id.tsx        # User profile page
│   └── users.tsx            # Users list (admin only)
├── lib/           # Utility functions
├── middleware.ts  # React Router middleware
├── root.tsx       # App root with layout
└── error.tsx      # Error boundary
auth/
├── index.ts       # Auth Lambda function
├── client.ts      # OpenAuth client
└── utils.ts       # Auth utilities
scripts/
├── migrate.ts     # Database migration script
├── sql.ts         # SQL query runner
└── create-user.ts # User creation utility
tests/
├── e2e/           # Playwright E2E tests
├── unit/          # Vitest unit tests
└── utils/         # Test utilities
drizzle/           # Database migrations
docs/              # Documentation
```

**Entry Points**: 
- `app/root.tsx` - Main React app entry
- `auth/index.ts` - Authentication Lambda

**Configuration Files**:
- `sst.config.ts` - Infrastructure configuration
- `package.json` - Dependencies and scripts
- `biome.json` - Code formatting rules
- `tsconfig.json` - TypeScript configuration
- `vite.config.ts` - Build configuration

**Generated Files**: 
- `.sst/` - SST build artifacts
- `dist/` - Production build
- `node_modules/` - Dependencies

**Scripts**: See `scripts/` directory for database utilities

**Documentation**: See `docs/` for testing strategy and development guides

# DEVELOPMENT CAPABILITIES

**⚠️ ENVIRONMENT SAFETY NOTE**: Commands in this section use `<YOUR_ENV>` as a placeholder for environment names. Replace this with your personal development environment name (e.g., your username or a dedicated dev environment). NEVER use shared environments like 'dev', 'staging', or 'production' without explicit permission.

## Setup & Initialization

### Using the Personal Development Environment

As the application is started with `npx sst dev` it's assumed that the default stage is appropriate - this will either be the username on the local machine, the stage in .sst/stage or the stage in the SST_STAGE env variable. See https://sst.dev/docs/reference/cli/#stage

### Install dependencies
`pnpm install`

### Start application
`npx sst dev`
- Runs in hot reload/watch mode
- Both infrastructure and code changes are automatically reloaded
- Code reloading takes a few seconds
- Infrastructure changes can take longer

### Stop application
`Ctrl+C` in the terminal running SST dev

### Application status
Run `find .sst -name "*.server" -type f` to check if a .server file exists in `.sst/`. If it exists, the app is running locally. If not, the app is not running.

### Application deployment info
`.sst/outputs.json` contains infrastructure outputs including backend endpoints and frontend URL

### Environment setup
See `.env.example` for required environment variables. SST handles most configuration automatically.

### One-command setup
```bash
pnpm install && npx sst dev
```

### Environment validation
SST validates required resources on startup

### Quick reset
`pnpm sql "DROP SCHEMA public CASCADE; CREATE SCHEMA public;" --stage <YOUR_ENV>` to reset database

## Build & Development

### Build project
`pnpm build`

### Clean build
`rm -rf dist .sst && pnpm build`

### Build for different environments
`npx sst deploy --stage <YOUR_ENV>`

### Watch mode
Included in `npx sst dev` - automatic rebuild on file changes

### Bundle analysis
[Not implemented]

### Source maps
Generated automatically in development mode

## Code Quality & Validation

### Run linter
`pnpm lint`

### Run type checker
`pnpm typecheck`

### Format code
`pnpm format`

### Fix linting issues
`pnpm lint:fix`

### Pre-commit validation
[Not implemented - could be added with husky]

### Security scan
`pnpm audit`

### Check code style
`pnpm lint`

### Detect unused code
[Not implemented]

## Testing

### Run all tests
`pnpm test:all`

### Run specific test
`pnpm test -- path/to/test.spec.ts`

### Run unit tests
`pnpm test`

### Test in watch mode
`pnpm test:watch`

### Test coverage report
`pnpm test:coverage`

### Run integration tests
[Included in unit tests]

### Run E2E tests
`pnpm test:e2e`

### Debug specific test
`pnpm test:e2e:ui` for Playwright UI mode

### Generate test
[Not implemented]

### Create test user
`pnpm create-user --stage <YOUR_ENV> --email test@example.com [--admin]`

### Log in test user
Development mode logs OTP codes to console. Check `.sst/log/auth.log` for the 6-digit code after requesting login.

## Database Operations

### Run migrations
`pnpm migrate --stage <YOUR_ENV>`

### Connect to database
`pnpm db studio --stage <YOUR_ENV>` for Drizzle Studio UI

### Reset database
`pnpm sql "DROP SCHEMA public CASCADE; CREATE SCHEMA public;" --stage <YOUR_ENV>`

### Seed database
[Not implemented - use create-user script for test users]

### Run arbitrary query
`pnpm sql "SELECT * FROM users" --stage <YOUR_ENV>`

### View schema
`pnpm db studio --stage <YOUR_ENV>` or check `app/db/schema.ts`

### Rollback migration
[Not implemented - forward-only migrations]

### Backup database
[Use AWS RDS snapshots]

### Restore database
[Use AWS RDS restore from snapshot]

### Query performance analysis
Use `EXPLAIN` prefix in SQL queries via `pnpm sql`

## Debugging & Inspection

### View logs
Logs are written to `.sst/log/`:
- `.sst/log/pulumi.log` - infrastructure logs
- `.sst/log/auth.log` - authentication function logs  
- `.sst/log/web.log` - web app logs (requires redirect in package.json dev script)

### Tail logs
`tail -f .sst/log/*.log`

### Interactively use the app
Use Playwright E2E tests with `pnpm test:e2e:ui` for interactive debugging

### Search logs
`grep "pattern" .sst/log/*.log`

### Filter logs by level
`grep -E "ERROR|WARN" .sst/log/*.log`

### Connect debugger
Use VS Code debugger with Node.js configuration

### Inspect running process
[Use AWS Lambda monitoring]

### Profile performance
[Use Chrome DevTools for frontend profiling]

### Trace requests
Check `.sst/log/web.log` for request logs

## Monitoring

### Health check
Check application URL returns 200 status

### View metrics
[Use AWS CloudWatch]

### Check service status
`curl <app-url>` to verify app is responding

### Monitor dashboard
[Use AWS CloudWatch dashboard]

### View error rates
[Check CloudWatch Logs]

## Deployment & Release

### Build for production
`npx sst deploy --stage production`

### Run production mode locally
`NODE_ENV=production pnpm build && pnpm preview`

### View deployment status
Check AWS CloudFormation console or SST outputs

### Rollback deployment
[Use AWS CloudFormation rollback]

### Run smoke tests
Run E2E tests against deployed URL

### Generate release notes
[Not implemented]

## Further Documentation

### Project overview
See README.md and `docs/` directory

### Available commands
Run `pnpm run` to list all scripts

### Environment variables
See `.env.example` for all options

### API documentation
[Not implemented - see route files for endpoints]

### Generate documentation
[Not implemented]

### View documentation locally
[Not implemented]

### Architecture overview
See this Developer Guide and `sst.config.ts`

### Troubleshooting guide
See `docs/testing-strategy.md` for test debugging

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
[Not implemented]

### Find unused dependencies
[Not implemented]

### Validate dependencies
`pnpm install --frozen-lockfile`

## API/Service Specific

### List endpoints
Check `app/routes/` directory for all routes

### Test endpoint
Use `curl` or Playwright tests

### View API documentation
[Not implemented]

### Mock external services
[Not applicable - no external service dependencies]

### Validate request/response
Zod schemas validate all inputs (see `app/db/schema.ts`)

### Load test endpoint
[Not implemented]

## Frontend Specific

### Build frontend
`pnpm build`

### Bundle size analysis
[Not implemented]

### Component explorer
[Not implemented - could add Storybook]

### Run accessibility audit
[Not implemented]

### Lighthouse audit
Use Chrome DevTools Lighthouse

### Browser testing
E2E tests run in Chrome via Playwright

## Configuration & Environment

### List configuration
Check `.sst/outputs.json` for current deployment

### Switch environment
`export SST_STAGE=<YOUR_ENV>` or use `--stage` flag

### Validate configuration
SST validates on deployment

### Diff configurations
Compare `.sst/outputs.json` between stages

### Encrypt secrets
Use `npx sst secret set <name> <value> --stage <YOUR_ENV>`

### Export configuration
`cat .sst/outputs.json`

---
Built with https://github.com/martinpllu/agent-dev-guide v1.0.0