# AI Job Search App — Flutter Boilerplate

Clean Architecture skeleton for the app described in your spec:
Login/Signup, Profile, AI Job Search (USA/UK/Canada remote), AI Resume
Builder, AI Cover Letter, ATS Score, Saved Jobs, Apply Tracker, Push
Notifications, Dashboard, Dark/Light mode, English + Hindi.

## Stack
Flutter · Riverpod (flutter_riverpod) · go_router · Firebase Auth ·
Cloud Firestore · Firebase Cloud Messaging · Dio (REST to your FastAPI
backend) · easy_localization · google_fonts

## Folder structure
```
lib/
 ├── core/        # theme, router, network (Dio client), error handling, constants
 ├── config/      # env.dart (.env reader), firebase_options.dart (placeholder)
 ├── features/
 │    ├── auth/           # login, register, splash, Firebase auth repo + riverpod
 │    ├── jobs/            # AI job search, job details, saved jobs
 │    ├── resume/          # AI resume builder, ATS score
 │    ├── cover_letter/    # AI cover letter generator
 │    ├── tracker/         # application tracker (Kanban-style status)
 │    ├── profile/         # profile + settings (theme/lang/notifications)
 │    └── dashboard/       # stats grid + recent jobs + bottom nav
 ├── shared/      # reusable widgets (buttons, textfields, loaders) + UserModel
 └── main.dart
```

Each feature follows: `data/models` → `data/repositories` (impl) →
`domain/repositories` (interface) → `domain/providers` (Riverpod) →
`presentation/screens`.

## Setup

1. **Install packages**
   ```
   flutter pub get
   ```

2. **Firebase**
   ```
   dart pub global activate flutterfire_cli
   flutterfire configure
   ```
   This generates the real `lib/config/firebase_options.dart`. Then in
   `main.dart` uncomment the `Firebase.initializeApp(...)` line.
   Enable **Email/Password Auth**, **Firestore**, and **Cloud Messaging**
   in the Firebase console.

3. **Environment variables**
   ```
   cp .env.example .env
   ```
   Fill in `API_BASE_URL` — your FastAPI backend URL. Never put the
   OpenAI API key in the Flutter app; keep it server-side in FastAPI only.

4. **Backend (FastAPI) — not included here**
   The app calls these endpoints (see `core/network/api_endpoints.dart`):
   - `GET  /api/v1/jobs/search` — job search/aggregation (+ optional AI match score)
   - `GET  /api/v1/jobs/{id}` — job details
   - `POST /api/v1/ai/resume/generate` — OpenAI-generated resume summary
   - `POST /api/v1/ai/resume/ats-score` — ATS scoring
   - `POST /api/v1/ai/cover-letter/generate` — OpenAI-generated cover letter
   Build these in FastAPI, calling OpenAI server-side, and return JSON
   matching the model shapes in `features/*/data/models`.

5. **Run**
   ```
   flutter run
   ```

## What's stubbed vs. what's real
- **Real, working logic:** Firebase Auth (sign up/in/out, password
  reset), Firestore reads/writes for saved jobs, applications, resume,
  cover letters; Riverpod state management; go_router with auth-based
  redirects; theme + basic i18n switching UI.
- **Needs your backend:** job search results and all 3 AI features call
  your FastAPI endpoints — swap in real responses matching the JSON
  shapes already parsed by the models.
- **TODOs left in code:** push notification wiring (FCM token save +
  handlers), profile photo upload (needs `firebase_storage`), job apply
  URL launch (`url_launcher`), admin panel (not in this app — build as
  a separate web dashboard).

## Admin Panel note
User Management / Job Management / Analytics / Push Notifications from
your spec are typically a **separate web admin dashboard** (e.g. React
or Flutter Web) hitting the same FastAPI backend — not part of the
end-user mobile app. Ask if you want that scaffolded separately.
