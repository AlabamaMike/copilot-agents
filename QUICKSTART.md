# Quick Start Guide: Codebase Analysis Agent

Get started with the `@codebase-analysis` agent in 5 minutes!

## What is the Codebase Analysis Agent?

The codebase-analysis agent is your expert assistant for understanding HOW code works. It specializes in:
- 🔍 Analyzing implementation details
- 📊 Tracing data flow through your application
- 📝 Providing precise file:line references
- 🏗️ Explaining architectural patterns and design decisions

## How to Use

Simply mention `@codebase-analysis` in your GitHub Copilot chat followed by your question.

### Basic Usage

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

## Next Steps

1. **Try it**: Open GitHub Copilot chat and ask a question
2. **Explore**: Check out [examples/codebase-analysis-examples.md](examples/codebase-analysis-examples.md) for more detailed examples
3. **Learn**: Read the full documentation in [README.md](README.md)

## Troubleshooting

**Q: The agent isn't responding to my questions**
A: Make sure you're using the `@codebase-analysis` mention at the start of your message

**Q: The references don't match my codebase**
A: The agent analyzes your actual code - the examples in docs are just illustrations

**Q: Can it help me fix bugs?**
A: It can help you understand HOW code works, which aids in debugging, but it's not designed to write fixes

**Q: How detailed will the analysis be?**
A: The agent provides comprehensive analysis with precise references. Ask follow-up questions for more depth!

## Need Help?

- Check the [examples](examples/codebase-analysis-examples.md)
- Read the [full documentation](README.md)
- Review the [agent configuration](.github/agents/codebase-analysis.yml)

Happy analyzing! 🔍

