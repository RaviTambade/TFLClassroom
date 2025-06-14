
## 🔧 What Will Change?

Currently, messages are **ephemeral** (exist only in memory).
With MongoDB:

* All messages will be **saved per room**
* When a user joins a room, past messages will be **loaded from the database**
* We’ll use **Mongoose** as the ODM for ease of use

---

## 📦 Step 1: Install Mongoose

```bash
npm install mongoose
```

---

## 🗂️ Step 2: Create a Message Model

Create a new file `models/Message.js`:

```js
const mongoose = require('mongoose');

const MessageSchema = new mongoose.Schema({
  username: String,
  room: String,
  text: String,
  timestamp: { type: Date, default: Date.now }
});

module.exports = mongoose.model('Message', MessageSchema);
```

---

## 🔗 Step 3: Connect MongoDB in `server.js`

At the top of `server.js`, add:

```js
const mongoose = require('mongoose');
const Message = require('./models/Message'); // import the model

mongoose.connect('mongodb://localhost:27017/groupchat', {
  useNewUrlParser: true,
  useUnifiedTopology: true
}).then(() => console.log('✅ MongoDB connected'))
  .catch((err) => console.error('❌ MongoDB connection error:', err));
```

---

## 🧠 Step 4: Modify Socket Logic to Store and Load Messages

Update the `io.on('connection')` block in `server.js`:

```js
io.on('connection', (socket) => {
  console.log('✅ New client connected');

  socket.on('joinRoom', async ({ username, room }) => {
    socket.join(room);
    socket.username = username;
    socket.room = room;

    // Load last 20 messages
    const messages = await Message.find({ room }).sort({ timestamp: 1 }).limit(20);
    messages.forEach(msg => {
      socket.emit('message', `${msg.username}: ${msg.text}`);
    });

    // Welcome and notify
    socket.emit('message', `👋 Welcome ${username} to ${room} room!`);
    socket.to(room).emit('message', `📢 ${username} has joined the room`);

    // On chat message
    socket.on('chatMessage', async (msg) => {
      const message = new Message({
        username,
        room,
        text: msg
      });
      await message.save(); // Save to DB

      io.to(room).emit('message', `${username}: ${msg}`);
    });

    socket.on('disconnect', () => {
      io.to(room).emit('message', `🚪 ${username} left the room`);
    });
  });
});
```

---

## 🧪 Step 5: Test Your Chat

1. Start **MongoDB** using:

   ```bash
   mongod
   ```
2. Run your server:

   ```bash
   node server.js
   ```
3. Open multiple tabs and chat in a room
4. Refresh the page or rejoin — you'll see **previous messages reloaded**!

---

## 🧼 Optional Cleanup: Add `models/` Folder

Your project structure now:

```
group-chat-app/
├── models/
│   └── Message.js
├── public/
│   ├── index.html
│   └── chat.html
├── server.js
└── package.json
```

---

## 🧠 Mentor Reflection

> “A chat without memory is like a library with no books. MongoDB is our bookshelf — a simple, scalable way to make sure every coder's conversation is remembered.”
> — Mentor Ravi Tambade


