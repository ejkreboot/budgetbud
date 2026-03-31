# BudgetBud

BudgetBud is a lightweight, browser-based personal budgeting tool. Enter a budget name to load an existing budget or create a new one, then track your balance, recurring expenses, one-time costs, and occasional income to get a multi-month projection of where your finances are headed.

![BudgetBud preview](images/preview.webp)

---

## Features

- **Multiple budgets** — create and switch between named budgets stored in the cloud
- **Starting balance** — set your current account balance as the starting point
- **Monthly recurring expenses** — track regular bills and subscriptions
- **One-time expenses** — schedule irregular costs (e.g. car repairs, travel) to a specific month
- **Occasional income events** — add bonuses or side-income tied to a specific month
- **Financial projection** — visualise your projected balance 3–24 months into the future with an interactive bar chart
- **Summary cards** — at-a-glance view of current balance, total monthly expenses, and 12-month projection
- **Notes** — attach free-text reminders to a budget
- **Auto-save** — changes sync to the backend automatically with a visible save-status indicator

---

## Technology Stack

| Concern | Technology |
|---|---|
| UI | HTML5 + CSS3 + Vanilla JavaScript |
| Backend / Database | [Supabase](https://supabase.com/) (PostgreSQL) |
| Charts | [Chart.js v4](https://www.chartjs.org/) |
| Icons | Google Material Symbols |
| Fonts | Google Fonts (Playfair Display, Nunito) |

No build tools, no package manager, and no compilation step are required. The entire application lives in a single `index.html` file and runs directly in any modern browser.

---

## Architecture

```
index.html
├── <style>        — all CSS (variables, layout, responsive styles)
├── <body>         — HTML structure
│   ├── Login screen    (budget-name input)
│   └── App container
│       ├── Summary cards   (balance · monthly expenses · 12-month projection)
│       ├── Balance section (editable starting balance)
│       ├── Expenses        (monthly recurring expenses CRUD)
│       ├── Income events   (one-time income with month/year picker)
│       ├── Other expenses  (one-time costs with month/year picker)
│       ├── Projection      (bar chart + month-range selector)
│       └── Notes           (free-text textarea)
└── <script>       — all JavaScript
    ├── Supabase client initialisation
    ├── In-memory state object (`data`)
    ├── CRUD helpers for each collection
    ├── Projection calculation engine
    ├── Chart.js rendering
    └── Debounced auto-save
```

### Data model

All budget data is persisted as a single row per named budget in the Supabase `budgets` table:

```jsonc
{
  "name": "string",              // budget identifier (used as lookup key)
  "balance": 1234.56,            // current starting balance
  "expenses": [                  // monthly recurring expenses
    { "id": "uuid", "name": "Rent", "amount": 900 }
  ],
  "income_events": [             // one-time income events
    { "id": "uuid", "name": "Bonus", "amount": 2000, "event_date": "2025-06" }
  ],
  "other_expenses": [            // one-time expenses
    { "id": "uuid", "name": "Car repair", "amount": 500, "event_date": "2025-04" }
  ],
  "notes": "string",             // free-text notes
  "updated_at": "ISO-8601"       // set automatically on each save
}
```

---

## Getting Started

BudgetBud requires no installation. Open `index.html` in a browser:

```bash
# macOS
open index.html

# Linux
xdg-open index.html

# Windows
start index.html
```

For a better local-development experience (avoids browser CORS restrictions), serve the file with any static web server:

```bash
# Python 3
python -m http.server 8000

# Node.js (npx)
npx http-server .
```

Then open `http://localhost:8000` in your browser.

---

## Configuration

The application connects to a shared Supabase project by default. The connection settings are defined near the top of the `<script>` block in `index.html`:

```js
const SUPABASE_URL  = 'https://<project>.supabase.co';
const SUPABASE_ANON_KEY = '<anon-key>';
```

To use your own Supabase backend:

1. Create a new Supabase project.
2. Create a `budgets` table with the following columns:

   | Column | Type |
   |---|---|
   | `id` | `uuid` (primary key, default `gen_random_uuid()`) |
   | `name` | `text` (unique) |
   | `balance` | `numeric` |
   | `expenses` | `jsonb` |
   | `income_events` | `jsonb` |
   | `other_expenses` | `jsonb` |
   | `notes` | `text` |
   | `updated_at` | `timestamptz` |

3. Replace `SUPABASE_URL` and `SUPABASE_ANON_KEY` in `index.html` with your project's values.
4. Open `index.html` in a browser.
