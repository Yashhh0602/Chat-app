# 💬 Chat App

A real time one to one chat app. Node.js, Express and MongoDB on the backend, React and Vite on the frontend, with Socket.IO delivering new messages and online status.

---

## 🚀 Features

- **One to one conversations.** Pick a user from the sidebar and chat. History is stored in MongoDB.
- **Real time delivery.** New messages are pushed to the receiver over Socket.IO, with a notification sound.
- **Online status.** The server tracks connected users and broadcasts who is online.
- **Accounts.** Sign up, log in and log out. Passwords are hashed with bcrypt.
- **Cookie based auth.** A JWT is stored in an httpOnly, sameSite strict cookie and checked on every protected route.

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| **Backend** | Node.js, Express |
| **Real time** | Socket.IO |
| **Database** | MongoDB, Mongoose |
| **Auth** | JWT (httpOnly cookie), bcryptjs |
| **Frontend** | React, Vite, Zustand, Tailwind CSS, daisyUI |

---

## 📁 Project Structure

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

---

## ⚙️ Getting Started

### Prerequisites

- Node.js
- MongoDB (local or Atlas)

### Install and build

```bash
git clone https://github.com/Yashhh0602/Chat-app.git
cd Chat-app

# installs backend and frontend dependencies and builds the frontend
npm run build
```

### Environment variables

Create a `.env` file in the root directory:

```env
PORT=5000
MONGO_DB_URI=your_mongodb_connection_string
JWT_SECRET=at_least_32_random_characters
CLIENT_ORIGIN=http://localhost:5173
NODE_ENV=development
```

- `JWT_SECRET` signs the auth cookie. The server refuses to start if it is missing or shorter than 32 characters. Generate one with `node -e "console.log(require('crypto').randomBytes(48).toString('hex'))"`.
- `CLIENT_ORIGIN` is the comma separated list of origins allowed to open a Socket.IO connection. It defaults to `http://localhost:5173,http://localhost:5000`.

### Run

```bash
npm start        # or: npm run server (nodemon)
```

The server serves the built frontend and the API on `http://localhost:5000`.

---

## 🔌 API Endpoints

### Auth
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/auth/signup` | Create an account |
| POST | `/api/auth/login` | Log in and set the JWT cookie |
| POST | `/api/auth/logout` | Clear the JWT cookie |

### Users
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/users` | Users for the sidebar (protected) |

### Messages
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/messages/:id` | Conversation history with a user (protected) |
| POST | `/api/messages/send/:id` | Send a message to a user (protected) |

---

## 📡 Socket Events

| Event | Direction | Description |
|-------|-----------|-------------|
| `getOnlineUsers` | Server → Client | Current list of online user IDs |
| `newMessage` | Server → Client | A new message for the receiver |

---

## 👤 Author

**Yashvardhan Khanduja**  
[GitHub](https://github.com/Yashhh0602) · [LinkedIn](https://www.linkedin.com/in/yashvardhan-khanduja07) · vardhan.yash265@gmail.com
