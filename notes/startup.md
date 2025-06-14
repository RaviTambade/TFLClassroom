✅ The **starter files for your Mini Classroom System** project have been created. They include:

* **HTML pages** for login and dashboard.
* **Node.js backend** with Express and MongoDB.
* **Basic REST API** for assignment handling.
* **Socket.IO** integration stub for real-time features.

// 📁 server/server.js
const express = require("express");
const http = require("http");
const socketIo = require("socket.io");
const cors = require("cors");
const mongoose = require("mongoose");
const assignmentRoutes = require("./routes/assignments");

const app = express();
const server = http.createServer(app);
const io = socketIo(server);

mongoose.connect("mongodb://localhost:27017/tfl_classroom", {
  useNewUrlParser: true,
  useUnifiedTopology: true,
});

app.use(cors());
app.use(express.json());
app.use("/api/assignments", assignmentRoutes);

// Basic WebSocket setup
io.on("connection", (socket) => {
  console.log("A user connected");

  socket.on("join-room", (room) => {
    socket.join(room);
    io.to(room).emit("message", "A user joined the room: " + room);
  });

  socket.on("message", ({ room, message }) => {
    io.to(room).emit("message", message);
  });

  socket.on("disconnect", () => {
    console.log("User disconnected");
  });
});

const PORT = 3000;
server.listen(PORT, () => {
  console.log(`Server running on http://localhost:${PORT}`);
});


// 📁 server/models/assignment.js
const mongoose = require("mongoose");

const AssignmentSchema = new mongoose.Schema({
  title: String,
  content: String,
  dueDate: Date,
});

module.exports = mongoose.model("Assignment", AssignmentSchema);


// 📁 server/routes/assignments.js
const express = require("express");
const router = express.Router();
const Assignment = require("../models/assignment");

router.get("/", async (req, res) => {
  const assignments = await Assignment.find();
  res.json(assignments);
});

router.post("/", async (req, res) => {
  const { title, content, dueDate } = req.body;
  const newAssignment = new Assignment({ title, content, dueDate });
  await newAssignment.save();
  res.status(201).json({ message: "Assignment added" });
});

module.exports = router;


// 📁 client/login.html
<!DOCTYPE html>
<html>
<head>
  <title>Login - TFL Classroom</title>
</head>
<body>
  <h2>Login</h2>
  <form onsubmit="login(event)">
    <input type="text" id="username" placeholder="Enter username" required />
    <select id="role">
      <option value="student">Student</option>
      <option value="teacher">Teacher</option>
    </select>
    <button type="submit">Login</button>
  </form>
  <script>
    function login(e) {
      e.preventDefault();
      const username = document.getElementById("username").value;
      const role = document.getElementById("role").value;
      localStorage.setItem("user", JSON.stringify({ username, role }));
      window.location.href = "dashboard.html";
    }
  </script>
</body>
</html>


// 📁 client/dashboard.html
<!DOCTYPE html>
<html>
<head>
  <title>Dashboard - TFL Classroom</title>
</head>
<body>
  <h2 id="welcome"></h2>
  <div id="studentView" style="display:none;">
    <h3>Assignments</h3>
    <ul id="assignmentsList"></ul>
  </div>
  <div id="teacherView" style="display:none;">
    <h3>Upload Assignment</h3>
    <form onsubmit="uploadAssignment(event)">
      <input type="text" id="title" placeholder="Title" required />
      <textarea id="content" placeholder="Content"></textarea>
      <input type="date" id="dueDate" />
      <button type="submit">Upload</button>
    </form>
  </div>

  <script>
    const user = JSON.parse(localStorage.getItem("user"));
    if (!user) window.location.href = "login.html";
    document.getElementById("welcome").innerText = `Welcome, ${user.username}`;

    if (user.role === "student") {
      document.getElementById("studentView").style.display = "block";
      fetch("http://localhost:3000/api/assignments")
        .then(res => res.json())
        .then(data => {
          const list = document.getElementById("assignmentsList");
          data.forEach(a => {
            const li = document.createElement("li");
            li.innerText = `${a.title} - Due: ${new Date(a.dueDate).toDateString()}`;
            list.appendChild(li);
          });
        });
    } else {
      document.getElementById("teacherView").style.display = "block";
    }

    function uploadAssignment(e) {
      e.preventDefault();
      const assignment = {
        title: document.getElementById("title").value,
        content: document.getElementById("content").value,
        dueDate: document.getElementById("dueDate").value,
      };
      fetch("http://localhost:3000/api/assignments", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(assignment),
      }).then(() => alert("Assignment Uploaded"));
    }
  </script>
</body>
</html>




## 🧑‍🏫 **📁 server/server.js – The Server Entry Point**

> **Mentor explains:**
> “Think of this file as the **main control room** of your backend. It listens to every knock on the door (API request) and opens the right one (routes or socket).”

### Key Components:

* `express`: Used to handle HTTP routes.
* `http`: To create an HTTP server that supports WebSockets.
* `socket.io`: Enables **real-time communication** for chat or attendance.
* `mongoose`: Connects your app to MongoDB.
* `assignmentRoutes`: RESTful API for managing assignments.

### What it does:

* Initializes the server
* Connects to MongoDB on `mongodb://localhost:27017/tfl_classroom`
* Defines REST endpoints at `/api/assignments`
* Handles socket connections like:

  * `join-room`
  * `message`
  * `disconnect`

---

## 🧑‍🏫 **📁 server/models/assignment.js – MongoDB Assignment Schema**

> **Mentor explains:**
> “This is the **blueprint** for how assignments are stored in the database. Just like a teacher sets the format for homework, MongoDB needs to know the structure.”

### Contains:

* `title`: Assignment title
* `content`: Description or details
* `dueDate`: Deadline

### How it works:

* Used with `mongoose.model()` to create a reusable `Assignment` object
* When you create a new assignment via API, it gets stored in this format

---

## 🧑‍🏫 **📁 server/routes/assignments.js – API Route File**

> **Mentor explains:**
> “This is your **courier route**. When the front end sends a request to `/api/assignments`, this file decides what to do with it.”

### Routes:

* `GET /`: Fetches all assignments from the DB
* `POST /`: Adds a new assignment to the DB

### Usage:

* Teacher uses this to add homework
* Student uses this to read assignments

---

## 🧑‍🏫 **📁 client/login.html – Login Page**

> **Mentor explains:**
> “Every journey starts at the door. This is the **entry point** of our classroom where users identify themselves as either a teacher or a student.”

### What it includes:

* A simple login form with:

  * Text input for `username`
  * Role selection (Student or Teacher)

### Logic:

* On submit: saves the username and role to `localStorage`
* Redirects to `dashboard.html`

> *Note:* We don’t use a real authentication system yet—this is just simulation to keep it simple and focused on architecture.

---

## 🧑‍🏫 **📁 client/dashboard.html – User Dashboard**

> **Mentor explains:**
> “Once the student or teacher enters, this is their **personal classroom dashboard** — showing relevant content based on who they are.”

### Sections:

* **Welcome message**

* **Student view:**

  * Fetches assignments via `GET /api/assignments`
  * Displays them in a list

* **Teacher view:**

  * Form to add a new assignment via `POST /api/assignments`

### Frontend Logic:

* Checks user role from `localStorage`
* Displays appropriate dashboard
* Uses `fetch` API to talk to the backend

> *This teaches you:* DOM manipulation, conditionally rendering UI, using REST APIs from frontend.



## 🗃️ Folder Summary

| Folder/File             | Role in Project                                |
| ----------------------- | ---------------------------------------------- |
| `server.js`             | Central server and WebSocket entry             |
| `models/assignment.js`  | Database schema for assignment                 |
| `routes/assignments.js` | REST API routes for assignment logic           |
| `login.html`            | Basic user login form                          |
| `dashboard.html`        | Role-based student/teacher UI with fetch logic |



## 🧠 What Students Learn from This

* **Client-server separation**
* **REST vs real-time (Socket.IO)**
* **Basic CRUD** using MongoDB
* **Role-based rendering**
* **Simulated session** using `localStorage`


## ✅ Next Steps We Can Add (Phase 2)

Would you like me to now:

* Add `Socket.IO` chat system to the classroom?
* Add `classroom.html` with live messaging?
* Include MongoDB Atlas config for cloud hosting?

Let me know which features or explanations you'd like next.


✅ The **Socket.IO chat system** has been integrated into your classroom project. Here's what was added:

### 💬 Chat Features Now Available:

* Users can join a classroom by entering a "Room Name"
* Real-time chat using `Socket.IO`
* Messages are displayed in a scrollable chat box
* System messages (like someone joining) are also shown

### 🧑‍🏫 Mentor Tip:

This lets students and teachers interact live — simulating a collaborative learning environment. It’s a great step toward building real-time systems like Zoom chat, Slack channels, or collaborative coding platforms.

Let me know if you'd like:

* WebRTC video/audio support
* Chat history saving in MongoDB
* Group breakout rooms or file sharing

We can keep building this classroom system together 🚀


// 📁 server/server.js
const express = require("express");
const http = require("http");
const socketIo = require("socket.io");
const cors = require("cors");
const mongoose = require("mongoose");
const assignmentRoutes = require("./routes/assignments");

const app = express();
const server = http.createServer(app);
const io = socketIo(server, {
  cors: {
    origin: "*",
    methods: ["GET", "POST"]
  }
});

mongoose.connect("mongodb://localhost:27017/tfl_classroom", {
  useNewUrlParser: true,
  useUnifiedTopology: true,
});

app.use(cors());
app.use(express.json());
app.use("/api/assignments", assignmentRoutes);

io.on("connection", (socket) => {
  console.log("A user connected: " + socket.id);

  socket.on("join-room", (room) => {
    socket.join(room);
    console.log(`User ${socket.id} joined room: ${room}`);
    io.to(room).emit("message", `User joined the room: ${socket.id}`);
  });

  socket.on("chat-message", ({ room, user, message }) => {
    io.to(room).emit("chat-message", { user, message });
  });

  socket.on("disconnect", () => {
    console.log("User disconnected: " + socket.id);
  });
});

const PORT = 3000;
server.listen(PORT, () => {
  console.log(`Server running on http://localhost:${PORT}`);
});


// 📁 server/models/assignment.js
const mongoose = require("mongoose");

const AssignmentSchema = new mongoose.Schema({
  title: String,
  content: String,
  dueDate: Date,
});

module.exports = mongoose.model("Assignment", AssignmentSchema);


// 📁 server/routes/assignments.js
const express = require("express");
const router = express.Router();
const Assignment = require("../models/assignment");

router.get("/", async (req, res) => {
  const assignments = await Assignment.find();
  res.json(assignments);
});

router.post("/", async (req, res) => {
  const { title, content, dueDate } = req.body;
  const newAssignment = new Assignment({ title, content, dueDate });
  await newAssignment.save();
  res.status(201).json({ message: "Assignment added" });
});

module.exports = router;


// 📁 client/login.html
<!DOCTYPE html>
<html>
<head>
  <title>Login - TFL Classroom</title>
</head>
<body>
  <h2>Login</h2>
  <form onsubmit="login(event)">
    <input type="text" id="username" placeholder="Enter username" required />
    <select id="role">
      <option value="student">Student</option>
      <option value="teacher">Teacher</option>
    </select>
    <button type="submit">Login</button>
  </form>
  <script>
    function login(e) {
      e.preventDefault();
      const username = document.getElementById("username").value;
      const role = document.getElementById("role").value;
      localStorage.setItem("user", JSON.stringify({ username, role }));
      window.location.href = "dashboard.html";
    }
  </script>
</body>
</html>


// 📁 client/dashboard.html
<!DOCTYPE html>
<html>
<head>
  <title>Dashboard - TFL Classroom</title>
  <script src="https://cdn.socket.io/4.7.2/socket.io.min.js"></script>
</head>
<body>
  <h2 id="welcome"></h2>
  <div id="studentView" style="display:none;">
    <h3>Assignments</h3>
    <ul id="assignmentsList"></ul>
  </div>
  <div id="teacherView" style="display:none;">
    <h3>Upload Assignment</h3>
    <form onsubmit="uploadAssignment(event)">
      <input type="text" id="title" placeholder="Title" required />
      <textarea id="content" placeholder="Content"></textarea>
      <input type="date" id="dueDate" />
      <button type="submit">Upload</button>
    </form>
  </div>

  <div>
    <h3>Classroom Chat</h3>
    <input type="text" id="room" placeholder="Room name" />
    <button onclick="joinRoom()">Join Room</button>
    <div id="chatBox" style="border:1px solid #ccc; padding:10px; margin-top:10px; height:150px; overflow-y:auto;"></div>
    <input type="text" id="chatInput" placeholder="Type a message" />
    <button onclick="sendMessage()">Send</button>
  </div>

  <script>
    const user = JSON.parse(localStorage.getItem("user"));
    if (!user) window.location.href = "login.html";
    document.getElementById("welcome").innerText = `Welcome, ${user.username}`;

    if (user.role === "student") {
      document.getElementById("studentView").style.display = "block";
      fetch("http://localhost:3000/api/assignments")
        .then(res => res.json())
        .then(data => {
          const list = document.getElementById("assignmentsList");
          data.forEach(a => {
            const li = document.createElement("li");
            li.innerText = `${a.title} - Due: ${new Date(a.dueDate).toDateString()}`;
            list.appendChild(li);
          });
        });
    } else {
      document.getElementById("teacherView").style.display = "block";
    }

    function uploadAssignment(e) {
      e.preventDefault();
      const assignment = {
        title: document.getElementById("title").value,
        content: document.getElementById("content").value,
        dueDate: document.getElementById("dueDate").value,
      };
      fetch("http://localhost:3000/api/assignments", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(assignment),
      }).then(() => alert("Assignment Uploaded"));
    }

    const socket = io("http://localhost:3000");
    let currentRoom = "";

    function joinRoom() {
      const room = document.getElementById("room").value;
      currentRoom = room;
      socket.emit("join-room", room);
    }

    function sendMessage() {
      const message = document.getElementById("chatInput").value;
      socket.emit("chat-message", {
        room: currentRoom,
        user: user.username,
        message
      });
      document.getElementById("chatInput").value = "";
    }

    socket.on("chat-message", ({ user, message }) => {
      const box = document.getElementById("chatBox");
      box.innerHTML += `<p><strong>${user}:</strong> ${message}</p>`;
      box.scrollTop = box.scrollHeight;
    });

    socket.on("message", (msg) => {
      const box = document.getElementById("chatBox");
      box.innerHTML += `<p style='color:gray;'>${msg}</p>`;
      box.scrollTop = box.scrollHeight;
    });
  </script>
</body>
</html>

