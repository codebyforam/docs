# Database Design & Strategy

The Firestore schema for Atma Darshan is designed for read-heavy operations, utilizing NoSQL optimization techniques to prevent hotspots and ensure fast query execution.

## 1. Core Collections

### `users`
Contains profile data, FCM tokens, and preferences. Note: **Roles are NOT stored here for authorization purposes** (they are handled via Auth Custom Claims).

### `services`
Defines available ashram offerings.
* Uses **Soft Delete** (`status: "archived"` or `isDeleted: true`). Services with historical bookings are never hard-deleted.

### `slots`
Instances of services available at specific times.
* Contains `capacity`, `bookedCount`, and `remainingCapacity`.

### `bookings`
Individual or group reservations.
* **Denormalization:** Includes essential `slot` and `service` data (like date, time, service name) to avoid multi-document fetches when rendering user booking history.

### `audit_logs`
Immutable records of admin actions.

## 2. Data Lifecycle Strategy

To maintain a performant database and manage storage costs, the following lifecycle rules apply:

* **Archival Policy:** Bookings older than 2 years are exported to Google Cloud Storage or BigQuery and deleted from the live `bookings` collection.
* **Cleanup Jobs:** A scheduled Cloud Function purges expired notifications and abandoned (incomplete) booking attempts daily.
* **Slot Expiry:** Slots in the past are marked `status: "completed"` to optimize active queries.

## 3. Query Optimization & Indexing

* **Composite Indexes:** Deployed via `firestore.indexes.json` for complex queries, such as fetching `bookings` where `status == "pending"` AND `serviceId == XYZ` ordered by `createdAt`.
* **Hotspot Prevention:** In high-demand scenarios (e.g., flash bookings), standard documents can bottleneck. If a single slot exceeds 500 writes/second, the backend implements **Distributed Counters** for the `bookedCount`.

## 4. Security

Direct client writes to `slots`, `bookings`, and `services` are blocked via strict Firestore Security Rules. All state changes flow through Cloud Functions.
