# State Management Strategy

The Atma Darshan application adheres to a **Framework-Agnostic State Management** philosophy. The core architectural principles (Presentation, Domain, Data) matter more than the specific library chosen.

## Core Principles

1. **Separation of Concerns:** UI code must never make direct database calls. All requests route through state controllers to repositories.
2. **Immutability:** State objects should be immutable, producing new instances upon change to ensure predictable UI rebuilds.
3. **No Direct Writes:** The state layer is responsible for invoking Cloud Functions (via Repositories) for state mutations, rather than performing local Firebase client writes for sensitive entities like bookings.

## Implementation Flexibility

By adhering to Clean Architecture and the Repository Pattern, the project can utilize any state management solution cleanly:

* **GetX:** Can be used effectively if controllers are strictly separated from UI logic and rely on dependency-injected repositories rather than global singletons holding business logic.
* **Riverpod / BLoC:** Can be slotted into the presentation layer to manage complex state transitions while leaving the domain and data layers untouched.

Ultimately, the goal is high development velocity without compromising architectural integrity.
