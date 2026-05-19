# DevOps and Environments

Robust enterprise systems require strict separation of environments to safely build, test, and release features.

## 1. Firebase Projects Strategy

The application must utilize separate Firebase Projects for different environments to prevent accidental data corruption or test notifications reaching real users.

* **Development/Staging (`atma-darshan-dev`):** Used by developers and QA. Safe for destructive testing.
* **Production (`atma-darshan-prod`):** Live user data. Strict IAM access controls.

## 2. Build Flavors

The Flutter application utilizes build flavors (Android) and schemes (iOS) to manage environments:

* `dev`: Connects to `atma-darshan-dev`. Uses distinct app icons (e.g., a "DEV" banner) and bundle identifiers (e.g., `com.ashram.atmadarshan.dev`).
* `prod`: Connects to `atma-darshan-prod`.

## 3. Environment Variables (.env Handling)

Sensitive configuration (API keys for third-party services, feature flags) is never hardcoded.

* Flutter utilizes packages like `flutter_dotenv` or `--dart-define` compilation flags.
* Cloud Functions utilize Google Cloud Secret Manager or Firebase Environment Configuration to securely access secrets.

## 4. CI/CD Pipeline

Continuous Integration and Continuous Deployment are essential.

* **CI (GitHub Actions / GitLab CI):** On every Pull Request, the pipeline runs Flutter tests, static analysis (`flutter analyze`), and ensures the build succeeds.
* **CD (App Distribution / Stores):** Merging to `main` triggers a production build, which is deployed to Firebase App Distribution for internal testers, or directly to TestFlight/Google Play Console. Cloud Functions are deployed automatically using `firebase deploy --only functions`.
