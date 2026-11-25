# WP Industrial ERP

**A complete ERP for small industrial painting companies, built as a WordPress plugin.**
It covers the whole workflow of a blasting & coating shop: quotes, orders, delivery notes per truck, customer-signed work reports, monthly progress certifications, quality control (ITP inspection reports), paint warehouse, treasury, CRM and full double-entry accounting (Spanish PGC for SMEs).

![Version](https://img.shields.io/badge/version-0.27.0-e8711a)
![WordPress](https://img.shields.io/badge/WordPress-plugin-21759b)
![PHP](https://img.shields.io/badge/PHP-7.4%2B-777bb4)
![UI](https://img.shields.io/badge/UI-Spanish-1b2f4b)
![Status](https://img.shields.io/badge/status-in%20production-2e7d32)

![Dashboard](docs/img/01-panel.png)

> 🔒 **The source code is private.** This repository documents the project: features, workflow, architecture and screenshots. All screenshots were taken on a clean install with invented sample data (“Acme Coatings”, “NORTEC”, “ORION”…); no real company, customer or price data is shown. The user interface is in Spanish.
>
> Interested in a live demo or a code walkthrough? Contact me on [LinkedIn](https://www.linkedin.com/in/sergio-garcia-marquez-138b29398/).

---

## Contents

1. [Why](#why)
2. [Features at a glance](#features-at-a-glance)
3. [Workflow](#workflow)
4. [Module tour (screenshots)](#module-tour)
5. [Deployment](#deployment)
6. [Architecture](#architecture)
7. [Changelog](#changelog)
8. [Author](#author)

---

## Why

Small industrial companies often depend on legacy desktop ERPs plus a pile of spreadsheets (daily work reports, packing lists, certifications, inspection reports per truck). In the original company every work report was handled by three people: the foreman wrote it by hand, someone typed it into Excel, and then it was typed again into the ERP.

This plugin puts everything in one place, inside the company website (`/gestion/`), self-hosted, with no external servers or licences:

- **Enter data once.** The hours the foreman records end up in the customer's work report, the monthly certification, the invoice, the job cost and the accounting.
- **Built for a painting shop.** Trucks, paint systems, dry film thickness, dew point, inspection reports, contract price lists, subcontractor “valued statements”…
- **Office and field.** Works on desktop and on the workshop's mobile phones, with role-based permissions (the foreman never sees prices).

## Features at a glance

| Area | Includes |
|---|---|
| **Sales** | Customers · Quotes · Orders · Delivery notes (per truck) · Certifications · Invoices · Sales catalogue (1,100+ items) · Price lists · Email templates |
| **Purchasing** | Suppliers · Purchase orders · Goods received notes · Supplier invoices |
| **Production** | Jobs/projects · Customer work reports (two numbering series) · Internal timesheets · Quality control (ITP) · Workers & categories |
| **Warehouse** | Stock per warehouse · Issue to job, return, reassign, transfer · Stocktaking · Suggested purchase orders |
| **Treasury** | Receivables · Payables · Monthly cash-flow forecast |
| **CRM** | Follow-ups, assignable tasks, unanswered quotes, lost-quote reasons |
| **Accounting** | Automatic journal entries · Chart of accounts · Ledgers · Trial balance · Balance sheet & P&L · VAT books · Tax form drafts (303/390/111/190/347) · Job costing · Fixed assets & depreciation · Year-end closing |
| **Admin** | Company data · Per-area permissions · SMTP accounts & Outlook signatures · Contract price lists · Import from the legacy ERP · Encrypted backups · Error log · Security dashboard |
| **Extras** | AI assistant (Claude API) · Dark mode · Company colours · Export everything to Excel · Working-year selector |

## Workflow

```mermaid
flowchart LR
    Q[Quote] -->|accepted| O[Order + Job]
    O --> D[Delivery notes<br/>per truck]
    O --> WR[Customer work report]
    TS[Foreman timesheet] -->|→ create report| WR
    D --> ITP[Quality report<br/>per truck]
    D --> C[Monthly<br/>certification]
    WR --> C
    D --> I[Invoice]
    WR --> I
    C --> I
    I --> P[Payment]
    I --> JE[Automatic<br/>journal entry]
    P --> JE
    WH[Warehouse] -->|issue to job| JC[(Job cost)]
    TS --> JC
```

Main automations:

- **Accepted quote** → creates the order and opens the job.
- **Order** → partial delivery notes per truck; the order closes itself when fully delivered.
- **Delivery notes & work reports** → invoice or monthly certification; “Invoice everything pending” creates one invoice per customer in one click.
- **Invoice** → sequential number, due dates from the payment terms and automatic journal entry.
- **Purchases** → stock in and updated item cost; if bought for a job, charged to that job.
- **Each job** computes ordered, delivered, invoiced, real cost (labour + materials + purchases), margin, hours and m²/h.
- **Stock below minimum** → purchase order proposal.

---

## Module tour

### Dashboard

A per-user, service-desk style dashboard filtered by month, quarter, year or date range, job and customer. Every tile opens the filtered list.

![Dashboard](docs/img/01-panel.png)

**Customisable:** drag widgets, resize or remove them. Starter templates (Management, Office, Production & quality, Accounting) and 30+ widgets: overdue receivables, trucks without inspection report, reports not yet emailed, my tasks, quarterly VAT, margin per job…

![Customise dashboard](docs/img/36-panel-personalizar.png)

Personal light/dark mode. Documents always render on white, like paper.

![Dark mode](docs/img/37-panel-oscuro.png)

### Sales: quotes → orders → delivery notes → invoices

Customer records with their price list, payment terms, work-report series, accounting sub-account and who receives each document type.

![Customers](docs/img/02-clientes.png)

Filterable lists with document status and Excel export (with or without lines).

![Quotes](docs/img/03-ofertas.png)

A quote carries the paint system, surface in m², estimated margin from catalogue costs, and actions to email, download as PDF, duplicate or accept it.

![Quote](docs/img/04-oferta.png)

The order shows ordered, delivered and pending quantities per line. Delivery notes and work reports are created from here.

![Order](docs/img/05-pedido.png)

Delivery notes per truck, with plate number, **email tracking** (“✉ Not sent” in red until it is sent to the customer) and invoicing status.

![Delivery notes](docs/img/06-albaranes.png)

![Delivery note](docs/img/07-albaran.png)

Invoices with due dates and collection status.

![Invoices](docs/img/09-facturas.png)

### Jobs and certifications

The job record gathers everything: ordered, delivered, work reports, invoiced, real cost, margin, inspection reports, hours and m² delivered; the technical specification (paint system, RAL, ISO 12944 corrosivity, standards, inspection requirements) and the customer's preferred certification format.

![Job](docs/img/10-obra.png)

**Monthly certification** in each customer's format, e.g. a subcontractor “valued statement”: budget, previous cumulative, cumulative and this month per line, plus the month's work reports as extra lines. Exported to PDF and to Excel with live formulas (CERTIFICATION, MEASUREMENT and REPORTS sheets), emailed and invoiced.

![Certification](docs/img/08-certificacion.png)

### Work reports and timesheets

**Work report** = the report the customer signs (series PT, or PR for customers with a contract price list). Continuous numbering per series (`PT/260673`). Mark it as signed, invoice it directly or include it in the monthly certification.

![Work reports](docs/img/11-partes-trabajo.png)

Printed in the classic “service report” layout.

![Work report](docs/img/12-parte.png)

The editor shows the number in advance, suggests the customer's previous order and work-order numbers, and is filled in blocks: tradesmen (hours, overtime) and paint/materials.

![Work report editor](docs/img/13-parte-editor.png)

**Internal timesheets**: what the foreman dictates. They replace the daily Excel (which can also be imported as is). “→ Create report” turns them into the customer's work report with prices filled in by the ERP.

![Timesheets](docs/img/14-partes-horas.png)

![Daily timesheet](docs/img/15-parte-dia.png)

Contract price list for the PR series (sample codes and prices):

![Contract prices](docs/img/34-precios-contrato.png)

### Quality control (ITP)

One inspection report per delivery note/truck, pre-filled from the job's paint system and the paint batches issued. It computes the **dew point** and checks ΔT ≥ 3 °C, RH ≤ 85 %, surface profile and dry film thickness (mean ≥ nominal and minimum ≥ 80 %, ISO 19840). Photos, PDF and email to the customer.

![Inspection reports](docs/img/16-ppis.png)

![Inspection report](docs/img/17-ppi.png)

### Warehouse

Stock per warehouse, stock value and items below minimum.

![Stock](docs/img/18-stock.png)

Material movements: issue to a job (or a specific truck), return to warehouse, reassign to another job, transfer, manual receipts and write-offs. The job's material cost is what really leaves the warehouse, not what the report says.

![Material movements](docs/img/19-operaciones.png)

### Treasury and CRM

Receivables and payables with due dates, and a monthly cash-flow forecast.

![Receivables](docs/img/20-cobros.png)

![Cash-flow forecast](docs/img/21-prevision.png)

Sales follow-up: next actions, unanswered quotes, win rate and reasons for lost quotes.

![CRM](docs/img/22-seguimiento.png)

### Accounting

Double-entry bookkeeping with the Spanish chart of accounts for SMEs. Entries for invoices, collections and payments are created and rebuilt automatically; manual entries (payroll, loans, insurance…) can be duplicated.

![Journal](docs/img/23-asientos.png)

Abridged balance sheet and P&L compared with the previous year:

![Balance sheet and P&L](docs/img/24-balances.png)

Tax form drafts (VAT 303/390, withholdings 111/190, third parties 347):

![Tax forms](docs/img/25-modelos.png)

Cost accounting per job (income, expenses, warehouse material and labour):

![Job costing](docs/img/26-analitica.png)

### Reports and price lists

Result per job: delivered, labour, materials, purchases, margin, hours and m²/h.

![Result per job](docs/img/27-informe-obras.png)

Price lists: the catalogue is the general list; “Raise prices” applies a % after saving a copy, and a customer can keep an older list.

![Price lists](docs/img/28-tarifas.png)

Global search (document number, customer, job, truck…) across all years:

![Search](docs/img/35-buscar.png)

### Settings, permissions and data import

![Settings](docs/img/29-ajustes.png)

**Per-area permissions**: each person only sees in the menu, the search and their dashboard what they are allowed to. The workshop foreman never sees prices or amounts anywhere (not even in the page source).

![Permissions](docs/img/30-permisos.png)

**Import from the legacy ERP (golden.net)**: upload its Excel exports (customers, suppliers, items, chart of accounts and opening balances, projects, orders, delivery notes, invoices, receivables and payables) and the daily timesheet workbook. Preview before importing, automatic backup, no duplicates on re-import, chunked upload for large files.

![Import](docs/img/31-importar.png)

**Error log**: any failure shows a red box with a copy-ready report, and everything is grouped here.

![Error log](docs/img/32-errores.png)

### Security

- Two-step verification (Google / Microsoft Authenticator), mandatory for admins and anyone who sees amounts; 10 recovery codes.
- Session expiry (12 h, or 120 min idle) and “sign out of other devices”.
- Security headers (CSP, HSTS, X-Frame-Options…), IP lockout after failed logins, XML-RPC disabled, usernames hidden from the REST API.
- Email alerts (new IP, lockouts, exports…) and a full audit trail.
- Daily backups that only leave the server **encrypted** (AES-256 ZIP). SMTP passwords and the API key are stored encrypted.

![Security checks](docs/img/33-seguridad.png)

![Two-step verification](docs/img/39-dos-pasos.png)

### Mobile (workshop foreman)

The whole app is responsive. The foreman logs in with his own role and only sees what he needs: his dashboard, the **daily timesheet** (tap a worker to set 8 h; “same crew as last day on this job”) and the customer work report, with the camera to upload the signed paper copy.

<table>
<tr>
<td><img src="docs/img/40-movil-panel.png" width="240" alt="Mobile dashboard"></td>
<td><img src="docs/img/41-movil-partes.png" width="240" alt="Daily timesheet on mobile"></td>
<td><img src="docs/img/42-movil-parte-trabajo.png" width="240" alt="Work report on mobile"></td>
<td><img src="docs/img/43-movil-menu.png" width="240" alt="Mobile menu"></td>
</tr>
<tr>
<td align="center">Foreman dashboard</td>
<td align="center">Daily timesheet</td>
<td align="center">Work report (no prices)</td>
<td align="center">Menu (office user)</td>
</tr>
</table>

### AI assistant

With an Anthropic API key, office users get a “💬 Asistente” button. It answers questions about documents, jobs and margins, receivables, stock, catalogue and hours, and can create quotes **as drafts** and log follow-ups. It cannot issue, delete, collect or change prices, and has a monthly spending cap.

---

## Deployment

The ERP runs as a plugin inside an existing WordPress site (the company website), under a private `/gestion/` path served without the theme.

For development and demos it ships with a **Docker Compose** stack:

```mermaid
flowchart LR
    U[Browser<br/>desktop / mobile] -->|HTTP :8080| WP[wordpress<br/>PHP 8.3 + Apache]
    WP --> DB[(mariadb 11.4<br/>volume db_data)]
    S[setup<br/>one-shot container] -->|install WP, activate ERP,<br/>load sample data| WP
    S --> DB
    P[ERP plugin<br/>mounted read-only] -.-> WP
```

- `db`: MariaDB with a persistent volume and a health check; WordPress waits until the database is healthy.
- `wordpress`: official image, the plugin mounted read-only, file editing from the admin disabled.
- `setup`: one-shot container running as `www-data` that installs WordPress, activates the plugin, loads the sample data and creates a foreman user. Idempotent: running it again skips what is already done.
- Secrets live in a `.env` file that is never committed.

In production: HTTPS reverse proxy, two-step verification on, encrypted daily backups and SMTP email.

Roles:

| Role | Access |
|---|---|
| Administrator | Everything |
| Office | Everything except Settings |
| Workshop foreman | Work reports & timesheets (no prices) |
| Management user | Whatever is ticked in Permissions |

## Architecture

- **WordPress plugin** in plain PHP, no external dependencies or frameworks. The app is served at `/gestion/` without the theme, with its own CSS and JS (fast and mobile-friendly).
- **Database**: custom tables in the WordPress database with the `wp_pds_` prefix (documents, lines, jobs, reports, inspections, stock, journal entries, price lists, audit…).
- **Excel**: export with live formulas (certifications with SUMIFS, filtered measurement sheets) and row-by-row `.xlsx` reading to import large files with little memory.
- **Email**: several SMTP accounts with imported Outlook signatures; recipient rules per customer/job/document and templates with variables.
- **Backups**: daily, managed from WordPress, AES-256 encrypted.

Main modules:

| Module | Responsibility |
|---|---|
| Core | Bootstrap, routing, roles, schema and migrations |
| Documents | Quotes, orders, delivery notes, invoices (logic, views, actions) |
| Work reports | PT/PR reports, price lists, certifications, PDF and Excel output |
| Production | Timesheets and weekly summary |
| Quality | Inspection reports (ITP) |
| Warehouse | Stock, movements, stocktaking |
| Accounting | Journal, ledgers, statements, tax forms, closing |
| Import | Legacy ERP exports and Excel workbooks |
| Security | Headers, 2FA, sessions, lockouts, audit |
| Backups | Encrypted backups and restore |
| Assistant | AI assistant (Claude API) |

## Changelog

Full release notes (in Spanish) in [`CHANGELOG.md`](CHANGELOG.md). Highlights:

| Version | Highlight |
|---|---|
| 0.27 | Two-step verification, sessions, security alerts, encrypted backups |
| 0.26 | Security headers, IP lockout, security dashboard |
| 0.25 | Chunked upload for large imports; reuse number when deleting the last document |
| 0.24 | From foreman timesheet to customer work report in one click; worker import |
| 0.23 | Import the daily timesheet workbook; price-free reports for the foreman |
| 0.22 | Number shown when creating/duplicating; email delivery tracking |
| 0.21 | Simpler block-based work reports; PT/PR numbering series; contract prices |
| 0.20 | Dashboard filter by period, job and customer |
| 0.19 | SMTP accounts and Outlook signatures; export everything to Excel |
| 0.18 | Import documents from the legacy ERP |
| 0.17 | Working-year selector |
| 0.16 | Import records and chart of accounts from the legacy ERP |
| 0.15 | Full accounting, tax forms, year-end closing |
| 0.14 | Recipients per customer/job and email templates |
| 0.13 | Certification in each customer's format |
| 0.12 | Work reports, price lists and certifications |
| 0.10 | Workers by category; dark mode and company colours |
| 0.9 | Per-area permissions |
| 0.8 | Customisable dashboard, photo optimisation |
| 0.7 | Quality control, attachments, technical specification |
| 0.6 | Multiple warehouses and material movements |
| 0.5 | AI assistant |
| 0.4 | Sample data |

## Author

**Sergio García Márquez** – IT Systems & Network Administrator
[LinkedIn](https://www.linkedin.com/in/sergio-garcia-marquez-138b29398/) · [GitHub](https://github.com/Bandoler0o)
