# CampusConnect — integrated frontend and backend

This package contains the Express/MongoDB API and the existing browser frontend, connected through the frontend API helper.

## Requirements
- Node.js 20 or newer and npm
- MongoDB running locally, or a MongoDB Atlas connection string
- VS Code Live Server (recommended) or another static file server

## 1. Configure and start the backend
1. Open `CampusConnect/backend/CampusConnect` in a terminal.
2. Install dependencies: `npm install`
3. Copy `.env.example` to `.env` (PowerShell: `Copy-Item .env.example .env`).
4. Edit `.env`: set `MONGODB_URI` to your MongoDB connection string and replace `JWT_SECRET` with a random secret of at least 32 characters.
5. Keep `PORT=5000`. `CLIENT_ORIGIN` already allows the common Live Server ports 5500 and 3000; add your exact frontend origin if you use another port.
6. Start the API: `npm run dev`
7. Verify in your browser: `http://localhost:5000/api/health` should return JSON with `success: true`.

## 2. Start the frontend
1. Open `CampusConnect/frontend/CampusConnectFrontend/frontend` in VS Code.
2. Open `index.html` with Live Server. It will normally use `http://127.0.0.1:5500` or `http://localhost:5500`.
3. The frontend API URL is in `config.js`: `window.CAMPUSCONNECT_API_URL = "http://localhost:5000/api"`. Change it to your deployed API URL when deploying. Do not add a trailing slash after `/api`.
4. Register a student account and sign in. The browser sends JSON requests to the backend and includes the JWT as `Authorization: Bearer ...` for protected endpoints.

## 3. Important configuration
- Backend `.env` is intentionally not included. Create it from `.env.example`. Never share or commit `.env`.
- `CLIENT_ORIGIN` is a comma-separated allowlist of exact frontend origins (scheme + host + port), not a URL path.
- `MONGODB_URI` must point to a reachable database. The API will not start if MongoDB cannot connect.
- Public registration always creates a `student`. Create/promote an administrator using a controlled database operation; do not expose admin registration publicly.
- If your frontend uses a different Live Server port, add that origin to `CLIENT_ORIGIN` and restart the backend.

## 4. API communication tested by the frontend
- Authentication: register, login, current user, logout, change password
- Profile: read/update current user
- Announcements: list/create/delete (admin create/delete)
- Complaints: create/list/read/respond/update status/assign
- Admin: dashboard and user list/role/status management

## 5. Troubleshooting
- `Cannot reach the CampusConnect server`: start the backend and check `config.js` API URL.
- CORS error: compare the browser address origin with an entry in backend `.env` `CLIENT_ORIGIN`, then restart backend.
- MongoDB connection error: check that MongoDB is running and `MONGODB_URI` is correct.
- `401`: sign in again; the JWT may be expired or revoked.
- `403`: the logged-in role does not have permission for that operation.

## 6. Tests
From `CampusConnect/backend/CampusConnect`, run `npm test`. Tests requiring `mongodb-memory-server` may need to download a MongoDB binary on first execution.
