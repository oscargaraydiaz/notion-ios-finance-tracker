# 💸 Notion Finance Tracker — iOS Shortcuts + Notion API

> Automated personal finance system: register expenses and income in real time from iPhone directly into a Notion database, with dual currency support (USD/COP), automatic ID generation and smart categorization.

![Notion](https://img.shields.io/badge/Notion-API%20v1-black?logo=notion)
![iOS](https://img.shields.io/badge/iOS-Shortcuts-blue?logo=apple)
![Currency](https://img.shields.io/badge/Currency-USD%20%2B%20COP-green)
![Status](https://img.shields.io/badge/Status-Production-brightgreen)

---
<img width="2588" height="1484" alt="image" src="https://github.com/user-attachments/assets/da795377-095d-4f72-bb9f-0745b7e904ee" />

## 🎯 What it does

A single tap on your iPhone opens a guided shortcut that:

1. Asks for the transaction name
2. Lets you choose the currency (USD or COP)
3. Asks for the amount in the correct currency
4. Lets you select type, account, category and add optional notes
5. Auto-captures today's date in ISO 8601 format
6. Sends a POST request to the Notion API
7. Creates a new record with a unique auto-incremented ID (TXN-1, TXN-2...)
8. Confirms success with an alert

All in under 15 seconds, without opening Notion.

---

## 🗄️ Database Schema

| Field | Type | Options |
|---|---|---|
| Nombre | Title | Transaction name |
| Valor | Number | USD amount |
| Valor COP | Number | COP amount |
| Moneda | Select | USD / COP |
| Tipo | Select | Ingreso / Gasto Variable / Gasto Fijo / Obligacion / Inversion / Ahorro / Deuda |
| Cuenta | Select | PayPal / Bancolombia / Nequi / Efectivo / Rappi / trii.co |
| Categoria | Select | 14 categories |
| Fecha | Date | ISO 8601 auto-captured |
| Notas | Rich Text | Optional notes |
| ID | Unique ID | Auto: TXN-1, TXN-2... |
| Fecha de creación | Created Time | Auto-timestamp |

---

## ⚙️ Setup

### 1. Notion Integration

```
1. Go to developers.notion.com
2. Click "New Integration"
3. Name it (e.g. "iOS Finance")
4. Copy the Internal Integration Secret (starts with ntn_...)
5. Open your Notion database → ... → Connections → connect your integration
```

### 2. Get your Database ID

```
Open your database in browser. The URL looks like:
notion.so/Your-Workspace/DATABASE_ID?v=...

Copy the DATABASE_ID (32 characters, with or without hyphens)
```

### 3. iOS Shortcut Structure

```
Actions in order:
1. Ask for Input (Text)      → "Referencia"
2. Choose from List          → USD / COP → "Moneda"
3. If Moneda is USD
   ├── Ask for Input (Text)  → "MontoUSD"
   └── Text: 0               → "MontoCOP"
   Else
   ├── Ask for Input (Text)  → "MontoCOP"
   └── Text: 0               → "MontoUSD"
   End If
4. Choose from List          → Tipos → "Tipo"
5. Choose from List          → Cuentas → "Cuenta"
6. Choose from List          → Categorias → "Categoria"
7. Ask for Input (Text)      → "Notas" (optional)
8. Get Current Date          → Format: yyyy-MM-dd → "FechaISO"
9. Text (JSON body)          → See template below
10. Get Contents of URL      → POST to Notion API
11. Show Alert               → "Registered in Notion ✓"
```

### 4. JSON Body Template

```json
{
  "parent": {"database_id": "YOUR_DATABASE_ID"},
  "properties": {
    "Nombre": {"title": [{"text": {"content": "Referencia"}}]},
    "Valor": {"number": MontoUSD},
    "Valor COP": {"number": MontoCOP},
    "Moneda": {"select": {"name": "Moneda"}},
    "Tipo": {"select": {"name": "Tipo"}},
    "Cuenta": {"select": {"name": "Cuenta"}},
    "Categoria": {"select": {"name": "Categoria"}},
    "Fecha": {"date": {"start": "FechaISO"}},
    "Notas": {"rich_text": [{"text": {"content": "Notas"}}]}
  }
}
```

> ⚠️ `MontoUSD` and `MontoCOP` must be inserted as Shortcut variables (blue pills), NOT as quoted strings. Request them as **Text input** (not Number) to avoid iOS type-casting issues.

### 5. HTTP Request Configuration

```
URL:    https://api.notion.com/v1/pages
Method: POST
Body:   File → Text (the JSON block above)

Headers:
  Authorization:  Bearer YOUR_INTEGRATION_SECRET
  Content-type:   application/json
  Notion-Version: 2022-06-28
```

---

## 🐛 Troubleshooting

| Error | Cause | Fix |
|---|---|---|
| `401 unauthorized` | Wrong or missing Bearer token | Add `Bearer ` before your secret |
| `404 not found` | Integration not connected to DB | Go to DB → ... → Connections |
| `400 invalid_json` | Number variable quoted as string | Use Text input (not Number) for amounts |
| `400 validation_error` | Field name mismatch | Check exact field names, no trailing spaces |
| `400 body should not be present` | Wrong body type | Set body type to File → Text |

---

## 📁 Project Structure

```
notion-ios-finance-tracker/
├── README.md
├── shortcut/
│   └── Gastos.shortcut          # iOS Shortcut file
├── notion/
│   └── database-schema.json     # DB schema reference
└── docs/
    └── setup-guide.md           # Step by step setup
```

---

## 🛠️ Tech Stack

- **Notion API** v1 — database as backend
- **iOS Shortcuts** — native iPhone automation
- **REST / JSON** — HTTP POST requests
- **No third-party apps required**

---

## 👤 Author

**Oscar Fabian Garay Diaz**  
Systems Engineer · Data Analyst · AI Automation Builder  
Zarzal, Colombia · [LinkedIn](https://www.linkedin.com/in/oscargarayd/) · [GitHub](https://github.com/oscargaraydiaz)

---

## 📄 License

MIT — free to use, modify and share.
