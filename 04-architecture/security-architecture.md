# Security Architecture

Atma Darshan implements enterprise-grade security focusing on strict Role-Based Access Control (RBAC), secure data access, and immutable state management.

## 1. Role-Based Authorization

> [!CAUTION]
> Relying on a `role` field inside a user's Firestore document is insecure and vulnerable to spoofing.

Roles (`super_admin`, `admin`, `volunteer`, `content_manager`) are implemented exclusively using **Firebase Auth Custom Claims**.

* **Assignment:** Only a `super_admin` can trigger the `assignRole()` Cloud Function to set a custom claim on another user's authentication token.
* **Client Verification:** The Flutter app reads the token's claims to determine UI state (e.g., showing the Admin Dashboard).
* **Backend Verification:** Firestore Rules and Cloud Functions enforce security by explicitly checking `request.auth.token.role`.

## 2. Restricted Write Access

Clients (users and admins) should **NEVER** directly mutate sensitive data via the Firebase Client SDK.

* **Bookings:** `read` access for own uid; `write` access is **DENIED**.
* **Booking Status:** Mutating `status` from `pending` -> `approved` is **DENIED** for all clients. Only backend functions can transition status.
* **Slots/Capacity:** `read` access available; `write` access is **DENIED**.

## 3. Firestore Security Rules

Rules are written defensively. Example policy:
```javascript
match /bookings/{bookingId} {
  // Users can read their own bookings
  allow read: if request.auth != null && request.auth.uid == resource.data.userId;
  // Admins can read all
  allow read: if request.auth != null && request.auth.token.role == 'admin';
  // NO direct client writes
  allow write: if false; 
}
```

## 4. Input Validation

All data passed to HTTPS Callable Cloud Functions undergoes strict schema validation (e.g., using Joi or Zod) to prevent injection, overflow, or malformed data attacks before any database operations occur.
