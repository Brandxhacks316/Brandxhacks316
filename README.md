<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ABDUL BASIT CHAT PORTAL</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Courier New', Courier, monospace; }
        body { background-color: #030000; color: #ff0033; overflow: hidden; height: 100vh; }
        
        /* HACKER ANIMATIONS & EFFECTS */
        canvas#matrix { position: fixed; top: 0; left: 0; width: 100%; height: 100%; z-index: 0; opacity: 0.6; }
        .scanlines { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: linear-gradient(rgba(18, 16, 16, 0) 50%, rgba(0, 0, 0, 0.25) 50%), linear-gradient(90deg, rgba(255, 0, 0, 0.06), rgba(0, 255, 0, 0.02), rgba(0, 0, 255, 0.06)); background-size: 100% 2px, 3px 100%; z-index: 5; pointer-events: none; }
        
        .screen { position: absolute; top: 0; left: 0; width: 100%; height: 100%; z-index: 10; display: none; flex-direction: column; align-items: center; justify-content: center; background: radial-gradient(circle, rgba(20,0,0,0.85) 0%, rgba(0,0,0,0.95) 100%); }
        .active { display: flex !important; }

        .owner-tag { color: #00ffcc; font-size: 1.5rem; margin-bottom: 15px; text-shadow: 0 0 10px #00ffcc, 0 0 20px #00ffcc; animation: flicker 0.15s infinite alternate; }
        
        /* 3D DANGER GLITCH TITLE */
        .title-3d { font-size: 3.5rem; font-weight: 900; color: #fff; text-align: center; text-transform: uppercase; margin-bottom: 50px; position: relative; text-shadow: 0.05em 0 0 rgba(255,0,0,0.75), -0.025em -0.05em 0 rgba(0,255,0,0.75), 0.025em 0.05em 0 rgba(0,0,255,0.75); animation: glitch 500ms infinite; }
        
        /* DANGER PULSE BUTTON */
        .login-btn { padding: 18px 35px; background: rgba(20, 0, 0, 0.5); border: 2px solid #ff0033; color: #ff0033; font-size: 1.3rem; cursor: pointer; text-transform: uppercase; font-weight: bold; transition: 0.3s; box-shadow: 0 0 15px #ff0033; animation: danger-pulse 1.5s infinite; letter-spacing: 2px; }
        .login-btn:hover { background: #ff0033; color: #000; box-shadow: 0 0 40px #ff0033; animation: none; }

        /* CHAT UI */
        .chat-container { width: 95%; max-width: 600px; height: 85vh; border: 1px solid #ff0033; background: rgba(5, 0, 0, 0.9); display: flex; flex-direction: column; position: relative; box-shadow: 0 0 30px rgba(255, 0, 51, 0.4); border-radius: 5px; }
        .chat-header { padding: 15px; border-bottom: 1px solid #ff0033; text-align: center; font-weight: bold; background: #0a0000; color: #ff3333; display: flex; justify-content: space-between; align-items: center; letter-spacing: 1px; box-shadow: 0 2px 10px rgba(255,0,0,0.2); }
        .logout-btn { background: none; border: 1px solid #ff0033; color: #ff0033; padding: 5px 15px; cursor: pointer; transition: 0.3s; }
        .logout-btn:hover { background: #ff0033; color: #000; }
        
        .messages { flex: 1; padding: 15px; overflow-y: auto; display: flex; flex-direction: column; gap: 10px; scrollbar-width: thin; scrollbar-color: #ff0033 #000; }
        .msg { padding: 12px; border-radius: 4px; max-width: 80%; word-wrap: break-word; text-shadow: 0 0 2px rgba(255,255,255,0.3); }
        .msg.my-msg { background: rgba(255, 0, 51, 0.15); border: 1px solid #ff0033; align-self: flex-end; color: #fff; box-shadow: -2px 2px 10px rgba(255,0,0,0.2); }
        .msg.other-msg { background: rgba(50, 50, 50, 0.3); border: 1px solid #555; align-self: flex-start; color: #00ffcc; box-shadow: 2px 2px 10px rgba(0,255,204,0.1); }
        
        .input-area { display: flex; padding: 15px; border-top: 1px solid #ff0033; background: #050000; }
        .input-area input { flex: 1; padding: 12px; background: rgba(20,0,0,0.5); border: 1px solid #ff0033; color: #fff; outline: none; font-size: 1rem; }
        .input-area input:focus { box-shadow: 0 0 10px inset #ff0033; }
        .input-area button { padding: 12px 25px; background: #ff0033; color: #000; border: none; cursor: pointer; font-weight: bold; margin-left: 10px; transition: 0.3s; }
        .input-area button:hover { box-shadow: 0 0 15px #ff0033; background: #fff; }

        .admin-layout { flex-direction: row; max-width: 1000px; }
        .sidebar { width: 30%; border-right: 1px solid #ff0033; overflow-y: auto; background: #050000; }
        .customer-item { padding: 15px; border-bottom: 1px dashed #440000; cursor: pointer; color: #aaa; transition: 0.2s; }
        .customer-item:hover { background: rgba(255,0,51,0.2); color: #fff; padding-left: 20px; border-left: 3px solid #ff0033; }
        .admin-chat-area { width: 70%; display: flex; flex-direction: column; }
        
        /* KEYFRAMES FOR EFFECTS */
        @keyframes flicker { 0% { opacity: 1; } 50% { opacity: 0.8; } 100% { opacity: 1; } }
        @keyframes danger-pulse { 0% { box-shadow: 0 0 10px #ff0033; transform: scale(1); } 50% { box-shadow: 0 0 25px #ff0033, 0 0 10px #ff0033 inset; transform: scale(1.02); } 100% { box-shadow: 0 0 10px #ff0033; transform: scale(1); } }
        @keyframes glitch {
          0% { text-shadow: 0.05em 0 0 rgba(255,0,0,0.75), -0.05em -0.025em 0 rgba(0,255,0,0.75), -0.025em 0.05em 0 rgba(0,0,255,0.75); }
          14% { text-shadow: 0.05em 0 0 rgba(255,0,0,0.75), -0.05em -0.025em 0 rgba(0,255,0,0.75), -0.025em 0.05em 0 rgba(0,0,255,0.75); }
          15% { text-shadow: -0.05em -0.025em 0 rgba(255,0,0,0.75), 0.025em 0.025em 0 rgba(0,255,0,0.75), -0.05em -0.05em 0 rgba(0,0,255,0.75); }
          49% { text-shadow: -0.05em -0.025em 0 rgba(255,0,0,0.75), 0.025em 0.025em 0 rgba(0,255,0,0.75), -0.05em -0.05em 0 rgba(0,0,255,0.75); }
          50% { text-shadow: 0.025em 0.05em 0 rgba(255,0,0,0.75), 0.05em 0 0 rgba(0,255,0,0.75), 0 -0.05em 0 rgba(0,0,255,0.75); }
          99% { text-shadow: 0.025em 0.05em 0 rgba(255,0,0,0.75), 0.05em 0 0 rgba(0,255,0,0.75), 0 -0.05em 0 rgba(0,0,255,0.75); }
          100% { text-shadow: -0.025em 0 0 rgba(255,0,0,0.75), -0.025em -0.025em 0 rgba(0,255,0,0.75), -0.025em -0.05em 0 rgba(0,0,255,0.75); }
        }
    </style>
</head>
<body>
    <canvas id="matrix"></canvas>
    <div class="scanlines"></div>

    <!-- 1. LOGIN SCREEN -->
    <div id="login-screen" class="screen active">
        <div class="owner-tag">[ OWNER MAGISK ]</div>
        <div class="title-3d">ABDUL BASIT<br>CHAT PORTAL</div>
        <button id="loginBtn" class="login-btn">> SYSTEM LOGIN</button>
    </div>

    <!-- 2. CUSTOMER CHAT SCREEN -->
    <div id="customer-screen" class="screen">
        <div class="chat-container">
            <div class="chat-header">
                <span>[ ENCRYPTED P2P CHAT ]</span>
                <button id="custLogoutBtn" class="logout-btn">DISCONNECT</button>
            </div>
            <div id="customerMessages" class="messages"></div>
            <div class="input-area">
                <input type="text" id="custMsgInput" placeholder="Enter encrypted text...">
                <button id="custSendBtn">TRANSMIT</button>
            </div>
        </div>
    </div>

    <!-- 3. ADMIN DASHBOARD -->
    <div id="admin-screen" class="screen">
        <div class="chat-container admin-layout">
            <div class="sidebar">
                <div class="chat-header">TARGETS <button id="adminLogoutBtn" class="logout-btn">X</button></div>
                <div id="customerList"></div>
            </div>
            <div class="admin-chat-area">
                <div class="chat-header" id="adminActiveChatHeader">WAITING FOR CONNECTION...</div>
                <div id="adminMessages" class="messages"></div>
                <div class="input-area">
                    <input type="text" id="adminMsgInput" placeholder="Command to target..." disabled>
                    <button id="adminSendBtn" disabled>EXECUTE</button>
                </div>
            </div>
        </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.5.0/firebase-app.js";
        // USING REDIRECT INSTEAD OF POPUP TO FIX ERROR
        import { getAuth, signInWithRedirect, getRedirectResult, GoogleAuthProvider, onAuthStateChanged, signOut } from "https://www.gstatic.com/firebasejs/10.5.0/firebase-auth.js";
        import { getFirestore, collection, addDoc, query, where, orderBy, onSnapshot, setDoc, doc, serverTimestamp } from "https://www.gstatic.com/firebasejs/10.5.0/firebase-firestore.js";

        const ADMIN_EMAIL = "khanji223353@gmail.com"; 
        
        const firebaseConfig = {
            apiKey: "AIzaSyD5odl3uHyob5GDwK88B9RHaQ1nDMmRrV0",
            authDomain: "abdulbasit8t1.firebaseapp.com",
            projectId: "abdulbasit8t1",
            storageBucket: "abdulbasit8t1.firebasestorage.app",
            messagingSenderId: "595773588592",
            appId: "1:595773588592:web:261ebaed28dd8dc15b60b5",
            measurementId: "G-P7XNBB3RGY"
        };

        const app = initializeApp(firebaseConfig);
        const auth = getAuth(app);
        const db = getFirestore(app);
        const provider = new GoogleAuthProvider();
        
        // YEH LINE HAMESHA ACCOUNT CHOOSE KARNE KA PUCHEGI (AUTO-LOGIN FIX)
        provider.setCustomParameters({ prompt: 'select_account' });

        let currentUser = null;
        let activeAdminChatUid = null;
        let unsubscribeMessages = null;

        function showScreen(screenId) {
            document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
            document.getElementById(screenId).classList.add('active');
        }

        // CATCH REDIRECT ERRORS
        getRedirectResult(auth).catch((error) => {
            alert("System Error: " + error.message);
        });

        // SAFE MOBILE LOGIN
        document.getElementById('loginBtn').addEventListener('click', () => {
            document.getElementById('loginBtn').innerText = "> CONNECTING...";
            signInWithRedirect(auth, provider);
        });

        const logout = () => signOut(auth);
        document.getElementById('custLogoutBtn').addEventListener('click', logout);
        document.getElementById('adminLogoutBtn').addEventListener('click', logout);

        onAuthStateChanged(auth, async (user) => {
            if (user) {
                currentUser = user;
                if (user.email === ADMIN_EMAIL) {
                    showScreen('admin-screen');
                    loadAdminDashboard();
                } else {
                    await setDoc(doc(db, "customers", user.uid), {
                        uid: user.uid, name: user.displayName, email: user.email, lastLogin: serverTimestamp()
                    }, { merge: true });
                    showScreen('customer-screen');
                    loadCustomerChat(user.uid);
                }
            } else {
                currentUser = null;
                if (unsubscribeMessages) unsubscribeMessages();
                showScreen('login-screen');
                document.getElementById('loginBtn').innerText = "> SYSTEM LOGIN";
            }
        });

        async function sendMessage(text, chatRoomId) {
            if (!text.trim()) return;
            await addDoc(collection(db, "messages"), {
                chatRoomId: chatRoomId, senderId: currentUser.uid, senderName: currentUser.displayName, text: text, timestamp: serverTimestamp()
            });
        }

        document.getElementById('custSendBtn').addEventListener('click', () => {
            const input = document.getElementById('custMsgInput');
            sendMessage(input.value, currentUser.uid);
            input.value = "";
        });

        document.getElementById('adminSendBtn').addEventListener('click', () => {
            const input = document.getElementById('adminMsgInput');
            if (activeAdminChatUid) {
                sendMessage(input.value, activeAdminChatUid);
                input.value = "";
            }
        });

        function loadCustomerChat(uid) {
            const q = query(collection(db, "messages"), where("chatRoomId", "==", uid), orderBy("timestamp", "asc"));
            if (unsubscribeMessages) unsubscribeMessages();
            
            unsubscribeMessages = onSnapshot(q, (snapshot) => {
                const box = document.getElementById('customerMessages');
                box.innerHTML = '';
                snapshot.forEach((doc) => {
                    const data = doc.data();
                    const isMe = data.senderId === currentUser.uid;
                    box.innerHTML += `<div class="msg ${isMe ? 'my-msg' : 'other-msg'}"><b>${isMe ? 'YOU' : 'ADMIN'}:</b> ${data.text}</div>`;
                });
                box.scrollTop = box.scrollHeight;
            });
        }

        function loadAdminDashboard() {
            const q = query(collection(db, "customers"), orderBy("lastLogin", "desc"));
            onSnapshot(q, (snapshot) => {
                const list = document.getElementById('customerList');
                list.innerHTML = '';
                snapshot.forEach((docSnap) => {
                    const cust = docSnap.data();
                    const div = document.createElement('div');
                    div.className = "customer-item";
                    div.innerHTML = `> ${cust.name}`;
                    div.onclick = () => openAdminChat(cust.uid, cust.name);
                    list.appendChild(div);
                });
            });
        }

        function openAdminChat(custUid, custName) {
            activeAdminChatUid = custUid;
            document.getElementById('adminActiveChatHeader').innerText = "LINKED: " + custName;
            document.getElementById('adminMsgInput').disabled = false;
            document.getElementById('adminSendBtn').disabled = false;

            const q = query(collection(db, "messages"), where("chatRoomId", "==", custUid), orderBy("timestamp", "asc"));
            if (unsubscribeMessages) unsubscribeMessages();
            
            unsubscribeMessages = onSnapshot(q, (snapshot) => {
                const box = document.getElementById('adminMessages');
                box.innerHTML = '';
                snapshot.forEach((doc) => {
                    const data = doc.data();
                    const isMe = data.senderId === currentUser.uid;
                    box.innerHTML += `<div class="msg ${isMe ? 'my-msg' : 'other-msg'}"><b>${isMe ? 'ADMIN' : custName.toUpperCase()}:</b> ${data.text}</div>`;
                });
                box.scrollTop = box.scrollHeight;
            });
        }

        // AGGRESSIVE DANGER MATRIX ANIMATION
        const canvas = document.getElementById('matrix');
        const ctx = canvas.getContext('2d');
        canvas.width = window.innerWidth; canvas.height = window.innerHeight;
        const letters = "01010101010 X Y Z ! @ # $ ERROR DANGER".split(" ");
        const fontSize = 14; const columns = canvas.width / fontSize;
        const drops = []; for (let x = 0; x < columns; x++) drops[x] = 1;
        
        function drawMatrix() {
            ctx.fillStyle = "rgba(0, 0, 0, 0.08)";
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            ctx.fillStyle = "#ff0033"; ctx.font = "bold " + fontSize + "px Courier New";
            for (let i = 0; i < drops.length; i++) {
                const text = letters[Math.floor(Math.random() * letters.length)];
                ctx.fillText(text, i * fontSize, drops[i] * fontSize);
                if (drops[i] * fontSize > canvas.height && Math.random() > 0.96) drops[i] = 0;
                drops[i]++;
            }
        }
        setInterval(drawMatrix, 40);
    </script>
</body>
</html>
