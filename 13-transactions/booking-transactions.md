# Booking Transactions & State Machine

To ensure absolute data integrity and operational safety, all booking lifecycles are strictly managed via Firestore Transactions and a defined state machine.

## 1. Firestore Transactions

A transaction ensures that multiple read/write operations execute atomically.

* **Atomic Updates:** During `createBooking`, the backend reads the slot's `remainingCapacity`, verifies it is > 0, creates the booking document, and decrements the capacity in a single atomic sweep.
* **Concurrency Protection:** If another user modifies the capacity during this sweep, the transaction automatically retries.
* **Slot Capacity Safety:** Transactions guarantee that `remainingCapacity` never drops below 0.

## 2. Booking State Machine

Bookings follow a strict state lifecycle. Clients cannot mutate states directly. Valid transitions include:

* `pending` → `approved` (Admin approval)
* `pending` → `rejected` (Admin rejection, slot capacity returned)
* `pending` → `cancelled` (User cancellation, slot capacity returned)
* `approved` → `completed` (Event finished)
* `approved` → `cancelled` (User/Admin cancellation, slot capacity returned)

### Invalid Transitions

The backend strictly prevents invalid transitions, such as:
* `completed` → `pending`
* `rejected` → `approved` (A new booking must be created instead)

This strict flow is the foundation of enterprise-grade booking lifecycle management.

## 3. Optional: Slot Reservation Logic

For complex booking flows requiring user form completion:
1. **Temporary Slot Lock:** Client requests a lock. Backend decrements capacity and creates a `Reservation`.
2. **Expiration Timer:** The lock is valid for a set time (e.g., 10 mins).
3. **Automatic Release:** If the booking is not finalized, a Cloud Function cron job automatically releases the lock and restores capacity.
