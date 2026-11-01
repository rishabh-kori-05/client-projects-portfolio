# Pharmacy ERP API — Multi-Tenant Business Backend

## Overview

The Pharmacy ERP API is a modular backend for pharmacy and retail-business workflows. The inspected repository is a TypeScript/Node.js Express API backed by PostgreSQL and Drizzle ORM. Its modules cover authentication, inventory, billing, reports, user administration and prescriptions.

This case study focuses on backend engineering. No frontend screenshots are required to demonstrate API architecture, data modelling and transaction design.

## Problem addressed

A pharmacy ERP backend must coordinate billing, stock, user permissions and pharmacy-specific prescription workflows while keeping tenant data separated and preserving financial consistency.

## Implemented backend structure observed in the repository

- Express application factory separated from HTTP server startup.
- Versioned API routes under `/api/v1`.
- Authentication middleware and route-level permission checks.
- Modules for inventory, billing, reports, users and prescriptions.
- PostgreSQL persistence through Drizzle ORM.
- Central database schema and migration workflow.
- Request context and centralized error handling.
- Health endpoint and graceful server/database shutdown.

## Key engineering details verified from source

### Multi-tenant data model

The Drizzle schema defines tenants and tenant-linked users. The schema documentation and table definitions show tenant identifiers on tenant-scoped records, alongside audit timestamps and actor fields. The database utilities expose tenant-scoped helpers.

This is evidence of tenant-aware data modelling; it is not, by itself, a substitute for a full authorization and cross-tenant isolation test audit.

### Role-based access control

The code defines roles including owner, admin, manager, staff and viewer. Billing, user and prescription routes apply authentication and permission middleware. The user-management routes distinguish permissions for managing users from viewing them.

### Transactional invoice creation

The billing service documents and implements a single transaction for invoice creation. The flow includes idempotency checking, product and batch validation, FEFO stock deduction, tax calculation, invoice and payment inserts, and an audit-log insert.

The controller requires an `Idempotency-Key` UUID for invoice creation. It does not accept the selling price from the client; product/batch data is fetched from the database.

### Inventory and FEFO

The billing service consumes inventory repositories inside the billing transaction to avoid opening a nested transaction. This keeps invoice creation and stock deduction within the same transaction boundary.

### GST-related invoice data

The inspected billing service documents intra-state CGST/SGST calculation and invoice totals in integer paise. The API response formatter includes CGST, SGST and IGST fields, HSN code, place of supply and GSTIN-related fields.

**GSTR-1 export and HSN-level report output have not yet been independently verified from the inspected source files for this portfolio.** They should only be added as confirmed features after reviewing the relevant report implementation and tests.

### Pharmacy workflows

A prescriptions module is mounted in the application. Its routes include listing, creating, reading, updating, deleting and sending a prescription alert, with authentication and permission checks.

## Technology

- Node.js 20+
- TypeScript
- Express 4
- PostgreSQL
- Drizzle ORM and drizzle-kit
- Zod
- JWT authentication
- bcryptjs

## Architecture

See [pharmacy-erp-api.mmd](../architecture/pharmacy-erp-api.mmd).

## API surface observed

| Area | Routes observed |
|---|---|
| Health | `GET /health` |
| Authentication | `/api/v1/auth` |
| Inventory | `/api/v1/inventory` |
| Billing | `/api/v1/billing/invoices` |
| Reports | `/api/v1/reports` |
| Users | `/api/v1/users` |
| Prescriptions | `/api/v1/prescriptions` |

The exact request/response contracts should be taken from the route schemas and controllers before publishing API examples.

## Verification boundaries

This document is based on source files inspected in the private `PHARMACY-API` repository, including `package.json`, `tsconfig.json`, `src/app.ts`, `src/server.ts`, authentication/billing/user/prescription routers, the billing service/controller, database schema and database lifecycle module.

A full build, automated test suite, migration run and live PostgreSQL integration test have **not** been executed as part of preparing this case study. The package scripts define build and type-check commands, but their successful execution is not claimed here.

Do not publish `.env` files, database dumps, tenant/customer data, JWT secrets, private API credentials or proprietary source code.