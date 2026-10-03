# Hire Desk System — AV Equipment Rental

## The Problem

An AV equipment rental business was managing bookings manually. There was 
no central system, no real-time visibility into what equipment was out or 
overdue, and no structured way for customers to submit hire requests. Staff 
were tracking everything by hand, chasing returns reactively, and had no 
single place to see the status of all active bookings.

## What I Built

I designed and built a complete hire desk operations system from scratch — 
without writing a single line of code.

The system has three parts working together:

- **A customer-facing booking form** — multi-step, with conditional logic 
  for new vs existing customers, equipment selection linked to live pricing, 
  and an automatic confirmation on submission
- **A real-time staff dashboard** — showing all bookings grouped by status 
  (Waiting, Confirmed, Out, Overdue, Returned) with individual booking 
  detail views including equipment, quantities, daily rates, and totals
- **An automated daily workflow** — running every morning at 08:00, 
  detecting overdue returns via REST API calls, updating their status 
  automatically, and sending email alerts to staff — no manual checking required

## The Result

Bookings flow from customer submission directly into the staff dashboard. 
Staff arrive each morning with an up-to-date view of all active equipment 
and an email summary of anything overdue — without touching a spreadsheet 
or chasing anything manually. Overdue returns are caught and flagged 
automatically, every day, without human intervention.

## Live System

Staff dashboard: https://6hvdk3x1c2.zite.so

## How It Works

### System Overview

| Layer | Tool | Purpose |
|---|---|---|
| Booking form | Fillout | Customer-facing multi-step form with conditional logic |
| Database | Zite Database | Three-table relational model: Customers, Equipment, Bookings |
| Staff dashboard | Zite Apps | Real-time internal view with status cards and filters |
| Automation | Make.com | Scheduled daily workflow for overdue detection |
| Notifications | Gmail | Automated email alerts for overdue bookings |

### Database Structure

Three relational tables:
- **Customers** — name, email, linked bookings
- **Equipment** — item name, daily rate, linked bookings
- **Bookings** — booking number, customer, equipment (up to 3 lines), 
  quantities, days, return date, returned date, total, status

### Booking Lifecycle

Waiting → Confirmed → Out → Overdue (auto-detected) → Returned


### Automation — Overdue Detection

A Make.com scenario runs every day at 08:00.

| Step | Action |
|---|---|
| Trigger | Scheduled daily at 08:00 |
| HTTP 1 | POST to Zite REST API — query bookings where Return Date is past and Returned Date is empty |
| Iterator | Loop through each matching booking record |
| HTTP 2 | PATCH each record's Status field to "Overdue" |
| Gmail | Send email alert with booking details for each overdue item |

Verified execution: Oct 2 2026, 8:00:35 AM — Status: Success, Duration: 4 seconds.

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
