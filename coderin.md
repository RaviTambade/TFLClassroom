## 🎯 Project Goal

> Build a real-time **group chat** app where:
>
> * Users can **join rooms** (e.g., "Java", "Python", "C#")
> * Messages are **broadcast only within rooms**
> * New users see who joins or leaves
> * Simple and powerful backend with `Socket.IO`


## 🧙 Mentor Story: “The Coder’s Inn”

> In the magical land of **Codeville**, there was an inn with many rooms—Java, Python, C#. Coders from all over the world visited the inn to discuss topics of their choice.
>
> Each room had its own fireplace (Socket room), and when someone spoke (sent a message), only people near that fireplace could hear it.
>
> You’re the innkeeper now. Let’s build this magical inn with rooms and real-time chats.

---

## 🧱 Project Structure

```
group-chat-app/
├── public/
│   ├── index.html
│   └── chat.html
├── server.js
└── package.json
```

---

## 📦 Step 1: Setup

```bash
mkdir group-chat-app
cd group-chat-app
npm init -y
npm install express socket.io
```

---

## 📄 Step 2: `server.js`

```js
const express = require('express');
const http = require('http');
const { Server } = require('socket.io');

const app = express();
const server = http.createServer(app);
const io = new Server(server);

app.use(express.static('public'));

app.get('/', (req, res) => {
  res.sendFile(__dirname + '/public/index.html');
});

app.get('/chat', (req, res) => {
  res.sendFile(__dirname + '/public/chat.html');
});

io.on('connection', (socket) => {
  console.log('✅ New client connected');

  socket.on('joinRoom', ({ username, room }) => {
    socket.join(room);
    socket.username = username;
    socket.room = room;

    // Welcome user
    socket.emit('message', `👋 Welcome ${username} to ${room} room!`);

    // Broadcast to others
    socket.to(room).emit('message', `📢 ${username} has joined the room`);

    // Handle chat messages
    socket.on('chatMessage', (msg) => {
      io.to(room).emit('message', `${username}: ${msg}`);
    });

    // Handle disconnect
    socket.on('disconnect', () => {
      io.to(room).emit('message', `🚪 ${username} left the room`);
    });
  });
});

server.listen(3000, () => {
  console.log('🚀 Server is running on http://localhost:3000');
});
```

---

## 🖼️ Step 3: `public/index.html` – Join Room UI

```html
<!DOCTYPE html>
<html>
<head><title>Join Chat Room</title></head>
<body>
  <h2>🏠 Join the Coder’s Inn</h2>
  <form id="joinForm">
    <input id="username" placeholder="Your name" required />
    <select id="room">
      <option value="Java">Java</option>
      <option value="Python">Python</option>
      <option value="CSharp">C#</option>
    </select>
    <button type="submit">Join Room</button>
  </form>

  <script>
    document.getElementById('joinForm').addEventListener('submit', function(e) {
      e.preventDefault();
      const username = document.getElementById('username').value;
      const room = document.getElementById('room').value;
      window.location.href = `/chat?username=${username}&room=${room}`;
    });
  </script>
</body>
</html>
```

---

## 💬 Step 4: `public/chat.html` – Chat Room

```html
<!DOCTYPE html>
<html>
<head><title>Chat Room</title></head>
<body>
  <h2 id="roomTitle">Chat Room</h2>
  <ul id="messages"></ul>
  <input id="msgInput" autocomplete="off" placeholder="Type a message..." />
  <button onclick="sendMessage()">Send</button>

  <script src="/socket.io/socket.io.js"></script>
  <script>
    const socket = io();
    const params = new URLSearchParams(window.location.search);
    const username = params.get('username');
    const room = params.get('room');

    document.getElementById('roomTitle').innerText = `Room: ${room}`;

    socket.emit('joinRoom', { username, room });

    socket.on('message', function(msg) {
      const li = document.createElement('li');
      li.textContent = msg;
      document.getElementById('messages').appendChild(li);
    });

    function sendMessage() {
      const input = document.getElementById('msgInput');
      const message = input.value;
      socket.emit('chatMessage', message);
      input.value = '';
    }
  </script>
</body>
</html>
```

---

## 🧪 Try It Out!

1. Run the app:

   ```bash
   node server.js
   ```
2. Open `http://localhost:3000`
3. Join as **Alice** in **Java**
4. Open another browser/tab and join as **Bob** in **Python**
5. They can chat *only* within their rooms!

---

## 🔒 Possible Extensions

* ✅ Add **user list per room**
* ✅ Add **message timestamp**
* ✅ Add **private messages (DM)**
* ✅ Add **authentication** (e.g., JWT)
* ✅ Save messages in **MongoDB**

---

## 🧠 Mentor Takeaway

> “A well-structured app is like a clean room in an inn—organized, welcoming, and ready for conversations.
> Each room serves a purpose, and as the innkeeper, it’s your job to maintain peace, privacy, and performance.”
> — Mentor Ravi Tambade


