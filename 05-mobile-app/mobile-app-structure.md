# Mobile App Structure

The Flutter application (Mobile App and Admin Panel) follows a **Framework-Agnostic Clean Architecture** approach. This ensures that the architecture principles hold regardless of the specific state management library used (e.g., GetX, Riverpod, BLoC).

## Layered Architecture

The project is divided into distinct layers to enforce separation of concerns:

### 1. Presentation Layer
* **UI/Widgets:** Visual components, screens, and dialogs.
* **State Controllers:** Manages the ephemeral UI state and coordinates with the Domain layer. This layer reacts to user input and updates the UI accordingly.

### 2. Domain Layer
* **Entities:** Core business objects (e.g., `User`, `Booking`, `Slot`).
* **Use Cases (Optional):** Specific business rules applied to the frontend (though core logic remains on the backend).

### 3. Data Layer
* **Repositories:** The abstraction layer that defines the contract for fetching or sending data. The presentation layer only talks to Repositories, unaware of the underlying database.
* **Data Sources / Services:** The concrete implementations that communicate with Firebase Auth, Firestore, and Cloud Functions.

## Directory Structure Strategy

```text
lib/
├── core/                   # Shared utilities, theme, network, errors
├── features/
│   ├── auth/
│   │   ├── domain/         # Entities, repository interfaces
│   │   ├── data/           # Repository implementations, DTOs
│   │   └── presentation/   # UI, state controllers
│   ├── bookings/
│   ├── services/
│   └── notifications/
└── main.dart
```

This structure allows the application to cleanly interface with the backend API (Cloud Functions) via the Data layer without polluting the UI code.
