# Data Dictionary

**Client (fictional):** Karoo Industrial Supplies (Pty) Ltd
**Period:** FY2025 (1 Jan - 31 Dec 2025). A few entries dated January 2026 are included on purpose.
**Currency:** ZAR. **VAT:** 15%.
**Client policy:** manual journals of R250,000 or more require CFO approval; smaller ones require Financial Controller approval.
**Note:** the ledger contains activity only (no opening balances).

All data is synthetic. It is safe to publish.

## journal_headers.csv (one row per journal entry)
| Column | Meaning |
|---|---|
| je_id | Unique journal entry number |
| posting_date | Accounting date of the entry |
| entry_date | Date the entry was actually keyed into the system |
| period | YYYY-MM |
| fiscal_year | 2025 or 2026 |
| source | AR, AP, CASH, PAYROLL, DEPR, BANK or MANUAL |
| description | Narrative |
| created_by | user_id of preparer |
| approved_by | user_id of approver (blank = none recorded) |
| is_manual | 1 = manual journal, 0 = system/sub-ledger |
| doc_ref | Source document number (invoice, receipt, etc.) |
| counterparty_id | Vendor (V###) or customer (C###) |

## journal_lines.csv (one row per debit/credit line)
je_id, line_no, account_id, debit, credit

## chart_of_accounts.csv
account_id, account_name, account_type, normal_balance, fs_line

## users.csv
user_id, full_name, role, department, hire_date, termination_date (blank = still employed), is_system_account

## counterparties.csv
counterparty_id, counterparty_name, counterparty_type (Vendor/Customer)

## public_holidays.csv
holiday_date, holiday_name (South Africa, 2025)
