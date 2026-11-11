# Sa'adah Islamic Academy Portal

This repository includes a Firebase/Cloud Firestore configuration template and security rules for setting up shared cloud storage. **Cloud storage is not active until you create a Firebase project, provide its web config, deploy the rules, and connect authenticated users.** Do not put a Firebase service-account key in this repository or in browser code.

## Firebase setup

1. Create a Firebase project and register a Web app in the Firebase console.
2. Enable Cloud Firestore.
3. Copy `firebase-config.example.js` to `firebase-config.js` and replace the placeholders using the Web app config from **Project settings → General → Your apps**. The web config is intended to be public; service-account credentials are not.
4. Load `firebase-config.js` before `portal-db.js` in the page. The config must exist as `window.PORTAL_FIREBASE_CONFIG` when the storage layer initializes.
5. Deploy `firestore.rules` to the project's Firestore database.
6. Configure Firebase Authentication and grant the `admin: true` custom claim only from a trusted server using the Firebase Admin SDK. Never grant admin privileges from browser code.

The rules allow access to `portalData/{document}` only for signed-in accounts with the admin custom claim. Until Authentication is integrated into the portal, the current demo login is not secure and will not satisfy these rules. **Do not loosen the rules to public read/write to make the demo work.**

## Important storage limitations

- The existing `portal-db.js` is a local database compatibility layer with an experimental Firestore sync path; it is not yet a production-ready, multi-user cloud database implementation. The current demo owner check always returns `true`, and the local database API is synchronous. Firebase setup alone does not provide secure login, robust real-time synchronization, conflict handling, or safe per-role access.
- The current sync stores the portal state as one Firestore document (`portalData/schoolState`). This is unsuitable for growing school data and can hit Firestore's document size limit or overwrite concurrent changes. A production migration should use separate documents/collections and authenticated, role-based access.
- Existing browser `localStorage` records are not automatically migrated. Back them up and plan a one-time migration before relying on cloud data.
- Never use personal student data in a test deployment with permissive Firestore rules.

## Demo login credentials

- Parent login: phone `08012345678`, code `123456`
- Teacher login: staff ID `STF-001`, code `123456`
- Admin dashboard: accessible as the school owner account in the portal

These credentials are demo-only and must not be used for a live school deployment.

## Run tests

```sh
npm test
```
