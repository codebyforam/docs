# Realtime Strategy

Firebase Firestore provides powerful real-time capabilities via `onSnapshot` listeners. However, poor listener management can lead to massive billing costs and performance degradation.

## 1. Optimized Listening

* **Mobile Clients:** Clients should **never** listen to entire collections (like `services` or `slots`).
  * Clients fetch slot lists via standard one-time reads (Futures).
  * Real-time listeners are restricted exclusively to the user's specific active bookings to show live status updates (e.g., when an admin approves their request).
* **Admin Panel:** The admin dashboard utilizes targeted listeners with strong query limits (e.g., listening to `bookings` where `status == 'pending'` and `date == today`) to monitor incoming requests live.

## 2. Presence and Disconnects

For admin tracking, Firebase Realtime Database (RTDB) presence features can be used to show which volunteers are currently online and managing gates.

## 3. Unsubscribing

All Flutter state controllers must strictly unsubscribe from streams in their `dispose` methods to prevent memory leaks and ghost reads.
