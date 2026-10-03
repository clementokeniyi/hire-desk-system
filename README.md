# Hire Desk System — AV Equipment Rental

A no-code booking and operations system built for an AV equipment rental 
business. Customers submit hire requests through a multi-step online form. 
Staff manage all bookings through a real-time internal dashboard. A 
scheduled automation detects overdue returns daily and sends email alerts — 
no code written.

## System Overview

| Layer | Tool | Purpose |
|---|---|---|
| Booking form | Fillout | Customer-facing multi-step form with conditional logic |
| Database | Zite Database | Three-table relational model: Customers, Equipment, Bookings |
| Staff dashboard | Zite Apps | Real-time internal view with status cards and filters |
| Automation | Make.com | Scheduled daily workflow for overdue detection |
| Notifications | Gmail | Automated email alerts for overdue bookings |

## Database Structure

Three relational tables:
- **Customers** — name, email, linked bookings
- **Equipment** — item name, daily rate, linked bookings
- **Bookings** — booking number, customer, equipment (up to 3 lines), quantities, days, return date, returned date, total, status

## Booking Lifecycle
Waiting → Confirmed → Out → Overdue (auto-detected) → Returned

The dashboard surfaces each status in real time with counts.
Overdue is highlighted in red when active.

## Customer Booking Form

Multi-step Fillout form (7 pages: Your details → Booking details → Equipment lines 1–3 → Submit → Confirmation). Includes conditional logic: existing customers select their name from a linked lookup; new customers enter their details directly. Form submission creates a new record in the Zite Bookings table via native integration with field mapping.

## Automation — How Overdue Detection Works

A Make.com scenario runs every day at 08:00.

| Step | Action |
|---|---|
| Trigger | Scheduled daily at 08:00 |
| HTTP 1 | POST to Zite REST API — query bookings where Return Date is past and Returned Date is empty |
| Iterator | Loop through each matching booking record |
| HTTP 2 | PATCH each record's Status field to "Overdue" |
| Gmail | Send email alert with booking details for each overdue item |

Execution history confirms the scenario runs successfully on schedule (verified: Oct 2 2026, 8:00:35 AM, Status: Success, Duration: 4 seconds).

## Live System

Staff dashboard: https://6hvdk3x1c2.zite.so

## Screenshots

See `/screenshots` for the full system walkthrough:

- `01-dashboard-overview` — real-time status cards with active Overdue alert
- `02-make-automation-diagram` — full four-step automation pipeline
- `03-make-execution-history` — confirmed scheduled runs with success status
- `04-booking-detail` — individual booking view with line items and totals
- `05-form-customer-details` — conditional logic for new vs existing customers
- `06-form-equipment-selection` — equipment picker linked to database rates
- `07-form-confirmation` — customer-facing submission confirmation
- `08-form-database-mapping` — Fillout to Zite field mapping configuration
- `09-database-overdue-record` — auto-detected overdue record in database
- `10-database-bookings` — full bookings table with status labels
- `11-database-customers` — customers table with linked booking counts
- `12-database-equipment` — equipment catalogue with daily rates

## Built With

- [Zite](https://zite.so) — database and staff dashboard
- [Fillout](https://fillout.com) — customer booking form
- [Make.com](https://make.com) — automation (scheduled + REST API)
- Gmail — email notifications
