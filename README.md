# Simple MERN To-do List

A small React application with an Express API and MongoDB persistence. Add tasks, edit titles, mark tasks complete/incomplete, delete tasks, and filter by status. All changes go through the backend; there is no localStorage fallback.

## 1. Install dependencies

Use Node.js 22.12 or newer. Open a terminal in this project folder:

```powershell
npm install
Copy-Item backend/.env.example backend/.env
```

Dependencies are managed with npm workspaces; one install covers both folders. If `backend/.env` already exists, edit it instead of copying over it.

## 2. Connect MongoDB

### Option A: MongoDB Atlas

1. Sign in at https://cloud.mongodb.com/ and create a project and cluster (choose a free option if available).
2. Create a **database user** with a username and password and permission to read/write your database. This is different from your Atlas login.
3. In **Network Access / IP Access List**, add your current public IP address.
4. On the cluster, select **Connect → Drivers → Node.js** and copy the connection string.
5. Edit `backend/.env` as follows, replacing the placeholders with your actual database user, password and cluster hostname:

```dotenv
MONGO_URI=mongodb+srv://YOUR_DB_USER:YOUR_ENCODED_PASSWORD@YOUR_CLUSTER.mongodb.net/todo_db?retryWrites=true&w=majority
PORT=5000
CLIENT_ORIGIN=http://localhost:5173
```

Use the actual Atlas-provided URI, adding `todo_db` before the `?` if it has no database name. Replace all placeholders, including angle brackets. URL-encode special characters in the username/password (for example `@` becomes `%40`). Keep this URI in `backend/.env` only; never put it in React code or commit it. MongoDB creates the database/collection when the first task is saved.

Official guide: https://www.mongodb.com/docs/atlas/connect-to-database-deployment/

### Option B: Local MongoDB

Install and start MongoDB Community Server (Compass alone is not the database server), then use:

```dotenv
MONGO_URI=mongodb://127.0.0.1:27017/todo_db
PORT=5000
CLIENT_ORIGIN=http://localhost:5173
```

## 3. Run

From the project root:

```powershell
npm start
```

Open **http://localhost:5173**. The backend runs on port 5000. The terminal should print `MongoDB connected`. Use Ctrl+C to stop both servers.

Alternatively, run `npm start` inside `backend` and `frontend` in two separate terminals. Vite proxies `/api` to the backend; if you change the backend port, update `frontend/vite.config.js` as well. The frontend port is fixed so CORS configuration remains consistent.

## Project structure

```text
backend/
  models/Task.js        Mongoose schema
  app.js               Express routes and validation
  server.js            Environment loading and database connection
  app.test.js          HTTP API tests with a model double
  .env.example         Environment variable template
frontend/
  src/components/      TaskForm, TaskList, TaskItem
  src/App.jsx          Task state and API actions
  src/api.js           Fetch helper
  src/style.css        Responsive styles
```

## API

| Method | Path | Purpose |
| --- | --- | --- |
| GET | `/api/tasks` | List tasks, newest first |
| POST | `/api/tasks` | Create with `{ "title": "Read notes" }` (201) |
| PUT | `/api/tasks/:id` | Edit title and/or boolean `completed` |
| DELETE | `/api/tasks/:id` | Delete task |

Titles are converted with `String()`, trimmed, and limited to 1–200 characters. Invalid requests return 400; missing tasks return 404. CORS permits the configured frontend origin. This assignment app has a shared list and no user authentication; run it locally.

## Verify

```powershell
npm test
npm run build
```

The API tests use a model double, so they do not prove a live MongoDB connection. After configuring MongoDB, check: add two tasks, edit one, mark it complete, refresh to verify persistence, filter the list, delete a task, and refresh again. Confirm the records in Atlas Data Explorer or Compass.

The build produces `frontend/dist`. `npm start` is the supported local full-stack workflow; hosting the frontend alone also requires a deployed API and proxy configuration.

## Troubleshooting

- **Missing MONGO_URI:** create `backend/.env` and fill in the URI.
- **MongoDB connection failed:** check the database user's credentials, encoded password, IP access list and cluster availability. For local MongoDB, start the database service.
- **Cannot reach server:** check both terminal processes, fix the database connection, then restart `npm start` and press Retry.
- **Port already used:** stop the other process using port 5000/5173, or change the ports and associated proxy/CORS settings together.

## Assignment submission checklist

The app source is ready locally. GitHub publication, merged PR screenshots, live database screenshots and Classroom submission are separate remaining steps.

- Create your GitHub repository and follow the assignment's `feature/backend` and `feature/frontend` branch workflow. Make meaningful commits during your own development; open and merge a PR for each branch. Do not invent a development history.
- Keep `.env` and `node_modules` out of Git (already covered by `.gitignore`). Commit `.env.example` and the lockfile.
- Capture 2–3 screenshots of adding, completing and deleting tasks after connecting MongoDB, plus your merged PRs in GitHub's Closed tab.
- Zip the source, README, lockfile and screenshots, excluding `.env`, `node_modules`, `.git` and generated build files.
- Name the archive `RollNo_Name_Assignment2_MERN_ToDoList.zip`, add the repository URL where requested, and submit it yourself in Classroom.

No GitHub repository, PR, database account or Classroom submission is created by this source project.
