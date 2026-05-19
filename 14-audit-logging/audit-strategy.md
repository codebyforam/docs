# Audit Logging Strategy

To maintain operational transparency and accountability, all significant state changes and administrative actions are immutably logged.

## 1. Audit Collections

### `audit_logs`
A top-level collection recording administrative actions.
* **Tracked Events:** Modifying user roles, updating slot capacities, deleting (soft-deleting) services, modifying global settings.
* **Schema:** `adminId`, `actionType`, `targetId`, `previousState`, `newState`, `timestamp`.

### `booking_history`
A sub-collection (or dedicated collection) for tracking the lifecycle of an individual booking.
* **Tracked Events:** Every state machine transition (e.g., `pending` -> `approved`).
* **Schema:** `bookingId`, `userId` (actor), `oldStatus`, `newStatus`, `timestamp`.

## 2. Immutability

Audit logs are write-only. Firestore Security Rules explicitly deny `update` and `delete` operations on these collections for all users, including admins.

## 3. Implementation

Logging is handled entirely by Cloud Functions. When an admin calls `approveBooking()`, the function handles the state transition AND writes the corresponding audit log within the same atomic operation or immediately after.
