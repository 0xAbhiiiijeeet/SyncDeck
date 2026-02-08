# SyncDeck

An offline-first task manager. Create and view tasks with or without internet; tasks saved offline are synced to the server automatically once the device is back online.

## Features

- Sign up and log in with email and password
- Stay logged in after closing the app
- Create tasks with a title, description, color and due date
- View tasks organized by date
- Works offline: tasks and user data are stored locally in SQLite
- Unsynced tasks are pushed to the server in one batch when the device reconnects

## Tech Stack

| Part | Technologies |
| --- | --- |
| App | Flutter, Bloc (Cubit), SQLite (sqflite), Shared Preferences |
| Backend | Node.js, Express, TypeScript, JWT, bcrypt |
| Database | PostgreSQL with Drizzle ORM |
| DevOps | Docker, Docker Compose |

## How Offline Sync Works

1. Every task is saved in the local SQLite database with an `isSynced` flag.
2. Tasks created without internet are saved with `isSynced = 0`.
3. When connectivity returns, the app sends all unsynced tasks to `POST /tasks/sync`.
4. Once the server accepts them, the tasks are marked as synced locally.

## API Endpoints

| Method | Route | Description |
| --- | --- | --- |
| POST | `/auth/signup` | Create an account |
| POST | `/auth/login` | Log in and receive a token |
| POST | `/auth/tokenIsValid` | Check if a saved token is still valid |
| GET | `/auth` | Get the logged-in user |
| POST | `/tasks` | Create a task |
| GET | `/tasks` | Get all tasks of the user |
| DELETE | `/tasks` | Delete a task |
| POST | `/tasks/sync` | Upload tasks created offline |

Protected routes expect the token in the `x-auth-token` header.

## Project Structure

```
backend/    Express + TypeScript API, Drizzle schema, Docker setup
frontend/   Flutter app (auth and home features, local and remote repositories)
```

## Getting Started

### Backend

```bash
cd backend
docker compose up --build
```

The API runs on `http://localhost:8000` and PostgreSQL on port `5432`. Database data is kept in a Docker volume.

Create the tables with Drizzle (run once, with the containers running):

```bash
cd backend
npm install
cd src
npx drizzle-kit push
```

### Flutter app

```bash
cd frontend
flutter pub get
flutter run
```

The server address is set in `frontend/lib/core/constants/constants.dart`. Change `localhost` to your computer's IP address when testing on a physical device.
