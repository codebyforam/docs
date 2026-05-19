# Role-Based Access Control (RBAC)

Security is paramount in managing ashram operations. The system strictly forbids role management via client-side database fields.

## 1. Firebase Auth Custom Claims

Roles (`super_admin`, `admin`, `volunteer`, `content_manager`) are implemented exclusively using **Custom Claims** on the user's Auth Token.

* **Prevention of Role Spoofing:** Because claims are securely baked into the JWT token by the backend, a malicious client cannot modify their own role.

## 2. Claim Assignment

Only users with the `super_admin` claim can invoke the `assignRole()` Cloud Function. The function utilizes the Firebase Admin SDK to attach the new claim to the target user's UID.

## 3. Client and Rules Enforcement

* **Flutter App:** Decodes the JWT token on login to determine which UI elements (like the Admin Dashboard) to render.
* **Firestore Rules:** All rules rely on the token.
  ```javascript
  allow write: if request.auth.token.role == 'admin';
  ```
