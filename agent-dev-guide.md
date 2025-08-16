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
Team Task Tracker - A demo application for experimenting with AI agent development workflows. Provides a kanban board for teams to manage tasks with role-based access control.

### Type
Web application with full-stack React Router v7 SSR

### Domain
Task management, team collaboration, internal tool

### Key Features
- Kanban board with backlog, in-progress, and done columns
- Authentication with custom email code flow
- Role-based access control (user, admin)
- Real-time task management
- Admin user management panel
- See `README.md` for more

## Architecture Shape

### Pattern
Modular monolith with SSR frontend and Aurora Data API backend

### Deployment
Serverless on AWS using SST v3, auto-scaling Aurora Serverless v2

### Communication
HTTP/HTTPS with React Router loaders/actions, Aurora Data API for database

### Scalability
Stateless application tier, auto-scaling database (0-16 ACU production, 0-4 ACU dev)

## Tech Stack

### Primary Language(s)
TypeScript

### Runtime/Platform
Node.js 18+, AWS Lambda

### Package Manager
pnpm

### Key Dependencies
- React Router v7
- Drizzle ORM
- SST v3
- OpenAuth
- shadcn/ui
- Tailwind CSS

## Frontend

### Framework
React Router v7

### Rendering
SSR (Server-Side Rendering) by default

### Routing
File-based routing with `app/routes.ts` configuration

### Styling
Tailwind CSS with shadcn/ui components

### State Management
React Router loaders/actions with server state

### Build Tool
Vite

### UI Components
shadcn/ui component library - see `app/components/ui/`

### API Communication
React Router loaders/actions pattern

## Backend

### Framework
React Router v7 loaders/actions with Hono for auth endpoints

### API Style
Form actions and data loaders pattern

### Middleware
Custom auth middleware - see `app/auth/auth-middleware.ts`

### Background Jobs
[Not implemented yet]

### File Handling
[Not implemented yet]

## Authentication & Authorization

### Authentication Method
Custom email verification code flow with OpenAuth

### Auth Provider
Self-managed with OpenAuth library

### Session Management
Base64 encoded tokens stored in cookies, 24-hour expiry

### Multi-Factor Auth
[Not implemented yet]

### Password Policy
[Not applicable - passwordless email code authentication]

### Authorization Model
RBAC with isAdmin and isValidated flags

### Roles & Permissions
- Guest: View tasks (read-only)
- User: Create, edit own tasks, update status
- Admin: Full task management, user management

### API Security
Token-based authentication in cookies

### Token Management
24-hour access tokens, stored in httpOnly cookies

### SSO Integration
[Not implemented yet]

## Data Layer

### Primary Database
Aurora Serverless v2 PostgreSQL 16.4

### ORM/ODM
Drizzle ORM with Aurora Data API driver

### Migrations
Drizzle Kit - see `drizzle/` directory

### Caching
[Not implemented yet]

### Search
Database queries via Drizzle ORM

### Data Validation
Zod and Valibot for schema validation

## Infrastructure & Deployment

### Hosting
AWS via SST v3

### Containerization
[Not applicable - serverless app]

### Infrastructure as Code
SST v3. Note that there are significant differences between SSTv2 and v3. Please consult online docs at https://sst.dev/docs if you're not sure.

### CI/CD
[Not implemented yet]

### Monitoring
[Not implemented yet]

### Logging
Console logging, SST dev logs in `.sst/log/`

### Secrets Management
AWS Secrets Manager for database credentials

## Development Patterns

### Code Style
ESLint configuration - see `.eslintrc`

### Git Workflow
Main branch development

### Testing Strategy
Unit tests with Vitest, E2E tests with Playwright - see `docs/test-overview.md`

### Error Handling
Centralized error handler - see `app/utils/error-handler.ts`

### Security Patterns
Input validation with Zod, parameterized queries via Drizzle ORM

### Documentation
Inline JSDoc comments, comprehensive docs in `docs/` directory

### Configuration
Environment-based via SST, database config in `app/db/client.ts`

## Codebase Structure

```
/Users/martin/dev/agent-dev-example/
├── app/                    # React Router frontend application
│   ├── routes/            # File-based routing pages
│   │   ├── home.tsx      # Landing page
│   │   ├── tasks.tsx     # Main task board
│   │   ├── user.tsx      # User profile
│   │   ├── auth.*.tsx    # Authentication flows
│   │   └── admin.*.tsx   # Admin pages
│   ├── components/        # UI components
│   │   ├── ui/           # shadcn/ui base components  
│   │   ├── tasks/        # Task-specific components
│   │   └── layout.tsx    # Main layout wrapper
│   ├── auth/             # Authentication logic
│   │   ├── auth-middleware.ts  # Role-based middleware
│   │   ├── auth-server.ts      # Auth utilities
│   │   └── auth-actions.ts     # Auth form actions
│   ├── db/               # Database layer
│   │   ├── schema.ts     # Drizzle schema definitions
│   │   ├── client.ts     # Aurora Data API client
│   │   └── repositories/ # Data access patterns
│   ├── model/            # Business logic models
│   ├── types/            # TypeScript type definitions
│   └── utils/            # Utility functions
├── auth/                  # Auth backend function
│   ├── index.ts          # Hono auth endpoints
│   ├── subjects.ts       # OpenAuth subjects
│   └── file-storage.ts   # Dev mode storage
├── scripts/              # Database and dev utilities
│   ├── create-user.ts    # User creation script
│   ├── migrate.ts        # Migration runner
│   ├── sql-query.ts      # Direct SQL execution
│   └── debug-browser.ts  # Interactive debugging
├── tests/                # Test suites
│   ├── pages/           # Page Object Models
│   ├── *.e2e.test.ts    # E2E test files
│   └── *-utils.ts       # Test utilities
├── drizzle/              # Database migrations
├── docs/                 # Documentation
├── sst.config.ts         # SST infrastructure config
├── package.json          # Dependencies and scripts
├── drizzle.config.ts     # Drizzle ORM config
├── playwright.config.ts  # E2E test config
└── vitest.config.ts      # Unit test config
```

**Entry Points**: 
- `app/root.tsx` - Main React application root
- `auth/index.ts` - Auth function handler
- `sst.config.ts` - Infrastructure entry point

**Configuration Files**:
- `sst.config.ts` - SST infrastructure as code
- `drizzle.config.ts` - Database ORM configuration  
- `react-router.config.ts` - React Router settings
- `tailwind.config.ts` - Styling configuration
- `playwright.config.ts` - E2E test configuration
- `vitest.config.ts` - Unit test configuration

**Generated Files**: 
- `.sst/` - SST build artifacts (gitignored)
- `build/` - React Router build output (gitignored)
- `node_modules/` - Dependencies (gitignored)
- `.sst/outputs.json` - Infrastructure outputs

**Scripts**: See `scripts/README.md` for utilities

**Documentation**: See `docs/` for detailed guides

# DEVELOPMENT CAPABILITIES

## Setup & Initialization

### Using the Personal Development Environment

- As the application is started with `npx sst dev` it's assumed that the default stage is appropriate - this will either be the username on the local machine, the stage in .sst/stage or the stage in the SST_STAGE env variable. See https://sst.dev/docs/reference/cli/#stage

### Install dependencies
`pnpm install`

### Start application
`npx sst dev`

- In sst dev, all changes to backend/frontend code and infrastructure (in sst.config.ts) will be automatically reloaded. 
- Code reloading takes a few seconds. 
- Infrastructure changes can take longer so you might want to watch the logs.

### Stop application
Press Ctrl+C in the terminal running `npx sst dev`

### Application status
Run `find .sst -name "*.server" -type f` to check if a .server file exist in `.sst/`. If it exists, the app is running locally. If not, the app is not running.

### Application deployment info
- `.sst/outputs.json` contains infrastructure outputs, e.g. backend endpoints.

### Environment setup
Environment variables are managed by SST and injected at runtime. See `sst.config.ts` for configuration.

### One-command setup
```bash
pnpm install && npx sst deploy --stage dev && pnpm migrate --stage dev && pnpm create-user --stage dev --email test@example.com --admin
```

### Environment validation
Check `.sst/outputs.json` exists and contains database configuration

### Quick reset
`pnpm delete-all-data --stage dev && pnpm migrate --stage dev`

## Build & Development

### Build project
`pnpm build`

### Clean build
`rm -rf build .sst && pnpm build`

### Build for different environments
Handled via SST stages: `npx sst deploy --stage production`

### Watch mode
Automatic with `npx sst dev` - hot reload enabled

### Bundle analysis
[Not implemented yet - could use vite-bundle-visualizer]

### Source maps
Generated automatically in development mode

## Code Quality & Validation

### Run linter
`pnpm lint`

### Run type checker
`pnpm typecheck`

### Format code
[Not implemented yet - could add prettier]

### Fix linting issues
`pnpm lint:fix`

### Pre-commit validation
[Not implemented yet - could add husky]

### Security scan
[Not implemented yet - could add npm audit]

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
[Not applicable - backend/frontend integrated]

### Run E2E tests
`pnpm test:e2e`

### Debug specific test
`pnpm test:e2e:ui` for Playwright UI mode

### Generate test
[Not implemented yet]

### Create test user
`pnpm create-user --stage dev --email test@example.com --admin`

### Log in test user
Use email code flow - code appears in SST console logs during development

## Database Operations

### Run migrations
`pnpm migrate --stage dev`

### Connect to database
`pnpm db studio --stage dev` opens Drizzle Studio

### Reset database
`pnpm delete-all-data --stage dev && pnpm migrate --stage dev`

### Seed database
Use `pnpm create-user` to create test users

### Run arbitrary query
`pnpm sql "SELECT * FROM users;" --stage dev`

### View schema
See `app/db/schema.ts` or use Drizzle Studio

### Rollback migration
[Not implemented yet - manually edit migration files]

### Backup database
[Not implemented yet - use AWS RDS snapshots]

### Restore database
[Not implemented yet - use AWS RDS snapshots]

### Query performance analysis
Use Aurora Data API insights in AWS console

## Debugging & Inspection

### View logs
Logs are written to `.sst/log`. The exact logs will depend on the type of application and infrastructure used, but they may include:

- `.sst/log/pulumi.log` - infrastructure logs
- `.sst/lambda/<MyFunction>/<id>.log` - lambda invocation logs
- `.sst/log/web.log` - web app logs (e.g. nextjs or React Router app). You can get the URL of the web app in this log.
    - If you are trying to read web.log but it doesn't exist, prompt the user as follows:
    - "No web log found at .sst/log/web.log. Ensure that the `dev` command in `package.json` ends with ` > .sst/log/web.log 2>&1`. This captures the output of the local web server (e.g. vite) to a log file that the agent can read. This will be addressed in https://github.com/sst/sst/pull/5898."

### Tail logs
`tail -f .sst/log/web.log`

### Interactively use the app
`pnpm tsx scripts/debug-browser.ts` - launches interactive Playwright REPL

### Search logs
`grep "pattern" .sst/log/*.log`

### Filter logs by level
`grep -E "ERROR|WARN" .sst/log/web.log`

### Connect debugger
Use VSCode debugger with Node.js attach configuration

### Inspect running process
`ps aux | grep sst` to find processes

### Profile performance
[Not implemented yet - could add performance monitoring]

### Trace requests
Check `.sst/log/web.log` for request logs

## Monitoring

### Health check
Check application URL returns 200 status

### View metrics
[Not implemented yet - could add CloudWatch metrics]

### Check service status
`find .sst -name "*.server" -type f` to verify SST is running

### Monitor dashboard
[Not implemented yet - could add CloudWatch dashboard]

### View error rates
`grep ERROR .sst/log/*.log | wc -l`

## Deployment & Release

### Build for production
`npx sst deploy --stage production`

### Run production mode locally
[Not applicable - use SST stages]

### View deployment status
Check AWS CloudFormation console or SST dashboard

### Rollback deployment
[Not implemented yet - use AWS CloudFormation rollback]

### Run smoke tests
`pnpm test:quick` after deployment

### Generate release notes
[Not implemented yet]

## Further Documentation

### Project overview
See `README.md`

### Available commands
`pnpm run` to list all scripts

### Environment variables
Managed via SST - see `sst.config.ts`

### API documentation
[Not implemented yet - routes documented inline]

### Generate documentation
[Not implemented yet]

### View documentation locally
Open `docs/` directory files

### Architecture overview
See Architecture section above and `README.md`

### Troubleshooting guide
See `docs/test-dynamic-port-detection.md` for port issues

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
[Not implemented yet - could add plop]

### Find unused dependencies
[Not implemented yet - could add depcheck]

### Validate dependencies
`pnpm install --frozen-lockfile`

## API/Service Specific

### List endpoints
See `app/routes.ts` for all routes

### Test endpoint
Use `scripts/debug-browser.ts` or curl

### View API documentation
[Not implemented yet - routes self-documenting]

### Mock external services
[Not applicable - no external service dependencies]

### Validate request/response
TypeScript types in `app/types/` and `app/model/`

### Load test endpoint
[Not implemented yet - could add k6 or artillery]

## Frontend Specific

### Build frontend
`pnpm build`

### Bundle size analysis
[Not implemented yet - could add rollup-plugin-visualizer]

### Component explorer
[Not implemented yet - could add Storybook]

### Run accessibility audit
[Not implemented yet - could add axe-core]

### Lighthouse audit
Use Chrome DevTools Lighthouse

### Browser testing
Playwright tests cover Chrome - see `playwright.config.ts`

## Configuration & Environment

### List configuration
See `.sst/outputs.json` for current infrastructure config

### Switch environment
Use `--stage` parameter: `npx sst dev --stage staging`

### Validate configuration
`npx sst diff --stage dev`

### Diff configurations
`npx sst diff --stage dev --stage production`

### Encrypt secrets
Handled automatically by AWS Secrets Manager

### Export configuration
`cat .sst/outputs.json`

---
Built with https://github.com/martinpllu/agent-dev-guide v1.0.0