# Cloud Functions Architecture

Firebase Cloud Functions form the backend core of Atma Darshan, handling all transactional logic and preventing direct client database mutation.

## Required Functions

### 1. `createBooking(data)`
* **Purpose:** Validates slot capacity and creates a booking atomically.
* **Trigger:** HTTPS Callable.
* **Behavior:** Uses a Firestore Transaction. Reads capacity, decrements, and writes booking. Returns failure if slot is full.

### 2. `approveBooking(data)`
* **Purpose:** Transitions a booking from pending to approved.
* **Trigger:** HTTPS Callable (Admin only).
* **Behavior:** Updates state machine and triggers notification.

### 3. `rejectBooking(data)`
* **Purpose:** Transitions a booking from pending to rejected.
* **Trigger:** HTTPS Callable (Admin only).
* **Behavior:** Releases slot capacity atomically if it was locked.

### 4. `cancelBooking(data)`
* **Purpose:** Cancels an approved/pending booking.
* **Trigger:** HTTPS Callable.
* **Behavior:** Runs a transaction to increment `remainingCapacity` back to the slot.

### 5. `createSlot(data)`
* **Purpose:** Admin creation of new service slots.
* **Trigger:** HTTPS Callable (Admin only).

### 6. `updateSlotCapacity(data)`
* **Purpose:** Securely increases/decreases capacity of an existing slot.
* **Trigger:** HTTPS Callable (Admin only).

### 7. `assignRole(data)`
* **Purpose:** Sets Firebase Auth Custom Claims (`super_admin`, `admin`, etc.).
* **Trigger:** HTTPS Callable (Super Admin only).

### 8. `sendNotification(data)`
* **Purpose:** Dispatches FCM messages asynchronously.
* **Trigger:** Firestore document trigger or internal function call. Notification failure does not break the parent transaction.
