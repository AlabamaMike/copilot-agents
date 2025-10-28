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

## Combining Agents

Use agents together for the best workflow:

1. **Find code** with `@code-finder`
2. **Understand it** with `@codebase-analysis`
3. **Modify it** with your tools

**Example:**
```
@code-finder Find payment processing files
→ Located: src/payment/payment-processor.js

@codebase-analysis How does src/payment/payment-processor.js work?
→ Understanding: Uses Stripe API, implements retry logic...

[Make your changes]
```

---

## Next Steps

1. **Try it**: Open GitHub Copilot chat and ask a question
2. **Explore**: Check out the examples:
   - [Codebase Analysis Examples](examples/codebase-analysis-examples.md)
   - [Code Finder Examples](examples/code-finder-examples.md)
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

## Need Help?

- Check the examples:
  - [Codebase Analysis Examples](examples/codebase-analysis-examples.md)
  - [Code Finder Examples](examples/code-finder-examples.md)
- Read the [full documentation](README.md)
- Review the agent configurations:
  - [Codebase Analysis Config](.github/agents/codebase-analysis.yml)
  - [Code Finder Config](.github/agents/code-finder.yml)

Happy analyzing and finding! 🔍📁

