
## 🧑‍🏫 **"Build Your Mini Online Classroom System"**

### 🛠️ Tech Stack (Simple & Effective)

| Layer     | Tool / Tech              | Purpose                                |
| --------- | ------------------------ | -------------------------------------- |
| Frontend  | HTML, CSS, JavaScript    | Student/Teacher Interface              |
| Backend   | Node.js + Express.js     | REST API and WebSocket handling        |
| Database  | MongoDB (local or cloud) | Store users, messages, assignments     |
| Real-time | Socket.IO                | Live chat / attendance / collaboration |
| Optional  | JWT, Bootstrap, Postman  | Security, UI, API testing              |

---

## 🎯 Learning Goals

By completing this project, students will:

* Understand **client-server interaction**
* Learn **IP/port configuration**
* Build **RESTful APIs** and real-time **WebSocket communication**
* Apply **CRUD** operations and database logic
* Work with **user roles** (student, teacher)
* Experience **collaborative feature design**

---

## 🧩 Project Structure

### 🌐 1. `Frontend` (HTML + JavaScript)

Pages:

* `/login.html`
* `/dashboard.html`
* `/classroom.html`

### ⚙️ 2. `Backend` (Node.js)

APIs:

* `POST /api/login`
* `GET /api/assignments`
* `POST /api/submit-assignment`
* WebSocket endpoint: `/live`

---

## 🧪 Phase-wise Implementation Plan

### ✅ **Phase 1: Setup & Login**

* Create a basic login page
* Use `POST /api/login` to authenticate
* Hardcode 2 roles: `student`, `teacher`

> ✅ *Outcome:* Understand HTTP POST, IP address, ports (e.g., localhost:3000)

---

### ✅ **Phase 2: Dashboard per Role**

* If teacher logs in, show:
  → Upload Assignment
  → View Submissions

* If student logs in, show:
  → View Assignments
  → Submit Homework

> ✅ *Outcome:* Learn conditional rendering and role-based routing

---

### ✅ **Phase 3: Live Class using WebSocket (Socket.IO)**

* Connect students and teacher to a “room”
* Enable:

  * Real-time chat
  * Real-time attendance (user joins/leave)

> ✅ *Outcome:* Understand sockets, events (`connect`, `message`, `disconnect`)

---

### ✅ **Phase 4: Assignment System**

* Teacher uploads assignment (title, date, content)
* Students can:

  * View all assignments (`GET /api/assignments`)
  * Submit solution (`POST /api/submit-assignment`)

> ✅ *Outcome:* Understand basic MongoDB schema, RESTful routing

---

### ✅ **Phase 5: Chat System per Class**

* Reuse socket to create room-based chat
* Broadcast messages only to room participants

> ✅ *Outcome:* Learn namespace vs room in socket communication

---

## 📜 Example User Stories

1. **As a student**, I want to submit my assignment so that my teacher can review it.
2. **As a teacher**, I want to broadcast messages to my class in real-time.
3. **As a student**, I want to see all upcoming assignments and their deadlines.
4. **As a teacher**, I want to track which students are present in class today.

---

## 🧠 Extra Challenges (Optional)

* Add JWT-based login
* Add session timeout
* Add voice call using WebRTC
* Host project on Render/Glitch/Heroku
* Create multiple class rooms with different URLs

---

## 📁 Sample Folder Structure

```
tfl-mini-classroom/
├── client/
│   ├── login.html
│   ├── dashboard.html
│   ├── classroom.html
│   └── script.js
├── server/
│   ├── server.js
│   ├── routes/
│   │   └── assignments.js
│   ├── models/
│   │   └── assignment.js
│   └── sockets/
│       └── classroom.js
├── package.json
└── README.md
```

---

## 🚀 How to Run (Locally)

1. Clone the repo or create files manually.
2. Run:

```bash
npm install
npm start
```

3. Open:

```
http://localhost:3000/login.html
```

---

## 🎓 How to Use in Class (Mentor Flow)

| Day | Activity                                           |
| --- | -------------------------------------------------- |
| 1   | Introduce Networking, Client-Server, Setup Project |
| 2   | Implement Login & Role-Based Dashboard             |
| 3   | Real-Time Class via Socket.IO                      |
| 4   | Assignment Upload and Submission                   |
| 5   | Collaborative Chat + Bonus Challenges              |
| 6   | Present Projects in Teams                          |

