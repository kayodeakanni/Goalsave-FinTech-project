# Goalsave-FinTech-project
 Capstone Goalsave FinTech Project Adv Apr'26 Group 3 

# GoalSave API 💰
**Improving Saving Habits Through a Simple Digital Budget and Savings Tracker**

A secure RESTful API built with Node.js, Express, and MongoDB. GoalSave helps users budget, track income/expenses, set savings goals, and get financial insights without the complexity.

**Live API**: `https://goalsave-api.onrender.com`  
**Status**: Capstone Project - MVP

---

### Features
- **User Authentication**: Secure signup/login with `bcryptjs` + JWT
- **Protected Routes**: `requireAuth` middleware for all financial data
- **Budget Management**: Create weekly/monthly budgets by category
- **Income & Expense Tracking**: Log transactions with auto categorization
- **Savings Goals**: Create goals, track progress, add contributions
- **Spending Analytics**: Charts data by category and monthly trends
- **Financial Reports**: Weekly and monthly summaries
- **Smart Notifications**: Endpoints to support reminders for budgets/bills/savings
- **Robust Validation**: Joi validation on all inputs
- **Error Handling**: Centralized middleware for MongoDB and route errors
- **Ownership Verification**: Users can only access/modify their own data

### Tech Stack
| Layer | Technology |
| --- | --- |
| Runtime | Node.js + Express |
| Database | MongoDB Atlas + Mongoose |
| Auth | JWT, bcryptjs |
| Validation | Joi |
| Config | dotenv |
| Hosting | Render.com |

### Project Structure
goalsave-api/
├── middleware/
│   ├── http://auth.js
│   ├── http://errorHandler.js
│   ├── http://logger.js
│   └── http://validate.js
├── models/
│   ├── http://user.model.js
│   ├── http://budget.model.js
│   ├── http://transaction.model.js
│   └── http://savingsGoal.model.js
├── routes/
│   ├── http://auth.routes.js
│   ├── http://budget.routes.js
│   ├── http://transaction.routes.js
│   └── http://goal.routes.js
├── controllers/
│   ├── http://auth.controller.js
│   ├── http://budget.controller.js
│   ├── http://transaction.controller.js
│   └── http://goal.controller.js
├── .env
├── .gitignore
├── http://package.json
└── http://server.js

### Data Models

#### 1. User
```js
{
  name: { type: String, required: true },
  email: { type: String, required: true, unique: true, lowercase: true },
  password: { type: String, required: true },
  createdAt: { type: Date, default: Date.now }
}
#### 2. Budget
{
  user: { type: ObjectId, ref: 'User', required: true },
  type: { type: String, enum: ['weekly', 'monthly'], required: true },
  period: { type: String, required: true }, // "2026-07" for monthly
  totalIncome: { type: Number, default: 0 },
  categories: [{
    name: String, // "Food", "Rent", "Transport"
    allocated: Number
  }],
  createdAt: { type: Date, default: Date.now },
  updatedAt: { type: Date, default: Date.now }
}
#### 3. Transaction
{
  user: { type: ObjectId, ref: 'User', required: true },
  type: { type: String, enum: ['income', 'expense'], required: true },
  amount: { type: Number, required: true },
  category: { type: String, required: true },
  description: { type: String },
  date: { type: Date, default: Date.now }
}
#### 4. SavingsGoal
{
  user: { type: ObjectId, ref: 'User', required: true },
  title: { type: String, required: true }, // "Emergency Fund"
  targetAmount: { type: Number, required: true },
  currentAmount: { type: Number, default: 0 },
  deadline: { type: Date },
  contributions: [{ amount: Number, date: Date }],
  status: { type: String, enum: ['active', 'completed'], default: 'active' },
  createdAt: { type: Date, default: Date.now }
}
Text index added on `transaction.category` + `transaction.description` for search.

### API Documentation

#### Auth Routes
Method	Endpoint	Description
POST	`/api/auth/register`	Register new user
POST	`/api/auth/login`	Login and receive JWT
#### Budget Routes - Protected
Method	Endpoint	Description
POST	`/api/budgets`	Create new budget
GET	`/api/budgets`	Get all budgets for logged-in user
GET	`/api/budgets/:id`	Get single budget
PUT	`/api/budgets/:id`	Update budget
DELETE	`/api/budgets/:id`	Delete budget
#### Transaction Routes - Protected
Method	Endpoint	Description
POST	`/api/transactions`	Log income or expense
GET	`/api/transactions`	Get all transactions. Query: `?type=expense&category=Food&month=2026-07&search=groceries`
GET	`/api/transactions/analytics`	Get spending by category + monthly trend
PUT	`/api/transactions/:id`	Update transaction
DELETE	`/api/transactions/:id`	Delete transaction
#### Savings Goal Routes - Protected
Method	Endpoint	Description
POST	`/api/goals`	Create savings goal
GET	`/api/goals`	Get all goals with progress %
GET	`/api/goals/:id`	Get single goal
PUT	`/api/goals/:id/contribute`	Add money to goal
DELETE	`/api/goals/:id`	Delete goal
### Example Requests

*1. Register*
POST /api/auth/register
Content-Type: application/json

{
  "name": "David Okoro",
  "email": "david@mail.com",
  "password": "securePass123"
}
*2. Create Budget*
POST /api/budgets
Authorization: Bearer <token>
{
  "type": "monthly",
  "period": "2026-07",
  "totalIncome": 250000,
  "categories": [
    {"name": "Food", "allocated": 60000},
    {"name": "Transport", "allocated": 25000},
    {"name": "Savings", "allocated": 50000}
  ]
}
*3. Log Expense*
POST /api/transactions
Authorization: Bearer <token>
{
  "type": "expense",
  "amount": 3500,
  "category": "Food",
  "description": "Lunch"
}
*4. Get Analytics*
GET /api/transactions/analytics?month=2026-07
Authorization: Bearer <token>
*5. Contribute to Goal*
PUT /api/goals/64f1a2b3c4d5e6f7g8h9i0j/contribute
Authorization: Bearer <token>
{
  "amount": 20000
}
### Validation - Joi
All POST/PUT routes validated with Joi:
- `email`: valid email
- `password`: min 6 characters
- `amount`: number > 0
- `type`: "income" or "expense"
- `category`: required string

### Error Handling
Standardized JSON responses:
{
  "success": false,
  "message": "Not authorized"
}
Status Codes used: `200, 201, 400, 401, 403, 404, 500`

### Setup & Run Locally
*1. Install dependencies*
git clone <repo-url>
cd goalsave-api
npm install
*2. Create .env*
PORT=5000
MONGO_URI=mongodb+srv://<user>:<pass>@cluster.mongodb.net/goalsave
JWT_SECRET=supersecretkey123
NODE_ENV=development
*3. Start server*
npm run dev
Server runs on `http://localhost:5000`

### Deployment
1. Push to GitHub
2. Deploy to http://Render.com as Web Service
3. Add Environment Variables: `MONGO_URI`, `JWT_SECRET`
4. Build Command: `npm install` | Start Command: `npm start`

### Methodology
Agile development: Discovery → Design → Development → Testing → Deployment.  
User interviews done with students and young professionals to keep the API simple.

### Success Metrics
Metric	Target
User registration rate	5,000 users in 6 months
Weekly active users	60% of registered users
Budget completion rate	75%
Savings goal completion	50%
90-day retention	50%
### Conclusion
GoalSave makes budgeting and saving approachable. By focusing on core features and clean data, the API empowers users to take control of their finances and build long-term habits.

---
Built by Betechified advanced Apr'26 Group 3  Capstone 2026
