# Auth API

This service is the only component that connects to PostgreSQL. It reads connection settings from the project-root `PostgreSQL_ DB.txt`, creates `User_Info` when it does not exist (or uses an optional `Table name` setting), and stores passwords as bcrypt hashes.

Run it from this directory:

```powershell
npm install
$env:PGSSL_REJECT_UNAUTHORIZED='true'
npm start
```

Set `JWT_SECRET` to a long random value outside local development. The API returns an 8-hour bearer token after login and requires it for `PATCH /api/me`, so users can update only their own profile.

Before registering or logging in, request `GET /api/captcha`. Submit its `id` as `captchaId` and the answer as `captchaAnswer` in the corresponding `POST /api/register` or `POST /api/login` JSON body. Challenges expire after five minutes and can be used once; request a new CAPTCHA after an invalid, expired, or already-used challenge.

The API loads the project-root `ca.pem` certificate into the PostgreSQL TLS connection and requires certificate verification. Keep `PGSSL_REJECT_UNAUTHORIZED=true`; do not disable verification in production.

The Flutter app uses `http://localhost:3000/api` by default. For an Android emulator, use the host machine address (often `http://10.0.2.2:3000/api`); for a physical device or another computer, use the API machine's LAN address:

```powershell
flutter run -d chrome --dart-define=API_BASE_URL=http://localhost:3000/api
flutter run -d windows --dart-define=API_BASE_URL=http://localhost:3000/api
flutter run -d android --dart-define=API_BASE_URL=http://10.0.2.2:3000/api
```

The existing table must have these columns if it already exists: `id`, `name`, `email`, `password_hash`, and `created_at`. PostgreSQL connection settings are read from `PostgreSQL_ DB.txt` in the project root.
