mkdir socketio-chat
cd socketio-chat
mkdir server client
cd server
npm init -y
npm install express socket.io cors
npm install nodemon --save-dev
const express = require("express");
const http = require("http");
const { Server } = require("socket.io");
const cors = require("cors");

const app = express();
app.use(cors());

const server = http.createServer(app);
const io = new Server(server, {
    cors: { origin: "http://localhost:5173", methods: ["GET", "POST"] }
});

io.on("connection", (socket) => {
    console.log("User connected:", socket.id);

    socket.on("send_message", (data) => {
        io.emit("receive_message", data);
    });

    socket.on("disconnect", () => {
        console.log("User disconnected:", socket.id);
    });
});

server.listen(3000, () => {
    console.log("Server running on port 3000");
});
cd ../client
npm create vite@latest
# Choose React
cd client
npm install
npm install socket.io-client
import { io } from "socket.io-client";

const socket = io("http://localhost:3000");
export default socket;
import { useState, useEffect } from "react";
import socket from "./socket";

function App() {
  const [message, setMessage] = useState("");
  const [messages, setMessages] = useState([]);

  useEffect(() => {
    socket.on("receive_message", (data) => {
      setMessages((prev) => [...prev, data]);
    });
  }, []);

  const sendMessage = () => {
    socket.emit("send_message", { text: message, time: new Date().toLocaleTimeString() });
    setMessage("");
  };

  return (
    <div>
      <h2>Global Chat</h2>
      <div>
        {messages.map((msg, idx) => (
          <p key={idx}>{msg.text} <small>{msg.time}</small></p>
        ))}
      </div>
      <input
        value={message}
        onChange={(e) => setMessage(e.target.value)}
        placeholder="Type here..."
      />
      <button onClick={sendMessage}>Send</button>
    </div>
  );
}

export default App;
if (Notification.permission === "granted") {
  new Notification("New message received");
}
