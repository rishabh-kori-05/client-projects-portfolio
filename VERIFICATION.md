# Verification status

This portfolio intentionally separates source-observed facts from claims that still need runtime evidence.

## Pharmacy ERP API

### Inspected
- `package.json` and TypeScript configuration.
- Express app factory and server lifecycle.
- Authentication, billing, user and prescription routers.
- Billing service and controller.
- Drizzle schema and database lifecycle module.

### Not executed during portfolio preparation
- `npm run build`
- `npm run type-check`
- Automated test suite (no test script was present in the inspected package.json).
- PostgreSQL integration tests.
- Migration execution against a clean database.
- Cross-tenant authorization tests.

### Claims intentionally withheld
- Production readiness or security certification.
- Verified GSTR-1 export / HSN report output (until report implementation is inspected).
- Performance, scale, uptime or customer metrics.

## TradeDesk POS

Screenshots supplied for the portfolio show the dashboard, hawker billing and stock-report UI. Financial and party data require redaction. One stock report screenshot had no results.

## TradeDesk Sentinel

Screenshots supplied show dashboard, devices and activity log. Devices were offline in the captured state. Windows 10 Pro validation and deployment were pending in the project notes.