<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>VideoChat | Звонки + Кружки</title>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-database-compat.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/fingerprintjs2/2.1.0/fingerprint2.min.js"></script>
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
            --system-text: #2c3e4e;
            --input-bg: #ffffff;
            --input-border: #e2e8f0;
            --icon-color: #4a6cf7;
            --call-overlay-bg: rgba(0,0,0,0.95);
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
            --system-text: #cbd5e1;
            --input-bg: #334155;
            --input-border: #475569;
            --icon-color: #60a5fa;
        }
        
        body { background: var(--bg-body); min-height: 100vh; display: flex; justify-content: center; align-items: center; padding: 16px; }
        .chat-container { width: 100%; max-width: 900px; height: 92vh; background: var(--chat-bg); border-radius: 28px; display: flex; flex-direction: column; overflow: hidden; box-shadow: 0 20px 60px rgba(0,0,0,0.3); }
        
        .chat-header { background: var(--header-bg); color: var(--header-text); padding: 12px 20px; display: flex; align-items: center; justify-content: space-between; }
        .header-left { display: flex; align-items: center; gap: 12px; }
        .user-avatar { width: 44px; height: 44px; border-radius: 50%; object-fit: cover; border: 2px solid rgba(255,255,255,0.4); cursor: pointer; }
        .header-text h1 { font-size: 1.2rem; }
        .header-text p { font-size: 0.65rem; opacity: 0.8; }
        .header-buttons { display: flex; gap: 8px; }
        .icon-btn { background: rgba(255,255,255,0.2); border: none; width: 38px; height: 38px; border-radius: 50%; cursor: pointer; color: white; font-size: 1.1rem; transition: 0.2s; }
        .call-btn { background: #10b981; }
        .call-btn:hover { background: #059669; }
        
        .messages-area { flex: 1; overflow-y: auto; padding: 16px; background: var(--message-area-bg); display: flex; flex-direction: column; gap: 10px; }
        
        .message { display: flex; max-width: 80%; animation: fadeIn 0.2s ease; position: relative; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(8px); } to { opacity: 1; transform: translateY(0); } }
        .my-message { align-self: flex-end; justify-content: flex-end; }
        .other-message { align-self: flex-start; }
        .bubble { padding: 8px 14px; border-radius: 20px; word-wrap: break-word; background: var(--other-bubble); color: var(--other-text); position: relative; }
        .my-message .bubble { background: var(--my-bubble); color: var(--my-text); border-bottom-right-radius: 4px; }
        .message-header { display: flex; align-items: center; gap: 8px; margin-bottom: 5px; }
        .msg-avatar { width: 26px; height: 26px; border-radius: 50%; object-fit: cover; cursor: pointer; }
        .message-name { font-size: 0.75rem; font-weight: 600; cursor: pointer; }
        .message-time { font-size: 0.55rem; opacity: 0.7; margin-top: 4px; display: block; }
        .my-message .message-time { text-align: right; }
        
        .delete-btn { position: absolute; top: -8px; right: -8px; background: #ef4444; color: white; border: none; width: 22px; height: 22px; border-radius: 50%; cursor: pointer; font-size: 10px; opacity: 0; transition: opacity 0.2s; display: flex; align-items: center; justify-content: center; z-index: 10; }
        .message:hover .delete-btn { opacity: 1; }
        
        .circle-video { width: 180px; height: 180px; border-radius: 50%; object-fit: cover; margin-top: 6px; cursor: pointer; background: #000; }
        .circle-video video { width: 100%; height: 100%; border-radius: 50%; object-fit: cover; }
        
        .system-message { text-align: center; font-size: 0.7rem; background: var(--system-bg); color: var(--system-text); padding: 6px 12px; border-radius: 20px; margin: 4px auto; width: fit-content; }
        .error-message { background: #fee2e2; color: #dc2626; }
        .success-message { background: #dcfce7; color: #16a34a; }
        
        .input-area { background: var(--input-bg); border-top: 1px solid var(--input-border); padding: 12px 16px; }
        .input-row { display: flex; gap: 8px; align-items: center; flex-wrap: wrap; }
        .message-input { flex: 1; padding: 12px 16px; border: 1px solid var(--input-border); border-radius: 25px; background: var(--input-bg); color: var(--input-text); outline: none; min-width: 120px; }
        .media-btn, .voice-btn, .camera-btn, .video-msg-btn { background: transparent; border: none; font-size: 1.3rem; cursor: pointer; color: var(--icon-color); width: 40px; height: 40px; border-radius: 50%; transition: 0.2s; }
        .video-msg-btn.recording { background: #ef4444; color: white; animation: pulse 1s infinite; }
        .send-btn { background: var(--icon-color); color: white; border: none; width: 46px; height: 46px; border-radius: 50%; cursor: pointer; }
        
        @keyframes pulse { 0% { transform: scale(1); opacity: 1; } 50% { transform: scale(1.1); opacity: 0.8; } 100% { transform: scale(1); opacity: 1; } }
        
        .permission-buttons { display: flex; gap: 10px; justify-content: center; margin: 10px 0; flex-wrap: wrap; }
        .perm-btn { background: #4a6cf7; color: white; border: none; padding: 10px 20px; border-radius: 30px; cursor: pointer; font-size: 0.9rem; margin: 5px; }
        .perm-btn.camera { background: #10b981; }
        .perm-btn.mic { background: #f59e0b; }
        .perm-btn.both { background: #8b5cf6; }
        
        .call-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: var(--call-overlay-bg); z-index: 2000; display: none; flex-direction: column; justify-content: center; align-items: center; }
        .call-video-container { width: 100%; height: 80%; display: flex; flex-wrap: wrap; justify-content: center; align-items: center; gap: 20px; padding: 20px; }
        .local-video, .remote-video { width: 45%; height: 45%; background: #000; border-radius: 16px; object-fit: cover; }
        .call-controls { position: absolute; bottom: 30px; display: flex; justify-content: center; gap: 20px; background: rgba(0,0,0,0.6); padding: 15px; border-radius: 60px; }
        .call-control-btn { background: #334155; border: none; width: 55px; height: 55px; border-radius: 50%; cursor: pointer; color: white; font-size: 1.5rem; }
        .call-control-btn.end-call { background: #ef4444; }
        .call-control-btn.active { background: #ef4444; }
        .incoming-call-modal { position: fixed; bottom: 100px; left: 20px; right: 20px; max-width: 350px; background: var(--chat-bg); border-radius: 20px; padding: 20px; z-index: 2100; box-shadow: 0 10px 40px rgba(0,0,0,0.3); display: none; }
        .modal { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.7); z-index: 1000; justify-content: center; align-items: center; }
        .modal-content { background: var(--chat-bg); border-radius: 24px; padding: 24px; width: 90%; max-width: 500px; }
        .modal-content input, .modal-content button { margin-top: 12px; width: 100%; padding: 12px; border-radius: 24px; }
        .modal-content button { background: var(--icon-color); color: white; border: none; cursor: pointer; }
        
        @media (max-width: 600px) { .local-video, .remote-video { width: 90%; height: 35%; } .message { max-width: 90%; } .circle-video { width: 130px; height: 130px; } }
    </style>
</head>
<body>

<div class="chat-container">
    <div class="chat-header">
        <div class="header-left">
            <img id="headerAvatar" class="user-avatar" src="">
            <div class="header-text">
                <h1>📹 VideoChat <span id="adminBadge" style="display:none; background:#ef4444; padding:2px 8px; border-radius:12px; font-size:0.7rem;">ADMIN</span></h1>
                <p>Звонки • Кружки • Фото/Видео</p>
            </div>
        </div>
        <div class="header-buttons">
            <button class="icon-btn call-btn" id="callBtn"><i class="fas fa-phone-alt"></i></button>
            <button class="icon-btn" id="settingsBtn"><i class="fas fa-user-cog"></i></button>
            <button class="icon-btn" id="themeToggle"><i class="fas fa-moon"></i></button>
        </div>
    </div>

    <div class="messages-area" id="messagesArea"></div>

    <div class="input-area">
        <div class="input-row">
            <input type="text" id="messageInput" class="message-input" placeholder="Сообщение..." autocomplete="off">
            <button class="camera-btn" id="photoBtn" title="Фото из галереи"><i class="fas fa-image"></i></button>
            <button class="camera-btn" id="takePhotoBtn" title="Сделать фото"><i class="fas fa-camera"></i></button>
            <button class="media-btn" id="videoFileBtn" title="Видео"><i class="fas fa-video"></i></button>
            <button class="video-msg-btn" id="circleVideoBtn" title="Кружок (видео до 30с)"><i class="fas fa-circle"></i></button>
            <button class="voice-btn" id="voiceBtn" title="Голосовое"><i class="fas fa-microphone"></i></button>
            <button class="send-btn" id="sendBtn"><i class="fas fa-paper-plane"></i></button>
        </div>
        <input type="file" id="photoInput" accept="image/*" style="display:none">
        <input type="file" id="videoFileInput" accept="video/*" style="display:none">
        <input type="file" id="cameraCaptureInput" accept="image/*" capture="environment" style="display:none">
    </div>
</div>

<div id="callOverlay" class="call-overlay">
    <div class="call-video-container">
        <video id="localVideo" class="local-video" autoplay muted playsinline></video>
        <video id="remoteVideo" class="remote-video" autoplay playsinline></video>
    </div>
    <div class="call-controls">
        <button class="call-control-btn" id="toggleMuteBtn"><i class="fas fa-microphone"></i></button>
        <button class="call-control-btn" id="toggleVideoBtn"><i class="fas fa-video"></i></button>
        <button class="call-control-btn" id="shareScreenBtn"><i class="fas fa-desktop"></i></button>
        <button class="call-control-btn end-call" id="endCallBtn"><i class="fas fa-phone-slash"></i></button>
    </div>
</div>

<div id="incomingCallModal" class="incoming-call-modal">
    <h3 id="callerName">📞 Входящий звонок...</h3>
    <div style="display:flex; gap:10px; margin-top:15px;">
        <button id="acceptCallBtn" style="flex:1; background:#10b981; padding:12px; border:none; border-radius:30px; color:white;">Принять</button>
        <button id="rejectCallBtn" style="flex:1; background:#ef4444; padding:12px; border:none; border-radius:30px; color:white;">Отклонить</button>
    </div>
</div>

<div id="profileModal" class="modal"><div class="modal-content"><h3>Настройки</h3><input type="text" id="modalName" placeholder="Имя"><input type="file" id="modalAvatar" accept="image/*"><button id="saveProfileBtn">Сохранить</button><button id="closeModalBtn">Отмена</button></div></div>

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
    
    let currentUserId, currentFingerprint, currentUserName, currentUserAvatar, isAdmin = false;
    let peerConnection = null;
    let localStream = null;
    let currentCallId = null;
    let isCallActive = false;
    let isMuted = false;
    let isVideoOff = false;
    let hasCamera = false;
    let hasMic = false;
    let callListeners = {};
    
    const messagesArea = document.getElementById('messagesArea');
    
    function addSystemMessage(text, type = '') {
        const div = document.createElement('div');
        div.className = `system-message ${type}`;
        div.innerHTML = text;
        messagesArea.appendChild(div);
        messagesArea.scrollTop = messagesArea.scrollHeight;
        console.log('[System]', text);
    }
    
    // ========== ЗАПРОС РАЗРЕШЕНИЙ ==========
    async function requestCamera() {
        try {
            const stream = await navigator.mediaDevices.getUserMedia({ video: true });
            stream.getTracks().forEach(track => track.stop());
            hasCamera = true;
            addSystemMessage('✅ Доступ к камере разрешён!', 'success-message');
            localStorage.setItem('camera_granted_' + currentFingerprint, 'true');
            return true;
        } catch(e) {
            addSystemMessage('❌ Нет доступа к камере: ' + e.message, 'error-message');
            return false;
        }
    }
    
    async function requestMicrophone() {
        try {
            const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
            stream.getTracks().forEach(track => track.stop());
            hasMic = true;
            addSystemMessage('✅ Доступ к микрофону разрешён!', 'success-message');
            localStorage.setItem('mic_granted_' + currentFingerprint, 'true');
            return true;
        } catch(e) {
            addSystemMessage('❌ Нет доступа к микрофону: ' + e.message, 'error-message');
            return false;
        }
    }
    
    async function requestBoth() {
        await requestCamera();
        await requestMicrophone();
        updatePermissionButtons();
    }
    
    function updatePermissionButtons() {
        const container = document.getElementById('permissionContainer');
        if (container) {
            if (hasCamera && hasMic) {
                container.innerHTML = '<div class="system-message success-message">✅ Все разрешения получены! Можно звонить.</div>';
            } else {
                container.innerHTML = `
                    <div class="permission-buttons">
                        <button class="perm-btn camera" id="reqCameraBtn">📷 Разрешить камеру</button>
                        <button class="perm-btn mic" id="reqMicBtn">🎤 Разрешить микрофон</button>
                        <button class="perm-btn both" id="reqBothBtn">🎥 Разрешить оба</button>
                    </div>
                `;
                document.getElementById('reqCameraBtn')?.addEventListener('click', requestCamera);
                document.getElementById('reqMicBtn')?.addEventListener('click', requestMicrophone);
                document.getElementById('reqBothBtn')?.addEventListener('click', requestBoth);
            }
        }
    }
    
    // ========== ОЧИСТКА СТАРЫХ ДАННЫХ ==========
    async function cleanOldDataAndInit() {
        const cleanFlag = localStorage.getItem('chat_cleaned_v4');
        if (!cleanFlag) {
            addSystemMessage('🧹 Первый запуск. Очищаем старые данные...', 'system-message');
            await db.ref('users').remove();
            await db.ref('messages').remove();
            await db.ref('calls').remove();
            await db.ref('admin').remove();
            localStorage.setItem('chat_cleaned_v4', 'true');
            addSystemMessage('✅ Старые данные удалены. Первый зашедший станет администратором.', 'success-message');
        }
    }
    
    // ========== FINGERPRINT И ПОЛЬЗОВАТЕЛЬ ==========
    function getFingerprint() { 
        return new Promise((resolve) => { 
            Fingerprint2.get((components) => { 
                const hash = Fingerprint2.x64hash128(components.map(c=>c.value).join(''), 31); 
                resolve(hash); 
            }); 
        }); 
    }
    
    async function initUser() {
        const fp = await getFingerprint();
        currentFingerprint = fp;
        
        hasCamera = localStorage.getItem('camera_granted_' + fp) === 'true';
        hasMic = localStorage.getItem('mic_granted_' + fp) === 'true';
        
        const adminSnap = await db.ref('admin').once('value');
        const usersRef = db.ref('users');
        const snap = await usersRef.orderByChild('fingerprint').equalTo(fp).once('value');
        
        if (snap.exists()) {
            snap.forEach(c => { 
                currentUserId = c.key; 
                currentUserName = c.val().name; 
                currentUserAvatar = c.val().avatar || ''; 
            });
            const adminData = await db.ref('admin').once('value');
            if (adminData.exists() && adminData.val().adminFingerprint === fp) isAdmin = true;
        } else {
            const adminExists = adminSnap.exists();
            if (!adminExists) isAdmin = true;
            currentUserName = 'User_' + Math.floor(Math.random() * 10000);
            const newUser = usersRef.push();
            currentUserId = newUser.key;
            await newUser.set({ fingerprint: fp, name: currentUserName, avatar: '', createdAt: Date.now() });
            if (isAdmin) {
                await db.ref('admin').set({ adminFingerprint: fp, adminId: currentUserId });
                addSystemMessage('👑 Вы стали администратором чата!', 'success-message');
            }
        }
        
        if (isAdmin) document.getElementById('adminBadge').style.display = 'inline-block';
        
        document.getElementById('headerAvatar').src = currentUserAvatar || `https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(currentUserName)}`;
        document.getElementById('modalName').value = currentUserName;
        
        if (!hasCamera || !hasMic) {
            const permDiv = document.createElement('div');
            permDiv.id = 'permissionContainer';
            messagesArea.appendChild(permDiv);
            updatePermissionButtons();
        } else {
            addSystemMessage('✅ Разрешения на камеру и микрофон уже выданы.', 'success-message');
        }
    }
    
    // ========== УДАЛЕНИЕ СООБЩЕНИЯ ==========
    window.deleteMessage = async (messageId) => {
        if (confirm('Удалить это сообщение?')) {
            await db.ref('messages/' + messageId).remove();
        }
    };
    
    // ========== МЕССЕНДЖЕР ==========
    function setupMessaging() {
        const messagesRef = db.ref('messages');
        const messageInput = document.getElementById('messageInput');
        const sendBtn = document.getElementById('sendBtn');
        
        window.sendMessage = (text, mediaType, mediaUrl, isCircle = false) => {
            if (!text && !mediaUrl) return;
            messagesRef.push({ 
                userId: currentUserId, name: currentUserName, avatar: currentUserAvatar || '', 
                text: text || '', time: Date.now(), mediaType: mediaType || null, mediaUrl: mediaUrl || null, isCircle: isCircle || false
            });
        };
        
        sendBtn.onclick = () => { if (messageInput.value.trim()) sendMessage(messageInput.value.trim()); messageInput.value = ''; messageInput.focus(); };
        messageInput.addEventListener('keypress', (e) => { if (e.key === 'Enter') sendBtn.click(); });
        
        document.getElementById('photoBtn').onclick = () => document.getElementById('photoInput').click();
        document.getElementById('photoInput').onchange = (e) => {
            if (e.target.files[0]) {
                const reader = new FileReader();
                reader.onload = (ev) => sendMessage('📷 Фото', 'image', ev.target.result);
                reader.readAsDataURL(e.target.files[0]);
                e.target.value = '';
            }
        };
        
        document.getElementById('takePhotoBtn').onclick = () => document.getElementById('cameraCaptureInput').click();
        document.getElementById('cameraCaptureInput').onchange = (e) => {
            if (e.target.files[0]) {
                const reader = new FileReader();
                reader.onload = (ev) => sendMessage('📷 Фото с камеры', 'image', ev.target.result);
                reader.readAsDataURL(e.target.files[0]);
                e.target.value = '';
            }
        };
        
        document.getElementById('videoFileBtn').onclick = () => document.getElementById('videoFileInput').click();
        document.getElementById('videoFileInput').onchange = (e) => {
            if (e.target.files[0] && e.target.files[0].size <= 100 * 1024 * 1024) {
                const reader = new FileReader();
                reader.onload = (ev) => sendMessage('🎥 Видео', 'video', ev.target.result);
                reader.readAsDataURL(e.target.files[0]);
                e.target.value = '';
            } else alert('Видео не более 100 МБ');
        };
        
        // Голосовые
        document.getElementById('voiceBtn').onclick = async () => {
            if (!hasMic) { alert('Сначала разрешите доступ к микрофону!'); return; }
            try {
                const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
                const mediaRecorder = new MediaRecorder(stream);
                let chunks = [];
                mediaRecorder.ondataavailable = e => chunks.push(e.data);
                mediaRecorder.onstop = () => {
                    const blob = new Blob(chunks, { type: 'audio/webm' });
                    const reader = new FileReader();
                    reader.onload = (e) => sendMessage('🎤 Голосовое', 'audio', e.target.result);
                    reader.readAsDataURL(blob);
                    stream.getTracks().forEach(t => t.stop());
                };
                mediaRecorder.start();
                const btn = document.getElementById('voiceBtn');
                btn.style.background = '#ef4444';
                btn.style.color = 'white';
                setTimeout(() => { if (mediaRecorder.state === 'recording') mediaRecorder.stop(); btn.style.background = ''; btn.style.color = ''; }, 15000);
            } catch(e) { alert('Нет доступа к микрофону'); }
        };
        
        // КРУЖКИ
        let circleRecorder = null, circleChunks = [], isCircleRecording = false, circleStream = null;
        const circleBtn = document.getElementById('circleVideoBtn');
        
        circleBtn.onclick = async () => {
            if (!hasCamera) { alert('Сначала разрешите доступ к камере!'); return; }
            if (!hasMic) { alert('Для кружка нужен микрофон!'); return; }
            
            if (isCircleRecording) {
                if (circleRecorder && circleRecorder.state === 'recording') circleRecorder.stop();
                isCircleRecording = false;
                circleBtn.classList.remove('recording');
                circleBtn.innerHTML = '<i class="fas fa-circle"></i>';
                if (circleStream) circleStream.getTracks().forEach(t => t.stop());
            } else {
                try {
                    const stream = await navigator.mediaDevices.getUserMedia({ video: true, audio: true });
                    circleStream = stream;
                    circleRecorder = new MediaRecorder(stream, { mimeType: 'video/webm' });
                    circleChunks = [];
                    circleRecorder.ondataavailable = (e) => { if (e.data.size > 0) circleChunks.push(e.data); };
                    circleRecorder.onstop = () => {
                        const blob = new Blob(circleChunks, { type: 'video/webm' });
                        const reader = new FileReader();
                        reader.onload = (e) => sendMessage('🟢 Кружок', 'video', e.target.result, true);
                        reader.readAsDataURL(blob);
                        if (circleStream) circleStream.getTracks().forEach(t => t.stop());
                        circleStream = null;
                    };
                    circleRecorder.start();
                    isCircleRecording = true;
                    circleBtn.classList.add('recording');
                    circleBtn.innerHTML = '<i class="fas fa-stop"></i>';
                    setTimeout(() => {
                        if (isCircleRecording && circleRecorder && circleRecorder.state === 'recording') {
                            circleRecorder.stop();
                            isCircleRecording = false;
                            circleBtn.classList.remove('recording');
                            circleBtn.innerHTML = '<i class="fas fa-circle"></i>';
                        }
                    }, 30000);
                } catch(e) { alert('Ошибка доступа к камере/микрофону'); }
            }
        };
        
        messagesRef.orderByChild('time').limitToLast(200).on('child_added', (snap) => {
            const msg = snap.val();
            if (!msg) return;
            const isMe = msg.userId === currentUserId;
            const div = document.createElement('div');
            div.className = `message ${isMe ? 'my-message' : 'other-message'}`;
            let media = '';
            if (msg.mediaType === 'image') media = `<img src="${msg.mediaUrl}" style="max-width:200px; border-radius:12px; margin-top:6px; cursor:pointer;" onclick="window.open('${msg.mediaUrl}','_blank')">`;
            else if (msg.mediaType === 'video') {
                if (msg.isCircle) media = `<div class="circle-video"><video controls playsinline style="width:100%; height:100%; border-radius:50%; object-fit:cover;"><source src="${msg.mediaUrl}"></video></div>`;
                else media = `<video controls style="max-width:200px; border-radius:12px; margin-top:6px;"><source src="${msg.mediaUrl}"></video>`;
            } else if (msg.mediaType === 'audio') media = `<audio controls src="${msg.mediaUrl}" style="margin-top:6px;"></audio>`;
            
            const avatarUrl = msg.avatar || `https://ui-avatars.com/api/?background=6b4eff&color=fff&name=${encodeURIComponent(msg.name)}`;
            const deleteBtn = isMe ? `<button class="delete-btn" onclick="deleteMessage('${snap.key}')"><i class="fas fa-trash"></i></button>` : '';
            
            div.innerHTML = `<div class="bubble">
                ${deleteBtn}
                <div class="message-header">${!isMe ? `<img class="msg-avatar" src="${avatarUrl}">` : ''}<span class="message-name">${escapeHtml(msg.name)}</span></div>
                ${msg.text && msg.text !== '🟢 Кружок' && msg.text !== '📷 Фото' && msg.text !== '🎥 Видео' ? `<div>${escapeHtml(msg.text)}</div>` : ''}
                ${media}
                <span class="message-time">${new Date(msg.time).toLocaleTimeString()}</span>
            </div>`;
            messagesArea.appendChild(div);
            messagesArea.scrollTop = messagesArea.scrollHeight;
        });
    }
    
    function escapeHtml(s) { if (!s) return ''; return s.replace(/[&<>]/g, m => ({ '&':'&amp;', '<':'&lt;', '>':'&gt;' }[m])); }
    
    function setupProfile() {
        document.getElementById('settingsBtn').onclick = () => document.getElementById('profileModal').style.display = 'flex';
        document.getElementById('closeModalBtn').onclick = () => document.getElementById('profileModal').style.display = 'none';
        document.getElementById('saveProfileBtn').onclick = async () => {
            let newName = document.getElementById('modalName').value.trim();
            if (!newName) newName = 'Аноним';
            currentUserName = newName;
            await db.ref('users/' + currentUserId).update({ name: newName });
            await db.ref('messages').orderByChild('userId').equalTo(currentUserId).once('value', snap => snap.forEach(s => s.ref.update({ name: newName })));
            const avatarFile = document.getElementById('modalAvatar').files[0];
            if (avatarFile && avatarFile.size <= 2 * 1024 * 1024) {
                const reader = new FileReader();
                reader.onload = async (e) => {
                    currentUserAvatar = e.target.result;
                    await db.ref('users/' + currentUserId).update({ avatar: currentUserAvatar });
                    await db.ref('messages').orderByChild('userId').equalTo(currentUserId).once('value', snap => snap.forEach(s => s.ref.update({ avatar: currentUserAvatar })));
                    document.getElementById('headerAvatar').src = currentUserAvatar;
                    document.getElementById('profileModal').style.display = 'none';
                };
                reader.readAsDataURL(avatarFile);
            } else {
                document.getElementById('headerAvatar').src = currentUserAvatar || `https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(currentUserName)}`;
                document.getElementById('profileModal').style.display = 'none';
            }
        };
    }
    
    function setupTheme() {
        if (localStorage.getItem('theme') === 'dark') document.body.classList.add('dark');
        document.getElementById('themeToggle').onclick = () => { document.body.classList.toggle('dark'); localStorage.setItem('theme', document.body.classList.contains('dark') ? 'dark' : 'light'); };
    }
    
    // ========== ЗВОНКИ (ПЕРЕПИСАНА ЛОГИКА) ==========
    const configuration = { iceServers: [{ urls: 'stun:stun.l.google.com:19302' }, { urls: 'stun:stun1.l.google.com:19302' }] };
    const callsRef = db.ref('calls');
    
    async function startCall() {
        if (isCallActive) { alert('Уже в звонке'); return; }
        if (!hasCamera || !hasMic) { alert('Сначала разрешите доступ к камере и микрофону!'); return; }
        
        addSystemMessage('📞 Начинаем звонок...', 'system-message');
        
        try {
            // Получаем локальный поток
            localStream = await navigator.mediaDevices.getUserMedia({ video: true, audio: true });
            document.getElementById('localVideo').srcObject = localStream;
            
            // Создаём PeerConnection
            peerConnection = new RTCPeerConnection(configuration);
            
            // Добавляем треки
            localStream.getTracks().forEach(track => {
                peerConnection.addTrack(track, localStream);
            });
            
            // Получаем удалённый поток
            peerConnection.ontrack = (event) => {
                document.getElementById('remoteVideo').srcObject = event.streams[0];
                addSystemMessage('📹 Удалённое видео подключено', 'success-message');
            };
            
            // Обработка ICE кандидатов
            peerConnection.onicecandidate = (event) => {
                if (event.candidate && currentCallId) {
                    db.ref('calls/' + currentCallId + '/candidates').push({
                        candidate: event.candidate,
                        from: currentUserId,
                        time: Date.now()
                    });
                }
            };
            
            // Обработка состояния подключения
            peerConnection.onconnectionstatechange = () => {
                addSystemMessage(`🔌 Состояние соединения: ${peerConnection.connectionState}`, 'system-message');
                if (peerConnection.connectionState === 'connected') {
                    addSystemMessage('✅ Звонок установлен!', 'success-message');
                } else if (peerConnection.connectionState === 'failed') {
                    addSystemMessage('❌ Соединение разорвано', 'error-message');
                    endCall();
                }
            };
            
            // Создаём offer
            const offer = await peerConnection.createOffer();
            await peerConnection.setLocalDescription(offer);
            
            // Создаём запись о звонке
            currentCallId = 'call_' + Date.now() + '_' + Math.random().toString(36).substr(2, 6);
            await callsRef.child(currentCallId).set({
                type: 'offer',
                offer: { sdp: offer.sdp, type: offer.type },
                from: currentUserId,
                fromName: currentUserName,
                timestamp: Date.now(),
                active: true
            });
            
            isCallActive = true;
            document.getElementById('callOverlay').style.display = 'flex';
            
            // Слушаем ответ
            const answerListener = callsRef.child(currentCallId).on('value', async (snap) => {
                const data = snap.val();
                if (data && data.type === 'answer' && peerConnection && peerConnection.signalingState !== 'stable') {
                    await peerConnection.setRemoteDescription(new RTCSessionDescription(data.answer));
                    addSystemMessage('📞 Ответ получен, соединение устанавливается...', 'system-message');
                }
                if (data && data.candidates) {
                    // Обработка кандидатов
                    const candidatesSnap = await callsRef.child(currentCallId + '/candidates').once('value');
                    if (candidatesSnap.exists()) {
                        candidatesSnap.forEach(async (candSnap) => {
                            const cand = candSnap.val();
                            if (cand.from !== currentUserId && peerConnection) {
                                try {
                                    await peerConnection.addIceCandidate(new RTCIceCandidate(cand.candidate));
                                } catch(e) {}
                            }
                        });
                    }
                }
                if (data && data.endCall) {
                    endCall();
                }
            });
            
            callListeners[currentCallId] = answerListener;
            
        } catch(e) {
            console.error('Start call error:', e);
            addSystemMessage('❌ Ошибка при начале звонка: ' + e.message, 'error-message');
        }
    }
    
    function listenForIncomingCalls() {
        callsRef.on('child_added', (snap) => {
            const call = snap.val();
            if (!call || call.from === currentUserId) return;
            if (call.active && !isCallActive && !call.rejected && !call.answered) {
                currentCallId = snap.key;
                document.getElementById('callerName').innerHTML = `📞 ${call.fromName} звонит...`;
                document.getElementById('incomingCallModal').style.display = 'block';
                
                window.answerCall = async (accept) => {
                    document.getElementById('incomingCallModal').style.display = 'none';
                    
                    if (!accept) {
                        await callsRef.child(currentCallId).update({ rejected: true, active: false });
                        return;
                    }
                    
                    if (!hasCamera || !hasMic) {
                        alert('Сначала разрешите доступ к камере и микрофону!');
                        return;
                    }
                    
                    addSystemMessage('📞 Принимаем звонок...', 'system-message');
                    
                    try {
                        localStream = await navigator.mediaDevices.getUserMedia({ video: true, audio: true });
                        document.getElementById('localVideo').srcObject = localStream;
                        
                        peerConnection = new RTCPeerConnection(configuration);
                        localStream.getTracks().forEach(track => peerConnection.addTrack(track, localStream));
                        
                        peerConnection.ontrack = (event) => {
                            document.getElementById('remoteVideo').srcObject = event.streams[0];
                        };
                        
                        peerConnection.onicecandidate = (event) => {
                            if (event.candidate && currentCallId) {
                                db.ref('calls/' + currentCallId + '/candidates').push({
                                    candidate: event.candidate,
                                    from: currentUserId,
                                    time: Date.now()
                                });
                            }
                        };
                        
                        await peerConnection.setRemoteDescription(new RTCSessionDescription(call.offer));
                        const answer = await peerConnection.createAnswer();
                        await peerConnection.setLocalDescription(answer);
                        await callsRef.child(currentCallId).update({
                            type: 'answer',
                            answer: { sdp: answer.sdp, type: answer.type },
                            answered: true
                        });
                        
                        isCallActive = true;
                        document.getElementById('callOverlay').style.display = 'flex';
                        
                        // Слушаем завершение
                        const endListener = callsRef.child(currentCallId).on('value', (snap) => {
                            if (snap.val() && snap.val().endCall) endCall();
                        });
                        callListeners[currentCallId] = endListener;
                        
                    } catch(e) {
                        console.error('Answer error:', e);
                        addSystemMessage('❌ Ошибка ответа: ' + e.message, 'error-message');
                    }
                };
            }
        });
    }
    
    function endCall() {
        if (peerConnection) {
            peerConnection.close();
            peerConnection = null;
        }
        if (localStream) {
            localStream.getTracks().forEach(t => t.stop());
            localStream = null;
        }
        if (currentCallId) {
            db.ref('calls/' + currentCallId).update({ endCall: true, active: false });
            if (callListeners[currentCallId]) {
                callsRef.child(currentCallId).off('value', callListeners[currentCallId]);
                delete callListeners[currentCallId];
            }
        }
        isCallActive = false;
        currentCallId = null;
        document.getElementById('callOverlay').style.display = 'none';
        document.getElementById('localVideo').srcObject = null;
        document.getElementById('remoteVideo').srcObject = null;
        addSystemMessage('📞 Звонок завершён', 'system-message');
    }
    
    function setupCallHandlers() {
        document.getElementById('callBtn').onclick = startCall;
        document.getElementById('endCallBtn').onclick = endCall;
        
        document.getElementById('toggleMuteBtn').onclick = () => {
            if (localStream) {
                isMuted = !isMuted;
                localStream.getAudioTracks().forEach(t => t.enabled = !isMuted);
                document.getElementById('toggleMuteBtn').innerHTML = isMuted ? '<i class="fas fa-microphone-slash"></i>' : '<i class="fas fa-microphone"></i>';
                document.getElementById('toggleMuteBtn').classList.toggle('active', isMuted);
            }
        };
        
        document.getElementById('toggleVideoBtn').onclick = () => {
            if (localStream) {
                isVideoOff = !isVideoOff;
                localStream.getVideoTracks().forEach(t => t.enabled = !isVideoOff);
                document.getElementById('toggleVideoBtn').innerHTML = isVideoOff ? '<i class="fas fa-video-slash"></i>' : '<i class="fas fa-video"></i>';
            }
        };
        
        document.getElementById('shareScreenBtn').onclick = async () => {
            if (!peerConnection) { alert('Сначала начните звонок'); return; }
            try {
                const screenStream = await navigator.mediaDevices.getDisplayMedia({ video: true });
                const videoTrack = screenStream.getVideoTracks()[0];
                const sender = peerConnection.getSenders().find(s => s.track && s.track.kind === 'video');
                if (sender) sender.replaceTrack(videoTrack);
                videoTrack.onended = async () => {
                    if (localStream) {
                        const newVideoTrack = localStream.getVideoTracks()[0];
                        if (sender && newVideoTrack) sender.replaceTrack(newVideoTrack);
                    }
                };
            } catch(e) { alert('Не удалось поделиться экраном'); }
        };
    }
    
    window.acceptCall = () => { if (window.answerCall) window.answerCall(true); };
    window.rejectCall = () => { if (window.answerCall) window.answerCall(false); };
    document.getElementById('acceptCallBtn').onclick = () => window.acceptCall();
    document.getElementById('rejectCallBtn').onclick = () => window.rejectCall();
    
    // ЗАПУСК
    (async () => {
        await cleanOldDataAndInit();
        await initUser();
        setupMessaging();
        setupCallHandlers();
        setupProfile();
        setupTheme();
        listenForIncomingCalls();
    })();
</script>
</body>
</html>
