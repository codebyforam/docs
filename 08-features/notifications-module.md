# Notifications Module

The Notifications Module manages transactional and promotional communication with users via Firebase Cloud Messaging (FCM) and email fallbacks.

## 1. Notification Architecture

Notifications are strictly triggered by the backend after successful transactions.

* **Trigger:** When the `updateBookingStatus()` or `createBooking()` Cloud Function completes a Firestore Transaction successfully, it asynchronously dispatches a notification payload.

## 2. Failure Recovery

> [!IMPORTANT]
> Notification failures must NOT break the booking flow.

If FCM returns an error (e.g., token unregistered, network failure), the transaction itself (the booking creation or approval) remains valid.

* **Retries:** The backend logs the failed notification attempt and utilizes a background queue (or Cloud Tasks) to retry sending the message.
* **Cleanup:** Stale or permanently failed notifications are purged via regular cleanup jobs (see Data Lifecycle Strategy).
