<!DOCTYPE html>
<html>
<head>
  <title>Öffentlicher Chat</title>
  <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
  <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>
  <style>
    body { font-family: sans-serif; padding: 20px; background: #f0f0f0; }
    #chat { max-height: 300px; overflow-y: auto; border: 1px solid #ccc; padding: 10px; background: white; }
    .msg { margin: 5px 0; }
  </style>
</head>
<body>

<h2>💬 Öffentlicher Chat</h2>
<p>🟢 Online: <span id="online-count">0</span></p>

<div id="chat"></div>

<input type="text" id="name" placeholder="Dein Name" /><br><br>
<input type="text" id="message" placeholder="Nachricht..." />
<button onclick="sendMessage()">Senden</button>

<script>
  // 🔧 DEIN FIREBASE CONFIG HIER EINTRAGEN
  var firebaseConfig = {
    apiKey: "DEIN_API_KEY",
    authDomain: "DEIN_PROJEKT.firebaseapp.com",
    databaseURL: "https://DEIN_PROJEKT.firebaseio.com",
    projectId: "DEIN_PROJEKT_ID",
    storageBucket: "DEIN_PROJEKT.appspot.com",
    messagingSenderId: "DEIN_SENDER_ID",
    appId: "DEINE_APP_ID"
  };

  firebase.initializeApp(firebaseConfig);
  var db = firebase.database();

  // Nachrichten empfangen
  db.ref("messages").on("child_added", function(snapshot) {
    let data = snapshot.val();
    let chat = document.getElementById("chat");
    let div = document.createElement("div");
    div.className = "msg";
    div.textContent = `${data.name}: ${data.text}`;
    chat.appendChild(div);
    chat.scrollTop = chat.scrollHeight;
  });

  // Nachricht senden
  function sendMessage() {
    let name = document.getElementById("name").value;
    let text = document.getElementById("message").value;
    if (name && text) {
      db.ref("messages").push({ name, text });
      document.getElementById("message").value = "";
    }
  }

  // Online-Zähler
  const onlineRef = db.ref("onlineUsers");
  const myRef = onlineRef.push(true);
  myRef.onDisconnect().remove();

  onlineRef.on("value", (snapshot) => {
    document.getElementById("online-count").textContent = snapshot.numChildren();
  });
</script>

</body>
</html>
