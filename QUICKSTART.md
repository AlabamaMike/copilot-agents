# Quick Start Guide: Custom Agents

Get started with custom agents in GitHub Copilot in 5 minutes!

## Available Agents

### 🔍 Codebase Analysis Agent (`@codebase-analysis`)

Your expert assistant for understanding HOW code works. It specializes in:
- 🔍 Analyzing implementation details
- 📊 Tracing data flow through your application
- 📝 Providing precise file:line references
- 🏗️ Explaining architectural patterns and design decisions

### 📁 Code Finder Agent (`@code-finder`)

Your expert assistant for finding WHERE code lives. It specializes in:
- 🔎 Locating files by feature, topic, or type
- 📂 Organizing files by purpose (implementation, tests, config, docs, types, examples)
- 🗂️ Showing directory structure and file clusters
- 📋 Providing file inventories and organization patterns

### 🔎 Pattern Finder Agent (`@pattern-finder`)

Your expert assistant for finding code patterns and examples. It specializes in:
- 🔍 Finding similar implementations to use as templates
- 📋 Showing multiple variations of the same pattern
- 💡 Locating usage examples without critiquing them
- 🧪 Including test patterns alongside implementation patterns
- 📚 Cataloging existing patterns like a reference library

---

## Codebase Analysis Agent

## How to Use

### Codebase Analysis Agent

Simply mention `@codebase-analysis` in your GitHub Copilot chat followed by your question.

#### Basic Usage

```
@codebase-analysis How does [feature/function] work?
```

### Common Use Cases

#### 1. Understand a Function
```
@codebase-analysis Explain how the login function works
```

#### 2. Trace Data Flow
```
@codebase-analysis Trace the data flow from the form submission to the database
```

#### 3. Analyze Architecture
```
@codebase-analysis What design patterns are used in the notification system?
```

#### 4. Debug Issues
```
@codebase-analysis How does error handling work in the payment module?
```

#### 5. Understand Dependencies
```
@codebase-analysis What are the dependencies of the user authentication module?
```

## What to Expect

The agent will provide:

✅ **Precise References**: Every explanation includes exact `file:line` references
```
src/auth/login.js:45-67
```

✅ **Step-by-Step Traces**: Detailed walkthrough of data and control flow
```
1. User input (src/ui/Form.jsx:123)
2. Validation (src/validation/schema.js:45)
3. API call (src/api/users.js:89)
```

✅ **Code Snippets**: Relevant code excerpts with line numbers
```javascript
// src/auth/login.js:45-50
async function login(email, password) {
  const user = await db.findUser(email);
  return bcrypt.compare(password, user.hash);
}
```

✅ **Technical Context**: Explanations of patterns, algorithms, and design decisions

## Tips for Best Results

### 1. Be Specific
❌ "How does authentication work?"
✅ "How does the JWT token generation work in the login function?"

### 2. Ask for Flows
❌ "Tell me about the checkout process"
✅ "Trace the data flow from checkout form submission to order creation"

### 3. Request References
❌ "Where is the payment processing?"
✅ "Explain the payment processing implementation with file references"

### 4. Focus on Understanding
The agent is designed to help you understand existing code, not modify it.
- ✅ "How does this work?"
- ✅ "What happens when...?"
- ✅ "Trace the execution path..."
- ❌ "Fix this bug" (use other agents)
- ❌ "Optimize this code" (use other agents)

## Example Session

**You:**
```
@codebase-analysis How does the user registration process work?
```

**Agent:**
```
The user registration is implemented across multiple files:

### Overview
Registration follows a validation → creation → notification pattern with email verification.

### Implementation Flow

1. Form Submission (src/ui/Register.jsx:89)
   - Captures user input: email, password, name
   - Client-side validation with Yup schema

2. API Endpoint (src/api/auth/register.js:23)
   - POST /api/auth/register
   - Server-side validation (lines 25-30)
   - Checks for duplicate email (line 32)

3. Password Hashing (src/services/auth.js:45)
   - Uses bcrypt with salt rounds of 10
   - Stores hash, not plain password

4. Database Creation (src/db/users.js:67)
   - Creates user record via Sequelize
   - Sets initial status to 'pending'

5. Verification Email (src/services/email.js:89)
   - Generates verification token (line 90)
   - Sends via SendGrid (line 95)

### Code Reference
[code snippet with line numbers]

### Technical Insights
- Implements OWASP password guidelines
- Uses transaction for atomicity
- Includes rate limiting via Express middleware
```

---

## Code Finder Agent

### How to Use

Simply mention `@code-finder` in your GitHub Copilot chat followed by your request.

#### Basic Usage

```
@code-finder Find all files related to [feature/topic]
```

#### Common Use Cases

##### 1. Find Files by Feature
```
@code-finder Find all authentication-related files
```

##### 2. Locate Specific File Types
```
@code-finder Show me all test files for the user module
```

##### 3. Find Configuration Files
```
@code-finder List all configuration files
```

##### 4. Understand File Organization
```
@code-finder How is the payment code organized?
```

##### 5. Find Where to Add Code
```
@code-finder Where should I add new shopping cart features?
```

#### What to Expect

The agent will provide:

✅ **Organized File Lists**: Files grouped by category
```
📦 Implementation Files (8 files)
🧪 Test Files (3 files)
⚙️ Configuration Files (2 files)
📚 Documentation Files (1 file)
```

✅ **Full Paths**: Complete paths from repository root
```
src/auth/authenticate.js
src/auth/login-handler.js
tests/auth/auth.test.js
```

✅ **Directory Clusters**: Shows where related files are grouped
```
Clusters:
- src/auth/ (5 files) - Core authentication logic
- tests/auth/ (3 files) - Authentication tests
```

✅ **Organization Patterns**: Explains file structure conventions
```
Pattern: Routes in src/api/routes/, controllers in src/api/controllers/
Convention: Each domain has its own route and controller file
```

#### Tips for Best Results

##### 1. Use Domain/Feature Names
❌ "Find files"
✅ "Find all shopping cart files"

##### 2. Be Specific About Types
✅ "Find all TypeScript type definitions"
✅ "Show me all API route files"

##### 3. Ask About Organization
✅ "How is the notification system organized?"
✅ "Where are the test files located?"

##### 4. Request Categories
✅ "Find all configuration files"
✅ "List all documentation for the API"

#### Example Session

**You:**
```
@code-finder Find all authentication files
```

**Agent:**
```
### Search Summary
Found 23 files across 6 categories related to authentication.

### Files by Category

#### 📦 Implementation Files (12 files)
src/auth/authenticate.js
src/auth/login-handler.js
src/auth/session-manager.js
...

#### 🧪 Test Files (5 files)
tests/auth/auth.test.js
...

#### ⚙️ Configuration Files (3 files)
config/auth.yml
...

### Directory Organization
- Primary location: src/auth/ (8 files)
- Tests: tests/auth/ (5 files)
- Config: config/ (3 files)

Recommendation: Add new auth features in src/auth/
```

---

## Pattern Finder Agent

### How to Use

Simply mention `@pattern-finder` in your GitHub Copilot chat followed by your request.

#### Basic Usage

```
@pattern-finder Show me [pattern type] patterns
```

#### Common Use Cases

##### 1. Find Implementation Patterns
```
@pattern-finder Show me pagination patterns in the codebase
```

##### 2. Find Error Handling Patterns
```
@pattern-finder Find error handling patterns in API routes
```

##### 3. Find State Management Patterns
```
@pattern-finder Show me state management patterns in React components
```

##### 4. Find Authentication Patterns
```
@pattern-finder How is authentication implemented? Show me examples
```

##### 5. Find Database Patterns
```
@pattern-finder Find database query patterns using the ORM
```

#### What to Expect

The agent will provide:

✅ **Multiple Pattern Examples**: Shows different variations of the same pattern
```
Pattern 1: Offset-Based Pagination (src/api/users.js:45-67)
Pattern 2: Cursor-Based Pagination (src/api/products.js:89-120)
```

✅ **Code Snippets with Context**: Complete examples with file:line references
```javascript
// src/api/users.js:45-67
router.get('/users', async (req, res) => {
  const { page = 1, limit = 20 } = req.query;
  // ... implementation
});
```

✅ **Key Characteristics**: Important aspects of each pattern
```
Key aspects:
- Uses query parameters for page/limit
- Calculates offset from page number
- Returns pagination metadata
```

✅ **Testing Patterns**: Shows how patterns are tested
```javascript
// tests/api/pagination.test.js:15-45
describe('Pagination', () => {
  it('should paginate results', async () => {
    // ... test implementation
  });
});
```

✅ **Pattern Usage**: Where patterns are used in the codebase
```
- Offset pagination: Found in user listings, admin dashboards
- Cursor pagination: Found in API endpoints, mobile app feeds
```

#### Tips for Best Results

##### 1. Be Specific About Pattern Type
❌ "Show me code patterns"
✅ "Show me pagination patterns in API routes"
✅ "Find error handling patterns"

##### 2. Ask for Examples
✅ "Show me authentication patterns with examples"
✅ "Find all variations of form validation"

##### 3. Request Multiple Variations
✅ "Show me all the different ways pagination is implemented"
✅ "Find different error handling approaches"

##### 4. Include Tests
✅ "Show me state management patterns and how they're tested"
✅ "Find authentication patterns including test examples"

#### Example Session

**You:**
```
@pattern-finder Show me pagination patterns in the codebase
```

**Agent:**
```
### Pattern Examples: Pagination

Found 2 distinct pagination patterns in use across the codebase.

#### Pattern 1: Offset-Based Pagination
**Found in**: `src/api/users.js:45-67`
**Used for**: User listing with pagination

[code snippet with complete implementation]

**Key aspects**:
- Uses query parameters for page/limit
- Calculates offset from page number
- Returns pagination metadata
- Handles defaults

#### Pattern 2: Cursor-Based Pagination
**Found in**: `src/api/products.js:89-120`
**Used for**: Product listing with cursor-based pagination

[code snippet with complete implementation]

**Key aspects**:
- Uses cursor instead of page numbers
- More efficient for large datasets
- Stable pagination

#### Testing Patterns
[test examples for both patterns]

#### Pattern Usage in Codebase
- Offset pagination: Found in 8 endpoints
- Cursor pagination: Found in 5 endpoints

#### Related Utilities
- src/utils/pagination.js - Shared helpers
- src/middleware/validate.js - Validation
```

#### Important Note

The Pattern Finder agent is a **documentarian, not a critic**. It shows existing patterns as they are, without:
- ❌ Judging which pattern is better
- ❌ Suggesting improvements
- ❌ Critiquing code quality
- ❌ Recommending which pattern to use

It simply catalogs what exists so you can understand current conventions and choose the approach that fits your needs.

---

## Combining Agents

Use agents together for the best workflow:

1. **Find patterns** with `@pattern-finder` to discover existing implementations
2. **Find code locations** with `@code-finder` to locate specific files
3. **Understand implementation** with `@codebase-analysis` to learn how it works
4. **Modify it** with your tools

**Example Workflow 1: Learning from existing patterns**
```
@pattern-finder Show me authentication patterns
→ Discovered: JWT and Session-based patterns

@code-finder Find all files using JWT authentication
→ Located: src/auth/jwt-auth.js, src/middleware/jwt-auth.js, ...

@codebase-analysis How does JWT validation work in src/middleware/jwt-auth.js?
→ Understanding: Token verification, error handling, etc.

[Implement your feature using learned patterns]
```

**Example Workflow 2: Implementing a new feature**
```
@pattern-finder Show me pagination patterns
→ Found: Offset and cursor-based patterns with examples

[Choose the pattern that fits your needs]

@code-finder Find pagination utility files
→ Located: src/utils/pagination.js

[Use existing utilities in your implementation]
```

**Example Workflow 3: Understanding and extending**
```
@code-finder Find payment processing files
→ Located: src/payment/payment-processor.js

@pattern-finder Show me error handling patterns in services
→ Found: Try-catch patterns, error middleware, etc.

@codebase-analysis How does src/payment/payment-processor.js work?
→ Understanding: Uses Stripe API, implements retry logic...

[Make your changes following existing patterns]
```

---

## Next Steps

1. **Try it**: Open GitHub Copilot chat and ask a question
2. **Explore**: Check out the examples:
   - [Codebase Analysis Examples](examples/codebase-analysis-examples.md)
   - [Code Finder Examples](examples/code-finder-examples.md)
   - [Pattern Finder Examples](examples/pattern-finder-examples.md)
3. **Learn**: Read the full documentation in [README.md](README.md)

## Troubleshooting

### Codebase Analysis Agent

**Q: The agent isn't responding to my questions**
A: Make sure you're using the `@codebase-analysis` mention at the start of your message

**Q: The references don't match my codebase**
A: The agent analyzes your actual code - the examples in docs are just illustrations

**Q: Can it help me fix bugs?**
A: It can help you understand HOW code works, which aids in debugging, but it's not designed to write fixes

**Q: How detailed will the analysis be?**
A: The agent provides comprehensive analysis with precise references. Ask follow-up questions for more depth!

### Code Finder Agent

**Q: Why isn't the agent finding my files?**
A: Make sure you're using `@code-finder` at the start. Try different search terms or synonyms.

**Q: Can it analyze what the code does?**
A: No, it only locates files. Use `@codebase-analysis` to understand code functionality.

**Q: How accurate are the file categorizations?**
A: Very accurate - it uses file patterns, naming conventions, and directory structure to categorize files.

### Pattern Finder Agent

**Q: Why is the agent showing patterns without recommendations?**
A: The Pattern Finder is a documentarian, not a consultant. It shows existing patterns as they are, without judging or recommending. This lets you make your own informed decisions.

**Q: Can it tell me which pattern is best?**
A: No, it only catalogs what exists. It won't critique or recommend patterns. If you need help choosing, consider asking `@codebase-analysis` for detailed technical insights about specific implementations.

**Q: How many pattern variations will it show?**
A: It shows all distinct variations it finds in the codebase, typically 2-4 variations per pattern type.

**Q: Will it include test examples?**
A: Yes! The Pattern Finder includes test patterns alongside implementation patterns to show you how existing code is tested.

## Need Help?

- Check the examples:
  - [Codebase Analysis Examples](examples/codebase-analysis-examples.md)
  - [Code Finder Examples](examples/code-finder-examples.md)
  - [Pattern Finder Examples](examples/pattern-finder-examples.md)
- Read the [full documentation](README.md)
- Review the agent configurations:
  - [Codebase Analysis Config](.github/agents/codebase-analysis.yml)
  - [Code Finder Config](.github/agents/code-finder.yml)
  - [Pattern Finder Config](.github/agents/pattern-finder.yml)

Happy analyzing, finding, and pattern discovering! 🔍📁🔎

