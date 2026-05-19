# Monitoring and Observability

To ensure high availability and rapid issue resolution, the system implements comprehensive observability tools.

## 1. Firebase Crashlytics

Integrated into both the mobile app and admin panel, Crashlytics tracks fatal and non-fatal errors.
* Unhandled Flutter exceptions are routed to Crashlytics.
* Custom keys and user identifiers (UIDs) are attached to crash reports to trace the exact user state preceding a failure.

## 2. Cloud Function Logging

Firebase Cloud Functions utilize structured Google Cloud Logging.
* **Audit Trails:** All HTTPS callable functions log the invoker's UID and the attempted action.
* **Error Tracking:** If a transaction fails or an external API errors out, the exact stack trace and context are logged to Google Cloud Console. Alerts are configured for critical failure thresholds.

## 3. Analytics Events

Firebase Analytics is used to track business metrics and user funnels.
* **Key Events:** `booking_started`, `booking_completed`, `booking_failed`.
* **Retention Policy:** Analytics data is retained according to the defined Data Lifecycle Strategy, generally archived after 14 months to BigQuery for historical analysis.

## 4. Admin Activity Monitoring

Beyond `audit_logs` in Firestore, high-level administrative activity (e.g., bulk data imports or Super Admin role assignments) triggers internal notifications (via Slack/Discord webhooks) to ensure the core team is immediately aware of high-impact system changes.
