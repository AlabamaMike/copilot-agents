# Pattern Finder Agent - Usage Examples

This document provides examples of how to use the `@pattern-finder` agent effectively to find code patterns and examples in your codebase.

## Example 1: Finding Pagination Patterns

**User Query:**
```
@pattern-finder Show me pagination patterns in the codebase
```

**Expected Agent Response:**
```
### Pattern Examples: Pagination

Found 2 distinct pagination patterns in use across the codebase.

#### Pattern 1: Offset-Based Pagination
**Found in**: `src/api/users.js:45-67`  
**Used for**: User listing with pagination

```javascript
// src/api/users.js:45-67
router.get('/users', async (req, res) => {
  const { page = 1, limit = 20 } = req.query;
  const offset = (page - 1) * limit;

  const users = await db.users.findMany({
    skip: offset,
    take: limit,
    orderBy: { createdAt: 'desc' }
  });

  const total = await db.users.count();

  res.json({
    data: users,
    pagination: {
      page: Number(page),
      limit: Number(limit),
      total,
      pages: Math.ceil(total / limit)
    }
  });
});
```

**Key aspects**:
- Uses query parameters `page` and `limit`
- Calculates offset as `(page - 1) * limit`
- Returns pagination metadata with total count
- Provides defaults for page (1) and limit (20)
- Includes total pages calculation

#### Pattern 2: Cursor-Based Pagination
**Found in**: `src/api/products.js:89-120`  
**Used for**: Product listing with cursor-based pagination

```javascript
// src/api/products.js:89-120
router.get('/products', async (req, res) => {
  const { cursor, limit = 20 } = req.query;

  const query = {
    take: limit + 1, // Fetch one extra to check if more exist
    orderBy: { id: 'asc' }
  };

  if (cursor) {
    query.cursor = { id: cursor };
    query.skip = 1; // Skip the cursor itself
  }

  const products = await db.products.findMany(query);
  const hasMore = products.length > limit;

  if (hasMore) products.pop(); // Remove the extra item

  res.json({
    data: products,
    cursor: products[products.length - 1]?.id,
    hasMore
  });
});
```

**Key aspects**:
- Uses cursor instead of page numbers
- Fetches one extra record to determine if more exist
- More efficient for large datasets
- Stable pagination (no skipped items on concurrent updates)
- Returns next cursor and hasMore flag

#### Testing Patterns

**Found in**: `tests/api/pagination.test.js:15-45`

```javascript
// tests/api/pagination.test.js:15-45
describe('Pagination', () => {
  it('should paginate results', async () => {
    // Create test data
    await createUsers(50);

    // Test first page
    const page1 = await request(app)
      .get('/users?page=1&limit=20')
      .expect(200);

    expect(page1.body.data).toHaveLength(20);
    expect(page1.body.pagination.total).toBe(50);
    expect(page1.body.pagination.pages).toBe(3);
  });

  it('should handle last page correctly', async () => {
    await createUsers(50);

    const lastPage = await request(app)
      .get('/users?page=3&limit=20')
      .expect(200);

    expect(lastPage.body.data).toHaveLength(10);
  });
});
```

#### Pattern Usage in Codebase
- **Offset pagination**: Used in 8 endpoints (users, admin dashboards, reports)
- **Cursor pagination**: Used in 5 endpoints (products, feeds, activity logs)
- Offset pattern is more common for UI pagination
- Cursor pattern is used for infinite scroll and APIs

#### Related Utilities
- `src/utils/pagination.js:12-45` - Shared pagination helpers
- `src/middleware/validate.js:34-56` - Query parameter validation
- `src/types/pagination.ts:8-20` - TypeScript pagination types
```

---

## Example 2: Finding Error Handling Patterns

**User Query:**
```
@pattern-finder Find error handling patterns in API routes
```

**Expected Agent Response:**
```
### Pattern Examples: Error Handling in API Routes

Found 3 distinct error handling patterns in use.

#### Pattern 1: Try-Catch with Custom Error Classes
**Found in**: `src/api/users.js:78-95`  
**Used for**: User creation endpoint

```javascript
// src/api/users.js:78-95
router.post('/users', async (req, res, next) => {
  try {
    const user = await userService.create(req.body);
    res.status(201).json(user);
  } catch (error) {
    if (error instanceof ValidationError) {
      return res.status(400).json({ 
        error: 'Validation failed', 
        details: error.details 
      });
    }
    if (error instanceof DuplicateError) {
      return res.status(409).json({ 
        error: 'User already exists' 
      });
    }
    next(error); // Pass to error middleware
  }
});
```

**Key aspects**:
- Uses try-catch blocks
- Distinguishes error types with instanceof
- Returns appropriate HTTP status codes
- Passes unknown errors to error middleware with next()

#### Pattern 2: Centralized Error Middleware
**Found in**: `src/middleware/error-handler.js:12-45`  
**Used for**: Application-wide error handling

```javascript
// src/middleware/error-handler.js:12-45
function errorHandler(err, req, res, next) {
  // Log error
  logger.error({
    message: err.message,
    stack: err.stack,
    path: req.path,
    method: req.method
  });

  // Operational errors
  if (err.isOperational) {
    return res.status(err.statusCode).json({
      error: err.message,
      code: err.code
    });
  }

  // Programming errors - don't leak details
  res.status(500).json({
    error: 'Internal server error'
  });
}

module.exports = errorHandler;
```

**Key aspects**:
- Centralized error handling
- Logs all errors with context
- Distinguishes operational vs programming errors
- Prevents error detail leakage in production

#### Pattern 3: Async Handler Wrapper
**Found in**: `src/utils/async-handler.js:5-15`  
**Used for**: Wrapping async route handlers

```javascript
// src/utils/async-handler.js:5-15
const asyncHandler = (fn) => (req, res, next) => {
  Promise.resolve(fn(req, res, next)).catch(next);
};

module.exports = asyncHandler;

// Usage in routes:
// src/api/products.js:23
router.get('/products/:id', asyncHandler(async (req, res) => {
  const product = await productService.findById(req.params.id);
  res.json(product);
}));
```

**Key aspects**:
- Eliminates need for try-catch in every route
- Automatically passes errors to next()
- Cleaner route handler code
- Used in 15 route files

#### Testing Patterns

**Found in**: `tests/api/error-handling.test.js:20-45`

```javascript
// tests/api/error-handling.test.js:20-45
describe('Error Handling', () => {
  it('should return 400 for validation errors', async () => {
    const response = await request(app)
      .post('/users')
      .send({ email: 'invalid' })
      .expect(400);

    expect(response.body.error).toBe('Validation failed');
    expect(response.body.details).toBeDefined();
  });

  it('should return 500 for unexpected errors', async () => {
    jest.spyOn(userService, 'create').mockRejectedValue(new Error('DB error'));

    await request(app)
      .post('/users')
      .send({ email: 'test@example.com' })
      .expect(500);
  });
});
```

#### Pattern Usage in Codebase
- **Try-catch pattern**: Used in 12 route handlers
- **Error middleware**: Registered once in app setup (src/app.js:67)
- **Async wrapper**: Used in 15 route files across all API modules
- All three patterns work together in the error handling flow

#### Related Files
- `src/errors/custom-errors.js:1-50` - Custom error class definitions
- `src/middleware/error-handler.js:1-60` - Error middleware
- `src/utils/async-handler.js:1-20` - Async wrapper utility
- `src/utils/http-status.js:1-30` - HTTP status code constants
```

---

## Example 3: Finding Authentication Patterns

**User Query:**
```
@pattern-finder Show me authentication patterns in the application
```

**Expected Agent Response:**
```
### Pattern Examples: Authentication

Found 2 authentication patterns in use.

#### Pattern 1: JWT Token-Based Authentication
**Found in**: `src/auth/jwt-auth.js:23-67`  
**Used for**: API authentication

```javascript
// src/auth/jwt-auth.js:23-45
async function authenticate(email, password) {
  // Find user
  const user = await User.findOne({ where: { email } });
  if (!user) {
    throw new AuthenticationError('Invalid credentials');
  }

  // Verify password
  const isValid = await bcrypt.compare(password, user.passwordHash);
  if (!isValid) {
    throw new AuthenticationError('Invalid credentials');
  }

  // Generate JWT
  const token = jwt.sign(
    { 
      userId: user.id, 
      email: user.email,
      role: user.role 
    },
    process.env.JWT_SECRET,
    { expiresIn: '24h' }
  );

  return { token, user };
}
```

**Key aspects**:
- Uses JWT tokens for authentication
- Passwords hashed with bcrypt
- Token expires after 24 hours
- Includes user ID and role in token payload
- Throws custom AuthenticationError on failure

#### Pattern 2: Session-Based Authentication
**Found in**: `src/auth/session-auth.js:34-78`  
**Used for**: Web application authentication

```javascript
// src/auth/session-auth.js:34-56
async function login(req, email, password) {
  const user = await User.findOne({ where: { email } });
  if (!user || !await bcrypt.compare(password, user.passwordHash)) {
    throw new AuthenticationError('Invalid credentials');
  }

  // Create session
  req.session.userId = user.id;
  req.session.email = user.email;
  req.session.role = user.role;

  // Store session in Redis
  await req.session.save();

  return user;
}

// src/auth/session-auth.js:58-65
function requireAuth(req, res, next) {
  if (!req.session.userId) {
    return res.status(401).json({ error: 'Unauthorized' });
  }
  next();
}
```

**Key aspects**:
- Uses server-side sessions stored in Redis
- Session data includes user ID, email, and role
- Middleware checks for session presence
- Sessions persist across requests

#### Middleware Patterns

**JWT Middleware** - `src/middleware/jwt-auth.js:12-35`

```javascript
// src/middleware/jwt-auth.js:12-35
function authenticateJWT(req, res, next) {
  const token = req.headers.authorization?.split(' ')[1];
  
  if (!token) {
    return res.status(401).json({ error: 'No token provided' });
  }

  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded;
    next();
  } catch (error) {
    return res.status(401).json({ error: 'Invalid token' });
  }
}
```

**Session Middleware** - `src/middleware/session-auth.js:8-20`

```javascript
// src/middleware/session-auth.js:8-20
function requireSession(req, res, next) {
  if (!req.session.userId) {
    return res.status(401).json({ error: 'Unauthorized' });
  }
  next();
}

function requireRole(role) {
  return (req, res, next) => {
    if (req.session.role !== role) {
      return res.status(403).json({ error: 'Forbidden' });
    }
    next();
  };
}
```

#### Testing Patterns

**Found in**: `tests/auth/jwt-auth.test.js:15-55`

```javascript
// tests/auth/jwt-auth.test.js:15-55
describe('JWT Authentication', () => {
  it('should authenticate valid credentials', async () => {
    const user = await createUser({ email: 'test@example.com', password: 'password123' });

    const result = await authenticate('test@example.com', 'password123');

    expect(result.token).toBeDefined();
    expect(result.user.id).toBe(user.id);
  });

  it('should reject invalid credentials', async () => {
    await createUser({ email: 'test@example.com', password: 'password123' });

    await expect(
      authenticate('test@example.com', 'wrongpassword')
    ).rejects.toThrow(AuthenticationError);
  });

  it('should include user data in token', async () => {
    const user = await createUser({ email: 'test@example.com', role: 'admin' });
    const { token } = await authenticate('test@example.com', 'password123');

    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    expect(decoded.userId).toBe(user.id);
    expect(decoded.role).toBe('admin');
  });
});
```

#### Pattern Usage in Codebase
- **JWT authentication**: Used for REST API endpoints (15 endpoints)
- **Session authentication**: Used for web application routes (8 routes)
- Both patterns coexist in the application
- API routes use JWT, web routes use sessions

#### Related Files
- `src/auth/jwt-auth.js:1-90` - JWT authentication logic
- `src/auth/session-auth.js:1-85` - Session authentication logic
- `src/middleware/jwt-auth.js:1-50` - JWT middleware
- `src/middleware/session-auth.js:1-45` - Session middleware
- `src/errors/auth-errors.js:1-20` - Authentication error classes
- `config/session.js:1-25` - Session configuration
```

---

## Example 4: Finding State Management Patterns

**User Query:**
```
@pattern-finder Find state management patterns in React components
```

**Expected Agent Response:**
```
### Pattern Examples: React State Management

Found 4 distinct state management patterns in use.

#### Pattern 1: useState Hook for Local State
**Found in**: `src/components/UserProfile.jsx:15-45`  
**Used for**: Simple component state

```javascript
// src/components/UserProfile.jsx:15-45
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    async function fetchUser() {
      try {
        setLoading(true);
        const data = await api.getUser(userId);
        setUser(data);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    }
    fetchUser();
  }, [userId]);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  return <div>{user.name}</div>;
}
```

**Key aspects**:
- Uses multiple useState hooks for different state pieces
- Manages loading and error states
- Updates state in useEffect
- Simple and straightforward pattern

#### Pattern 2: useReducer Hook for Complex State
**Found in**: `src/components/ShoppingCart.jsx:20-80`  
**Used for**: Complex state with multiple actions

```javascript
// src/components/ShoppingCart.jsx:20-50
const cartReducer = (state, action) => {
  switch (action.type) {
    case 'ADD_ITEM':
      return {
        ...state,
        items: [...state.items, action.payload],
        total: state.total + action.payload.price
      };
    case 'REMOVE_ITEM':
      const item = state.items.find(i => i.id === action.payload);
      return {
        ...state,
        items: state.items.filter(i => i.id !== action.payload),
        total: state.total - item.price
      };
    case 'CLEAR_CART':
      return { items: [], total: 0 };
    default:
      return state;
  }
};

function ShoppingCart() {
  const [state, dispatch] = useReducer(cartReducer, { items: [], total: 0 });

  const addItem = (item) => dispatch({ type: 'ADD_ITEM', payload: item });
  const removeItem = (id) => dispatch({ type: 'REMOVE_ITEM', payload: id });
  const clearCart = () => dispatch({ type: 'CLEAR_CART' });

  return (
    <div>
      {state.items.map(item => (
        <div key={item.id}>
          {item.name} - ${item.price}
          <button onClick={() => removeItem(item.id)}>Remove</button>
        </div>
      ))}
      <div>Total: ${state.total}</div>
      <button onClick={clearCart}>Clear Cart</button>
    </div>
  );
}
```

**Key aspects**:
- Uses useReducer for related state updates
- Defines actions and reducer function
- Better for complex state logic
- Easier to test reducer in isolation

#### Pattern 3: Context API for Global State
**Found in**: `src/context/AuthContext.jsx:12-67`  
**Used for**: Sharing authentication state across components

```javascript
// src/context/AuthContext.jsx:12-45
const AuthContext = createContext(null);

export function AuthProvider({ children }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    // Check for existing session
    async function checkAuth() {
      try {
        const user = await api.getCurrentUser();
        setUser(user);
      } catch (err) {
        setUser(null);
      } finally {
        setLoading(false);
      }
    }
    checkAuth();
  }, []);

  const login = async (email, password) => {
    const user = await api.login(email, password);
    setUser(user);
  };

  const logout = async () => {
    await api.logout();
    setUser(null);
  };

  return (
    <AuthContext.Provider value={{ user, loading, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}

export function useAuth() {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error('useAuth must be used within AuthProvider');
  }
  return context;
}
```

**Key aspects**:
- Uses Context API for global state
- Provides custom hook for accessing context
- Encapsulates authentication logic
- Used across multiple components

#### Pattern 4: Custom Hooks for Reusable State Logic
**Found in**: `src/hooks/useFetch.js:8-45`  
**Used for**: Reusable data fetching logic

```javascript
// src/hooks/useFetch.js:8-45
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    let isMounted = true;

    async function fetchData() {
      try {
        setLoading(true);
        const response = await fetch(url);
        const json = await response.json();
        
        if (isMounted) {
          setData(json);
          setError(null);
        }
      } catch (err) {
        if (isMounted) {
          setError(err.message);
        }
      } finally {
        if (isMounted) {
          setLoading(false);
        }
      }
    }

    fetchData();

    return () => {
      isMounted = false;
    };
  }, [url]);

  return { data, loading, error };
}

// Usage:
const { data, loading, error } = useFetch('/api/users');
```

**Key aspects**:
- Encapsulates common state logic
- Reusable across components
- Handles cleanup with isMounted flag
- Returns consistent data structure

#### Testing Patterns

**Found in**: `tests/components/ShoppingCart.test.jsx:15-50`

```javascript
// tests/components/ShoppingCart.test.jsx:15-50
import { renderHook, act } from '@testing-library/react-hooks';
import { render, screen, fireEvent } from '@testing-library/react';

describe('ShoppingCart', () => {
  it('should add items to cart', () => {
    render(<ShoppingCart />);
    
    const addButton = screen.getByText('Add Item');
    fireEvent.click(addButton);

    expect(screen.getByText(/Total: \$10/)).toBeInTheDocument();
  });

  it('should remove items from cart', () => {
    render(<ShoppingCart />);
    
    fireEvent.click(screen.getByText('Add Item'));
    fireEvent.click(screen.getByText('Remove'));

    expect(screen.getByText(/Total: \$0/)).toBeInTheDocument();
  });
});

describe('useFetch hook', () => {
  it('should fetch data', async () => {
    const { result, waitForNextUpdate } = renderHook(() => 
      useFetch('/api/test')
    );

    expect(result.current.loading).toBe(true);
    
    await waitForNextUpdate();
    
    expect(result.current.loading).toBe(false);
    expect(result.current.data).toBeDefined();
  });
});
```

#### Pattern Usage in Codebase
- **useState**: Used in 45 components for simple state
- **useReducer**: Used in 8 components for complex state
- **Context API**: Used for auth, theme, and app settings (3 contexts)
- **Custom hooks**: 12 custom hooks for reusable logic

#### Related Files
- `src/hooks/useFetch.js:1-60` - Data fetching hook
- `src/hooks/useForm.js:1-80` - Form state management hook
- `src/context/AuthContext.jsx:1-90` - Authentication context
- `src/context/ThemeContext.jsx:1-60` - Theme context
- `src/utils/reducers.js:1-120` - Shared reducer functions
```

---

## Example 5: Finding Database Query Patterns

**User Query:**
```
@pattern-finder Show me database query patterns using the ORM
```

**Expected Agent Response:**
```
### Pattern Examples: Database Queries

Found 3 common query patterns in use with Sequelize ORM.

#### Pattern 1: Basic CRUD Operations
**Found in**: `src/repositories/user-repository.js:23-78`  
**Used for**: Standard user operations

```javascript
// src/repositories/user-repository.js:23-78
class UserRepository {
  async findById(id) {
    return await User.findByPk(id, {
      attributes: { exclude: ['passwordHash'] }
    });
  }

  async findByEmail(email) {
    return await User.findOne({ 
      where: { email },
      attributes: { exclude: ['passwordHash'] }
    });
  }

  async create(data) {
    return await User.create({
      email: data.email,
      name: data.name,
      passwordHash: await bcrypt.hash(data.password, 10)
    });
  }

  async update(id, data) {
    const user = await User.findByPk(id);
    if (!user) {
      throw new NotFoundError('User not found');
    }
    return await user.update(data);
  }

  async delete(id) {
    const user = await User.findByPk(id);
    if (!user) {
      throw new NotFoundError('User not found');
    }
    return await user.destroy();
  }
}
```

**Key aspects**:
- Repository pattern encapsulates database access
- Excludes sensitive fields (passwordHash)
- Validates existence before update/delete
- Uses async/await consistently

#### Pattern 2: Complex Queries with Associations
**Found in**: `src/repositories/order-repository.js:45-89`  
**Used for**: Order queries with related data

```javascript
// src/repositories/order-repository.js:45-89
class OrderRepository {
  async findWithDetails(id) {
    return await Order.findByPk(id, {
      include: [
        {
          model: User,
          as: 'customer',
          attributes: ['id', 'name', 'email']
        },
        {
          model: OrderItem,
          as: 'items',
          include: [
            {
              model: Product,
              attributes: ['id', 'name', 'price']
            }
          ]
        }
      ]
    });
  }

  async findByUser(userId, options = {}) {
    const { page = 1, limit = 20 } = options;
    const offset = (page - 1) * limit;

    return await Order.findAndCountAll({
      where: { userId },
      include: [
        {
          model: OrderItem,
          as: 'items',
          include: [{ model: Product }]
        }
      ],
      limit,
      offset,
      order: [['createdAt', 'DESC']]
    });
  }
}
```

**Key aspects**:
- Uses include for eager loading associations
- Specifies which attributes to fetch
- Implements pagination with findAndCountAll
- Orders results by creation date

#### Pattern 3: Transaction Handling
**Found in**: `src/services/order-service.js:67-112`  
**Used for**: Creating orders with multiple operations

```javascript
// src/services/order-service.js:67-112
class OrderService {
  async createOrder(userId, cartItems) {
    const t = await sequelize.transaction();

    try {
      // Create order
      const order = await Order.create({
        userId,
        status: 'pending',
        total: 0
      }, { transaction: t });

      // Create order items and calculate total
      let total = 0;
      for (const item of cartItems) {
        const product = await Product.findByPk(item.productId, { transaction: t });
        
        if (product.stock < item.quantity) {
          throw new InsufficientStockError('Product out of stock');
        }

        await OrderItem.create({
          orderId: order.id,
          productId: item.productId,
          quantity: item.quantity,
          price: product.price
        }, { transaction: t });

        // Update product stock
        await product.update({
          stock: product.stock - item.quantity
        }, { transaction: t });

        total += product.price * item.quantity;
      }

      // Update order total
      await order.update({ total }, { transaction: t });

      // Commit transaction
      await t.commit();

      return order;
    } catch (error) {
      // Rollback on error
      await t.rollback();
      throw error;
    }
  }
}
```

**Key aspects**:
- Uses transactions for atomic operations
- Wraps in try-catch for proper rollback
- Validates business rules within transaction
- Updates multiple tables atomically

#### Testing Patterns

**Found in**: `tests/repositories/user-repository.test.js:20-55`

```javascript
// tests/repositories/user-repository.test.js:20-55
describe('UserRepository', () => {
  beforeEach(async () => {
    await User.destroy({ where: {} });
  });

  describe('findById', () => {
    it('should find user by id', async () => {
      const created = await User.create({
        email: 'test@example.com',
        name: 'Test User',
        passwordHash: 'hash'
      });

      const found = await userRepository.findById(created.id);

      expect(found.id).toBe(created.id);
      expect(found.email).toBe('test@example.com');
      expect(found.passwordHash).toBeUndefined(); // Excluded
    });

    it('should return null if not found', async () => {
      const found = await userRepository.findById(999);
      expect(found).toBeNull();
    });
  });

  describe('create', () => {
    it('should create new user', async () => {
      const user = await userRepository.create({
        email: 'new@example.com',
        name: 'New User',
        password: 'password123'
      });

      expect(user.id).toBeDefined();
      expect(user.email).toBe('new@example.com');
    });
  });
});
```

#### Pattern Usage in Codebase
- **Repository pattern**: Used for all database entities (8 repositories)
- **Association loading**: Used in 12 query methods
- **Transactions**: Used in 6 service methods for complex operations
- All repositories follow consistent structure and naming

#### Related Files
- `src/repositories/user-repository.js:1-150` - User repository
- `src/repositories/order-repository.js:1-180` - Order repository
- `src/repositories/product-repository.js:1-120` - Product repository
- `src/models/index.js:1-80` - Model definitions and associations
- `src/db/config.js:1-40` - Database configuration
```

---

## Tips for Using the Pattern Finder Agent

### 1. Be Specific About Pattern Type
❌ "Show me code patterns"
✅ "Show me pagination patterns in API routes"
✅ "Find error handling patterns"
✅ "Show me state management patterns in React"

### 2. Specify the Domain or Area
✅ "Find authentication patterns in the API"
✅ "Show me form validation patterns in components"
✅ "Find database transaction patterns"

### 3. Ask for Multiple Examples
✅ "Show me all the different ways pagination is implemented"
✅ "Find variations of error handling in the codebase"

### 4. Request Test Patterns Too
✅ "Show me pagination patterns and how they're tested"
✅ "Find authentication patterns including test examples"

## Common Pattern Categories

### Code Organization Patterns
- File structure patterns
- Module organization
- Component hierarchy
- Directory layouts

### Functional Patterns
- Authentication/Authorization
- Data validation
- Error handling
- Logging
- Caching

### Data Patterns
- Database queries
- Data transformation
- API request/response
- State management

### Testing Patterns
- Test structure
- Mock/stub patterns
- Assertion patterns
- Test data setup

### Integration Patterns
- API integration
- Third-party services
- Database connections
- Message queues

## What the Pattern Finder Does

✅ Finds existing code patterns in the codebase
✅ Shows multiple variations of the same pattern
✅ Includes file:line references for each example
✅ Provides code snippets with context
✅ Shows how patterns are tested
✅ Documents where patterns are used
✅ Notes key characteristics of each pattern

## What the Pattern Finder Doesn't Do

❌ Judge or critique patterns
❌ Recommend which pattern to use
❌ Suggest improvements
❌ Identify anti-patterns or code smells
❌ Propose new patterns
❌ Perform code quality analysis
❌ Make architectural recommendations

## Combining with Other Agents

Use `@pattern-finder` to discover existing patterns, then:
- Use `@code-finder` to locate all files using a pattern
- Use `@codebase-analysis` to understand how a specific pattern works in detail

**Example workflow:**
1. `@pattern-finder Show me authentication patterns` ← Discover patterns
2. `@code-finder Find all files using JWT authentication` ← Locate implementations
3. `@codebase-analysis How does JWT validation work in middleware?` ← Deep dive
