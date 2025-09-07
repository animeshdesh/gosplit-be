# Splitwise Clone Backend 🧮

_Because splitting bills shouldn't require a PhD in mathematics_

## What We're Building

A backend API that handles the complex art of figuring out who owes whom money when your friends conveniently "forget" their wallet every time the check comes.

## Tech Stack

- **Node.js + Express**: Because we're not masochists
- **PostgreSQL**: Relational data for relational drama
- **JWT Authentication**: Secure enough for your friend group's financial secrets
- **Bcrypt**: Password hashing (much safer than storing "password123")

## Our Design Philosophy 🎯

### Database Decisions We Made (And Why)

1. **Highly Normalized Schema**: We chose consistency over speed because debugging "why is Alice's balance wrong?" is worse than waiting 2ms for a JOIN
2. **Calculate Balances On-Demand**: No stored balances table because sync issues are the devil
3. **UUID Primary Keys**: Auto-increment is so 2010
4. **Separate Junction Tables**: Many-to-many relationships done right

### API Design Choices

- **RESTful routes**: `/groups/:id/expenses` makes sense to humans
- **JWT in headers**: Cookies are for websites, headers are for APIs
- **Error responses**: Consistent JSON error format (your frontend will thank you)

## Database Schema

```sql
users (id, email, name, avatar_url, created_at)
groups (id, name, description, created_by, created_at)
group_members (id, group_id, user_id, joined_at)
expenses (id, group_id, paid_by, amount, description, expense_date)
expense_splits (id, expense_id, user_id, amount_owed)
```

_Simple enough to understand, complex enough to handle real-world mess_

## API Endpoints

### Authentication

- `POST /auth/register` - Join the financial tracking gang
- `POST /auth/login` - Welcome back, spender
- `POST /auth/logout` - See you next expense

### Groups

- `GET /groups` - All your money-sharing circles
- `POST /groups` - Create a new financial commitment
- `GET /groups/:id` - Group details + who's in the danger zone
- `POST /groups/:id/members` - Drag another friend into the debt web

### Expenses

- `GET /groups/:id/expenses` - The damage report
- `POST /groups/:id/expenses` - Someone spent money (again)
- `PUT /expenses/:id` - Fix that "I definitely only had one beer" mistake
- `DELETE /expenses/:id` - Make the evidence disappear

### Balances (The Moment of Truth)

- `GET /groups/:id/balances` - Who owes whom in this specific group
- `GET /users/:id/balances` - Your total financial doom across all groups

## Getting Started

1. **Clone and Install**

```bash
git clone <your-repo>
cd splitwise-backend
npm install
```

2. **Environment Setup**

```bash
cp .env.example .env
# Edit .env with your database credentials
```

3. **Database Setup**

```bash
npm run db:migrate
npm run db:seed  # Optional: adds some test data
```

4. **Development**

```bash
npm run dev  # Starts with nodemon
```

5. **Testing**

```bash
npm test  # Run the test suite
npm run test:watch  # Test-driven development mode
```

## Project Structure

```
src/
├── controllers/     # Route handlers (the business logic)
├── middleware/      # Auth, validation, error handling
├── models/          # Database models (our data shapes)
├── routes/          # Express routes (the API endpoints)
├── config/          # Database, JWT, other settings
└── utils/           # Helper functions and validators
```

## Development Principles

1. **Error Handling**: Every endpoint returns consistent error JSON
2. **Input Validation**: Trust no one, validate everything
3. **Database Transactions**: Financial operations are atomic or nothing
4. **Logging**: Debug-friendly logs (you'll need them at 2 AM)
5. **Testing**: Every endpoint has tests (your future self will thank you)

## The Math Behind the Magic

Balance calculation logic:

```
For users A and B:
Balance = SUM(amounts A paid that B owes) - SUM(amounts B paid that A owes)
```

If result > 0: A is owed money by B  
If result < 0: A owes money to B  
If result = 0: They're even (friendship intact)

## Deployment

- Designed for deployment on Railway/Render
- Environment variables for all config
- Health check endpoint at `/health`
- Proper CORS setup for frontend integration

---

_Made with ☕ and the burning desire to never manually calculate split bills again_
