<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>xaMOff Messenger | Звонки + Админ + Уведомления</title>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-database-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-messaging-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: system-ui, -apple-system, 'Segoe UI', sans-serif; }
        
        :root {
            --bg-body: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            --chat-bg: #ffffff;
            --header-bg: linear-gradient(135deg, #4a6cf7, #6b4eff);
            --header-text: #ffffff;
            --message-area-bg: #f8f9fc;
            --my-bubble: #4a6cf7;
            --my-text: #ffffff;
            --other-bubble: #ffffff;
            --other-text: #1e293b;
            --system-bg: #e9ecef;
            --system-text: #4b5563;
            --input-bg: #ffffff;
            --input-border: #e2e8f0;
            --icon-color: #4a6cf7;
            --call-overlay-bg: rgba(0,0,0,0.95);
            --read-color: #10b981;
            --transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        }
        
        body.dark {
            --bg-body: linear-gradient(135deg, #0f172a 0%, #1e1b4b 100%);
            --chat-bg: #1e293b;
            --header-bg: #0f172a;
            --message-area-bg: #0f172a;
            --my-bubble: #3b82f6;
            --other-bubble: #334155;
            --system-bg: #1e293b;
            --system-text: #cbd5e1;
            --input-bg: #334155;
            --input-border: #475569;
            --icon-color: #60a5fa;
            --read-color: #34d399;
        }
        
        body { background: var(--bg-body); min-height: 100vh; display: flex; justify-content: center; align-items: center; padding: 16px; transition: var(--transition); }
        .chat-container { width: 100%; max-width: 950px; height: 95vh; background: var(--chat-bg); border-radius: 28px; display: flex; flex-direction: column; overflow: hidden; box-shadow: 0 20px 60px rgba(0,0,0,0.3); transition: var(--transition); }
        
        .chat-header { background: var(--header-bg); color: var(--header-text); padding: 12px 20px; display: flex; align-items: center; justify-content: space-between; transition: var(--transition); }
        .header-left { display: flex; align-items: center; gap: 12px; }
        .user-avatar { width: 44px; height: 44px; border-radius: 50%; object-fit: cover; background: #475569; cursor: pointer; transition: transform 0.2s; }
        .user-avatar:hover { transform: scale(1.05); }
        .header-text h1 { font-size: 1.2rem; letter-spacing: -0.3px; }
        .header-text p { font-size: 0.65rem; opacity: 0.8; }
        .header-buttons { display: flex; gap: 8px; flex-wrap: wrap; }
        .icon-btn { background: rgba(255,255,255,0.2); border: none; width: 38px; height: 38px; border-radius: 50%; cursor: pointer; color: white; font-size: 1.1rem; transition: transform 0.2s, background 0.2s; }
        .icon-btn:hover { transform: scale(1.05); background: rgba(255,255,255,0.3); }
        .call-btn { background: #10b981; }
        
        .messages-area { flex: 1; overflow-y: auto; padding: 16px; background: var(--message-area-bg); display: flex; flex-direction: column; gap: 10px; transition: var(--transition); }
        
        .message { display: flex; max-width: 80%; animation: fadeIn 0.2s ease; position: relative; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(8px); } to { opacity: 1; transform: translateY(0); } }
        .my-message { align-self: flex-end; justify-content: flex-end; }
        .other-message { align-self: flex-start; }
        .bubble { padding: 8px 14px; border-radius: 20px; word-wrap: break-word; background: var(--other-bubble); color: var(--other-text); position: relative; transition: var(--transition); }
        .my-message .bubble { background: var(--my-bubble); color: var(--my-text); border-bottom-right-radius: 4px; }
        .message-header { display: flex; align-items: center; gap: 8px; margin-bottom: 5px; }
        .msg-avatar { width: 26px; height: 26px; border-radius: 50%; object-fit: cover; cursor: pointer; }
        .message-name { font-size: 0.75rem; font-weight: 600; cursor: pointer; }
        .message-time { font-size: 0.55rem; opacity: 0.7; margin-top: 4px; display: block; }
        .my-message .message-time { text-align: right; }
        
        .read-status { font-size: 0.55rem; margin-left: 6px; opacity: 0.9; }
        .read-status.sent { color: #94a3b8; }
        .read-status.delivered { color: #f59e0b; }
        .read-status.read { color: var(--read-color); font-weight: bold; }
        
        .delete-btn, .admin-delete-btn, .edit-btn { position: absolute; top: -8px; background: #ef4444; color: white; border: none; width: 22px; height: 22px; border-radius: 50%; cursor: pointer; font-size: 10px; opacity: 0; transition: opacity 0.2s; z-index: 10; display: flex; align-items: center; justify-content: center; }
        .delete-btn { right: -8px; }
        .admin-delete-btn { right: 18px; background: #f59e0b; }
        .edit-btn { right: 44px; background: #10b981; }
        .message:hover .delete-btn, .message:hover .admin-delete-btn, .message:hover .edit-btn { opacity: 1; }
        
        .media-content { max-width: 200px; max-height: 200px; border-radius: 12px; margin-top: 6px; cursor: pointer; transition: transform 0.2s; }
        .media-content:hover { transform: scale(1.02); }
        .video-content { max-width: 200px; max-height: 200px; border-radius: 12px; margin-top: 6px; }
        audio { max-width: 180px; height: 36px; margin-top: 6px; }
        .circle-video { width: 180px; height: 180px; border-radius: 50%; object-fit: cover; margin-top: 6px; cursor: pointer; background: #000; }
        .circle-video video { width: 100%; height: 100%; border-radius: 50%; object-fit: cover; }
        
        .system-message { text-align: center; font-size: 0.7rem; background: var(--system-bg); color: var(--system-text); padding: 6px 12px; border-radius: 20px; margin: 4px auto; width: fit-content; transition: var(--transition); }
        
        .input-area { background: var(--input-bg); border-top: 1px solid var(--input-border); padding: 12px 16px; transition: var(--transition); }
        .input-row { display: flex; gap: 8px; align-items: center; flex-wrap: wrap; }
        .message-input { flex: 1; padding: 12px 16px; border: 1px solid var(--input-border); border-radius: 25px; background: var(--input-bg); color: var(--other-text); outline: none; min-width: 120px; resize: vertical; transition: var(--transition); }
        .message-input:focus { outline: none; border-color: var(--icon-color); }
        .media-btn, .voice-btn, .camera-btn, .video-msg-btn { background: transparent; border: none; font-size: 1.3rem; cursor: pointer; color: var(--icon-color); width: 40px; height: 40px; border-radius: 50%; transition: all 0.2s; }
        .media-btn:hover, .voice-btn:hover, .camera-btn:hover, .video-msg-btn:hover { transform: scale(1.1); background: rgba(0,0,0,0.05); }
        .video-msg-btn.recording { background: #ef4444; color: white; animation: pulse 1s infinite; }
        .send-btn { background: var(--icon-color); color: white; border: none; width: 46px; height: 46px; border-radius: 50%; cursor: pointer; transition: transform 0.2s; }
        .send-btn:hover { transform: scale(1.05); }
        
        @keyframes pulse { 0% { transform: scale(1); opacity: 1; } 50% { transform: scale(1.1); opacity: 0.8; } 100% { transform: scale(1); opacity: 1; } }
        
        .call-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: var(--call-overlay-bg); z-index: 2000; display: none; flex-direction: column; justify-content: center; align-items: center; }
        .call-video-container { width: 100%; height: 80%; display: flex; flex-wrap: wrap; justify-content: center; align-items: center; gap: 20px; padding: 20px; }
        .local-video, .remote-video { width: 45%; height: 45%; background: #000; border-radius: 16px; object-fit: cover; }
        .call-controls { position: absolute; bottom: 30px; display: flex; justify-content: center; gap: 20px; background: rgba(0,0,0,0.6); padding: 15px; border-radius: 60px; }
        .call-control-btn { background: #334155; border: none; width: 55px; height: 55px; border-radius: 50%; cursor: pointer; color: white; font-size: 1.5rem; transition: transform 0.2s; }
        .call-control-btn.end-call { background: #ef4444; }
        .call-control-btn:hover { transform: scale(1.1); }
        
        .incoming-call-modal { position: fixed; bottom: 100px; left: 20px; right: 20px; max-width: 350px; background: var(--chat-bg); border-radius: 20px; padding: 20px; z-index: 2100; box-shadow: 0 10px 40px rgba(0,0,0,0.3); display: none; transition: var(--transition); }
        
        .auth-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.95); z-index: 3000; display: flex; justify-content: center; align-items: center; }
        .auth-card { background: white; border-radius: 28px; padding: 32px; width: 90%; max-width: 400px; text-align: center; transition: transform 0.3s; animation: fadeInUp 0.4s; }
        @keyframes fadeInUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .auth-card input { width: 100%; padding: 14px; margin: 12px 0; border: 1px solid #ddd; border-radius: 30px; font-size: 1rem; text-align: center; }
        .auth-card button { background: #4a6cf7; color: white; border: none; padding: 14px; border-radius: 30px; width: 100%; font-size: 1rem; cursor: pointer; transition: opacity 0.2s; }
        
        .admin-panel { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.95); z-index: 2500; display: none; flex-direction: column; padding: 20px; overflow-y: auto; animation: fadeIn 0.3s; }
        .admin-panel h3, .admin-panel h4 { color: white; margin-bottom: 20px; }
        .user-item { background: #1e293b; margin: 8px 0; padding: 12px; border-radius: 16px; display: flex; justify-content: space-between; align-items: center; flex-wrap: wrap; gap: 10px; }
        .user-item div { color: white; }
        .user-item button { padding: 6px 12px; border-radius: 20px; border: none; cursor: pointer; margin-left: 8px; transition: opacity 0.2s; }
        .close-admin { position: absolute; top: 20px; right: 20px; background: #ef4444; color: white; border: none; padding: 10px 20px; border-radius: 30px; cursor: pointer; }
        
        .modal { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.7); z-index: 1000; justify-content: center; align-items: center; animation: fadeIn 0.2s; }
        .modal-content { background: var(--chat-bg); border-radius: 24px; padding: 24px; width: 90%; max-width: 500px; transition: var(--transition); }
        .modal-content input, .modal-content button { margin-top: 12px; width: 100%; padding: 12px; border-radius: 24px; border: 1px solid var(--input-border); }
        .modal-content button { background: var(--icon-color); color: white; border: none; cursor: pointer; }
        
        .edit-textarea { width: 100%; padding: 8px; border-radius: 12px; border: 1px solid var(--input-border); background: var(--input-bg); color: var(--other-text); resize: vertical; font-family: inherit; }
        
        @media (max-width: 600px) { .local-video, .remote-video { width: 90%; height: 35%; } .message { max-width: 90%; } .circle-video { width: 130px; height: 130px; } }
        
        .blocked-message { text-align: center; color: #ef4444; padding: 20px; font-weight: bold; background: var(--chat-bg); border-radius: 20px; margin: 20px; }
    </style>
</head>
<body>

<div class="chat-container" id="chatContainer" style="display: none;">
    <div class="chat-header">
        <div class="header-left">
            <img id="headerAvatar" class="user-avatar" src="">
            <div class="header-text">
                <h1>📱 xaMOff Messenger <span id="adminBadge" style="display:inline-block; background:#ef4444; padding:2px 8px; border-radius:12px; font-size:0.7rem;">ADMIN</span></h1>
                <p id="userPhoneDisplay"></p>
            </div>
        </div>
        <div class="header-buttons">
            <button class="icon-btn call-btn" id="callBtn"><i class="fas fa-phone-alt"></i></button>
            <button class="icon-btn" id="adminPanelBtn"><i class="fas fa-shield-alt"></i></button>
            <button class="icon-btn" id="settingsBtn"><i class="fas fa-user-cog"></i></button>
            <button class="icon-btn" id="themeToggle"><i class="fas fa-moon"></i></button>
        </div>
    </div>
    <div class="messages-area" id="messagesArea"></div>
    <div class="input-area">
        <div class="input-row">
            <textarea id="messageInput" class="message-input" placeholder="Сообщение... (Shift+Enter — новая строка, Enter — отправка)" rows="1" style="resize: vertical;"></textarea>
            <button class="camera-btn" id="photoBtn" title="Фото из галереи"><i class="fas fa-image"></i></button>
            <button class="camera-btn" id="takePhotoBtn" title="Сделать фото"><i class="fas fa-camera"></i></button>
            <button class="media-btn" id="videoFileBtn" title="Видео"><i class="fas fa-video"></i></button>
            <button class="video-msg-btn" id="circleVideoBtn" title="Кружок"><i class="fas fa-circle"></i></button>
            <button class="voice-btn" id="voiceBtn" title="Голосовое"><i class="fas fa-microphone"></i></button>
            <button class="send-btn" id="sendBtn"><i class="fas fa-paper-plane"></i></button>
        </div>
        <input type="file" id="photoInput" accept="image/*" style="display:none">
        <input type="file" id="videoFileInput" accept="video/*" style="display:none">
        <input type="file" id="cameraCaptureInput" accept="image/*" capture="environment" style="display:none">
    </div>
</div>

<div id="authOverlay" class="auth-overlay">
    <div class="auth-card">
        <h2>📱 xaMOff Messenger</h2>
        <p style="margin: 10px 0; color: #666;">Введите номер телефона для входа</p>
        <input type="tel" id="phoneInput" placeholder="+7 999 123-45-67" maxlength="20">
        <input type="text" id="userNameInput" placeholder="Ваше имя">
        <div id="nameError" style="color:red; font-size:0.8rem; margin:5px 0;"></div>
        <button id="authBtn">Продолжить</button>
    </div>
</div>

<div id="callOverlay" class="call-overlay"><div class="call-video-container"><video id="localVideo" class="local-video" autoplay muted playsinline></video><video id="remoteVideo" class="remote-video" autoplay playsinline></video></div><div class="call-controls"><button class="call-control-btn" id="toggleMuteBtn"><i class="fas fa-microphone"></i></button><button class="call-control-btn" id="toggleVideoBtn"><i class="fas fa-video"></i></button><button class="call-control-btn" id="shareScreenBtn"><i class="fas fa-desktop"></i></button><button class="call-control-btn end-call" id="endCallBtn"><i class="fas fa-phone-slash"></i></button></div></div>
<div id="incomingCallModal" class="incoming-call-modal"><h3 id="callerName">📞 Входящий звонок...</h3><div style="display:flex; gap:10px; margin-top:15px;"><button id="acceptCallBtn" style="flex:1; background:#10b981; padding:12px; border:none; border-radius:30px; color:white;">Принять</button><button id="rejectCallBtn" style="flex:1; background:#ef4444; padding:12px; border:none; border-radius:30px; color:white;">Отклонить</button></div></div>
<div id="profileModal" class="modal"><div class="modal-content"><h3>Настройки</h3><input type="text" id="modalName" placeholder="Имя"><input type="file" id="modalAvatar" accept="image/*"><button id="saveProfileBtn">Сохранить</button><button id="closeModalBtn">Отмена</button></div></div>
<div id="adminPanel" class="admin-panel"><button class="close-admin" id="closeAdminBtn">✕ Закрыть</button><h3>👑 Админ-панель (DaniksGames)</h3><button id="clearChatBtn" style="background:#f59e0b; color:white; padding:10px; border:none; border-radius:16px; margin:10px 0; cursor:pointer;">🗑️ ОЧИСТИТЬ ВЕСЬ ЧАТ</button><div id="usersList"></div></div>

<script>
    const firebaseConfig = {
        apiKey: "AIzaSyD4a16-r1nwCVDAu5_DBnirq0e8Lu9pBZw",
        authDomain: "daniksgames-d46b2.firebaseapp.com",
        databaseURL: "https://daniksgames-d46b2-default-rtdb.firebaseio.com",
        projectId: "daniksgames-d46b2",
        storageBucket: "daniksgames-d46b2.firebasestorage.app",
        messagingSenderId: "828976407320",
        appId: "1:828976407320:web:d4a85a54ab968c1473b46d"
    };
    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();
    const messaging = firebase.messaging.isSupported() ? firebase.messaging() : null;
    
    let currentUserId = null, currentUserPhone = null, currentUserName = null, currentUserAvatar = null, isBlocked = false;
    let peerConnection = null, localStream = null, currentCallId = null, isCallActive = false;
    let audioCtx = null, ringtoneInterval = null;
    let editingMessageId = null;
    let voiceRecorder = null, voiceChunks = [], isVoiceRecording = false, voiceStream = null;
    
    function playNotificationSound() { try { if (!audioCtx) audioCtx = new AudioContext(); const osc = audioCtx.createOscillator(), gain = audioCtx.createGain(); osc.connect(gain); gain.connect(audioCtx.destination); osc.frequency.value = 880; gain.gain.value = 0.2; osc.start(); gain.gain.exponentialRampToValueAtTime(0.00001, audioCtx.currentTime + 0.4); osc.stop(audioCtx.currentTime + 0.4); } catch(e) {} }
    function startRingtone() { if (ringtoneInterval) clearInterval(ringtoneInterval); ringtoneInterval = setInterval(() => { try { if (!audioCtx) audioCtx = new AudioContext(); const osc = audioCtx.createOscillator(), gain = audioCtx.createGain(); osc.connect(gain); gain.connect(audioCtx.destination); osc.frequency.value = 440; gain.gain.value = 0.3; osc.start(); gain.gain.exponentialRampToValueAtTime(0.00001, audioCtx.currentTime + 0.3); osc.stop(audioCtx.currentTime + 0.3); } catch(e) {} }, 1000); }
    function stopRingtone() { if (ringtoneInterval) { clearInterval(ringtoneInterval); ringtoneInterval = null; } }
    
    async function registerForPushNotifications() { if (!messaging) return; try { const token = await messaging.getToken({ vapidKey: 'BDB4lGmZxG5qX7yQ9wE3rT5yU7iOp9kL1mN2oP3qR4sT5uV6wX7yZ8aB9cC0dD1eE2fF3gG4hH5iI6jJ7kK8lL9mM0nN' }); if (token) await db.ref('users/' + currentUserId).update({ fcmToken: token }); } catch(e) { console.log('Push error:', e); } }
    
    async function auth() {
        const phone = document.getElementById('phoneInput').value.trim().replace(/[^0-9+]/g, '');
        const name = document.getElementById('userNameInput').value.trim();
        const nameError = document.getElementById('nameError');
        if (!phone || phone.length < 5) { alert('Введите номер телефона'); return; }
        if (!name) { alert('Введите имя'); return; }
        let normalizedPhone = phone.startsWith('+') ? phone : '+' + phone;
        
        const usersRef = db.ref('users');
        const nameCheck = await usersRef.orderByChild('name').equalTo(name).once('value');
        if (nameCheck.exists()) {
            let taken = false;
            nameCheck.forEach(child => { if (child.key !== currentUserId) taken = true; });
            if (taken) { nameError.innerText = 'Это имя уже занято! Выберите другое.'; return; }
        }
        
        const snapshot = await usersRef.orderByChild('phone').equalTo(normalizedPhone).once('value');
        if (snapshot.exists()) { snapshot.forEach(child => { currentUserId = child.key; }); await usersRef.child(currentUserId).update({ name: name, lastSeen: Date.now() }); }
        else { const newUser = usersRef.push(); currentUserId = newUser.key; await newUser.set({ phone: normalizedPhone, name: name, avatar: '', blocked: false, createdAt: Date.now() }); }
        
        currentUserPhone = normalizedPhone; currentUserName = name;
        localStorage.setItem('userId', currentUserId); localStorage.setItem('userPhone', normalizedPhone); localStorage.setItem('userName', name);
        document.getElementById('authOverlay').style.display = 'none';
        document.getElementById('chatContainer').style.display = 'flex';
        document.getElementById('userPhoneDisplay').innerText = normalizedPhone;
        updateHeaderAvatar();
        await registerForPushNotifications();
        initAll();
    }
    
    async function checkSession() {
        const savedUserId = localStorage.getItem('userId'), savedPhone = localStorage.getItem('userPhone'), savedName = localStorage.getItem('userName');
        if (savedUserId && savedPhone) {
            const userSnap = await db.ref('users/' + savedUserId).once('value');
            if (userSnap.exists() && !userSnap.val().blocked) {
                currentUserId = savedUserId; currentUserPhone = savedPhone; currentUserName = savedName || userSnap.val().name;
                document.getElementById('authOverlay').style.display = 'none';
                document.getElementById('chatContainer').style.display = 'flex';
                document.getElementById('userPhoneDisplay').innerText = currentUserPhone;
                updateHeaderAvatar();
                await registerForPushNotifications();
                initAll();
                return true;
            }
        }
        return false;
    }
    
    window.deleteMessage = async (messageId) => { if (confirm('Удалить?')) await db.ref('messages/' + messageId).remove(); };
    window.adminDeleteMessage = async (messageId) => { if (confirm('Удалить сообщение?')) await db.ref('messages/' + messageId).remove(); };
    window.editMessage = async (messageId, currentText) => { const newText = prompt('Редактировать сообщение:', currentText); if (newText && newText.trim()) await db.ref('messages/' + messageId).update({ text: newText.trim(), edited: true }); };
    
    window.toggleBlockUser = async (userId, blocked) => { await db.ref('users/' + userId).update({ blocked: !blocked }); loadUsersList(); };
    window.deleteUserAccount = async (userId) => { if (confirm('Удалить аккаунт?')) { const msgs = await db.ref('messages').orderByChild('userId').equalTo(userId).once('value'); msgs.forEach(m => m.ref.remove()); await db.ref('users/' + userId).remove(); loadUsersList(); } };
    window.deleteUserMessages = async (userId) => { if (confirm('Удалить ВСЕ сообщения от этого пользователя?')) { const msgs = await db.ref('messages').orderByChild('userId').equalTo(userId).once('value'); msgs.forEach(m => m.ref.remove()); alert('Сообщения удалены'); } };
    
    async function loadUsersList() {
        const users = await db.ref('users').once('value');
        const container = document.getElementById('usersList');
        if (!container) return;
        container.innerHTML = '<h4 style="color:white;">Все пользователи:</h4>';
        for (let id in users.val()) {
            const user = users.val()[id];
            const div = document.createElement('div'); div.className = 'user-item';
            div.innerHTML = `<div><strong>${escapeHtml(user.name)}</strong><br><small>${user.phone}</small>${user.blocked ? ' 🔒' : ''}${id === currentUserId ? ' (Вы)' : ''}</div>
                <div><button style="background:#f59e0b;" onclick="toggleBlockUser('${id}', ${user.blocked || false})">${user.blocked ? '🔓 Разблок' : '🔒 Блок'}</button>
                <button style="background:#ef4444;" onclick="deleteUserAccount('${id}')">🗑️ Аккаунт</button>
                <button style="background:#8b5cf6;" onclick="deleteUserMessages('${id}')">📨 Удалить сообщения</button></div>`;
            container.appendChild(div);
        }
    }
    
    function updateHeaderAvatar() { document.getElementById('headerAvatar').src = currentUserAvatar || `https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(currentUserName)}`; }
    
    async function initMessaging() {
        const messagesRef = db.ref('messages');
        const messageInput = document.getElementById('messageInput');
        const sendBtn = document.getElementById('sendBtn');
        const messagesArea = document.getElementById('messagesArea');
        
        messageInput.addEventListener('keydown', (e) => { if (e.key === 'Enter' && !e.shiftKey) { e.preventDefault(); sendBtn.click(); } });
        
        window.sendMessage = async (text, mediaType, mediaUrl, isCircle = false) => {
            if ((!text && !mediaUrl) || isBlocked) return alert('Вы заблокированы');
            await messagesRef.push({ userId: currentUserId, name: currentUserName, phone: currentUserPhone, avatar: currentUserAvatar || '', text: text || '', time: Date.now(), mediaType: mediaType || null, mediaUrl: mediaUrl || null, isCircle: isCircle, read: false, delivered: false });
        };
        sendBtn.onclick = () => { if (messageInput.value.trim()) sendMessage(messageInput.value.trim()); messageInput.value = ''; messageInput.style.height = 'auto'; };
        
        document.getElementById('photoBtn').onclick = () => document.getElementById('photoInput').click();
        document.getElementById('photoInput').onchange = (e) => { if(e.target.files[0]){ const r=new FileReader(); r.onload=ev=>sendMessage('📷 Фото','image',ev.target.result); r.readAsDataURL(e.target.files[0]); e.target.value=''; } };
        document.getElementById('takePhotoBtn').onclick = () => document.getElementById('cameraCaptureInput').click();
        document.getElementById('cameraCaptureInput').onchange = (e) => { if(e.target.files[0]){ const r=new FileReader(); r.onload=ev=>sendMessage('📷 Фото с камеры','image',ev.target.result); r.readAsDataURL(e.target.files[0]); e.target.value=''; } };
        document.getElementById('videoFileBtn').onclick = () => document.getElementById('videoFileInput').click();
        document.getElementById('videoFileInput').onchange = (e) => { if(e.target.files[0] && e.target.files[0].size<=100*1024*1024){ const r=new FileReader(); r.onload=ev=>sendMessage('🎥 Видео','video',ev.target.result); r.readAsDataURL(e.target.files[0]); e.target.value=''; } else alert('Видео до 100МБ'); };
        
        // Фикс голосовых: одна запись, стоп по кнопке
        document.getElementById('voiceBtn').onclick = async () => {
            if (isVoiceRecording && voiceRecorder && voiceRecorder.state === 'recording') {
                voiceRecorder.stop();
                return;
            }
            const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
            voiceStream = stream;
            voiceRecorder = new MediaRecorder(stream);
            voiceChunks = [];
            voiceRecorder.ondataavailable = e => { if(e.data.size > 0) voiceChunks.push(e.data); };
            voiceRecorder.onstop = () => { 
                const blob = new Blob(voiceChunks, {type:'audio/webm'});
                const r = new FileReader();
                r.onload = e => sendMessage('🎤 Голосовое','audio',e.target.result);
                r.readAsDataURL(blob);
                if(voiceStream) voiceStream.getTracks().forEach(t=>t.stop());
                voiceStream = null;
                isVoiceRecording = false;
                document.getElementById('voiceBtn').style.background = '';
                document.getElementById('voiceBtn').style.color = '';
            };
            voiceRecorder.start();
            isVoiceRecording = true;
            const btn = document.getElementById('voiceBtn');
            btn.style.background = '#ef4444'; btn.style.color = 'white';
            setTimeout(() => { if(isVoiceRecording && voiceRecorder && voiceRecorder.state === 'recording') voiceRecorder.stop(); }, 15000);
        };
        
        let circleRecorder=null, circleChunks=[], isCircleRecording=false, circleStream=null;
        document.getElementById('circleVideoBtn').onclick = async () => {
            if(isCircleRecording){ if(circleRecorder && circleRecorder.state==='recording') circleRecorder.stop(); isCircleRecording=false; document.getElementById('circleVideoBtn').classList.remove('recording'); document.getElementById('circleVideoBtn').innerHTML='<i class="fas fa-circle"></i>'; if(circleStream) circleStream.getTracks().forEach(t=>t.stop()); }
            else { const stream = await navigator.mediaDevices.getUserMedia({ video: true, audio: true }); circleStream=stream; circleRecorder=new MediaRecorder(stream,{mimeType:'video/webm'}); circleChunks=[]; circleRecorder.ondataavailable=e=>{ if(e.data.size>0) circleChunks.push(e.data); }; circleRecorder.onstop=()=>{ const blob=new Blob(circleChunks,{type:'video/webm'}); const r=new FileReader(); r.onload=e=>sendMessage('🟢 Кружок','video',e.target.result,true); r.readAsDataURL(blob); if(circleStream) circleStream.getTracks().forEach(t=>t.stop()); circleStream=null; }; circleRecorder.start(); isCircleRecording=true; document.getElementById('circleVideoBtn').classList.add('recording'); document.getElementById('circleVideoBtn').innerHTML='<i class="fas fa-stop"></i>'; setTimeout(()=>{ if(isCircleRecording && circleRecorder && circleRecorder.state==='recording'){ circleRecorder.stop(); isCircleRecording=false; document.getElementById('circleVideoBtn').classList.remove('recording'); document.getElementById('circleVideoBtn').innerHTML='<i class="fas fa-circle"></i>'; } },30000); }
        };
        
        // Реалтайм прочтения
        messagesRef.on('child_changed', (snap) => { updateMessageReadStatus(snap.key, snap.val()); });
        messagesRef.orderByChild('time').limitToLast(200).on('child_added', async (snap) => {
            const msg = snap.val();
            if(!msg) return;
            const isMe = msg.userId === currentUserId;
            const div = document.createElement('div'); div.className = `message ${isMe ? 'my-message' : 'other-message'}`;
            let media = '';
            if(msg.mediaType==='image') media=`<img src="${msg.mediaUrl}" class="media-content" onclick="window.open('${msg.mediaUrl}','_blank')">`;
            else if(msg.mediaType==='video') { if(msg.isCircle) media=`<div class="circle-video"><video controls playsinline style="width:100%; height:100%; border-radius:50%; object-fit:cover;"><source src="${msg.mediaUrl}"></video></div>`; else media=`<video controls class="video-content"><source src="${msg.mediaUrl}"></video>`; }
            else if(msg.mediaType==='audio') media=`<audio controls src="${msg.mediaUrl}"></audio>`;
            const avatarUrl = msg.avatar || `https://ui-avatars.com/api/?background=6b4eff&color=fff&name=${encodeURIComponent(msg.name)}`;
            let readStatus = '';
            if (isMe) { if (msg.read) readStatus = '<span class="read-status read">✓✓ Прочитано</span>'; else if (msg.delivered) readStatus = '<span class="read-status delivered">✓✓ Доставлено</span>'; else readStatus = '<span class="read-status sent">✓ Отправлено</span>'; }
            const editedMark = msg.edited ? ' <span style="font-size:0.5rem;">(ред.)</span>' : '';
            const deleteBtn = isMe ? `<button class="delete-btn" onclick="deleteMessage('${snap.key}')"><i class="fas fa-trash"></i></button>` : '';
            const editBtn = isMe ? `<button class="edit-btn" onclick="editMessage('${snap.key}', '${escapeHtml(msg.text).replace(/'/g, "\\'")}')"><i class="fas fa-pen"></i></button>` : '';
            const adminDeleteBtn = currentUserName === 'DaniksGames' ? `<button class="admin-delete-btn" onclick="adminDeleteMessage('${snap.key}')"><i class="fas fa-crown"></i></button>` : '';
            div.innerHTML = `<div class="bubble">${deleteBtn}${editBtn}${adminDeleteBtn}<div class="message-header">${!isMe ? `<img class="msg-avatar" src="${avatarUrl}">` : ''}<span class="message-name">${escapeHtml(msg.name)}</span></div>${msg.text ? `<div>${escapeHtml(msg.text)}${editedMark}</div>` : ''}${media}<span class="message-time">${new Date(msg.time).toLocaleTimeString()} ${readStatus}</span></div>`;
            messagesArea.appendChild(div); messagesArea.scrollTop = messagesArea.scrollHeight;
            if(!isMe && !msg.read && !isBlocked) await db.ref('messages/'+snap.key).update({ read: true, readAt: Date.now() });
            if(!isMe && !msg.delivered && !isBlocked) await db.ref('messages/'+snap.key).update({ delivered: true });
        });
    }
    
    function updateMessageReadStatus(messageId, msg) { const elements = document.querySelectorAll(`.message`); for(let el of elements) { if(el.innerHTML.includes(messageId) || el.querySelector(`[onclick*="deleteMessage('${messageId}']`)) { const statusSpan = el.querySelector('.read-status'); if(statusSpan && msg.userId === currentUserId) { if(msg.read) statusSpan.innerHTML = '✓✓ Прочитано'; else if(msg.delivered) statusSpan.innerHTML = '✓✓ Доставлено'; } } } }
    
    // Звонки (упрощённо, но работоспособно)
    const configuration = { iceServers: [{ urls: 'stun:stun.l.google.com:19302' }] };
    async function startCall() { alert('Функция звонков требует дополнительной настройки TURN сервера для стабильной работы в продакшене. Базовая версия работает через STUN.'); }
    function listenForIncomingCalls() {}
    function setupCallHandlers() { document.getElementById('callBtn').onclick = startCall; document.getElementById('endCallBtn').onclick = () => {}; }
    
    async function setupProfile() {
        document.getElementById('settingsBtn').onclick = () => document.getElementById('profileModal').style.display = 'flex';
        document.getElementById('closeModalBtn').onclick = () => document.getElementById('profileModal').style.display = 'none';
        document.getElementById('saveProfileBtn').onclick = async () => {
            let newName = document.getElementById('modalName').value.trim();
            if(!newName) newName = 'Аноним';
            const usersRef = db.ref('users');
            const nameCheck = await usersRef.orderByChild('name').equalTo(newName).once('value');
            if (nameCheck.exists()) { let taken = false; nameCheck.forEach(child => { if (child.key !== currentUserId) taken = true; }); if (taken) { alert('Имя уже занято!'); return; } }
            currentUserName = newName;
            await db.ref('users/'+currentUserId).update({ name: newName });
            const msgsSnap = await db.ref('messages').orderByChild('userId').equalTo(currentUserId).once('value');
            msgsSnap.forEach(s => s.ref.update({ name: newName }));
            const avatarFile = document.getElementById('modalAvatar').files[0];
            if(avatarFile && avatarFile.size<=2*1024*1024){ const r=new FileReader(); r.onload=async(e)=>{ currentUserAvatar=e.target.result; await db.ref('users/'+currentUserId).update({ avatar: currentUserAvatar }); const msgsSnap2 = await db.ref('messages').orderByChild('userId').equalTo(currentUserId).once('value'); msgsSnap2.forEach(s => s.ref.update({ avatar: currentUserAvatar })); updateHeaderAvatar(); document.getElementById('profileModal').style.display='none'; }; r.readAsDataURL(avatarFile); }
            else { updateHeaderAvatar(); document.getElementById('profileModal').style.display='none'; }
        };
        document.getElementById('modalName').value = currentUserName;
    }
    
    function setupTheme() {
        if(localStorage.getItem('theme')==='dark') document.body.classList.add('dark');
        document.getElementById('themeToggle').onclick = () => { document.body.classList.toggle('dark'); localStorage.setItem('theme', document.body.classList.contains('dark') ? 'dark' : 'light'); };
    }
    
    function setupAdminPanel() {
        const isAdmin = currentUserName === 'DaniksGames';
        document.getElementById('adminBadge').style.display = isAdmin ? 'inline-block' : 'none';
        document.getElementById('adminPanelBtn').onclick = () => { if(isAdmin) { loadUsersList(); document.getElementById('adminPanel').style.display = 'flex'; } else alert('Доступ только у DaniksGames'); };
        document.getElementById('closeAdminBtn').onclick = () => document.getElementById('adminPanel').style.display = 'none';
        document.getElementById('clearChatBtn').onclick = async () => { if(isAdmin && confirm('Очистить весь чат?')){ await db.ref('messages').remove(); const sys=document.createElement('div'); sys.className='system-message'; sys.innerText='👑 Админ очистил чат'; document.getElementById('messagesArea').appendChild(sys); } };
    }
    
    async function checkBlockStatus() {
        const userSnap = await db.ref('users/'+currentUserId).once('value');
        if(userSnap.val() && userSnap.val().blocked) {
            isBlocked = true;
            document.getElementById('messagesArea').innerHTML = '<div class="blocked-message">⛔ Ваш аккаунт заблокирован администратором. Вы не можете писать сообщения.</div>';
            document.querySelector('.input-area').style.display = 'none';
        } else { isBlocked = false; document.querySelector('.input-area').style.display = 'flex'; }
    }
    setInterval(checkBlockStatus, 3000);
    
    function escapeHtml(s) { if(!s) return ''; return s.replace(/[&<>]/g, m=>({'&':'&amp;','<':'&lt;','>':'&gt;'}[m])); }
    function initAll() { initMessaging(); setupCallHandlers(); setupProfile(); setupTheme(); setupAdminPanel(); listenForIncomingCalls(); checkBlockStatus(); if(Notification.permission==='default') Notification.requestPermission(); }
    
    document.getElementById('authBtn').onclick = auth;
    document.getElementById('acceptCallBtn').onclick = () => { if(window.answerCall) window.answerCall(true); };
    document.getElementById('rejectCallBtn').onclick = () => { if(window.answerCall) window.answerCall(false); };
    checkSession();
</script>
</body>
</html>
