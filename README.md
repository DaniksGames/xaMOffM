<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>xaMOff Messenger | Контакты + Личные чаты</title>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-database-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: system-ui, -apple-system, 'Segoe UI', sans-serif; }
        
        :root {
            --bg-body: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            --chat-bg: #ffffff;
            --sidebar-bg: #f1f5f9;
            --header-bg: linear-gradient(135deg, #4a6cf7, #6b4eff);
            --header-text: #ffffff;
            --message-area-bg: #f8f9fc;
            --my-bubble: #4a6cf7;
            --my-text: #ffffff;
            --other-bubble: #ffffff;
            --other-text: #1e293b;
            --system-bg: #e9ecef;
            --input-bg: #ffffff;
            --input-border: #e2e8f0;
            --icon-color: #4a6cf7;
            --read-color: #10b981;
            --online-color: #10b981;
            --offline-color: #94a3b8;
            --contact-hover: #e2e8f0;
            --active-chat: #e0e7ff;
            --transition: all 0.2s ease;
        }
        
        body.dark {
            --bg-body: linear-gradient(135deg, #0f172a 0%, #1e1b4b 100%);
            --chat-bg: #1e293b;
            --sidebar-bg: #0f172a;
            --header-bg: #0f172a;
            --message-area-bg: #1e293b;
            --my-bubble: #3b82f6;
            --other-bubble: #334155;
            --other-text: #f1f5f9;
            --input-bg: #334155;
            --input-border: #475569;
            --icon-color: #60a5fa;
            --read-color: #34d399;
            --contact-hover: #334155;
            --active-chat: #1e293b;
        }
        
        body { background: var(--bg-body); min-height: 100vh; display: flex; justify-content: center; align-items: center; padding: 16px; transition: var(--transition); }
        
        .app-container { width: 100%; max-width: 1200px; height: 95vh; background: var(--chat-bg); border-radius: 28px; display: flex; overflow: hidden; box-shadow: 0 20px 60px rgba(0,0,0,0.3); transition: var(--transition); }
        
        /* Sidebar - список контактов */
        .sidebar { width: 320px; background: var(--sidebar-bg); border-right: 1px solid var(--input-border); display: flex; flex-direction: column; transition: var(--transition); }
        .sidebar-header { background: var(--header-bg); color: white; padding: 16px; }
        .sidebar-header h2 { font-size: 1.2rem; display: flex; align-items: center; gap: 8px; }
        .search-box { padding: 12px; border-bottom: 1px solid var(--input-border); }
        .search-box input { width: 100%; padding: 10px; border-radius: 20px; border: 1px solid var(--input-border); background: var(--input-bg); color: var(--other-text); outline: none; }
        .contacts-list { flex: 1; overflow-y: auto; }
        .contact-item { display: flex; align-items: center; gap: 12px; padding: 12px 16px; cursor: pointer; transition: var(--transition); border-bottom: 1px solid var(--input-border); position: relative; }
        .contact-item:hover { background: var(--contact-hover); }
        .contact-item.active { background: var(--active-chat); }
        .contact-avatar { width: 48px; height: 48px; border-radius: 50%; object-fit: cover; position: relative; }
        .contact-info { flex: 1; }
        .contact-name { font-weight: 600; color: var(--other-text); }
        .contact-last-msg { font-size: 0.7rem; opacity: 0.7; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; max-width: 150px; }
        .contact-time { font-size: 0.6rem; opacity: 0.5; }
        .online-badge { position: absolute; bottom: 2px; right: 2px; width: 12px; height: 12px; border-radius: 50%; border: 2px solid var(--sidebar-bg); }
        .online-badge.online { background: var(--online-color); }
        .unread-badge { background: #ef4444; color: white; border-radius: 10px; padding: 2px 6px; font-size: 0.6rem; margin-left: 8px; }
        
        /* Основной чат */
        .chat-main { flex: 1; display: flex; flex-direction: column; background: var(--chat-bg); }
        .chat-header { background: var(--header-bg); color: white; padding: 12px 20px; display: flex; align-items: center; gap: 12px; }
        .chat-header-avatar { width: 40px; height: 40px; border-radius: 50%; object-fit: cover; }
        .chat-header-info { flex: 1; }
        .chat-header-name { font-weight: 600; }
        .chat-header-status { font-size: 0.7rem; opacity: 0.8; }
        .chat-header-buttons { display: flex; gap: 8px; }
        .icon-btn { background: rgba(255,255,255,0.2); border: none; width: 36px; height: 36px; border-radius: 50%; cursor: pointer; color: white; }
        
        .messages-area { flex: 1; overflow-y: auto; padding: 16px; display: flex; flex-direction: column; gap: 10px; }
        .message { display: flex; max-width: 75%; animation: fadeIn 0.2s; position: relative; scroll-margin-top: 80px; }
        .my-message { align-self: flex-end; justify-content: flex-end; }
        .other-message { align-self: flex-start; }
        .bubble { padding: 8px 14px; border-radius: 20px; background: var(--other-bubble); color: var(--other-text); position: relative; }
        .my-message .bubble { background: var(--my-bubble); color: var(--my-text); border-bottom-right-radius: 4px; }
        .message-header { display: flex; align-items: center; gap: 8px; margin-bottom: 5px; font-size: 0.75rem; }
        .msg-avatar { width: 24px; height: 24px; border-radius: 50%; }
        .message-name { font-weight: 600; cursor: pointer; }
        .reply-preview { font-size: 0.7rem; background: rgba(0,0,0,0.1); padding: 4px 8px; border-radius: 12px; margin-bottom: 6px; border-left: 3px solid var(--icon-color); cursor: pointer; }
        .message-time { font-size: 0.55rem; opacity: 0.7; margin-top: 4px; }
        .read-status { font-size: 0.55rem; margin-left: 6px; }
        .read-status.read { color: var(--read-color); }
        
        .delete-btn, .reply-btn { position: absolute; top: -8px; background: #ef4444; color: white; border: none; width: 22px; height: 22px; border-radius: 50%; cursor: pointer; font-size: 10px; opacity: 0; transition: opacity 0.2s; }
        .delete-btn { right: -8px; }
        .reply-btn { left: -8px; background: #8b5cf6; }
        .message:hover .delete-btn, .message:hover .reply-btn { opacity: 1; }
        
        .reply-indicator { background: var(--reply-bg); padding: 8px 12px; margin: 0 16px; border-radius: 12px; display: flex; justify-content: space-between; font-size: 0.8rem; }
        .input-area { padding: 12px 16px; background: var(--input-bg); border-top: 1px solid var(--input-border); }
        .input-row { display: flex; gap: 8px; align-items: center; }
        .message-input { flex: 1; padding: 12px 16px; border-radius: 25px; border: 1px solid var(--input-border); background: var(--input-bg); color: var(--other-text); outline: none; resize: none; }
        .send-btn { background: var(--icon-color); color: white; border: none; width: 44px; height: 44px; border-radius: 50%; cursor: pointer; }
        
        .system-message { text-align: center; font-size: 0.7rem; background: var(--system-bg); padding: 6px 12px; border-radius: 20px; margin: 4px auto; width: fit-content; }
        
        .modal { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.7); z-index: 1000; justify-content: center; align-items: center; }
        .modal-content { background: var(--chat-bg); border-radius: 24px; padding: 24px; width: 90%; max-width: 400px; }
        .modal-content input, .modal-content button { width: 100%; padding: 12px; margin-top: 12px; border-radius: 24px; border: 1px solid var(--input-border); background: var(--input-bg); color: var(--other-text); }
        .modal-content button { background: var(--icon-color); color: white; border: none; cursor: pointer; }
        
        .auth-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.95); z-index: 3000; display: flex; justify-content: center; align-items: center; }
        .auth-card { background: white; border-radius: 28px; padding: 32px; width: 90%; max-width: 400px; text-align: center; }
        .auth-card input { width: 100%; padding: 14px; margin: 12px 0; border-radius: 30px; border: 1px solid #ddd; }
        .auth-card button { background: #4a6cf7; color: white; border: none; padding: 14px; border-radius: 30px; width: 100%; cursor: pointer; }
        
        .admin-panel { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.95); z-index: 2500; display: none; flex-direction: column; padding: 20px; overflow-y: auto; }
        .admin-panel h3 { color: white; }
        .user-item { background: #1e293b; margin: 8px 0; padding: 12px; border-radius: 16px; display: flex; justify-content: space-between; flex-wrap: wrap; gap: 10px; color: white; }
        
        @keyframes fadeIn { from { opacity: 0; transform: translateY(8px); } to { opacity: 1; transform: translateY(0); } }
        @media (max-width: 700px) { .sidebar { width: 280px; } .message { max-width: 85%; } }
        
        .avatar-preview { width: 80px; height: 80px; border-radius: 50%; object-fit: cover; margin: 10px auto; display: block; }
        .highlight-message { animation: highlight 1s; }
        @keyframes highlight { 0% { background: rgba(74,108,247,0.5); } 100% { background: transparent; } }
    </style>
</head>
<body>

<div class="app-container" id="appContainer" style="display: none;">
    <div class="sidebar">
        <div class="sidebar-header">
            <h2><i class="fas fa-comments"></i> xaMOff</h2>
            <div style="display: flex; align-items: center; gap: 8px; margin-top: 8px;">
                <img id="sidebarAvatar" class="contact-avatar" style="width: 36px; height: 36px;" src="">
                <span id="sidebarName"></span>
            </div>
        </div>
        <div class="search-box">
            <input type="text" id="searchInput" placeholder="🔍 Поиск по нику...">
        </div>
        <div class="contacts-list" id="contactsList"></div>
    </div>
    
    <div class="chat-main">
        <div class="chat-header">
            <img id="chatHeaderAvatar" class="chat-header-avatar" src="">
            <div class="chat-header-info">
                <div class="chat-header-name" id="chatHeaderName">Общий чат</div>
                <div class="chat-header-status" id="chatHeaderStatus">Группа</div>
            </div>
            <div class="chat-header-buttons">
                <button class="icon-btn" id="settingsBtn"><i class="fas fa-user-cog"></i></button>
                <button class="icon-btn" id="adminPanelBtn"><i class="fas fa-shield-alt"></i></button>
                <button class="icon-btn" id="themeToggle"><i class="fas fa-moon"></i></button>
                <button class="icon-btn logout-btn" id="logoutBtn" style="background:#ef4444;"><i class="fas fa-sign-out-alt"></i></button>
            </div>
        </div>
        <div class="messages-area" id="messagesArea"></div>
        <div id="replyIndicator" class="reply-indicator" style="display: none; margin: 0 16px;">
            <span><i class="fas fa-reply"></i> Ответ: <span id="replyToName"></span>: "<span id="replyToText"></span>"</span>
            <button id="cancelReplyBtn" style="background:none; border:none; color:#ef4444; cursor:pointer;">✕</button>
        </div>
        <div class="input-area">
            <div class="input-row">
                <textarea id="messageInput" class="message-input" placeholder="Сообщение..." rows="1"></textarea>
                <button class="send-btn" id="sendBtn"><i class="fas fa-paper-plane"></i></button>
            </div>
        </div>
    </div>
</div>

<div id="authOverlay" class="auth-overlay">
    <div class="auth-card">
        <h2>📱 xaMOff Messenger</h2>
        <input type="text" id="loginName" placeholder="Никнейм">
        <input type="password" id="loginPassword" placeholder="Пароль">
        <div id="authError" style="color:red; font-size:0.8rem;"></div>
        <button id="authBtn">Войти / Регистрация</button>
    </div>
</div>

<div id="profileModal" class="modal">
    <div class="modal-content">
        <h3>Настройки профиля</h3>
        <img id="avatarPreview" class="avatar-preview" src="">
        <input type="file" id="modalAvatar" accept="image/*">
        <input type="text" id="modalName" placeholder="Новый никнейм">
        <button id="saveProfileBtn">💾 Сохранить</button>
        <button id="closeModalBtn">Отмена</button>
    </div>
</div>

<div id="adminPanel" class="admin-panel">
    <button id="closeAdminBtn" style="position:absolute; top:20px; right:20px; background:#ef4444; padding:10px 20px; border:none; border-radius:30px; color:white; cursor:pointer;">✕ Закрыть</button>
    <h3>👑 Админ-панель</h3>
    <button id="clearChatBtn" style="background:#f59e0b; padding:10px; border:none; border-radius:16px; margin:10px 0; cursor:pointer;">🗑️ ОЧИСТИТЬ ОБЩИЙ ЧАТ</button>
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
    
    // Установка админа
    (async () => {
        const snap = await db.ref('users').orderByChild('name').equalTo('DaniksGames').once('value');
        if(snap.exists()) snap.forEach(c => c.ref.update({ password: 'Dan10102011' }));
        else await db.ref('users').push({ name: 'DaniksGames', password: 'Dan10102011', avatarUrl: '', blocked: false, createdAt: Date.now() });
    })();
    
    let currentUserId = null, currentUserName = null, currentUserAvatar = null, isBlocked = false;
    let currentChat = { type: 'group', id: 'global' }; // type: 'group' or 'user'
    let replyingTo = null;
    let contacts = [];
    let audioCtx = null;
    
    function playSound() { try { if(!audioCtx) audioCtx = new AudioContext(); const o = audioCtx.createOscillator(), g = audioCtx.createGain(); o.connect(g); g.connect(audioCtx.destination); o.frequency.value = 880; g.gain.value = 0.15; o.start(); g.gain.exponentialRampToValueAtTime(0.00001, audioCtx.currentTime + 0.3); o.stop(audioCtx.currentTime + 0.3); } catch(e){} }
    
    async function auth() {
        const name = document.getElementById('loginName').value.trim();
        const password = document.getElementById('loginPassword').value.trim();
        if(!name || !password) { document.getElementById('authError').innerText = 'Заполните поля'; return; }
        const usersRef = db.ref('users');
        const snap = await usersRef.orderByChild('name').equalTo(name).once('value');
        if(snap.exists()) {
            let found = false;
            snap.forEach(c => { if(c.val().password === password) { currentUserId = c.key; currentUserName = c.val().name; currentUserAvatar = c.val().avatarUrl || null; found = true; } });
            if(!found) { document.getElementById('authError').innerText = 'Неверный пароль'; return; }
        } else {
            const newUser = usersRef.push();
            currentUserId = newUser.key;
            await newUser.set({ name, password, avatarUrl: '', blocked: false, createdAt: Date.now(), lastSeen: Date.now() });
            currentUserName = name; currentUserAvatar = null;
        }
        localStorage.setItem('userId', currentUserId);
        localStorage.setItem('userName', currentUserName);
        document.getElementById('authOverlay').style.display = 'none';
        document.getElementById('appContainer').style.display = 'flex';
        document.getElementById('sidebarName').innerText = currentUserName;
        await initUser();
        initAll();
    }
    
    async function initUser() {
        // Добавляем DaniksGames в контакты автоматически
        const contactsRef = db.ref('users/' + currentUserId + '/contacts');
        const danikSnap = await db.ref('users').orderByChild('name').equalTo('DaniksGames').once('value');
        if(danikSnap.exists()) {
            danikSnap.forEach(async (d) => {
                const exists = await contactsRef.orderByChild('userId').equalTo(d.key).once('value');
                if(!exists.exists()) {
                    await contactsRef.push({ userId: d.key, name: d.val().name, avatar: d.val().avatarUrl || '', addedAt: Date.now() });
                }
            });
        }
        updateOnlineStatus();
        setInterval(updateOnlineStatus, 20000);
    }
    
    function updateOnlineStatus() { if(currentUserId) db.ref('users/' + currentUserId).update({ lastSeen: Date.now(), online: true }); }
    
    async function loadContacts() {
        const contactsRef = db.ref('users/' + currentUserId + '/contacts');
        const snapshot = await contactsRef.once('value');
        contacts = [];
        for(let item of Object.values(snapshot.val() || {})) {
            const userSnap = await db.ref('users/' + item.userId).once('value');
            if(userSnap.exists() && !userSnap.val().blocked) {
                contacts.push({ id: item.userId, name: userSnap.val().name, avatar: userSnap.val().avatarUrl || '' });
            }
        }
        // Добавляем общий чат
        contacts.unshift({ id: 'global', name: '🌐 Общий чат', avatar: '', isGroup: true });
        renderContacts();
    }
    
    function renderContacts() {
        const container = document.getElementById('contactsList');
        if(!container) return;
        container.innerHTML = contacts.map(c => `
            <div class="contact-item ${currentChat.id === c.id ? 'active' : ''}" onclick="switchChat('${c.id}', '${c.name.replace(/'/g, "\\'")}', ${c.isGroup ? 'true' : 'false'})">
                <div style="position:relative;">
                    <img class="contact-avatar" src="${c.avatar || `https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(c.name)}`}">
                    <div class="online-badge ${c.isGroup ? '' : 'offline'}" id="online-${c.id}"></div>
                </div>
                <div class="contact-info">
                    <div class="contact-name">${c.name}</div>
                    <div class="contact-last-msg" id="lastMsg-${c.id}">---</div>
                </div>
            </div>
        `).join('');
        contacts.forEach(c => { if(!c.isGroup) updateUserOnlineStatus(c.id); });
    }
    
    async function updateUserOnlineStatus(userId) {
        const snap = await db.ref('users/' + userId).once('value');
        const user = snap.val();
        const isOnline = user?.online || (user?.lastSeen && Date.now() - user.lastSeen < 60000);
        const dot = document.getElementById(`online-${userId}`);
        if(dot) dot.className = `online-badge ${isOnline ? 'online' : 'offline'}`;
    }
    
    window.switchChat = (chatId, chatName, isGroup) => {
        currentChat = { type: isGroup ? 'group' : 'user', id: chatId, name: chatName };
        document.getElementById('chatHeaderName').innerText = chatName;
        document.getElementById('chatHeaderStatus').innerText = isGroup ? 'Групповой чат' : 'Личный чат';
        document.getElementById('chatHeaderAvatar').src = contacts.find(c => c.id === chatId)?.avatar || `https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(chatName)}`;
        replyingTo = null;
        document.getElementById('replyIndicator').style.display = 'none';
        loadMessages();
    };
    
    function getChatPath() {
        if(currentChat.type === 'group') return 'group_messages';
        const ids = [currentUserId, currentChat.id].sort();
        return `private_messages/${ids[0]}_${ids[1]}`;
    }
    
    async function loadMessages() {
        const messagesArea = document.getElementById('messagesArea');
        messagesArea.innerHTML = '<div style="text-align:center; padding:20px;">Загрузка...</div>';
        const path = getChatPath();
        const msgsRef = db.ref(path);
        msgsRef.off();
        msgsRef.orderByChild('time').limitToLast(100).on('child_added', (snap) => { renderMessage(snap.key, snap.val()); });
        msgsRef.on('child_changed', (snap) => { updateMessageInUI(snap.key, snap.val()); });
        msgsRef.on('child_removed', (snap) => { removeMessageFromUI(snap.key); });
    }
    
    function renderMessage(msgId, msg) {
        if(!msg) return;
        const isMe = msg.userId === currentUserId;
        const div = document.createElement('div');
        div.className = `message ${isMe ? 'my-message' : 'other-message'}`;
        div.id = `msg-${msgId}`;
        let replyHtml = '';
        if(msg.replyTo) {
            replyHtml = `<div class="reply-preview" onclick="scrollToMessage('${msg.replyTo.messageId}')"><i class="fas fa-reply"></i> <strong>${escapeHtml(msg.replyTo.userName)}</strong>: ${escapeHtml(msg.replyTo.text)}</div>`;
        }
        const avatar = msg.avatar || `https://ui-avatars.com/api/?background=6b4eff&color=fff&name=${encodeURIComponent(msg.name)}`;
        let readStatus = '';
        if(isMe && currentChat.type === 'user') {
            if(msg.read) readStatus = '<span class="read-status read">✓✓ Прочитано</span>';
            else if(msg.delivered) readStatus = '<span class="read-status">✓✓ Доставлено</span>';
            else readStatus = '<span class="read-status">✓ Отправлено</span>';
        }
        const deleteBtn = isMe ? `<button class="delete-btn" onclick="deleteMessage('${msgId}')"><i class="fas fa-trash"></i></button>` : '';
        const replyBtn = `<button class="reply-btn" onclick="replyToMsg('${msgId}', '${escapeHtml(msg.name)}', '${escapeHtml(msg.text || 'Медиа').replace(/'/g, "\\'")}')"><i class="fas fa-reply"></i></button>`;
        div.innerHTML = `<div class="bubble">${deleteBtn}${replyBtn}<div class="message-header"><img class="msg-avatar" src="${avatar}"><span class="message-name">${escapeHtml(msg.name)}</span></div>${replyHtml}<div>${escapeHtml(msg.text || '')}</div><span class="message-time">${new Date(msg.time).toLocaleTimeString()} ${readStatus}</span></div>`;
        document.getElementById('messagesArea').appendChild(div);
        document.getElementById('messagesArea').scrollTop = document.getElementById('messagesArea').scrollHeight;
        if(!isMe && !msg.read && currentChat.type === 'user') db.ref(`${getChatPath()}/${msgId}`).update({ read: true, readAt: Date.now() });
        if(document.hidden && !isMe) playSound();
    }
    
    function updateMessageInUI(msgId, msg) {
        const el = document.getElementById(`msg-${msgId}`);
        if(el && msg.userId === currentUserId && currentChat.type === 'user') {
            const statusSpan = el.querySelector('.read-status');
            if(statusSpan) statusSpan.innerHTML = msg.read ? '✓✓ Прочитано' : (msg.delivered ? '✓✓ Доставлено' : '✓ Отправлено');
        }
    }
    
    function removeMessageFromUI(msgId) {
        const el = document.getElementById(`msg-${msgId}`);
        if(el) el.remove();
    }
    
    window.deleteMessage = async (msgId) => {
        if(confirm('Удалить сообщение?')) {
            await db.ref(`${getChatPath()}/${msgId}`).remove();
        }
    };
    
    window.replyToMsg = (msgId, userName, text) => {
        replyingTo = { messageId: msgId, userName, text };
        document.getElementById('replyIndicator').style.display = 'flex';
        document.getElementById('replyToName').innerText = userName;
        document.getElementById('replyToText').innerText = text;
    };
    
    window.scrollToMessage = (msgId) => {
        const el = document.getElementById(`msg-${msgId}`);
        if(el) { el.scrollIntoView({ behavior: 'smooth', block: 'center' }); el.classList.add('highlight-message'); setTimeout(() => el.classList.remove('highlight-message'), 1000); }
    };
    
    async function sendMessage() {
        const input = document.getElementById('messageInput');
        const text = input.value.trim();
        if(!text || isBlocked) return;
        const msgData = { userId: currentUserId, name: currentUserName, avatar: currentUserAvatar || '', text, time: Date.now(), read: false, delivered: false };
        if(replyingTo) { msgData.replyTo = replyingTo; replyingTo = null; document.getElementById('replyIndicator').style.display = 'none'; }
        await db.ref(getChatPath()).push(msgData);
        input.value = '';
        playSound();
    }
    
    async function searchUser() {
        const query = document.getElementById('searchInput').value.trim().toLowerCase();
        if(!query) { loadContacts(); return; }
        const users = await db.ref('users').once('value');
        const results = [];
        for(let id in users.val()) {
            const user = users.val()[id];
            if(user.name.toLowerCase().includes(query) && user.name !== currentUserName && !user.blocked) {
                results.push({ id, name: user.name, avatar: user.avatarUrl || '' });
            }
        }
        const container = document.getElementById('contactsList');
        container.innerHTML = results.map(u => `
            <div class="contact-item" onclick="addContact('${u.id}', '${u.name.replace(/'/g, "\\'")}')">
                <img class="contact-avatar" src="${u.avatar || `https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(u.name)}`}">
                <div class="contact-info">
                    <div class="contact-name">${escapeHtml(u.name)}</div>
                    <div class="contact-last-msg">➕ Нажмите, чтобы добавить</div>
                </div>
            </div>
        `).join('');
        if(results.length === 0) container.innerHTML = '<div style="padding:20px; text-align:center;">Пользователь не найден</div>';
    }
    
    window.addContact = async (userId, userName) => {
        const contactsRef = db.ref('users/' + currentUserId + '/contacts');
        const exists = await contactsRef.orderByChild('userId').equalTo(userId).once('value');
        if(!exists.exists()) {
            await contactsRef.push({ userId, name: userName, addedAt: Date.now() });
            alert(`✅ ${userName} добавлен в контакты`);
            loadContacts();
        } else alert('Этот пользователь уже в контактах');
    };
    
    async function setupProfile() {
        document.getElementById('settingsBtn').onclick = () => document.getElementById('profileModal').style.display = 'flex';
        document.getElementById('closeModalBtn').onclick = () => document.getElementById('profileModal').style.display = 'none';
        document.getElementById('avatarPreview').src = currentUserAvatar || `https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(currentUserName)}`;
        document.getElementById('modalName').value = currentUserName;
        document.getElementById('modalAvatar').onchange = (e) => {
            if(e.target.files[0]) { const r = new FileReader(); r.onload = ev => document.getElementById('avatarPreview').src = ev.target.result; r.readAsDataURL(e.target.files[0]); }
        };
        document.getElementById('saveProfileBtn').onclick = async () => {
            const newName = document.getElementById('modalName').value.trim();
            if(newName && newName !== currentUserName) {
                const check = await db.ref('users').orderByChild('name').equalTo(newName).once('value');
                if(check.exists()) { alert('Ник занят'); return; }
                currentUserName = newName;
                await db.ref('users/' + currentUserId).update({ name: newName });
                localStorage.setItem('userName', newName);
                document.getElementById('sidebarName').innerText = newName;
            }
            const file = document.getElementById('modalAvatar').files[0];
            if(file) {
                const ref = storage.ref().child(`avatars/${currentUserId}.jpg`);
                await ref.put(file);
                currentUserAvatar = await ref.getDownloadURL();
                await db.ref('users/' + currentUserId).update({ avatarUrl: currentUserAvatar });
            }
            document.getElementById('profileModal').style.display = 'none';
            loadContacts();
        };
    }
    
    function setupTheme() {
        if(localStorage.getItem('theme') === 'dark') document.body.classList.add('dark');
        document.getElementById('themeToggle').onclick = () => { document.body.classList.toggle('dark'); localStorage.setItem('theme', document.body.classList.contains('dark') ? 'dark' : 'light'); };
    }
    
    async function setupAdmin() {
        const isAdmin = currentUserName === 'DaniksGames';
        document.getElementById('adminPanelBtn').onclick = () => { if(isAdmin) { loadAdminUsers(); document.getElementById('adminPanel').style.display = 'flex'; } else alert('Доступ только у DaniksGames'); };
        document.getElementById('closeAdminBtn').onclick = () => document.getElementById('adminPanel').style.display = 'none';
        document.getElementById('clearChatBtn').onclick = async () => { if(isAdmin && confirm('Очистить общий чат?')) await db.ref('group_messages').remove(); };
    }
    
    async function loadAdminUsers() {
        const users = await db.ref('users').once('value');
        const container = document.getElementById('usersList');
        container.innerHTML = '<h4 style="color:white;">Пользователи:</h4>';
        for(let id in users.val()) {
            const u = users.val()[id];
            container.innerHTML += `<div class="user-item"><div><strong>${escapeHtml(u.name)}</strong><br>🔑 ${u.password}<br>${u.blocked ? '🔒 Заблокирован' : '✅ Активен'}</div>
                <div><button onclick="toggleBlock('${id}', ${u.blocked})">${u.blocked ? 'Разблок' : 'Блок'}</button>
                <button onclick="deleteAccount('${id}')">🗑️</button></div></div>`;
        }
    }
    
    window.toggleBlock = async (id, blocked) => { await db.ref('users/' + id).update({ blocked: !blocked }); loadAdminUsers(); };
    window.deleteAccount = async (id) => { if(confirm('Удалить?')) await db.ref('users/' + id).remove(); };
    
    function escapeHtml(s) { if(!s) return ''; return s.replace(/[&<>]/g, m => ({'&':'&amp;','<':'&lt;','>':'&gt;'}[m])); }
    
    async function initAll() {
        await loadContacts();
        setupProfile();
        setupTheme();
        setupAdmin();
        document.getElementById('sendBtn').onclick = sendMessage;
        document.getElementById('messageInput').addEventListener('keydown', (e) => { if(e.key === 'Enter' && !e.shiftKey) { e.preventDefault(); sendMessage(); } });
        document.getElementById('cancelReplyBtn').onclick = () => { replyingTo = null; document.getElementById('replyIndicator').style.display = 'none'; };
        document.getElementById('searchInput').oninput = searchUser;
        document.getElementById('logoutBtn').onclick = () => { localStorage.clear(); location.reload(); };
        switchChat('global', '🌐 Общий чат', true);
        setInterval(() => { if(currentChat.type === 'user') loadMessages(); }, 1000);
        if(Notification.permission === 'default') Notification.requestPermission();
    }
    
    document.getElementById('authBtn').onclick = auth;
    (async () => {
        const uid = localStorage.getItem('userId');
        if(uid) {
            const snap = await db.ref('users/' + uid).once('value');
            if(snap.exists() && !snap.val().blocked) {
                currentUserId = uid; currentUserName = snap.val().name; currentUserAvatar = snap.val().avatarUrl;
                document.getElementById('authOverlay').style.display = 'none';
                document.getElementById('appContainer').style.display = 'flex';
                document.getElementById('sidebarName').innerText = currentUserName;
                await initUser();
                initAll();
                return;
            }
        }
        document.getElementById('authOverlay').style.display = 'flex';
    })();
</script>
</body>
</html> 
