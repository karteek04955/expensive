# Expense Tracker — Full Stack (Backend + Frontend)

A fully working Expense Tracker with a REST API backend (Node.js + Express)
and a vanilla HTML/CSS/JS frontend. No native dependencies, no build step —
runs anywhere Node.js runs.

```
expense-tracker/
├── backend/
│   ├── server.js         # Express app entry point
│   ├── store.js          # JSON-file data store (auto-creates expenses.json)
│   ├── routes/
│   │   └── expenses.js   # CRUD + summary API routes
│   └── package.json
└── frontend/
    ├── index.html
    ├── style.css
    └── app.js             # calls the backend API with fetch()
```

## 1. Prerequisites

- [Node.js](https://nodejs.org) v18+ installed (includes npm)
- [VS Code](https://code.visualstudio.com)
- (Optional but recommended) the **Live Server** extension in VS Code, for the frontend

## 2. Run the Backend API

Open a terminal in VS Code (`` Ctrl+` `` / `` Cmd+` ``):

```bash
cd expense-tracker/backend
npm install
npm start
```

You should see:

```
🚀 Expense Tracker API running at http://localhost:5000
```

A `expenses.json` file will be auto-created in `backend/` the first time you run it,
pre-seeded with one sample income and one sample expense entry — this is your database.

### Verify it's working

Visit **http://localhost:5000/api/health** in a browser, or run:

```bash
curl http://localhost:5000/api/expenses
```

## 2b. Run Both Backend & Frontend Together (one command)

From the **root** `expense-tracker/` folder (not `backend/`):

```bash
npm run install:all   # one-time setup, installs backend deps
npm run dev            # starts backend (5000) + frontend (5500) together
```

Then open **http://localhost:5500** in your browser. Press `Ctrl+C` in the
terminal to stop both at once.

## 3. Run the Frontend (manually, alternative to step 2b)

You don't need a build step — just open the file, or (better) use Live Server so
`fetch()` calls work smoothly:

**Option A — Live Server (recommended)**
1. In VS Code, right-click `frontend/index.html` → **Open with Live Server**
2. It opens at something like `http://127.0.0.1:5500`

**Option B — Just open the file**
1. Double-click `frontend/index.html` to open it directly in your browser

Either way, as long as the backend is running on port 5000, the app will load your
data, show totals, and let you add/edit/delete transactions.

## 4. New Features

### Set Monthly Income
On the dashboard, use the **Monthly Income** box to save your income. It's
stored on the backend (in `expenses.json` under `settings`) and used to
calculate a **Remaining Budget** card: `Monthly Income − Total Expenses`.
Turns red if you've gone over budget.

### Export to Excel
Click **⬇ Export to Excel** above the transaction table to download all your
visible transactions (respects any active search/type filter) as a real
`.xlsx` file — Date, Title, Category, Type, Amount, Notes columns. This runs
entirely in the browser using [SheetJS](https://sheetjs.com), no backend
involved, so it works even offline once the page is loaded.

## 5. API Reference

Base URL: `http://localhost:5000/api/expenses`

| Method | Endpoint                | Description                              |
|--------|--------------------------|-------------------------------------------|
| GET    | `/`                      | List all expenses (filters below)        |
| GET    | `/summary`               | Totals: income, expense, balance, by category |
| GET    | `/:id`                   | Get a single expense                     |
| POST   | `/`                      | Create a new expense                     |
| PUT    | `/:id`                   | Update an expense (partial updates OK)   |
| DELETE | `/:id`                   | Delete an expense                        |

**Query filters on `GET /`:** `category`, `type` (`income`/`expense`), `from`, `to` (dates), `search`

**Example POST body:**
```json
{
  "title": "Groceries",
  "amount": 1200,
  "type": "expense",
  "category": "Food",
  "date": "2026-07-03",
  "notes": "Weekly shopping"
}
```

`title`, `amount`, and `date` are required. `type` defaults to `"expense"`,
`category` defaults to `"General"`.

## 6. Troubleshooting

- **"Could not reach the backend" alert in the browser** → make sure
  `npm start` is running in `backend/` and nothing else is using port 5000.
- **CORS errors** → the backend already has `cors()` enabled for all origins,
  so this shouldn't happen unless you changed the API URL in `app.js`.
- **Change the port** → set an environment variable before starting:
  `PORT=6000 npm start`, and update `API_BASE` in `frontend/app.js` to match.

## 7. Notes on the "AI Triad" Prompt Pipeline

This project maps to the 5-stage pipeline from your mini-project brief:

1. **Role & Context** → this backend defines the domain model (title, amount, type, category, date, notes)
2. **Few-Shot** → seeded sample records establish the expected input/output shape
3. **Structured Outputs** → every API response follows `{ success, data | error }` JSON
4. **Chain-of-Thought** → validation logic in `expenses.js` step-by-step checks each field
5. **Multi-Step Chaining** → frontend (`app.js`) chains fetch → render table → render summary into one pipeline
