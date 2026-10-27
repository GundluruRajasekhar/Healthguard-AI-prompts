# HealthGuard AI: Requirements

## 1. Goal
A personal healthcare platform that monitors health, manages records, gives AI wellness insights, tracks fitness, books appointments and responds to emergencies. Its standout feature is **AI Early Disease Risk Detection**.

## 2. Functional requirements
| ID | Module | Requirement |
|----|--------|-------------|
| F1 | AI Assistant | Chat, symptom triage, health education, English/Hindi/Telugu, voice in and out, red-flag escalation |
| F2 | Dashboard | Heart rate, BP, glucose, SpO₂, BMI, weight, interactive charts |
| F3 | Medicines | Reminders, dose tracking, missed-dose alerts, refill notices, adherence advice |
| F4 | Records | Encrypted upload of prescriptions, labs, vaccinations; search; PDF export |
| F5 | Nutrition | Meal plans, calories, water, disease-specific diets |
| F6 | Fitness | Steps, exercise, sleep, goals, badges |
| F7 | SOS | One-click alert, contact notification, live location, critical info, history |
| F8 | Appointments | Scheduling, calendar sync, history, follow-ups, telemedicine |
| F9 | Mental health | Mood log, stress check, meditation, daily check-in, support assistant |
| F10 | Predictive analytics | Risk insights, trends, preventive advice, health score, 7-day forecast |
| F11 | **Early Risk Detection** | 14-day trend regression on BP, glucose, HR, SpO₂, sleep and mood; flags drift before thresholds are crossed; always shows a medical disclaimer |
| F13 | Multi-agent AI | Orchestrator runs all nine agents on shared state, ranks findings, links related ones (e.g. Monitoring → Nutrition → Appointment) and returns one action plan |
| F14 | Fall detection | Accelerometer detects free-fall, impact and stillness; 15-second "I'm OK" countdown; automatic SOS with location; sensor permission handling |
| F15 | Medical report analysis | Parse pasted lab values, compare with adult reference ranges, flag low/high, explain in plain language, notify agents |
| F16 | Strong security | AES-256-GCM vault (PBKDF2), role-based access, hash-chained audit log, CSP, output escaping, idle session lock, rate-limited unlock |
| F17 | Automated testing | In-app test runner (28 tests) plus Jest/Supertest/Playwright suites in CI |
| F18 | Wearables | Web Bluetooth pairing for the standard Heart Rate and Blood Pressure profiles (mmHg and kPa), simulator fallback; glucose meters planned |
| F19 | Real AI model | Optional customer-hosted https endpoint; red-flag screening always runs locally first; consent checkbox; fallback to built-in guidance |
| F20 | Encrypted storage | Records persist on the device only as AES-256-GCM ciphertext; erase option |
| F21 | Caregiver alerts | Ordered chain, acknowledgement, timed escalation, sms:/tel: hand-off with location, final step to 112; server-side SMS/push/voice for production |
| F22 | Elder care | Daily check-in, CDC STEADI fall-risk screen, elder mode (large text, high contrast) |
| F23 | Clinical feedback | Structured clinician feedback form with JSON export and a written validation plan |
| F12 | Admin | User management, audit log viewer, analytics, agent health |

## 3. Agentic architecture
Nine agents (Monitoring, Symptom, Medication, Nutrition, Fitness, Appointment, Emergency, Records, Mental Wellness) communicate over an event bus (Redis Streams or Mongo change streams). Example: Monitoring flags rising BP → Nutrition proposes a low-salt plan → Appointment suggests a check-up → Mental Wellness checks stress.

## 4. Non-functional requirements
- **Security:** JWT (short-lived access plus refresh), OAuth (Google), RBAC (patient, doctor, admin), AES-256 field encryption, TLS, audit logs, rate limiting, input validation, HIPAA-inspired privacy.
- **Accessibility:** WCAG 2.2 AA, keyboard navigation, ARIA live regions, high contrast, font scaling, voice, reduced motion.
- **Performance:** LCP under 2.5 s on 4G, API p95 under 300 ms.
- **PWA:** installable, offline cache, background sync, push notifications.
- **Quality:** unit, integration and e2e tests; at least 80% coverage on core logic.

## 5. Tech stack
React, TypeScript, Tailwind CSS, Recharts, Node.js, Express, MongoDB, LLM API, Docker, GitHub Actions.

## 6. Safety and compliance
Not a diagnostic device. Every AI health output carries a disclaimer. Red-flag symptoms always route to emergency guidance. Users can export or delete their data.

## 7. Test requirements
- Unit: trend slope, BMI, risk indicators, report parser, fall detector, red-flag filter, escaping, permissions.
- Security: AES-GCM round trip, wrong passphrase rejected, audit tamper detection.
- Integration (production): auth flows, RBAC on every route, encrypted record CRUD.
- End-to-end (production): login, log reading, risk flag, SOS, report upload.
- Accessibility: axe checks in CI, keyboard-only pass.
