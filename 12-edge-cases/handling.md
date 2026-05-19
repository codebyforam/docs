# Edge Cases & Failure Recovery Strategy

Enterprise systems must degrade gracefully. This document outlines how the Atma Darshan architecture handles failures and edge cases.

## 1. Concurrency and Race Conditions

* **Simultaneous Bookings:** Handled natively via Firestore Transactions. If 10 users tap "Book" for the final slot, the backend serializes the requests. 9 requests will fail gracefully with a "Slot Full" error.
* **Double Booking / Duplicate Requests:** The client passes an `idempotencyKey` (UUID) with each request. If a network retry causes the function to trigger twice, the backend checks the key and ignores the duplicate.

## 2. Stale Data and Abandoned Flows

* **Stale Slot Data:** If a user leaves the app open, the slot capacity might change in the background. The final source of truth is always the backend transaction at the moment of booking.
* **Abandoned Flows:** If a slot reservation logic is implemented (pessimistic locking), abandoned bookings are swept by a cron job every 5 minutes and capacity is returned.

## 3. Failure Recovery Strategy

When backend operations fail mid-flight, the system requires clear recovery protocols:

* **Failed Transactions:** If a transaction fails due to precondition failure (e.g. no capacity), the backend returns a clear error code. The client UI must handle this and refresh slot availability.
* **Network Recovery:** Mobile clients cache offline actions where appropriate, but booking creation requires an active network. If interrupted, the app alerts the user to retry.
* **Notification Failures:** Notification triggers are asynchronous. If FCM delivery fails, it **does not** roll back the booking transaction. A separate Cloud Task queues the notification for retry.
* **Partial Operation Rollback:** If an admin action (e.g. deleting a service) fails halfway through cleaning up associated data, a cleanup job will catch orphaned documents later.

## 4. Administrative Edge Cases

* **Admin Deleting Active Services:** Admins are blocked from hard-deleting services/slots with active bookings. They must use soft deletes (`isDeleted: true`), preventing data inconsistency.
* **Admin Interruption:** If an admin loses internet while approving 50 bookings, the client syncs the pending approvals locally and dispatches them when the network returns.
