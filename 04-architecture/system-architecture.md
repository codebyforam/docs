# System Architecture

The Atma Darshan system is built on a decoupled, scalable architecture separating the frontend presentation layer from the backend business logic.

## Overview

The system consists of three primary layers:

1. **Frontend (Flutter Mobile & Web Admin)**
   * Responsible solely for user interaction, form presentation, and rendering data.
   * Framework-agnostic at its core (Clean Architecture), communicating with the backend via Repositories and Services.
2. **Backend (Firebase Cloud Functions)**
   * The authoritative source of truth for all business logic.
   * Handles transactions, state machine transitions, notifications, and role assignments.
3. **Database (Firestore)**
   * NoSQL database serving as the persistence layer.
   * Protected by strict Security Rules preventing direct client manipulation of critical data.

## Communication Flow

1. **Read Operations:** The Flutter client fetches data directly from Firestore (respecting Security Rules) using optimized queries.
2. **Write Operations:** The Flutter client triggers an HTTPS Callable Cloud Function. The backend validates the request, executes a Firestore Transaction if necessary, updates the database, and returns the result to the client.

This architecture ensures high concurrency safety and prevents clients from bypassing validation logic.
