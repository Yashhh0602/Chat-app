# Chat App

A real time one to one chat app built in 2022 while learning full stack development. Node.js, Express and MongoDB on the backend, React and Vite on the frontend, with Socket.IO delivering messages and online status.

> Status: learning project, not currently deployed. The code is maintained and runs locally.

## Features

* **One to one conversations.** Pick a user from the sidebar and chat. History is stored in MongoDB.
* **Real time delivery.** New messages are pushed to the receiver over Socket.IO, with a notification sound.
* **Online status.** The server tracks connected users and broadcasts who is online.
* **Accounts.** Sign up, log in and log out. Passwords are hashed with bcrypt.
* **Cookie based auth.** A JWT is stored in an httpOnly, sameSite strict cookie and checked on every protected route.

## Tech Stack

| Layer     | Technology                                   |
|-----------|----------------------------------------------|
| Backend   | Node.js, Express                             |
| Real time | Socket.IO                                    |
| Database  | MongoDB, Mongoose                            |
| Auth      | JWT (httpOnly cookie), bcryptjs              |
| Frontend  | React, Vite, Zustand, Tailwind CSS, daisyUI  |

## Project Structure

```
Chat-app/
├── backend/
│   ├── controllers/   # auth, message and user handlers
│   ├── db/            # MongoDB connection
│   ├── middleware/    # protectRoute (JWT cookie check)
│   ├── models/        # User, Conversation, Message
│   ├── routes/        # REST routes
│   ├── socket/        # Socket.IO server and online users
│   ├── utils/         # token generation
│   └── server.js      # entry point, also serves the built frontend
├── frontend/
│   └── src/
│       ├── components/  # messages, sidebar, skeletons
│       ├── context/     # auth and socket context
│       ├── hooks/       # data and socket hooks
│       ├── pages/       # home, login, signup
│       └── zustand/     # conversation state
└── package.json
```

## Getting Started

### Prerequisites

* Node.js 18 or later
* MongoDB, local or a free Atlas cluster

### Environment variables

Create a `.env` file in the root directory:

```
PORT=5000
MONGO_DB_URI=your_mongodb_connection_string
JWT_SECRET=at_least_32_random_characters
CLIENT_ORIGIN=http://localhost:5173
NODE_ENV=development
```

* `JWT_SECRET` signs the auth cookie. The server refuses to start if it is missing or shorter than 32 characters. Generate one with:
  ```
  node -e "console.log(require('crypto').randomBytes(48).toString('hex'))"
  ```
* `CLIENT_ORIGIN` is a comma separated list of origins allowed to open a Socket.IO connection. Defaults to `http://localhost:5173,http://localhost:5000`.

Optional, in `frontend/.env`:

```
VITE_SOCKET_URL=http://localhost:5000
```

If unset, the frontend connects to the origin it was served from.

### Production mode

```
git clone https://github.com/Yashhh0602/Chat-app.git
cd Chat-app
npm run build   # installs backend and frontend dependencies, builds the frontend
npm start
```

The server serves the built frontend and the API on http://localhost:5000.

### Development mode

Run the backend and frontend separately for hot reload:

```
npm run server            # backend on :5000 with nodemon
cd frontend && npm run dev  # frontend on :5173
```

Vite proxies `/api` requests to the backend.

## API Endpoints

### Auth

| Method | Endpoint           | Description                    |
|--------|--------------------|--------------------------------|
| POST   | /api/auth/signup   | Create an account              |
| POST   | /api/auth/login    | Log in and set the JWT cookie  |
| POST   | /api/auth/logout   | Clear the JWT cookie           |

### Users

| Method | Endpoint    | Description                        |
|--------|-------------|------------------------------------|
| GET    | /api/users  | Users for the sidebar (protected)  |

### Messages

| Method | Endpoint                | Description                                  |
|--------|-------------------------|----------------------------------------------|
| GET    | /api/messages/:id       | Conversation history with a user (protected) |
| POST   | /api/messages/send/:id  | Send a message to a user (protected)         |

Protected routes return `401` when the cookie is missing, invalid or expired.

## Socket Events

| Event           | Direction        | Description                      |
|-----------------|------------------|----------------------------------|
| getOnlineUsers  | Server to client | Current list of online user IDs  |
| newMessage      | Server to client | A new message for the receiver   |

## Design Notes

* **httpOnly cookie instead of localStorage.** JavaScript on the page cannot read the token, so an XSS bug cannot steal it. sameSite strict blocks the cookie on cross site requests, which covers CSRF for this app.
* **Online users live in server memory.** A map of user ID to socket ID is kept in the Socket.IO process. Simple and fast for a single instance.
* **Messages are saved first, then pushed.** The REST call writes to MongoDB, then emits `newMessage` to the receiver's socket if they are online. If they are offline, they load it from history later.

## What I'd Change Today

* **Horizontal scaling.** The in memory online user map breaks with more than one server instance. I'd move presence to Redis and use the Socket.IO Redis adapter so events reach users on any instance.
* **Delivery guarantees.** Add message acknowledgements, read receipts and retry on reconnect.
* **Tests.** Integration tests for auth and message routes, plus socket event tests.
* **Rate limiting and input validation** on auth and message endpoints.
* **Group conversations.** The Conversation model already stores a participants array, so the schema mostly supports it.

## Author

**Yashvardhan Khanduja**
[GitHub](https://github.com/Yashhh0602) · [LinkedIn](YOUR_LINKEDIN_URL) · vardhan.yash265@gmail.com
