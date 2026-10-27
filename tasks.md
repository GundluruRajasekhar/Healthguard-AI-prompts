# HealthGuard AI: Tasks

## Phase 0: Demo (done in `index.html`)
- [x] Glassmorphism UI, dark/light, high contrast, font size controls
- [x] Dashboard with vitals logging, charts, BMI, health score
- [x] AI Early Disease Risk Detection with 7-day forecast and disclaimer
- [x] Assistant with triage, 3 languages, voice in/out
- [x] Medicines, records (search, PDF via print), nutrition, fitness, SOS, appointments, mental health
- [x] Agent activity feed
- [x] Multi-agent consultation with orchestrator, hand-offs and action plan
- [x] Fall detection (motion sensor, countdown, auto SOS, simulator)
- [x] Medical report analysis (lab parser, reference ranges, guidance)
- [x] Security: AES-GCM vault, RBAC, hash-chained audit log, CSP, escaping, idle lock, unlock rate limit
- [x] Automated tests: 28 in-app tests

- [x] Wearables via Web Bluetooth (heart rate, blood pressure) with simulator
- [x] Optional real-AI endpoint hook with consent and local red-flag screening
- [x] Encrypted on-device record storage
- [x] Caregiver alert workflow with acknowledgement and escalation to 112
- [x] Elder care: daily check-in, STEADI screen, elder mode
- [x] Clinical feedback form and validation plan
- [x] Measured logic coverage (100% lines, 97.2% branches)

## Phase 0.5: Still to do before claiming readiness
- [ ] Test wearables with real hardware
- [ ] Deploy the AI proxy and connect a model
- [ ] Server-side SMS/push/voice for caregiver alerts
- [ ] Collect feedback from at least 3 clinicians
- [ ] Record the demo video and add live, video and GitHub links

## Phase 1: Foundation
- [ ] Vite + React + TypeScript + Tailwind project
- [ ] Express API, MongoDB models (User, Vital, Medicine, Record, Appointment, Alert, AuditLog)
- [ ] Docker Compose, ESLint, Prettier, CI pipeline

## Phase 2: Security
- [ ] JWT access/refresh, Google OAuth
- [ ] RBAC middleware (patient, doctor, admin)
- [ ] AES-256 encryption for records and uploads
- [ ] Audit logging, rate limiting, validation (zod), Helmet

## Phase 3: Core features
- [ ] Vitals CRUD and Recharts dashboard
- [ ] Medicine reminders (cron plus push)
- [ ] Records upload, search, PDF export
- [ ] Nutrition, fitness, mental health modules
- [ ] Appointments with calendar sync, telemedicine room

## Phase 4: AI and agents
- [ ] Event bus and agent orchestrator
- [ ] Nine agents with shared memory
- [ ] LLM assistant with safety guardrails and red-flag routing
- [ ] Early Risk Detection service (trend regression, later ML model)
- [ ] Multi-language voice (Web Speech API)

## Phase 5: Emergency
- [ ] SOS with SMS/push to contacts, live location link
- [ ] Emergency history and critical info card

## Phase 6: PWA, admin, quality
- [ ] Service worker, offline cache, background sync, push notifications
- [ ] Admin panel (users, audit logs, analytics)
- [ ] Accessibility audit (axe, screen reader pass)
- [ ] Unit, integration and e2e tests (80%+ core coverage)

## Phase 7: Launch
- [ ] Deploy (Vercel client, Render/Railway API, MongoDB Atlas)
- [ ] Demo video, pitch deck, final README polish
