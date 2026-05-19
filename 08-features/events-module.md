# Events & Services Module

The Events & Services module defines the core offerings of the Atma Darshan ashram.

## 1. Core Logic

Services can allow multiple bookings, support individual/group/batch booking, and define capacities and slot durations.

* **Backend Validation:** Booking logic is NOT executed in the Flutter app. The backend exclusively handles slot validation, capacity checks, and booking creation.
* **Transactional Integrity:** Bookings are processed via Firestore Transactions to ensure that concurrent booking attempts do not overfill a slot.

## 2. Slot Management

Slots are individual instances of a Service.
* **Soft Deletes:** An admin deleting an active service or slot utilizes a soft delete (`status: "archived"`). This preserves historical data.

## 3. Booking State Machine

Bookings follow a strict state machine lifecycle controlled entirely by the backend:
`pending` → `approved` | `rejected`
`approved` → `completed` | `cancelled`

Clients cannot bypass or illegally mutate these states (e.g., cannot go from `completed` → `pending`).
