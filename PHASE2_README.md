# OWL STORE Ultimate 3.0 — Phase 2 staging candidate

## Changes
- Admin orders list groups EMI installments under one parent order. EMI plan detail lists every installment.
- Customer bill lookup lists parent EMI plans, with a consolidated installment view.
- Server-side sequential payment restrictions on Cashfree start, UPI QR generation, and UPI reference submission. Later installments remain locked.
- Admin can initiate a single Cashfree foreclosure order for the remaining balance. Signed successful webhook settles unpaid installments and marks the parent FORECLOSED. No Cashfree QR is generated.
- New `emi_foreclosure` table; migration `migrations/002_emi_foreclosure.sql`.

## Important — NOT PRODUCTION READY
- Python syntax compilation passed. Automated tests did NOT run (Flask missing in this environment).
- **Do not deploy this ZIP directly to owldigital.shop.** Test with a separate Render staging service and a Neon database branch.
- Apply migrations 001 then 002 to staging before starting the new app.
- Validate duplicate/delayed webhook handling, transaction races, payment retry reconciliation, Cashfree foreclosure gateway order creation and status, manual payment approval, security and mobile UI.
- Foreclosure initiation is admin-only in this candidate. Customer-facing foreclosure request/payment authorization, secure customer OTP authentication, partial payments, and robust transaction locking are outstanding.
- A pending foreclosure blocks normal installments; a failed or abandoned foreclosure currently needs admin reconciliation. Do not expose to production until cancellation/reconciliation is implemented.
- Payment totals must be checked carefully for accounting (parent plan excluded from revenue; foreclosure settled children must be included in collections exactly once).
- Existing UPI QR remains unchanged; no Cashfree QR.

## Migration
1. Take a Neon backup / branch.
2. Apply migrations/001_ultimate3_emi_archive.sql if not already applied.
3. Apply migrations/002_emi_foreclosure.sql to staging.
4. Test on staging before production.
