# Backend Architecture

The Atma Darshan backend operates on a **Backend-For-Frontend (BFF)** pattern using Firebase Cloud Functions, Firestore, and Firebase Auth.

To ensure enterprise-grade security, scalability, and data integrity, all business logic and transactional operations are strictly controlled by the backend.

## 1. Separation of Responsibilities

**Flutter Client Responsibilities:**
* UI rendering and animations
* Form validation (basic structure and input formatting)
* Displaying data to users
* Triggering backend functions via HTTPS Callables

**Backend Responsibilities (Cloud Functions):**
* Slot validation and capacity checks
* Atomic booking creation
* Booking state lifecycle management (approval, rejection, completion)
* Transactional updates to prevent concurrency issues
* Security enforcement and Role-Based Access Control (RBAC) validation
* Notification triggers (FCM/Email)

> [!CAUTION]
> **Clients must NEVER directly modify:** slots, capacity, user roles, or booking statuses.

## 2. Core Cloud Functions

The backend consists of several specific HTTPS Callable and Trigger functions:

* `createBooking()`: Initiates a booking via a Firestore transaction to prevent double booking.
* `approveBooking()`: Admin action to transition booking from pending -> approved.
* `rejectBooking()`: Admin action to reject a booking.
* `cancelBooking()`: User/Admin action to cancel, releasing slot capacity atomically.
* `createSlot()`: Admin action to initialize a service slot.
* `updateSlotCapacity()`: Secure modification of capacity.
* `assignRole()`: Super-Admin action assigning Custom Claims.
* `sendNotification()`: Asynchronous trigger for FCM pushes.

## 3. Transactional Integrity

All writes involving constrained resources (like `remainingCapacity`) MUST utilize Firestore Transactions. This guarantees that concurrent bookings do not overbook a slot.

## 4. Error Handling and Recovery

Cloud functions implement robust error handling, returning standardized `HttpsError` codes (e.g., `failed-precondition` if a slot is full). The client maps these to user-friendly messages.

See the Failure Recovery Strategy and Edge Cases documentation for detailed retry and rollback mechanisms.
