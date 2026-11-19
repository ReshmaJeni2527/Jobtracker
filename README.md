# JobTrail – Job Application Tracker

Full-stack MERN app: **React (Vite)** frontend, **Node.js + Express** REST API, **MongoDB** database (Mongoose).

```
jobtrail/
├── client/                 React frontend
│   └── src/
│       ├── api/client.js          all fetch() calls to the backend
│       ├── context/AppContext.jsx loads data from the API + add/edit/delete functions
│       ├── components/            Sidebar, Navbar, DashboardCard, charts, forms, table...
│       ├── pages/                 Dashboard, Applications, AddApplication, Details, Interviews
│       └── data/initialData.js    dropdown options (statuses, job types, ...)
├── server/                 Express backend
│   ├── .env                       PORT, MONGO_URI, CLIENT_URL
│   └── src/
│       ├── server.js              app setup, routes, DB connection
│       ├── models/                Application.js, Interview.js (Mongoose schemas + validation)
│       ├── controllers/           the logic for each endpoint
│       ├── routes/                URL → controller mapping
│       ├── middleware/            id validation, error handler
│       └── seed.js                optional sample data
└── package.json            root scripts (run both apps together)
```

## 1. Requirements
- **Node.js 18 or newer**
- **MongoDB** – either
  - local: install *MongoDB Community Server* (it runs on `mongodb://127.0.0.1:27017`), or
  - cloud: a free *MongoDB Atlas* cluster (copy its connection string into `server/.env`)

## 2. Setup and run
```bash
npm run install:all     # installs root, server and client packages
npm run seed            # optional: adds sample applications and interviews
npm run dev             # starts API (port 5000) and website (port 5173) together
```
Open **http://localhost:5173**.

You can also run them separately in two terminals: `npm run server` and `npm run client`.

`server/.env` is already created with local defaults:
```
PORT=5000
MONGO_URI=mongodb://127.0.0.1:27017/jobtrail
CLIENT_URL=http://localhost:5173
```
For Atlas use something like  
`MONGO_URI=mongodb+srv://<user>:<password>@<cluster>.mongodb.net/jobtrail`

`npm run seed` only adds data when the database is empty. `npm run seed:reset` **deletes everything** and adds fresh samples.

## 3. API
Base URL: `http://localhost:5000/api`

| Method | Endpoint | Purpose |
|---|---|---|
| GET | `/health` | server + database status |
| GET | `/applications` | list all applications |
| POST | `/applications` | create an application |
| GET | `/applications/:id` | one application |
| PUT | `/applications/:id` | update (send only the changed fields) |
| DELETE | `/applications/:id` | delete it and its interviews |
| GET / POST | `/interviews` | list / create interviews |
| GET / PUT / DELETE | `/interviews/:id` | read / update / delete one interview |

Validation errors return `400` with `{ "message": "...", "errors": { field: "..." } }`.

Dates are stored as text (`YYYY-MM-DD` for applications, `YYYY-MM-DDTHH:mm` for interviews) so there are no timezone shifts.

## 4. Deploy
1. **Database:** create a MongoDB Atlas cluster, create a database user, allow network access, copy the connection string.
2. **Backend (Render / Railway):** root directory `server`, build command `npm install`, start command `npm start`. Environment variables: `MONGO_URI` (Atlas string) and `CLIENT_URL` (your Vercel URL, e.g. `https://jobtrail.vercel.app`).
3. **Frontend (Vercel):** root directory `client`, framework Vite. Add the environment variable `VITE_API_URL=https://your-backend.onrender.com/api`.

Never upload `server/.env` to GitHub (it is in `.gitignore`); set the values in the hosting dashboard instead.
