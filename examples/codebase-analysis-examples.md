# Codebase Analysis Agent - Usage Examples

This document provides examples of how to use the `@codebase-analysis` agent effectively.

## Example 1: Understanding Function Implementation

**User Query:**
```
@codebase-analysis How does the user authentication function work?
```

**Expected Agent Response:**
```
The user authentication is implemented in `src/auth/authenticate.js:23-67`.

### Overview
The authentication flow uses JWT tokens with bcrypt password hashing and includes rate limiting.

### Implementation Details

1. **Password Validation** (src/auth/authenticate.js:25-32)
   - Retrieves user from database via `getUserByEmail()` (src/db/users.js:45)
   - Compares hashed passwords using bcrypt (src/auth/authenticate.js:30)
   - Returns null if credentials don't match

2. **Token Generation** (src/auth/authenticate.js:34-42)
   - Creates JWT payload with user ID and role (src/auth/authenticate.js:35)
   - Signs token with secret from environment (src/auth/authenticate.js:38)
   - Sets expiration to 24 hours

3. **Rate Limiting** (src/auth/authenticate.js:44-50)
   - Checks Redis for failed attempts (src/cache/redis.js:89)
   - Blocks IP after 5 failed attempts
   - Clears counter on successful auth

### Code References
```javascript
// src/auth/authenticate.js:23-45
async function authenticate(email, password) {
  const user = await getUserByEmail(email);
  if (!user || !await bcrypt.compare(password, user.passwordHash)) {
    await incrementFailedAttempts(email);
    return null;
  }
  
  const token = jwt.sign(
    { userId: user.id, role: user.role },
    process.env.JWT_SECRET,
    { expiresIn: '24h' }
  );
  
  await clearFailedAttempts(email);
  return { token, user };
}
```

### Technical Insights
- Uses async/await for database operations
- Implements security best practices (hashing, rate limiting)
- Follows separation of concerns (auth logic separate from DB layer)
```

## Example 2: Tracing Data Flow

**User Query:**
```
@codebase-analysis Trace the data flow from the checkout form submission to database storage
```

**Expected Agent Response:**
```
### Data Flow: Checkout Form → Database

**Step-by-Step Trace:**

1. **Form Submission** (src/ui/Checkout.jsx:156)
   - User clicks "Complete Purchase" button
   - React form handler captures form data
   - Data structure: `{ items, shippingAddress, paymentMethod }`

2. **Client-Side Validation** (src/ui/Checkout.jsx:158-165)
   - Validates required fields using formik
   - Checks email format with regex
   - Validates credit card using Luhn algorithm (src/utils/validation.js:23)

3. **API Request** (src/ui/Checkout.jsx:167)
   - POST request to `/api/orders` endpoint
   - Data serialized as JSON
   - Includes authentication token in headers

4. **Server-Side Reception** (src/api/orders.js:45)
   - Express route handler receives request
   - Parses JSON body
   - Extracts user ID from JWT token (src/middleware/auth.js:12)

5. **Validation Layer** (src/api/orders.js:48-55)
   - Validates with Joi schema (src/validation/order-schema.js:8)
   - Checks inventory availability (src/services/inventory.js:78)
   - Verifies shipping address (src/services/shipping.js:34)

6. **Payment Processing** (src/services/payment.js:89)
   - Transforms data to payment gateway format
   - Sends to Stripe API (src/services/payment.js:95)
   - Receives transaction ID

7. **Database Storage** (src/db/orders.js:123)
   - Creates order record with transaction ID
   - Uses Sequelize ORM
   - Executes within database transaction (src/db/orders.js:125)
   - Stores in `orders` table with foreign keys to `users` and `products`

8. **Response** (src/api/orders.js:67)
   - Returns order confirmation object
   - Includes order ID and estimated delivery date
   - HTTP 201 status code

**Data Transformations:**
- Form data → Validated object (Step 2)
- Client object → JSON string (Step 3)
- JSON → JavaScript object (Step 4)
- API format → Database model (Step 7)
- Database model → Response DTO (Step 8)
```

## Example 3: Understanding Design Patterns

**User Query:**
```
@codebase-analysis What design patterns are used in the notification system?
```

**Expected Agent Response:**
```
The notification system (src/notifications/) implements several design patterns:

### 1. Observer Pattern (src/notifications/NotificationManager.js:12-89)

The NotificationManager acts as the subject:
- Maintains list of subscribers (src/notifications/NotificationManager.js:15)
- `subscribe()` method adds observers (line 23)
- `notify()` method alerts all subscribers (line 45)

Observers include:
- EmailNotifier (src/notifications/channels/EmailNotifier.js)
- SMSNotifier (src/notifications/channels/SMSNotifier.js)
- PushNotifier (src/notifications/channels/PushNotifier.js)

### 2. Factory Pattern (src/notifications/NotificationFactory.js:8-34)

Creates appropriate notifier instances:
- `createNotifier(type)` method (line 10)
- Returns different implementations based on type parameter
- Encapsulates instantiation logic

### 3. Strategy Pattern (src/notifications/channels/)

Different notification channels implement common interface:
- `NotificationChannel` interface (src/notifications/interfaces.js:5)
- `send()` method implemented by each channel
- Allows runtime selection of notification method

### 4. Template Method Pattern (src/notifications/BaseNotifier.js:12-45)

BaseNotifier defines algorithm structure:
- `sendNotification()` orchestrates the process (line 15)
- Calls abstract methods: `validate()`, `format()`, `deliver()`
- Subclasses implement specific steps

**Code Reference:**
```javascript
// src/notifications/BaseNotifier.js:15-28
async sendNotification(recipient, message) {
  // Template method pattern
  await this.validate(recipient);     // Step 1 (overridden by subclasses)
  const formatted = this.format(message); // Step 2 (overridden)
  await this.deliver(recipient, formatted); // Step 3 (overridden)
  this.log(recipient, message);       // Step 4 (defined in base)
}
```

### Interaction Between Patterns:
1. Factory creates appropriate notifiers
2. NotificationManager observes events and notifies channels
3. Each channel uses template method for consistent flow
4. Strategy pattern allows switching channels dynamically
```

## Example 4: Analyzing Error Handling

**User Query:**
```
@codebase-analysis How does the application handle database connection errors?
```

**Expected Agent Response:**
```
### Database Error Handling Strategy

**Primary Error Handler** (src/db/connection.js:45-89)

1. **Connection Retry Logic** (src/db/connection.js:47-62)
   - Implements exponential backoff
   - Maximum 5 retry attempts
   - Delays: 1s, 2s, 4s, 8s, 16s
   
```javascript
// src/db/connection.js:47-62
async function connectWithRetry(attempt = 1) {
  try {
    await sequelize.authenticate();
    logger.info('Database connected');
  } catch (error) {
    if (attempt >= MAX_RETRIES) {
      throw new DatabaseConnectionError(error);
    }
    const delay = Math.pow(2, attempt) * 1000;
    await sleep(delay);
    return connectWithRetry(attempt + 1);
  }
}
```

2. **Connection Pool Error Handling** (src/db/connection.js:65-78)
   - Listens to pool error events
   - Logs errors with context (src/logger/db-logger.js:23)
   - Attempts to restore connection
   - Notifies monitoring service (src/monitoring/alerts.js:45)

3. **Query-Level Error Handling** (src/db/base-repository.js:34-56)
   - Wraps all queries in try-catch
   - Distinguishes error types:
     - ConnectionError → retry with backoff
     - TimeoutError → return cached data if available
     - ValidationError → throw immediately
   
4. **Circuit Breaker Pattern** (src/db/circuit-breaker.js:12-67)
   - Opens circuit after 10 consecutive failures
   - Half-open state after 30 seconds
   - Prevents cascade failures

5. **Graceful Degradation** (src/api/middleware/db-health.js:23)
   - Health check endpoint monitors DB status
   - Returns cached responses when DB is down
   - Queues write operations for later processing (src/queue/db-queue.js:45)

**Error Propagation:**
Database errors flow through these layers:
1. Sequelize → Connection module (src/db/connection.js:45)
2. Connection module → Repository layer (src/db/base-repository.js:34)
3. Repository → Service layer with custom error types (src/services/base-service.js:23)
4. Service → API controller with HTTP status codes (src/api/controllers/base.js:45)
5. Controller → Client with user-friendly messages (src/api/error-formatter.js:12)
```

## Tips for Using the Agent

1. **Be Specific**: Ask about specific files, functions, or features
2. **Request Examples**: Ask for code snippets with line numbers
3. **Ask for Flows**: Request data flow or execution flow traces
4. **Seek Patterns**: Inquire about design patterns and architectural decisions
5. **Dive Deep**: Ask follow-up questions to understand implementation details

## Common Queries

- "How does [feature] work?"
- "Trace the data flow from [start] to [end]"
- "Explain the implementation of [function/class]"
- "What design patterns are used in [module]?"
- "How does error handling work in [component]?"
- "What are the dependencies of [module]?"
- "How is [data] validated/sanitized?"
