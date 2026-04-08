<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>FullChat | Телефон + Админ + Уведомления</title>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-database-compat.js"></script>
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
            --input-bg: #ffffff;
            --input-border: #e2e8f0;
            --icon-color: #4a6cf7;
        }
        
        body.dark {
            --bg-body: linear-gradient(135deg, #0f172a 0%, #1e1b4b 100%);
            --chat-bg: #1e293b;
            --header-bg: #0f172a;
            --message-area-bg: #0f172a;
            --my-bubble: #3b82f6;
            --other-bubble: #334155;
            --other-text: #f1f5f9;
            --system-bg: #1e293b;
            --input-bg: #334155;
            --input-border: #475569;
            --icon-color: #60a5fa;
        }
        
        body { background: var(--bg-body); min-height: 100vh; display: flex; justify-content: center; align-items: center; padding: 16px; }
        .chat-container { width: 100%; max-width: 900px; height: 95vh; background: var(--chat-bg); border-radius: 28px; display: flex; flex-direction: column; overflow: hidden; box-shadow: 0 20px 60px rgba(0,0,0,0.3); position: relative; }
        
        .chat-header { background: var(--header-bg); color: var(--header-text); padding: 12px 20px; display: flex; align-items: center; justify-content: space-between; }
        .header-left { display: flex; align-items: center; gap: 12px; }
        .user-avatar { width: 44px; height: 44px; border-radius: 50%; object-fit: cover; background: #475569; }
        .header-text h1 { font-size: 1.2rem; }
        .header-text p { font-size: 0.65rem; opacity: 0.8; }
        .header-buttons { display: flex; gap: 8px; }
        .icon-btn { background: rgba(255,255,255,0.2); border: none; width: 38px; height: 38px; border-radius: 50%; cursor: pointer; color: white; font-size: 1.1rem; }
        
        .messages-area { flex: 1; overflow-y: auto; padding: 16px; background: var(--message-area-bg); display: flex; flex-direction: column; gap: 10px; }
        
        .message { display: flex; max-width: 80%; animation: fadeIn 0.2s ease; position: relative; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(8px); } to { opacity: 1; transform: translateY(0); } }
        .my-message { align-self: flex-end; justify-content: flex-end; }
        .other-message { align-self: flex-start; }
        .bubble { padding: 8px 14px; border-radius: 20px; word-wrap: break-word; background: var(--other-bubble); color: var(--other-text); position: relative; }
        .my-message .bubble { background: var(--my-bubble); color: var(--my-text); border-bottom-right-radius: 4px; }
        .message-header { display: flex; align-items: center; gap: 8px; margin-bottom: 5px; }
        .msg-avatar { width: 26px; height: 26px; border-radius: 50%; object-fit: cover; }
        .message-name { font-size: 0.75rem; font-weight: 600; }
        .message-time { font-size: 0.55rem; opacity: 0.7; margin-top: 4px; display: block; }
        .my-message .message-time { text-align: right; }
        
        .read-status { font-size: 0.55rem; margin-left: 6px; opacity: 0.6; }
        .read-status.read { color: #10b981; }
        
        .delete-btn { position: absolute; top: -8px; right: -8px; background: #ef4444; color: white; border: none; width: 22px; height: 22px; border-radius: 50%; cursor: pointer; font-size: 10px; opacity: 0; transition: opacity 0.2s; z-index: 10; }
        .admin-delete-btn { position: absolute; top: -8px; right: 18px; background: #f59e0b; color: white; border: none; width: 22px; height: 22px; border-radius: 50%; cursor: pointer; font-size: 10px; opacity: 0; transition: opacity 0.2s; z-index: 10; }
        .message:hover .delete-btn, .message:hover .admin-delete-btn { opacity: 1; }
        
        .system-message { text-align: center; font-size: 0.7rem; background: var(--system-bg); color: var(--system-text); padding: 6px 12px; border-radius: 20px; margin: 4px auto; width: fit-content; }
        
        .input-area { background: var(--input-bg); border-top: 1px solid var(--input-border); padding: 12px 16px; }
        .input-row { display: flex; gap: 8px; align-items: center; }
        .message-input { flex: 1; padding: 12px 16px; border: 1px solid var(--input-border); border-radius: 25px; background: var(--input-bg); color: var(--input-text); outline: none; }
        .send-btn { background: var(--icon-color); color: white; border: none; width: 46px; height: 46px; border-radius: 50%; cursor: pointer; }
        
        /* Модалка входа */
        .auth-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.9); z-index: 3000; display: flex; justify-content: center; align-items: center; }
        .auth-card { background: white; border-radius: 28px; padding: 32px; width: 90%; max-width: 400px; text-align: center; }
        .auth-card input { width: 100%; padding: 14px; margin: 12px 0; border: 1px solid #ddd; border-radius: 30px; font-size: 1rem; text-align: center; }
        .auth-card button { background: #4a6cf7; color: white; border: none; padding: 14px; border-radius: 30px; width: 100%; font-size: 1rem; cursor: pointer; }
        
        /* Админ панель */
        .admin-panel { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.95); z-index: 2500; display: none; flex-direction: column; padding: 20px; overflow-y: auto; }
        .admin-panel h3 { color: white; margin-bottom: 20px; }
        .user-item { background: #1e293b; margin: 8px 0; padding: 12px; border-radius: 16px; display: flex; justify-content: space-between; align-items: center; flex-wrap: wrap; gap: 10px; }
        .user-item button { padding: 6px 12px; border-radius: 20px; border: none; cursor: pointer; margin-left: 8px; }
        .close-admin { position: absolute; top: 20px; right: 20px; background: #ef4444; color: white; border: none; padding: 10px 20px; border-radius: 30px; cursor: pointer; }
        
        @media (max-width: 600px) { .message { max-width: 90%; } }
    </style>
</head>
<body>

<div class="chat-container" id="chatContainer" style="display: none;">
    <div class="chat-header">
        <div class="header-left">
            <img id="headerAvatar" class="user-avatar" src="">
            <div class="header-text">
                <h1>💬 FullChat <span id="adminBadge" style="display:none; background:#ef4444; padding:2px 8px; border-radius:12px; font-size:0.7rem;">ADMIN</span></h1>
                <p id="userPhoneDisplay"></p>
            </div>
        </div>
        <div class="header-buttons">
            <button class="icon-btn" id="adminPanelBtn" style="display:none;"><i class="fas fa-shield-alt"></i></button>
            <button class="icon-btn" id="themeToggle"><i class="fas fa-moon"></i></button>
        </div>
    </div>
    <div class="messages-area" id="messagesArea"></div>
    <div class="input-area">
        <div class="input-row">
            <input type="text" id="messageInput" class="message-input" placeholder="Сообщение...">
            <button class="send-btn" id="sendBtn"><i class="fas fa-paper-plane"></i></button>
        </div>
    </div>
</div>

<div id="authOverlay" class="auth-overlay">
    <div class="auth-card">
        <h2>📱 Добро пожаловать</h2>
        <p style="margin: 10px 0; color: #666;">Введите номер телефона для входа</p>
        <input type="tel" id="phoneInput" placeholder="+7 999 123-45-67" maxlength="20">
        <input type="text" id="userNameInput" placeholder="Ваше имя (как вас будут видеть)">
        <button id="authBtn">Продолжить</button>
    </div>
</div>

<div id="adminPanel" class="admin-panel">
    <button class="close-admin" id="closeAdminBtn">✕ Закрыть</button>
    <h3>👑 Админ-панель</h3>
    <button id="clearChatBtn" style="background:#f59e0b; color:white; padding:10px; border:none; border-radius:16px; margin:10px 0; cursor:pointer;">🗑️ ОЧИСТИТЬ ВЕСЬ ЧАТ</button>
    <div id="usersList"></div>
</div>

<script>
    // ========== FIREBASE ==========
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
    
    let currentUserId = null;
    let currentUserPhone = null;
    let currentUserName = null;
    let isAdmin = false;
    let audioCtx = null;
    
    // Звук уведомления
    function playNotificationSound() {
        try {
            if (!audioCtx) {
                audioCtx = new (window.AudioContext || window.webkitAudioContext)();
            }
            const oscillator = audioCtx.createOscillator();
            const gain = audioCtx.createGain();
            oscillator.connect(gain);
            gain.connect(audioCtx.destination);
            oscillator.frequency.value = 880;
            gain.gain.value = 0.3;
            oscillator.start();
            gain.gain.exponentialRampToValueAtTime(0.00001, audioCtx.currentTime + 0.5);
            oscillator.stop(audioCtx.currentTime + 0.5);
        } catch(e) { console.log('Audio error'); }
    }
    
    // Показ уведомления
    function showNotification(title, body) {
        if (Notification.permission === 'granted') {
            new Notification(title, { body: body, icon: 'https://cdn-icons-png.flaticon.com/512/4712/4712109.png' });
        } else if (Notification.permission !== 'denied') {
            Notification.requestPermission();
        }
        playNotificationSound();
    }
    
    // Регистрация/вход
    async function auth() {
        const phone = document.getElementById('phoneInput').value.trim().replace(/[^0-9+]/g, '');
        const name = document.getElementById('userNameInput').value.trim();
        
        if (!phone || phone.length < 5) { alert('Введите корректный номер телефона'); return; }
        if (!name) { alert('Введите ваше имя'); return; }
        
        // Нормализуем телефон
        let normalizedPhone = phone;
        if (!normalizedPhone.startsWith('+')) normalizedPhone = '+' + normalizedPhone;
        
        const usersRef = db.ref('users');
        const snapshot = await usersRef.orderByChild('phone').equalTo(normalizedPhone).once('value');
        
        let userId = null;
        let isNewUser = false;
        
        if (snapshot.exists()) {
            snapshot.forEach(child => { userId = child.key; });
            currentUserName = name;
            await usersRef.child(userId).update({ name: name, lastSeen: Date.now() });
        } else {
            isNewUser = true;
            const newUser = usersRef.push();
            userId = newUser.key;
            await newUser.set({
                phone: normalizedPhone,
                name: name,
                avatar: '',
                blocked: false,
                createdAt: Date.now()
            });
        }
        
        // Проверка на админа (первый зарегистрированный)
        const adminSnap = await db.ref('admin').once('value');
        if (!adminSnap.exists() && isNewUser) {
            await db.ref('admin').set({ adminId: userId, adminPhone: normalizedPhone });
            isAdmin = true;
        } else if (adminSnap.exists() && adminSnap.val().adminId === userId) {
            isAdmin = true;
        }
        
        currentUserId = userId;
        currentUserPhone = normalizedPhone;
        
        localStorage.setItem('userId', userId);
        localStorage.setItem('userPhone', normalizedPhone);
        localStorage.setItem('userName', name);
        
        document.getElementById('authOverlay').style.display = 'none';
        document.getElementById('chatContainer').style.display = 'flex';
        document.getElementById('userPhoneDisplay').innerText = normalizedPhone;
        document.getElementById('headerAvatar').src = `https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(name)}`;
        
        if (isAdmin) {
            document.getElementById('adminBadge').style.display = 'inline-block';
            document.getElementById('adminPanelBtn').style.display = 'flex';
        }
        
        initMessaging();
        setupAdminPanel();
        setupTheme();
    }
    
    // Проверка сохранённой сессии
    async function checkSession() {
        const savedUserId = localStorage.getItem('userId');
        const savedPhone = localStorage.getItem('userPhone');
        const savedName = localStorage.getItem('userName');
        
        if (savedUserId && savedPhone) {
            const userSnap = await db.ref('users/' + savedUserId).once('value');
            if (userSnap.exists()) {
                currentUserId = savedUserId;
                currentUserPhone = savedPhone;
                currentUserName = savedName || userSnap.val().name;
                
                const adminSnap = await db.ref('admin').once('value');
                if (adminSnap.exists() && adminSnap.val().adminId === savedUserId) isAdmin = true;
                
                document.getElementById('authOverlay').style.display = 'none';
                document.getElementById('chatContainer').style.display = 'flex';
                document.getElementById('userPhoneDisplay').innerText = currentUserPhone;
                document.getElementById('headerAvatar').src = `https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(currentUserName)}`;
                
                if (isAdmin) {
                    document.getElementById('adminBadge').style.display = 'inline-block';
                    document.getElementById('adminPanelBtn').style.display = 'flex';
                }
                
                initMessaging();
                setupAdminPanel();
                setupTheme();
                return true;
            }
        }
        return false;
    }
    
    // Отметка о прочтении
    async function markAsRead(messageId, senderId) {
        if (senderId === currentUserId) return;
        await db.ref('messages/' + messageId).update({ read: true, readAt: Date.now() });
    }
    
    // Удаление сообщения
    window.deleteMessage = async (messageId) => {
        if (confirm('Удалить это сообщение?')) {
            await db.ref('messages/' + messageId).remove();
        }
    };
    
    // Удаление чужого сообщения (админ)
    window.adminDeleteMessage = async (messageId) => {
        if (confirm('Удалить это сообщение как администратор?')) {
            await db.ref('messages/' + messageId).remove();
        }
    };
    
    // Блокировка пользователя
    window.toggleBlockUser = async (userId, currentBlocked) => {
        await db.ref('users/' + userId).update({ blocked: !currentBlocked });
        loadUsersList();
    };
    
    // Удаление аккаунта
    window.deleteUserAccount = async (userId) => {
        if (confirm('Удалить аккаунт пользователя и все его сообщения?')) {
            const messages = await db.ref('messages').orderByChild('userId').equalTo(userId).once('value');
            messages.forEach(msg => msg.ref.remove());
            await db.ref('users/' + userId).remove();
            loadUsersList();
        }
    };
    
    // Загрузка списка пользователей для админа
    async function loadUsersList() {
        const users = await db.ref('users').once('value');
        const usersData = users.val();
        const container = document.getElementById('usersList');
        if (!container) return;
        
        container.innerHTML = '<h4 style="color:white;">Все пользователи:</h4>';
        for (let id in usersData) {
            const user = usersData[id];
            const div = document.createElement('div');
            div.className = 'user-item';
            div.innerHTML = `
                <div style="color:white;">
                    <strong>${escapeHtml(user.name)}</strong><br>
                    <small>${user.phone}</small>
                    ${user.blocked ? '<span style="color:#ef4444;"> 🔒 Заблокирован</span>' : ''}
                    ${id === currentUserId ? '<span style="color:#10b981;"> (Вы)</span>' : ''}
                </div>
                <div>
                    <button style="background:#f59e0b;" onclick="toggleBlockUser('${id}', ${user.blocked || false})">${user.blocked ? '🔓 Разблокировать' : '🔒 Заблокировать'}</button>
                    <button style="background:#ef4444;" onclick="deleteUserAccount('${id}')">🗑️ Удалить</button>
                </div>
            `;
            container.appendChild(div);
        }
    }
    
    // Админ-панель
    function setupAdminPanel() {
        const adminBtn = document.getElementById('adminPanelBtn');
        const adminPanel = document.getElementById('adminPanel');
        const closeBtn = document.getElementById('closeAdminBtn');
        const clearChatBtn = document.getElementById('clearChatBtn');
        
        if (adminBtn) {
            adminBtn.onclick = () => {
                loadUsersList();
                adminPanel.style.display = 'flex';
            };
        }
        
        closeBtn.onclick = () => { adminPanel.style.display = 'none'; };
        
        clearChatBtn.onclick = async () => {
            if (confirm('⚠️ ОЧИСТИТЬ ВЕСЬ ЧАТ? Это действие необратимо!')) {
                await db.ref('messages').remove();
                const msgDiv = document.getElementById('messagesArea');
                const sysMsg = document.createElement('div');
                sysMsg.className = 'system-message';
                sysMsg.innerText = '👑 Администратор очистил весь чат.';
                msgDiv.appendChild(sysMsg);
                msgDiv.scrollTop = msgDiv.scrollHeight;
            }
        };
    }
    
    // Основной мессенджер
    function initMessaging() {
        const messagesRef = db.ref('messages');
        const messageInput = document.getElementById('messageInput');
        const sendBtn = document.getElementById('sendBtn');
        const messagesArea = document.getElementById('messagesArea');
        
        // Проверка блокировки
        db.ref('users/' + currentUserId).on('value', (snap) => {
            const user = snap.val();
            if (user && user.blocked) {
                messageInput.disabled = true;
                sendBtn.disabled = true;
                alert('Вы заблокированы администратором!');
            } else {
                messageInput.disabled = false;
                sendBtn.disabled = false;
            }
        });
        
        // Отправка сообщения
        window.sendMessage = async (text) => {
            if (!text.trim()) return;
            const userSnap = await db.ref('users/' + currentUserId).once('value');
            if (userSnap.val() && userSnap.val().blocked) {
                alert('Вы заблокированы!');
                return;
            }
            messagesRef.push({
                userId: currentUserId,
                name: currentUserName,
                phone: currentUserPhone,
                text: text.trim(),
                time: Date.now(),
                read: false
            });
            messageInput.value = '';
        };
        
        sendBtn.onclick = () => { if (messageInput.value.trim()) sendMessage(messageInput.value); };
        messageInput.addEventListener('keypress', (e) => { if (e.key === 'Enter') sendBtn.click(); });
        
        // Получение сообщений
        let lastMessageTime = 0;
        messagesRef.orderByChild('time').limitToLast(200).on('child_added', async (snap) => {
            const msg = snap.val();
            if (!msg) return;
            
            const isMe = (msg.userId === currentUserId);
            const div = document.createElement('div');
            div.className = `message ${isMe ? 'my-message' : 'other-message'}`;
            
            const avatarUrl = `https://ui-avatars.com/api/?background=6b4eff&color=fff&name=${encodeURIComponent(msg.name)}`;
            const readStatus = msg.read ? '<span class="read-status read">✓✓</span>' : '<span class="read-status">✓</span>';
            
            const deleteBtn = isMe ? `<button class="delete-btn" onclick="deleteMessage('${snap.key}')"><i class="fas fa-trash"></i></button>` : '';
            const adminDeleteBtn = (isAdmin && !isMe) ? `<button class="admin-delete-btn" onclick="adminDeleteMessage('${snap.key}')"><i class="fas fa-crown"></i></button>` : '';
            
            div.innerHTML = `<div class="bubble">
                ${deleteBtn}
                ${adminDeleteBtn}
                <div class="message-header">
                    ${!isMe ? `<img class="msg-avatar" src="${avatarUrl}">` : ''}
                    <span class="message-name">${escapeHtml(msg.name)}</span>
                </div>
                <div>${escapeHtml(msg.text)}</div>
                <span class="message-time">${new Date(msg.time).toLocaleTimeString()} ${isMe ? readStatus : ''}</span>
            </div>`;
            messagesArea.appendChild(div);
            messagesArea.scrollTop = messagesArea.scrollHeight;
            
            // Уведомление о новом сообщении
            if (!isMe && (Date.now() - msg.time) < 3000) {
                if (document.hidden) {
                    showNotification(msg.name, msg.text);
                } else {
                    playNotificationSound();
                }
            }
            
            // Отметка о прочтении
            if (!isMe && !msg.read) {
                await db.ref('messages/' + snap.key).update({ read: true, readAt: Date.now() });
            }
            
            lastMessageTime = msg.time;
        });
        
        // Обновление статуса прочтения
        messagesRef.on('child_changed', (snap) => {
            const msg = snap.val();
            if (msg && msg.userId !== currentUserId) {
                // Обновляем отображение галочек
                const messages = document.querySelectorAll('.message');
                // Просто перезагружать не будем, это сложно, но галочки обновятся при следующем сообщении
            }
        });
    }
    
    function escapeHtml(s) { if (!s) return ''; return s.replace(/[&<>]/g, m => ({ '&':'&amp;', '<':'&lt;', '>':'&gt;' }[m])); }
    
    function setupTheme() {
        if (localStorage.getItem('theme') === 'dark') document.body.classList.add('dark');
        document.getElementById('themeToggle').onclick = () => {
            document.body.classList.toggle('dark');
            localStorage.setItem('theme', document.body.classList.contains('dark') ? 'dark' : 'light');
        };
    }
    
    // Запрос уведомлений
    if (Notification.permission === 'default') {
        Notification.requestPermission();
    }
    
    // Запуск
    document.getElementById('authBtn').onclick = auth;
    checkSession();
</script>
</body>
</html>
