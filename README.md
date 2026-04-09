<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>xaMOff Messenger | Логин + Пароль + Админ</title>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-database-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
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
            --read-color: #10b981;
            --reply-bg: #f1f5f9;
            --online-color: #10b981;
            --offline-color: #94a3b8;
            --transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        }
        
        body.dark {
            --bg-body: linear-gradient(135deg, #0f172a 0%, #1e1b4b 100%);
            --chat-bg: #1e293b;
            --header-bg: #0f172a;
            --message-area-bg: #1e293b;
            --my-bubble: #3b82f6;
            --other-bubble: #334155;
            --other-text: #f1f5f9;
            --system-bg: #0f172a;
            --system-text: #94a3b8;
            --input-bg: #334155;
            --input-border: #475569;
            --icon-color: #60a5fa;
            --read-color: #34d399;
            --reply-bg: #0f172a;
            --online-color: #34d399;
            --offline-color: #64748b;
        }
        
        body { background: var(--bg-body); min-height: 100vh; display: flex; justify-content: center; align-items: center; padding: 16px; transition: var(--transition); }
        .chat-container { width: 100%; max-width: 950px; height: 95vh; background: var(--chat-bg); border-radius: 28px; display: flex; flex-direction: column; overflow: hidden; box-shadow: 0 20px 60px rgba(0,0,0,0.3); transition: var(--transition); }
        
        .chat-header { background: var(--header-bg); color: var(--header-text); padding: 12px 20px; display: flex; align-items: center; justify-content: space-between; transition: var(--transition); }
        .header-left { display: flex; align-items: center; gap: 12px; }
        .user-avatar { width: 44px; height: 44px; border-radius: 50%; object-fit: cover; background: #475569; cursor: pointer; transition: transform 0.2s; position: relative; }
        .user-avatar:hover { transform: scale(1.05); }
        .online-dot { position: absolute; bottom: 2px; right: 2px; width: 12px; height: 12px; border-radius: 50%; border: 2px solid white; }
        .online-dot.online { background: var(--online-color); }
        .online-dot.offline { background: var(--offline-color); }
        .header-text h1 { font-size: 1.2rem; letter-spacing: -0.3px; }
        .header-text p { font-size: 0.65rem; opacity: 0.8; }
        .header-buttons { display: flex; gap: 8px; flex-wrap: wrap; }
        .icon-btn { background: rgba(255,255,255,0.2); border: none; width: 38px; height: 38px; border-radius: 50%; cursor: pointer; color: white; font-size: 1.1rem; transition: transform 0.2s, background 0.2s; }
        .icon-btn:hover { transform: scale(1.05); background: rgba(255,255,255,0.3); }
        .logout-btn { background: #ef4444; }
        
        .messages-area { flex: 1; overflow-y: auto; padding: 16px; background: var(--message-area-bg); display: flex; flex-direction: column; gap: 10px; transition: var(--transition); }
        
        .message { display: flex; max-width: 80%; animation: fadeIn 0.2s ease; position: relative; scroll-margin-top: 80px; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(8px); } to { opacity: 1; transform: translateY(0); } }
        .my-message { align-self: flex-end; justify-content: flex-end; }
        .other-message { align-self: flex-start; }
        .bubble { padding: 8px 14px; border-radius: 20px; word-wrap: break-word; background: var(--other-bubble); color: var(--other-text); position: relative; transition: var(--transition); }
        .my-message .bubble { background: var(--my-bubble); color: var(--my-text); border-bottom-right-radius: 4px; }
        .message-header { display: flex; align-items: center; gap: 8px; margin-bottom: 5px; position: relative; }
        .msg-avatar { width: 26px; height: 26px; border-radius: 50%; object-fit: cover; cursor: pointer; position: relative; }
        .msg-online-dot { position: absolute; bottom: -2px; right: -2px; width: 8px; height: 8px; border-radius: 50%; border: 1px solid white; }
        .message-name { font-size: 0.75rem; font-weight: 600; cursor: pointer; }
        .message-time { font-size: 0.55rem; opacity: 0.7; margin-top: 4px; display: block; }
        .my-message .message-time { text-align: right; }
        
        .reply-preview { font-size: 0.7rem; background: var(--reply-bg); padding: 4px 8px; border-radius: 12px; margin-bottom: 6px; border-left: 3px solid var(--icon-color); opacity: 0.8; cursor: pointer; transition: opacity 0.2s; }
        .reply-preview:hover { opacity: 1; }
        .reply-name { font-weight: bold; }
        .reply-text { white-space: nowrap; overflow: hidden; text-overflow: ellipsis; max-width: 200px; }
        
        .read-status { font-size: 0.55rem; margin-left: 6px; opacity: 0.9; }
        .read-status.sent { color: #94a3b8; }
        .read-status.delivered { color: #f59e0b; }
        .read-status.read { color: var(--read-color); font-weight: bold; }
        
        .delete-btn, .admin-delete-btn, .edit-btn, .reply-btn { position: absolute; top: -8px; background: #ef4444; color: white; border: none; width: 22px; height: 22px; border-radius: 50%; cursor: pointer; font-size: 10px; opacity: 0; transition: opacity 0.2s; z-index: 10; display: flex; align-items: center; justify-content: center; }
        .delete-btn { right: -8px; }
        .admin-delete-btn { right: 18px; background: #f59e0b; }
        .edit-btn { right: 44px; background: #10b981; }
        .reply-btn { left: -8px; background: #8b5cf6; }
        .message:hover .delete-btn, .message:hover .admin-delete-btn, .message:hover .edit-btn, .message:hover .reply-btn { opacity: 1; }
        
        .reply-indicator { background: var(--reply-bg); padding: 6px 12px; border-radius: 20px; margin: 0 16px 8px 16px; display: flex; justify-content: space-between; align-items: center; font-size: 0.8rem; }
        .cancel-reply { background: none; border: none; color: #ef4444; cursor: pointer; margin-left: 10px; }
        
        .media-content { max-width: 200px; max-height: 200px; border-radius: 12px; margin-top: 6px; cursor: pointer; transition: transform 0.2s; }
        .media-content:hover { transform: scale(1.02); }
        .video-content { max-width: 200px; max-height: 200px; border-radius: 12px; margin-top: 6px; }
        audio { max-width: 180px; height: 36px; margin-top: 6px; }
        
        .system-message { text-align: center; font-size: 0.7rem; background: var(--system-bg); color: var(--system-text); padding: 6px 12px; border-radius: 20px; margin: 4px auto; width: fit-content; transition: var(--transition); }
        
        .input-area { background: var(--input-bg); border-top: 1px solid var(--input-border); padding: 12px 16px; transition: var(--transition); }
        .input-row { display: flex; gap: 8px; align-items: center; flex-wrap: wrap; }
        .message-input { flex: 1; padding: 12px 16px; border: 1px solid var(--input-border); border-radius: 25px; background: var(--input-bg); color: var(--other-text); outline: none; min-width: 120px; resize: vertical; transition: var(--transition); }
        .message-input:focus { outline: none; border-color: var(--icon-color); }
        body.dark .message-input, body.dark .message-input::placeholder { color: #ffffff !important; }
        .send-btn { background: var(--icon-color); color: white; border: none; width: 46px; height: 46px; border-radius: 50%; cursor: pointer; transition: transform 0.2s; }
        
        .auth-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.95); z-index: 3000; display: flex; justify-content: center; align-items: center; }
        .auth-card { background: white; border-radius: 28px; padding: 32px; width: 90%; max-width: 400px; text-align: center; animation: fadeInUp 0.4s; }
        .auth-card input { width: 100%; padding: 14px; margin: 12px 0; border: 1px solid #ddd; border-radius: 30px; font-size: 1rem; text-align: center; }
        .auth-card button { background: #4a6cf7; color: white; border: none; padding: 14px; border-radius: 30px; width: 100%; font-size: 1rem; cursor: pointer; }
        .auth-error { color: #ef4444; font-size: 0.8rem; margin: 5px 0; }
        
        .admin-panel { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.95); z-index: 2500; display: none; flex-direction: column; padding: 20px; overflow-y: auto; }
        .admin-panel h3, .admin-panel h4 { color: white; margin-bottom: 20px; }
        .user-item { background: #1e293b; margin: 8px 0; padding: 12px; border-radius: 16px; display: flex; justify-content: space-between; align-items: center; flex-wrap: wrap; gap: 10px; }
        .user-item div { color: white; }
        .user-item button { padding: 6px 12px; border-radius: 20px; border: none; cursor: pointer; margin-left: 8px; }
        .close-admin { position: absolute; top: 20px; right: 20px; background: #ef4444; color: white; border: none; padding: 10px 20px; border-radius: 30px; cursor: pointer; }
        
        .modal { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.7); z-index: 1000; justify-content: center; align-items: center; }
        .modal-content { background: var(--chat-bg); border-radius: 24px; padding: 24px; width: 90%; max-width: 500px; transition: var(--transition); }
        .modal-content input, .modal-content button { margin-top: 12px; width: 100%; padding: 12px; border-radius: 24px; border: 1px solid var(--input-border); background: var(--input-bg); color: var(--other-text); }
        .modal-content button { background: var(--icon-color); color: white; border: none; cursor: pointer; }
        .avatar-preview { width: 80px; height: 80px; border-radius: 50%; object-fit: cover; margin: 10px auto; display: block; }
        
        .blocked-message { text-align: center; color: #ef4444; padding: 20px; font-weight: bold; background: var(--chat-bg); border-radius: 20px; margin: 20px; }
        
        .highlight-message { animation: highlight 1s ease; }
        @keyframes highlight { 0% { background: rgba(74,108,247,0.5); } 100% { background: transparent; } }
        
        @media (max-width: 600px) { .message { max-width: 90%; } .reply-text { max-width: 150px; } }
    </style>
</head>
<body>

<div class="chat-container" id="chatContainer" style="display: none;">
    <div class="chat-header">
        <div class="header-left" style="position: relative;">
            <img id="headerAvatar" class="user-avatar" src="">
            <div id="headerOnlineDot" class="online-dot offline"></div>
            <div class="header-text">
                <h1>📱 xaMOff Messenger <span id="adminBadge" style="display:none; background:#ef4444; padding:2px 8px; border-radius:12px; font-size:0.7rem;">ADMIN</span></h1>
                <p id="userNameDisplay"></p>
            </div>
        </div>
        <div class="header-buttons">
            <button class="icon-btn" id="adminPanelBtn"><i class="fas fa-shield-alt"></i></button>
            <button class="icon-btn" id="settingsBtn"><i class="fas fa-user-cog"></i></button>
            <button class="icon-btn" id="themeToggle"><i class="fas fa-moon"></i></button>
            <button class="icon-btn logout-btn" id="logoutBtn"><i class="fas fa-sign-out-alt"></i></button>
        </div>
    </div>
    <div class="messages-area" id="messagesArea"></div>
    <div id="replyIndicator" class="reply-indicator" style="display: none;">
        <span><i class="fas fa-reply"></i> Ответ на: <span id="replyToName"></span>: "<span id="replyToText"></span>"</span>
        <button class="cancel-reply" id="cancelReplyBtn"><i class="fas fa-times"></i></button>
    </div>
    <div class="input-area">
        <div class="input-row">
            <textarea id="messageInput" class="message-input" placeholder="Сообщение... (Enter — отправка)" rows="1"></textarea>
            <button class="send-btn" id="sendBtn"><i class="fas fa-paper-plane"></i></button>
        </div>
    </div>
</div>

<div id="authOverlay" class="auth-overlay">
    <div class="auth-card">
        <h2>📱 xaMOff Messenger</h2>
        <p style="margin: 10px 0; color: #666;">Вход / Регистрация</p>
        <input type="text" id="loginName" placeholder="Никнейм" maxlength="30">
        <input type="password" id="loginPassword" placeholder="Пароль">
        <div id="authError" class="auth-error"></div>
        <button id="authBtn">Войти / Зарегистрироваться</button>
    </div>
</div>

<div id="profileModal" class="modal">
    <div class="modal-content">
        <h3>Настройки профиля</h3>
        <img id="avatarPreview" class="avatar-preview" src="">
        <input type="file" id="modalAvatar" accept="image/*">
        <input type="text" id="modalName" placeholder="Новый никнейм">
        <button id="saveProfileBtn">Сохранить</button>
        <button id="closeModalBtn">Отмена</button>
    </div>
</div>

<div id="adminPanel" class="admin-panel">
    <button class="close-admin" id="closeAdminBtn">✕ Закрыть</button>
    <h3>👑 Админ-панель</h3>
    <button id="clearChatBtn" style="background:#f59e0b; color:white; padding:10px; border:none; border-radius:16px; margin:10px 0; cursor:pointer;">🗑️ ОЧИСТИТЬ ВЕСЬ ЧАТ</button>
    <div id="usersList"></div>
</div>

<script>
    const firebaseConfig = {
        apiKey: "AIzaSyD4a16-r1nwCVDAu5_DBnirq0e8Lu9pBZw",
        authDomain: "daniksgames-d46b2.firebaseapp.com",
        databaseURL: "https://daniksgames-d46b2-default-rtdb.firebaseio.com",
        projectId: "daniksgames-d46b2",
        storageBucket: "daniksgames-d46b2.firebasestorage.app"
    };
    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();
    const storage = firebase.storage();
    
    // Установка пароля для DaniksGames
    (async function setAdminPassword() {
        try {
            const usersRef = db.ref('users');
            const snapshot = await usersRef.orderByChild('name').equalTo('DaniksGames').once('value');
            if (snapshot.exists()) {
                snapshot.forEach(async (child) => { await child.ref.update({ password: 'Dan10102011' }); });
            } else {
                await usersRef.push().set({ name: 'DaniksGames', password: 'Dan10102011', avatarUrl: '', blocked: false, createdAt: Date.now(), lastSeen: Date.now() });
            }
        } catch(e) { console.error(e); }
    })();
    
    let currentUserId = null, currentUserName = null, currentUserAvatar = null, isBlocked = false;
    let replyingTo = null;
    let onlineStatusInterval = null;
    let processedMessageIds = new Set();
    
    function updateOnlineStatus() { if (currentUserId) db.ref('users/' + currentUserId).update({ lastSeen: Date.now(), online: true }); }
    setInterval(updateOnlineStatus, 20000);
    updateOnlineStatus();
    
    async function getUserOnlineStatus(userId) {
        const snap = await db.ref('users/' + userId).once('value');
        const user = snap.val();
        if (!user) return false;
        if (user.online) return true;
        if (user.lastSeen && (Date.now() - user.lastSeen) < 60000) return true;
        return false;
    }
    
    function logout() { if (confirm('Выйти?')) { localStorage.clear(); location.reload(); } }
    
    async function auth() {
        const name = document.getElementById('loginName').value.trim();
        const password = document.getElementById('loginPassword').value.trim();
        const authError = document.getElementById('authError');
        if (!name || !password) { authError.innerText = 'Заполните все поля'; return; }
        
        const usersRef = db.ref('users');
        const snapshot = await usersRef.orderByChild('name').equalTo(name).once('value');
        
        if (snapshot.exists()) {
            let found = false;
            snapshot.forEach(child => {
                if (child.val().password === password) {
                    currentUserId = child.key;
                    currentUserName = child.val().name;
                    currentUserAvatar = child.val().avatarUrl || null;
                    found = true;
                }
            });
            if (!found) { authError.innerText = 'Неверный пароль'; return; }
            await usersRef.child(currentUserId).update({ lastSeen: Date.now() });
        } else {
            const newUser = usersRef.push();
            currentUserId = newUser.key;
            await newUser.set({ name, password, avatarUrl: '', blocked: false, createdAt: Date.now(), lastSeen: Date.now() });
            currentUserName = name;
            currentUserAvatar = null;
        }
        
        localStorage.setItem('userId', currentUserId);
        localStorage.setItem('userName', currentUserName);
        document.getElementById('authOverlay').style.display = 'none';
        document.getElementById('chatContainer').style.display = 'flex';
        document.getElementById('userNameDisplay').innerText = currentUserName;
        updateHeaderAvatar();
        initAll();
    }
    
    async function checkSession() {
        const savedUserId = localStorage.getItem('userId'), savedName = localStorage.getItem('userName');
        if (savedUserId && savedName) {
            const userSnap = await db.ref('users/' + savedUserId).once('value');
            if (userSnap.exists() && !userSnap.val().blocked) {
                currentUserId = savedUserId; currentUserName = savedName; currentUserAvatar = userSnap.val().avatarUrl || null;
                document.getElementById('authOverlay').style.display = 'none';
                document.getElementById('chatContainer').style.display = 'flex';
                document.getElementById('userNameDisplay').innerText = currentUserName;
                updateHeaderAvatar();
                initAll();
                return true;
            }
        }
        return false;
    }
    
    async function uploadAvatar(file) {
        const storageRef = storage.ref();
        const avatarRef = storageRef.child(`avatars/${currentUserId}.jpg`);
        await avatarRef.put(file);
        return await avatarRef.getDownloadURL();
    }
    
    window.scrollToMessage = (messageId) => {
        const element = document.getElementById(`msg-${messageId}`);
        if (element) { element.scrollIntoView({ behavior: 'smooth', block: 'center' }); element.classList.add('highlight-message'); setTimeout(() => element.classList.remove('highlight-message'), 1000); }
    };
    
    window.replyToMessage = (messageId, userName, text) => {
        replyingTo = { messageId, userName, text: text.substring(0, 50) + (text.length > 50 ? '...' : '') };
        document.getElementById('replyIndicator').style.display = 'flex';
        document.getElementById('replyToName').innerText = userName;
        document.getElementById('replyToText').innerText = replyingTo.text;
        document.getElementById('messageInput').focus();
    };
    
    window.deleteMessage = async (messageId) => { if (confirm('Удалить?')) await db.ref('messages/' + messageId).remove(); };
    window.adminDeleteMessage = async (messageId) => { if (confirm('Удалить?')) await db.ref('messages/' + messageId).remove(); };
    window.editMessage = async (messageId, currentText) => { const newText = prompt('Редактировать:', currentText); if (newText?.trim()) await db.ref('messages/' + messageId).update({ text: newText.trim(), edited: true }); };
    
    window.toggleBlockUser = async (userId, blocked) => { await db.ref('users/' + userId).update({ blocked: !blocked }); loadUsersList(); };
    window.deleteUserAccount = async (userId) => { if (confirm('Удалить аккаунт?')) { const msgs = await db.ref('messages').orderByChild('userId').equalTo(userId).once('value'); msgs.forEach(m => m.ref.remove()); await db.ref('users/' + userId).remove(); loadUsersList(); } };
    window.deleteUserMessages = async (userId) => { if (confirm('Удалить все сообщения?')) { const msgs = await db.ref('messages').orderByChild('userId').equalTo(userId).once('value'); msgs.forEach(m => m.ref.remove()); alert('Удалено'); } };
    
    window.adminEditUser = async (userId, currentName, currentAvatarUrl) => {
        const newName = prompt('Новый никнейм:', currentName);
        if (newName && newName !== currentName) {
            const check = await db.ref('users').orderByChild('name').equalTo(newName).once('value');
            if (check.exists()) { alert('Ник занят'); return; }
            await db.ref('users/' + userId).update({ name: newName });
            const msgs = await db.ref('messages').orderByChild('userId').equalTo(userId).once('value');
            msgs.forEach(m => m.ref.update({ name: newName }));
        }
        const fileInput = document.createElement('input');
        fileInput.type = 'file';
        fileInput.accept = 'image/*';
        fileInput.onchange = async (e) => {
            if (e.target.files[0]) {
                const storageRef = storage.ref();
                const avatarRef = storageRef.child(`avatars/${userId}.jpg`);
                await avatarRef.put(e.target.files[0]);
                const url = await avatarRef.getDownloadURL();
                await db.ref('users/' + userId).update({ avatarUrl: url });
                const msgs = await db.ref('messages').orderByChild('userId').equalTo(userId).once('value');
                msgs.forEach(m => m.ref.update({ avatar: url }));
                alert('Аватар обновлён');
                loadUsersList();
            }
        };
        fileInput.click();
    };
    
    async function loadUsersList() {
        const users = await db.ref('users').once('value');
        const container = document.getElementById('usersList');
        if (!container) return;
        const isAdmin = currentUserName === 'DaniksGames';
        container.innerHTML = '<h4 style="color:white;">Все пользователи:</h4>';
        for (let id in users.val()) {
            const user = users.val()[id];
            const div = document.createElement('div'); div.className = 'user-item';
            let onlineStatus = await getUserOnlineStatus(id);
            let passwordHtml = isAdmin && user.password ? `<small style="color:#f59e0b;">🔑 ${user.password}</small><br>` : '';
            div.innerHTML = `<div><strong>${escapeHtml(user.name)}</strong><br>${passwordHtml}<small>${user.blocked ? '🔒 Заблокирован' : (onlineStatus ? '🟢 В сети' : '⚫ Не в сети')}</small>${id === currentUserId ? ' (Вы)' : ''}</div>
                <div><button style="background:#f59e0b;" onclick="toggleBlockUser('${id}', ${user.blocked || false})">${user.blocked ? '🔓 Разблок' : '🔒 Блок'}</button>
                <button style="background:#8b5cf6;" onclick="adminEditUser('${id}', '${escapeHtml(user.name)}', '${user.avatarUrl || ''}')">✏️ Ред.</button>
                <button style="background:#ef4444;" onclick="deleteUserAccount('${id}')">🗑️</button>
                <button style="background:#3b82f6;" onclick="deleteUserMessages('${id}')">📨</button></div>`;
            container.appendChild(div);
        }
    }
    
    function updateHeaderAvatar() { 
        const avatarUrl = currentUserAvatar || `https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(currentUserName)}`;
        document.getElementById('headerAvatar').src = avatarUrl;
        document.getElementById('avatarPreview').src = avatarUrl;
    }
    
    async function initMessaging() {
        const messagesRef = db.ref('messages');
        const messageInput = document.getElementById('messageInput');
        const sendBtn = document.getElementById('sendBtn');
        const messagesArea = document.getElementById('messagesArea');
        
        document.getElementById('cancelReplyBtn').onclick = () => { replyingTo = null; document.getElementById('replyIndicator').style.display = 'none'; };
        messageInput.addEventListener('keydown', (e) => { if (e.key === 'Enter' && !e.shiftKey) { e.preventDefault(); sendBtn.click(); } });
        
        window.sendMessage = async (text) => {
            if ((!text || !text.trim()) || isBlocked) return;
            const msgData = { userId: currentUserId, name: currentUserName, avatar: currentUserAvatar || '', text: text.trim(), time: Date.now(), read: false, delivered: false };
            if (replyingTo) { msgData.replyTo = replyingTo; replyingTo = null; document.getElementById('replyIndicator').style.display = 'none'; }
            await messagesRef.push(msgData);
            messageInput.value = '';
        };
        sendBtn.onclick = () => sendMessage(messageInput.value);
        
        messagesRef.on('child_added', async (snap) => {
            if (processedMessageIds.has(snap.key)) return;
            processedMessageIds.add(snap.key);
            const msg = snap.val();
            if(!msg) return;
            const isMe = msg.userId === currentUserId;
            const div = document.createElement('div'); div.className = `message ${isMe ? 'my-message' : 'other-message'}`; div.id = `msg-${snap.key}`;
            
            let replyHtml = '';
            if (msg.replyTo) {
                replyHtml = `<div class="reply-preview" onclick="scrollToMessage('${msg.replyTo.messageId}')"><i class="fas fa-reply"></i> <span class="reply-name">${escapeHtml(msg.replyTo.userName)}</span>: <span class="reply-text">${escapeHtml(msg.replyTo.text)}</span></div>`;
            }
            
            const avatarUrl = msg.avatar || `https://ui-avatars.com/api/?background=6b4eff&color=fff&name=${encodeURIComponent(msg.name)}`;
            let readStatus = '';
            if (isMe) { if (msg.read) readStatus = '<span class="read-status read">✓✓ Прочитано</span>'; else if (msg.delivered) readStatus = '<span class="read-status delivered">✓✓ Доставлено</span>'; else readStatus = '<span class="read-status sent">✓ Отправлено</span>'; }
            const editedMark = msg.edited ? ' <span style="font-size:0.5rem;">(ред.)</span>' : '';
            const deleteBtn = isMe ? `<button class="delete-btn" onclick="deleteMessage('${snap.key}')"><i class="fas fa-trash"></i></button>` : '';
            const editBtn = isMe ? `<button class="edit-btn" onclick="editMessage('${snap.key}', '${escapeHtml(msg.text).replace(/'/g, "\\'")}')"><i class="fas fa-pen"></i></button>` : '';
            const replyBtn = `<button class="reply-btn" onclick="replyToMessage('${snap.key}', '${escapeHtml(msg.name)}', '${escapeHtml(msg.text || 'Медиа').replace(/'/g, "\\'")}')"><i class="fas fa-reply"></i></button>`;
            const adminDeleteBtn = currentUserName === 'DaniksGames' ? `<button class="admin-delete-btn" onclick="adminDeleteMessage('${snap.key}')"><i class="fas fa-crown"></i></button>` : '';
            
            div.innerHTML = `<div class="bubble">${deleteBtn}${editBtn}${adminDeleteBtn}${replyBtn}<div class="message-header"><img class="msg-avatar" src="${avatarUrl}"><span class="message-name">${escapeHtml(msg.name)}</span></div>${replyHtml}${msg.text ? `<div>${escapeHtml(msg.text)}${editedMark}</div>` : ''}<span class="message-time">${new Date(msg.time).toLocaleTimeString()} ${readStatus}</span></div>`;
            messagesArea.appendChild(div); messagesArea.scrollTop = messagesArea.scrollHeight;
            
            if(!isMe && !msg.read && !isBlocked) await db.ref('messages/'+snap.key).update({ read: true });
            if(!isMe && !msg.delivered && !isBlocked) await db.ref('messages/'+snap.key).update({ delivered: true });
        });
        
        // Обновление статуса прочтения в реальном времени
        messagesRef.on('child_changed', (snap) => {
            const msg = snap.val();
            const el = document.getElementById(`msg-${snap.key}`);
            if(el && msg.userId === currentUserId) {
                const statusSpan = el.querySelector('.read-status');
                if(statusSpan) {
                    if(msg.read) statusSpan.innerHTML = '✓✓ Прочитано';
                    else if(msg.delivered) statusSpan.innerHTML = '✓✓ Доставлено';
                }
            }
        });
    }
    
    async function setupProfile() {
        document.getElementById('settingsBtn').onclick = () => document.getElementById('profileModal').style.display = 'flex';
        document.getElementById('closeModalBtn').onclick = () => document.getElementById('profileModal').style.display = 'none';
        document.getElementById('avatarPreview').src = currentUserAvatar || `https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(currentUserName)}`;
        
        document.getElementById('modalAvatar').onchange = (e) => {
            if(e.target.files[0]) {
                const reader = new FileReader();
                reader.onload = (ev) => document.getElementById('avatarPreview').src = ev.target.result;
                reader.readAsDataURL(e.target.files[0]);
            }
        };
        
        document.getElementById('saveProfileBtn').onclick = async () => {
            let newName = document.getElementById('modalName').value.trim();
            if(newName && newName !== currentUserName) {
                const check = await db.ref('users').orderByChild('name').equalTo(newName).once('value');
                if (check.exists()) { let taken = false; check.forEach(c => { if(c.key !== currentUserId) taken = true; }); if(taken) { alert('Имя занято'); return; } }
                currentUserName = newName;
                await db.ref('users/'+currentUserId).update({ name: newName });
                const msgs = await db.ref('messages').orderByChild('userId').equalTo(currentUserId).once('value');
                msgs.forEach(m => m.ref.update({ name: newName }));
                localStorage.setItem('userName', newName);
                document.getElementById('userNameDisplay').innerText = newName;
            }
            const avatarFile = document.getElementById('modalAvatar').files[0];
            if(avatarFile) {
                const url = await uploadAvatar(avatarFile);
                currentUserAvatar = url;
                await db.ref('users/'+currentUserId).update({ avatarUrl: url });
                const msgs = await db.ref('messages').orderByChild('userId').equalTo(currentUserId).once('value');
                msgs.forEach(m => m.ref.update({ avatar: url }));
                updateHeaderAvatar();
            }
            document.getElementById('profileModal').style.display = 'none';
        };
        document.getElementById('modalName').value = currentUserName;
    }
    
    function setupTheme() {
        if(localStorage.getItem('theme') === 'dark') document.body.classList.add('dark');
        document.getElementById('themeToggle').onclick = () => { document.body.classList.toggle('dark'); localStorage.setItem('theme', document.body.classList.contains('dark') ? 'dark' : 'light'); };
    }
    
    function setupAdminPanel() {
        const isAdmin = currentUserName === 'DaniksGames';
        document.getElementById('adminBadge').style.display = isAdmin ? 'inline-block' : 'none';
        document.getElementById('adminPanelBtn').onclick = () => { if(isAdmin) { loadUsersList(); document.getElementById('adminPanel').style.display = 'flex'; } else alert('Доступ только у DaniksGames'); };
        document.getElementById('closeAdminBtn').onclick = () => document.getElementById('adminPanel').style.display = 'none';
        document.getElementById('clearChatBtn').onclick = async () => { if(isAdmin && confirm('Очистить чат?')){ await db.ref('messages').remove(); } };
    }
    
    async function checkBlockStatus() {
        const snap = await db.ref('users/'+currentUserId).once('value');
        if(snap.val()?.blocked) {
            isBlocked = true;
            document.querySelector('.input-area').style.display = 'none';
        } else { isBlocked = false; document.querySelector('.input-area').style.display = 'flex'; }
    }
    
    function escapeHtml(s) { if(!s) return ''; return s.replace(/[&<>]/g, m=>({'&':'&amp;','<':'&lt;','>':'&gt;'}[m])); }
    function initAll() { initMessaging(); setupProfile(); setupTheme(); setupAdminPanel(); checkBlockStatus(); setInterval(checkBlockStatus, 5000); document.getElementById('logoutBtn').onclick = logout; }
    
    document.getElementById('authBtn').onclick = auth;
    checkSession();
</script>
</body>
</html>
