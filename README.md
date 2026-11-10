# MediTrack

> One place for doctors and patients to manage appointments, prescriptions, and medicines.

MediTrack is a healthcare platform with an **Android app** for patients and a **web portal** for doctors and admins, both backed by a single secure API.

> **Status:** Early development (MVP in progress). The name `CareSync` is a working title.

---

## Features

### Patients
- Book, reschedule, and cancel appointments
- View appointment details and visit history
- Access digital prescriptions issued by doctors
- Medicine schedule with dosage reminders and refill alerts
- Track medicines purchased (manual entry or pharmacy bills)
- Store lab reports and visit notes

### Doctors
- Manage availability and daily schedule
- View patient list and history (with patient consent)
- Create and issue digital prescriptions
- Add visit notes and follow-ups

### Platform
- Role-based access (patient, doctor, pharmacy, admin)
- Push, SMS, and email notifications
- Audit logs for every access to health records

---

## Roadmap

- [ ] **MVP**: auth, appointments, doctor-issued prescriptions, medicine reminders
- [ ] **v1.1**: lab reports and visit notes
- [ ] **v2**: purchase tracking, pharmacy integration
- [ ] **Later**: iOS app, teleconsultation, ABDM integration

---

## Architecture

```
Android app (patients)      Web app (doctors + admin)
        \                         /
         ---- HTTPS / REST API ----
                    |
         Backend (modular monolith)
   ┌────────┬──────────────┬─────────────┬──────────────┐
 Auth    Appointments   Prescriptions   Medicines     Notifications
   └────────┴──────────────┴─────────────┴──────────────┘
        |            |             |              |
   PostgreSQL      Redis     Object storage    FCM / SMS / Email
```

| Layer | Technology |
|---|---|
| Android | Kotlin, Jetpack Compose, MVVM, Retrofit, Room, WorkManager |
| Web | Next.js (React), TypeScript |
| Backend | NestJS (Node.js), TypeScript *(swap for Spring Boot / FastAPI if preferred)* |
| Database | PostgreSQL |
| Cache / queues | Redis |
| File storage | S3-compatible object storage |
| Notifications | Firebase Cloud Messaging, SMS/email provider |
| Auth | OAuth2/OIDC, OTP/MFA, RBAC |
| CI/CD | GitHub Actions, Docker |

---

## Repository Structure

```
caresync/
├── backend/        # REST API (modular monolith)
├── web/            # Doctor/admin web portal (Next.js)
├── android/        # Patient Android app (Kotlin)
├── docs/           # API specs, schema, architecture notes
├── infra/          # Docker, IaC, deployment config
└── README.md
```

---

## Getting Started

### Prerequisites
- Node.js 20+
- Docker and Docker Compose
- Android Studio (latest stable) with JDK 17
- PostgreSQL 15+ and Redis 7+ (or use Docker Compose)

### 1. Clone

```bash
git clone https://github.com/<your-username>/caresync.git
cd caresync
```

### 2. Start dependencies

```bash
docker compose up -d postgres redis
```

### 3. Backend

```bash
cd backend
cp .env.example .env      # fill in values
npm install
npm run migrate
npm run start:dev
```

API runs at `http://localhost:4000`.

### 4. Web portal

```bash
cd web
cp .env.example .env.local
npm install
npm run dev
```

Web runs at `http://localhost:3000`.

### 5. Android app

1. Open the `android/` folder in Android Studio.
2. Add your `google-services.json` (Firebase) to `android/app/`.
3. Set `API_BASE_URL` in `local.properties` (use `http://10.0.2.2:4000` for the emulator).
4. Run on an emulator or device.

---

## Configuration

Example backend `.env`:

```env
DATABASE_URL=postgresql://user:password@localhost:5432/caresync
REDIS_URL=redis://localhost:6379
JWT_SECRET=change-me
FCM_SERVER_KEY=
STORAGE_BUCKET=
SMS_PROVIDER_KEY=
```

Never commit real secrets. Use `.env.example` for placeholders only.

---

## Testing

```bash
# Backend
cd backend && npm test

# Web
cd web && npm test

# Android
cd android && ./gradlew test
```

---

## Security and Privacy

CareSync handles sensitive health data. Core principles:

- Encryption in transit (TLS) and at rest
- Explicit patient consent before a doctor can view records
- Audit logging of all record access
- Least-privilege, role-based access control
- No personal health data in logs or analytics

Compliance depends on launch region (for example India's DPDP Act and ABDM guidelines, HIPAA, or GDPR). Review requirements before handling real patient data.

To report a vulnerability, please email **security@your-domain.com** instead of opening a public issue.

---

## Contributing

1. Fork the repo and create a branch: `git checkout -b feature/your-feature`
2. Commit using clear messages (Conventional Commits preferred)
3. Add or update tests
4. Open a pull request describing the change

---

## License

To be decided. Add a `LICENSE` file (MIT, Apache-2.0, or proprietary) before release.

---

## Disclaimer

CareSync is a record-keeping and scheduling tool. It does not provide medical advice, diagnosis, or treatment.
