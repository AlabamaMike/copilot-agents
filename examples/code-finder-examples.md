# Code Finder Agent - Usage Examples

This document provides examples of how to use the `@code-finder` agent effectively to locate files in your codebase.

## Example 1: Finding Authentication Files

**User Query:**
```
@code-finder Find all files related to authentication
```

**Expected Agent Response:**
```
### Search Summary
Searched for authentication-related files using patterns: auth*, login*, session*, token*, jwt*
Found 23 files across 6 categories.

### Files by Category

#### 📦 Implementation Files (12 files)
```
src/auth/authenticate.js
src/auth/login-handler.js
src/auth/session-manager.js
src/auth/token-service.js
src/middleware/auth-middleware.js
src/services/jwt-service.js
src/models/user-session.js
src/utils/auth-helpers.js
lib/passport-config.js
lib/oauth-provider.js
src/api/controllers/auth-controller.js
src/api/routes/auth-routes.js
```
**Clusters:**
- `src/auth/` (8 files) - Core authentication logic
- `src/api/` (2 files) - Authentication API endpoints
- `lib/` (2 files) - Third-party auth configurations

#### 🧪 Test Files (5 files)
```
src/auth/__tests__/authenticate.test.js
src/auth/__tests__/login-handler.test.js
src/auth/__tests__/session-manager.test.js
tests/integration/auth-flow.test.js
tests/e2e/login.spec.js
```

#### ⚙️ Configuration Files (3 files)
```
config/auth.yml
config/jwt.json
.env.example (contains AUTH_SECRET, JWT_SECRET)
```

#### 📚 Documentation Files (2 files)
```
docs/authentication.md
docs/api/auth-endpoints.md
```

#### 🔤 Type Definitions (1 file)
```
types/auth.d.ts
```

### Directory Organization
- **Primary location**: `src/auth/` - Contains all core authentication logic
- **API layer**: `src/api/controllers/` and `src/api/routes/` - HTTP endpoints
- **Tests mirror implementation**: `src/auth/__tests__/` for unit tests
- **Config centralized**: `config/` directory for all auth settings

**Recommendation**: New authentication features should go in `src/auth/` with corresponding tests in `src/auth/__tests__/`
```

## Example 2: Finding Payment Processing Code

**User Query:**
```
@code-finder Locate all files for payment processing
```

**Expected Agent Response:**
```
### Search Summary
Searched for payment-related files using keywords: payment*, billing*, checkout*, stripe*, transaction*, invoice*
Found 18 files across 5 categories.

### Files by Category

#### 📦 Implementation Files (9 files)
```
src/payment/payment-processor.js
src/payment/stripe-client.js
src/payment/transaction-manager.js
src/billing/invoice-generator.js
src/billing/subscription-handler.js
src/services/checkout-service.js
src/models/payment.js
src/models/transaction.js
src/api/controllers/payment-controller.js
```
**Clusters:**
- `src/payment/` (3 files) - Payment processing logic
- `src/billing/` (2 files) - Billing and invoicing
- `src/models/` (2 files) - Payment data models

#### 🧪 Test Files (4 files)
```
src/payment/__tests__/payment-processor.test.js
src/payment/__tests__/stripe-client.test.js
tests/integration/payment-flow.test.js
tests/mocks/stripe-mock.js
```

#### ⚙️ Configuration Files (2 files)
```
config/payment-gateways.yml
.env.example (contains STRIPE_API_KEY, STRIPE_WEBHOOK_SECRET)
```

#### 📚 Documentation Files (2 files)
```
docs/payment-integration.md
README-payments.md
```

#### 📝 Examples (1 file)
```
examples/payment-example.js
```

### Directory Organization
- **Payment logic**: `src/payment/` - Core payment processing
- **Billing logic**: `src/billing/` - Invoice and subscription management
- **Models**: `src/models/` - Database schemas for payments
- **External examples**: `examples/` - Sample implementations

**Note**: Payment files are separated from general API code, likely for security and maintainability
```

## Example 3: Finding API Endpoints

**User Query:**
```
@code-finder Show me all API route definitions
```

**Expected Agent Response:**
```
### Search Summary
Searched for API routes using patterns: *routes*, *router*, *endpoints*, api/*
Found 14 files in API layer.

### Files by Category

#### 📦 Implementation Files (10 files)
```
src/api/routes/index.js
src/api/routes/auth-routes.js
src/api/routes/user-routes.js
src/api/routes/product-routes.js
src/api/routes/order-routes.js
src/api/routes/payment-routes.js
src/api/controllers/auth-controller.js
src/api/controllers/user-controller.js
src/api/controllers/product-controller.js
src/api/controllers/order-controller.js
```
**Clusters:**
- `src/api/routes/` (6 files) - Route definitions
- `src/api/controllers/` (4 files) - Request handlers

#### 🧪 Test Files (2 files)
```
tests/api/routes.test.js
tests/integration/api-endpoints.test.js
```

#### 📚 Documentation Files (2 files)
```
docs/api/endpoints.md
docs/api/README.md
```

### Directory Organization
- **Pattern**: Routes in `src/api/routes/`, controllers in `src/api/controllers/`
- **Main entry**: `src/api/routes/index.js` aggregates all routes
- **Convention**: Each domain (auth, users, products) has its own route and controller file
- **Naming**: Follows pattern `{domain}-routes.js` and `{domain}-controller.js`

**Observation**: Clean separation between routing and business logic (MVC pattern)
```

## Example 4: Finding Test Files

**User Query:**
```
@code-finder Find all test files for the user module
```

**Expected Agent Response:**
```
### Search Summary
Searched for user-related test files in test directories and files matching *test*, *spec*
Found 11 test files.

### Files by Category

#### 🧪 Test Files (11 files)

**Unit Tests:**
```
src/user/__tests__/user-service.test.js
src/user/__tests__/user-validator.test.js
src/user/__tests__/user-repository.test.js
src/models/__tests__/user-model.test.js
```

**Integration Tests:**
```
tests/integration/user-api.test.js
tests/integration/user-auth.test.js
tests/integration/user-crud.test.js
```

**E2E Tests:**
```
tests/e2e/user-registration.spec.js
tests/e2e/user-profile.spec.js
```

**Test Utilities:**
```
tests/fixtures/user-fixtures.js
tests/mocks/user-mocks.js
```

### Directory Organization
- **Unit tests**: Co-located with implementation in `src/user/__tests__/`
- **Integration tests**: Centralized in `tests/integration/`
- **E2E tests**: In `tests/e2e/` directory
- **Test helpers**: In `tests/fixtures/` and `tests/mocks/`

**Test Coverage Areas**:
- User service logic (3 unit tests)
- API endpoints (3 integration tests)
- User workflows (2 e2e tests)
- Shared test utilities (2 helper files)
```

## Example 5: Finding Configuration Files

**User Query:**
```
@code-finder List all configuration files in the project
```

**Expected Agent Response:**
```
### Search Summary
Searched for configuration files across the repository
Found 19 configuration files.

### Files by Category

#### ⚙️ Configuration Files (19 files)

**Package Management:**
```
package.json
package-lock.json
yarn.lock
```

**Environment:**
```
.env.example
.env.development
.env.production
```

**Build Tools:**
```
webpack.config.js
babel.config.js
tsconfig.json
rollup.config.js
```

**Code Quality:**
```
.eslintrc.js
.prettierrc
.editorconfig
```

**CI/CD:**
```
.github/workflows/test.yml
.github/workflows/deploy.yml
```

**Application Config:**
```
config/database.yml
config/redis.yml
config/app.yml
```

### Directory Organization
- **Root level**: Package management and tooling configs
- **config/**: Application-specific configurations
- **.github/workflows/**: CI/CD pipeline definitions
- **Hidden files**: Code quality tools (ESLint, Prettier)

**Configuration Patterns**:
- Environment variables in `.env.*` files
- Build tools in root with `.config.js` suffix
- App configs in `config/` directory with `.yml` format
```

## Example 6: Finding Type Definitions

**User Query:**
```
@code-finder Find all TypeScript type definitions and interfaces
```

**Expected Agent Response:**
```
### Search Summary
Searched for TypeScript declaration files and type definitions
Found 15 files with type information.

### Files by Category

#### 🔤 Type Definitions (15 files)

**Declaration Files:**
```
types/global.d.ts
types/express.d.ts
types/custom.d.ts
src/@types/node.d.ts
```

**Interface Files:**
```
src/interfaces/user.interface.ts
src/interfaces/product.interface.ts
src/interfaces/order.interface.ts
src/interfaces/payment.interface.ts
```

**Type Files:**
```
src/types/auth.types.ts
src/types/api.types.ts
src/types/database.types.ts
```

**Schema Files:**
```
src/schemas/user.schema.ts
src/schemas/product.schema.ts
src/schemas/order.schema.ts
src/validation/types.ts
```

### Directory Organization
- **Global types**: `types/` directory at root - ambient declarations
- **Interface definitions**: `src/interfaces/` - shared interfaces
- **Module types**: Co-located `src/types/` - module-specific types
- **Validation schemas**: `src/schemas/` - runtime + compile-time types

**Type Organization Strategy**:
- Global/ambient types in root `types/`
- Shared interfaces in `src/interfaces/`
- Feature-specific types alongside features
- Schemas combine runtime validation with types
```

## Example 7: Finding Documentation

**User Query:**
```
@code-finder Find all documentation files
```

**Expected Agent Response:**
```
### Search Summary
Searched for Markdown files and documentation directories
Found 23 documentation files.

### Files by Category

#### 📚 Documentation Files (23 files)

**Root Documentation:**
```
README.md
CONTRIBUTING.md
CHANGELOG.md
LICENSE
CODE_OF_CONDUCT.md
```

**Feature Documentation:**
```
docs/authentication.md
docs/payment-integration.md
docs/api/endpoints.md
docs/api/authentication.md
docs/database/schema.md
docs/database/migrations.md
```

**Architecture:**
```
docs/architecture/overview.md
docs/architecture/patterns.md
docs/architecture/decisions.md
```

**Guides:**
```
docs/guides/getting-started.md
docs/guides/development.md
docs/guides/deployment.md
docs/guides/testing.md
```

**Component Docs:**
```
src/components/Button/README.md
src/components/Form/README.md
src/hooks/README.md
```

### Directory Organization
- **Project root**: High-level documentation (README, CONTRIBUTING, etc.)
- **docs/**: Structured documentation by category
  - `docs/architecture/` - System design docs
  - `docs/api/` - API documentation
  - `docs/guides/` - How-to guides
  - `docs/database/` - Database documentation
- **Component-level**: README files co-located with components

**Documentation Structure**:
- Clear hierarchy: project → feature → component
- Markdown format throughout
- Co-located docs for components
- Centralized docs for cross-cutting concerns
```

## Example 8: Finding Files by Feature Across Categories

**User Query:**
```
@code-finder Find everything related to the shopping cart feature
```

**Expected Agent Response:**
```
### Search Summary
Searched for cart-related files using patterns: cart*, basket*, shopping*
Found 16 files across all categories.

### Files by Category

#### 📦 Implementation Files (8 files)
```
src/cart/cart-service.js
src/cart/cart-storage.js
src/cart/cart-validator.js
src/models/cart.js
src/models/cart-item.js
src/api/controllers/cart-controller.js
src/api/routes/cart-routes.js
src/hooks/useCart.js
```

#### 🧪 Test Files (3 files)
```
src/cart/__tests__/cart-service.test.js
tests/integration/cart-api.test.js
tests/e2e/shopping-cart.spec.js
```

#### ⚙️ Configuration Files (1 file)
```
config/cart.yml
```

#### 📚 Documentation Files (2 files)
```
docs/features/shopping-cart.md
src/cart/README.md
```

#### 🔤 Type Definitions (2 files)
```
src/interfaces/cart.interface.ts
src/types/cart.types.ts
```

### Directory Organization
- **Primary location**: `src/cart/` (4 implementation files)
- **Models**: `src/models/` (2 cart data models)
- **API**: `src/api/controllers/` and `src/api/routes/` (2 files)
- **Frontend**: `src/hooks/` (1 React hook)
- **Tests**: Co-located and centralized

**Feature File Map**:
```
Shopping Cart Feature
├── Implementation: src/cart/ (core logic)
├── Data Models: src/models/ (persistence)
├── API Layer: src/api/ (HTTP interface)
├── Frontend: src/hooks/ (UI integration)
├── Tests: Multiple locations
├── Config: config/cart.yml
├── Types: src/interfaces/, src/types/
└── Docs: docs/features/, src/cart/README.md
```

**Complete feature spread across 8 directories**
```

## Tips for Using the Code Finder Agent

### 1. Be Specific About What You're Looking For
❌ "Find code files"
✅ "Find all authentication-related files"
✅ "Locate payment processing implementation files"

### 2. Use Feature Names or Domain Terms
✅ "Find shopping cart code"
✅ "Show me all user management files"
✅ "Locate notification system files"

### 3. Ask for Specific Categories
✅ "Find all test files for the API layer"
✅ "Show me all configuration files"
✅ "List all TypeScript type definitions"

### 4. Request Organization Patterns
✅ "Where is the authentication code organized?"
✅ "How are the API routes structured?"
✅ "Show me the file organization for payments"

### 5. Look for Clusters
✅ "Which directories contain the most files for feature X?"
✅ "Show me where related files are grouped together"

## Common Queries

### Finding by Feature/Domain
- "Find all files related to [feature]"
- "Locate [domain] code"
- "Show me everything for [module]"

### Finding by Type
- "Find all test files"
- "List all configuration files"
- "Show me all documentation"
- "Find type definitions"

### Finding by Pattern
- "Find all API routes"
- "Locate all database models"
- "Show me all React components"
- "Find all utility functions"

### Understanding Organization
- "How is [feature] organized?"
- "Where should I add [type] files for [feature]?"
- "What directories contain [feature] code?"

## What the Code Finder Does

✅ Locates files quickly
✅ Categorizes files by purpose
✅ Shows directory organization
✅ Provides full paths
✅ Identifies file clusters
✅ Notes naming patterns

## What the Code Finder Doesn't Do

❌ Analyze code contents (use `@codebase-analysis` for that)
❌ Explain how code works
❌ Suggest code changes
❌ Trace data flow
❌ Debug issues

## Combining with Other Agents

Use `@code-finder` first to locate files, then:
- Use `@codebase-analysis` to understand how those files work
- Use other agents to modify or analyze the code

**Example workflow:**
1. `@code-finder Find authentication files` ← Locate the code
2. `@codebase-analysis How does src/auth/authenticate.js work?` ← Understand it
3. Make changes with appropriate tools
