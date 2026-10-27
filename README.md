# ThetaRay Data Science Exercise: Datasets

This repository holds the datasets for the ThetaRay Data Science screening exercise. It contains data only. The instructions and tasks are in the exercise notebook.

The data is an anonymized data set from a European bank, covering 2018 to 2023.

## Files

| File | Rows | Description |
|:--|--:|:--|
| `accounts.csv` | 4,500 | Each record describes static characteristics of an account. |
| `clients.csv` | 4,500 | Each record describes characteristics of a client. |
| `client_accounts.csv` | 4,500 | Each record links a client to an account. |
| `standing_orders.csv` | 6,471 | Each record describes a standing payment order of an account. |
| `transactions.csv` | 1,027,938 | Each record describes one transaction on an account. |
| `fx_rates.csv` | 142 | Monthly exchange rates. |

## Relations

```
clients ──< client_accounts >── accounts ──< transactions
                                    │
                                    └──────< standing_orders
```

- `client_accounts.client_id` → `clients.client_id`
- `client_accounts.account_id` → `accounts.account_id`
- `transactions.account_id` → `accounts.account_id`
- `standing_orders.account_id` → `accounts.account_id`
- `fx_rates` is joined on month and currency.

## Loading the data

```python
import pandas as pd

DATA_DIR = "."  # folder (or URL) containing the exercise .csv files

accounts = pd.read_csv(f"{DATA_DIR}/accounts.csv")
clients = pd.read_csv(f"{DATA_DIR}/clients.csv")
client_accounts = pd.read_csv(f"{DATA_DIR}/client_accounts.csv")
standing_orders = pd.read_csv(f"{DATA_DIR}/standing_orders.csv")
transactions = pd.read_csv(f"{DATA_DIR}/transactions.csv")
fx_rates = pd.read_csv(f"{DATA_DIR}/fx_rates.csv")
```

## Data dictionary

All dates are strings in `YYYY-MM-DD` format.

### accounts

| Column | Type | Description |
|:--|:--|:--|
| `account_id` | integer | Account identifier (primary key) |
| `district_id` | integer | District of the account's branch |
| `statement_frequency` | text | How often statements are issued: `MONTHLY`, `WEEKLY` or `AFTER_TRANSACTION` |
| `open_date` | date | Date the account was opened |

### clients

| Column | Type | Description |
|:--|:--|:--|
| `client_id` | integer | Client identifier (primary key) |
| `birth_date` | date | Client's date of birth |
| `sex` | text | `F` or `M` |
| `district_id` | integer | District of the client's address |

### client_accounts

| Column | Type | Description |
|:--|:--|:--|
| `link_id` | integer | Link identifier (primary key) |
| `client_id` | integer | Client (foreign key to `clients`) |
| `account_id` | integer | Account (foreign key to `accounts`) |
| `role` | text | Role of the client on the account: `OWNER` |

### standing_orders

| Column | Type | Description |
|:--|:--|:--|
| `order_id` | integer | Order identifier (primary key) |
| `account_id` | integer | Account the order is paid from (foreign key to `accounts`) |
| `recipient_bank` | text | Bank of the recipient (code) |
| `recipient_account` | integer | Account of the recipient |
| `amount` | decimal | Amount of the order |
| `purpose` | text | `INSURANCE`, `HOUSEHOLD` (household bills), `LEASING` or `LOAN_PAYMENT` |

### transactions

| Column | Type | Description |
|:--|:--|:--|
| `transaction_id` | integer | Transaction identifier (primary key) |
| `account_id` | integer | Account (foreign key to `accounts`) |
| `date` | date | Date of the transaction |
| `type` | text | `CREDIT` (money in) or `DEBIT` (money out) |
| `operation` | text | `CASH_DEPOSIT`, `CASH_WITHDRAWAL`, `CARD_WITHDRAWAL`, `TRANSFER_IN` (from another bank) or `TRANSFER_OUT` (to another bank); empty for interest and fees |
| `amount` | decimal | Amount of the transaction |
| `currency` | text | Transaction currency: `EUR`, `USD` or `GBP` |
| `balance` | decimal | Account balance after the transaction |
| `purpose` | text | `INSURANCE`, `STATEMENT_FEE`, `INTEREST`, `PENALTY_INTEREST` (interest on a negative balance), `HOUSEHOLD`, `PENSION` or `LOAN_PAYMENT` |
| `partner_bank` | text | Bank of the counterparty (code), for transfers |
| `partner_account` | integer | Account of the counterparty, for transfers |

### fx_rates

| Column | Type | Description |
|:--|:--|:--|
| `month` | text (`YYYY-MM`) | Month the rate applies to |
| `currency` | text | `USD` or `GBP` |
| `rate_to_eur` | decimal | EUR per 1 unit of `currency` (monthly average) |
