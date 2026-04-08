<!DOCTYPE html>
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
            --call-overlay-bg: rgba(0,0,0,0.9);
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
        .chat-container { width: 100%; max-width: 900px; height: 92vh; background: var(--chat-bg); border-radius: 28px; display: flex; flex-direction: column; overflow: hidden; box-shadow: 0 20px 60px rgba(0,0,0,0.3); position: relative; }
        
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
        
        .circle-video { width: 180px; height: 180px; border-radius: 50%; object-fit: cover; margin-top: 6px; cursor: pointer; background: #000; }
        .circle-video video { width: 100%; height: 100%; border-radius: 50%; object-fit: cover; }
        
        .system-message { text-align: center; font-size: 0.7rem; background: var(--system-bg); color: var(--system-text); padding: 6px 12px; border-radius: 20px; margin: 4px auto; width: fit-content; }
        .error-message { background: #fee2e2; color: #dc2626; }
        .success-message { background: #dcfce7; color: #16a34a; }
        .warning-message { background: #fef3c7; color: #d97706; }
        
        .input-area { background: var(--input-bg); border-top: 1px solid var(--input-border); padding: 12px 16px; }
        .input-row { display: flex; gap: 8px; align-items: center; flex-wrap: wrap; }
        .message-input { flex: 1; padding: 12px 16px; border: 1px solid var(--input-border); border-radius: 25px; background: var(--input-bg); color: var(--input-text); outline: none; min-width: 120px; }
        .media-btn, .voice-btn, .camera-btn, .video-msg-btn { background: transparent; border: none; font-size: 1.3rem; cursor: pointer; color: var(--icon-color); width: 40px; height: 40px; border-radius: 50%; transition: 0.2s; }
        .video-msg-btn.recording { background: #ef4444; color: white; animation: pulse 1s infinite; }
        .send-btn { background: var(--icon-color); color: white; border: none; width: 46px; height: 46px; border-radius: 50%; cursor: pointer; }
        
        @keyframes pulse { 0% { transform: scale(1); opacity: 1; } 50% { transform: scale(1.1); opacity: 0.8; } 100% { transform: scale(1); opacity: 1; } }
        
        .call-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.95); z-index: 2000; display: none; flex-direction: column; justify-content: center; align-items: center; }
        .call-video-container { width: 100%; height: 80%; display: flex; flex-wrap: wrap; justify-content: center; align-items: center; gap: 20px; padding: 20px; }
        .local-video, .remote-video { width: 45%; height: 45%; background: #000; border-radius: 16px; object-fit: cover; }
        .call-controls { position: absolute; bottom: 30px; display: flex; justify-content: center; gap: 20px; background: rgba(0,0,0,0.6); padding: 15px; border-radius: 60px; }
        .call-control-btn { background: #334155; border: none; width: 55px; height: 55px; border-radius: 50%; cursor: pointer; color: white; font-size: 1.5rem; }
        .call-control-btn.end-call { background: #ef4444; }
        .incoming-call-modal { position: fixed; bottom: 100px; left: 20px; right: 20px; max-width: 350px; background: var(--chat-bg); border-radius: 20px; padding: 20px; z-index: 2100; box-shadow: 0 10px 40px rgba(0,0,0,0.3); display: none; }
        .modal { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.7); z-index: 1000; justify-content: center; align-items: center; }
        .modal-content { background: var(--chat-bg); border-radius: 24px; padding: 24px; width: 90%; max-width: 500px; }
        .modal-content input, .modal-content button { margin-top: 12px; width: 100%; padding: 12px; border-radius: 24px; }
        .modal-content button { background: var(--icon-color); color: white; border: none; cursor: pointer; }
        
        /* Кнопка запроса разрешений */
        .permission-request-btn { background: #10b981; color: white; border: none; padding: 10px 20px; border-radius: 30px; cursor: pointer; font-size: 0.9rem; margin-top: 10px; }
        .permission-status { display: inline-block; margin-left: 10px; font-size: 0.8rem; }
        
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
    // ========== ПРОВЕРКА БЕЗОПАСНОГО КОНТЕКСТА ==========
    const isSecureContext = location.protocol === 'https:' || location.hostname === 'localhost' || location.hostname === '127.0.0.1';
    const messagesArea = document.getElementById('messagesArea');
    
    function addSystemMessage(text, type = '') {
        const div = document.createElement('div');
        div.className = `system-message ${type}`;
        div.innerHTML = text;
        messagesArea.appendChild(div);
        messagesArea.scrollTop = messagesArea.scrollHeight;
    }
    
    if (!isSecureContext) {
        addSystemMessage('⚠️ <strong>ВНИМАНИЕ!</strong> Для работы камеры и микрофона страница должна открываться по <strong>HTTPS</strong> или <strong>localhost</strong>.<br>Сейчас используется ' + location.protocol + '// — камера НЕ БУДЕТ работать.<br><br>🔧 Решение: загрузите этот файл на HTTPS-хостинг (GitHub Pages, Netlify, Vercel) или используйте локальный сервер.', 'error-message');
        addSystemMessage('📌 <strong>Как временно обойти в Chrome:</strong><br>1. Откройте chrome://flags/#unsafely-treat-insecure-origin-as-secure<br>2. Включите флаг и добавьте ваш адрес в список разрешённых<br>3. Перезапустите Chrome', 'warning-message');
    } else {
        addSystemMessage('✅ <strong>Безопасный контекст!</strong> Камера и микрофон будут работать.', 'success-message');
    }
    
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
    let hasPermissions = false;
    
    // ========== ЗАПРОС РАЗРЕШЕНИЙ (ОБЯЗАТЕЛЬНО ПО КЛИКУ) ==========
    // ВАЖНО: getUserMedia() должен вызываться ТОЛЬКО по действию пользователя (клик) [citation:1][citation:5]
    async function requestPermissions() {
        addSystemMessage('🎥 Запрашиваем доступ к камере и микрофону...', 'system-message');
        
        try {
            // Запрашиваем оба устройства одновременно [citation:1]
            const stream = await navigator.mediaDevices.getUserMedia({ 
                video: true, 
                audio: true 
            });
            
            // Если получили поток — разрешения даны
            hasPermissions = true;
            
            // Сразу останавливаем треки, нам нужен был только запрос разрешений
            stream.getTracks().forEach(track => track.stop());
            
            addSystemMessage('✅ <strong>Доступ к камере и микрофону разрешён!</strong> Теперь можно звонить и записывать кружки.', 'success-message');
            
            // Сохраняем в localStorage что разрешения уже были
            localStorage.setItem('permissions_granted_' + currentFingerprint, 'true');
            
            return true;
        } catch (err) {
            console.error('Permission error:', err);
            let errorMsg = '';
            
            // Обрабатываем разные типы ошибок [citation:1][citation:5]
            switch(err.name) {
                case 'NotAllowedError':
                    errorMsg = '❌ Вы запретили доступ к камере/микрофону. Нажмите на иконку замка 🔒 в адресной строке и разрешите доступ для этого сайта, затем обновите страницу.';
                    break;
                case 'NotFoundError':
                    errorMsg = '❌ Камера или микрофон не найдены на вашем устройстве.';
                    break;
                case 'NotReadableError':
                    errorMsg = '❌ Камера/микрофон уже используются другим приложением (Zoom, Teams, Skype). Закройте их и попробуйте снова.';
                    break;
                case 'SecurityError':
                    errorMsg = '❌ Ошибка безопасности. Убедитесь что страница открыта по HTTPS.';
                    break;
                default:
                    errorMsg = '❌ Ошибка: ' + err.message;
            }
            
            addSystemMessage(errorMsg, 'error-message');
            
            // Инструкция по ручному разрешению [citation:4][citation:7]
            addSystemMessage('🔧 <strong>Как разрешить вручную:</strong><br>1. Нажмите на значок замка 🔒 в адресной строке<br>2. Найдите "Камера" и "Микрофон"<br>3. Выберите "Разрешить"<br>4. Обновите страницу', 'warning-message');
            
            return false;
        }
    }
    
    // ========== ОТДЕЛЬНЫЙ ЗАПРОС ТОЛЬКО МИКРОФОНА ==========
    async function requestMicrophoneOnly() {
        try {
            const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
            stream.getTracks().forEach(track => track.stop());
            return true;
        } catch(e) {
            addSystemMessage('❌ Нет доступа к микрофону. Разрешите доступ в настройках сайта.', 'error-message');
            return false;
        }
    }
    
    // ========== FINGERPRINT ==========
    function getFingerprint() { 
        return new Promise((resolve) => { 
            Fingerprint2.get((components) => { 
                const hash = Fingerprint2.x64hash128(components.map(c=>c.value).join(''), 31); 
                resolve(hash); 
            }); 
        }); 
    }
    
    // ========== ИНИЦИАЛИЗАЦИЯ ПОЛЬЗОВАТЕЛЯ ==========
    async function initUser() {
        const fp = await getFingerprint();
        currentFingerprint = fp;
        
        const adminSnap = await db.ref('admin').once('value');
        const usersRef = db.ref('users');
        const snap = await usersRef.orderByChild('fingerprint').equalTo(fp).once('value');
        
        if (snap.exists()) {
            snap.forEach(c => { 
                currentUserId = c.key; 
                currentUserName = c.val().name; 
                currentUserAvatar = c.val().avatar || ''; 
            });
        } else {
            if (!adminSnap.exists()) isAdmin = true;
            currentUserName = 'User_' + Math.floor(Math.random() * 10000);
            const newUser = usersRef.push();
            currentUserId = newUser.key;
            await newUser.set({ fingerprint: fp, name: currentUserName, avatar: '', createdAt: Date.now() });
            if (isAdmin) await db.ref('admin').set({ adminFingerprint: fp });
        }
        
        const adminData = await db.ref('admin').once('value');
        if (adminData.exists() && adminData.val().adminFingerprint === fp) isAdmin = true;
        if (isAdmin) document.getElementById('adminBadge').style.display = 'inline-block';
        
        document.getElementById('headerAvatar').src = currentUserAvatar || `https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(currentUserName)}`;
        document.getElementById('modalName').value = currentUserName;
        
        // Проверяем были ли уже разрешения
        const hadPermissions = localStorage.getItem('permissions_granted_' + fp);
        if (hadPermissions === 'true') {
            hasPermissions = true;
            addSystemMessage('✅ Разрешения на камеру и микрофон уже были выданы ранее.', 'success-message');
        } else if (isSecureContext) {
            // Добавляем КНОПКУ для запроса разрешений — только по клику! [citation:5]
            const requestBtn = document.createElement('button');
            requestBtn.className = 'permission-request-btn';
            requestBtn.innerHTML = '🎥 Нажмите, чтобы разрешить доступ к камере и микрофону';
            requestBtn.onclick = async () => {
                requestBtn.disabled = true;
                requestBtn.innerHTML = '⏳ Запрос разрешений...';
                await requestPermissions();
                requestBtn.remove();
            };
            messagesArea.appendChild(requestBtn);
            messagesArea.scrollTop = messagesArea.scrollHeight;
        }
    }
    
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
            const hasMic = await requestMicrophoneOnly();
            if (!hasMic) return;
            
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
            if (!hasPermissions) {
                alert('Сначала разрешите доступ к камере через кнопку выше!');
                return;
            }
            
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
                } catch(e) { alert('Нет доступа к камере'); }
            }
        };
        
        messagesRef.orderByChild('time').limitToLast(200).on('child_added', (snap) => {
            const msg = snap.val();
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
            div.innerHTML = `<div class="bubble"><div class="message-header">${!isMe ? `<img class="msg-avatar" src="${avatarUrl}">` : ''}<span class="message-name">${escapeHtml(msg.name)}</span></div>${msg.text && msg.text !== '🟢 Кружок' && msg.text !== '📷 Фото' && msg.text !== '🎥 Видео' ? `<div>${escapeHtml(msg.text)}</div>` : ''}${media}<span class="message-time">${new Date(msg.time).toLocaleTimeString()}</span></div>`;
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
    
    // ========== ЗВОНКИ ==========
    const configuration = { iceServers: [{ urls: 'stun:stun.l.google.com:19302' }, { urls: 'stun:stun1.l.google.com:19302' }] };
    const callsRef = db.ref('calls');
    let callListener = null;
    
    async function startCall(isVideoCall = true) {
        if (isCallActive) { alert('Уже в звонке'); return; }
        if (!hasPermissions) { alert('Сначала разрешите доступ к камере через кнопку выше!'); return; }
        
        try {
            localStream = await navigator.mediaDevices.getUserMedia({ video: isVideoCall, audio: true });
            document.getElementById('localVideo').srcObject = localStream;
            peerConnection = new RTCPeerConnection(configuration);
            localStream.getTracks().forEach(track => peerConnection.addTrack(track, localStream));
            peerConnection.ontrack = (event) => { document.getElementById('remoteVideo').srcObject = event.streams[0]; };
            peerConnection.onicecandidate = (event) => {
                if (event.candidate) db.ref('calls/' + currentCallId + '/candidate').push({ candidate: event.candidate, from: currentUserId });
            };
            const offer = await peerConnection.createOffer();
            await peerConnection.setLocalDescription(offer);
            currentCallId = 'call_' + Date.now();
            await callsRef.child(currentCallId).set({ type: 'offer', offer: { sdp: offer.sdp, type: offer.type }, from: currentUserId, fromName: currentUserName, timestamp: Date.now(), active: true });
            isCallActive = true;
            document.getElementById('callOverlay').style.display = 'flex';
            listenForCallResponse();
        } catch(e) { console.error(e); alert('Ошибка доступа к камере/микрофону'); }
    }
    
    function listenForCallResponse() {
        if (callListener) callListener.off();
        callListener = callsRef.child(currentCallId).on('value', async (snap) => {
            const data = snap.val();
            if (!data) return;
            if (data.type === 'answer' && peerConnection && peerConnection.signalingState !== 'stable') {
                await peerConnection.setRemoteDescription(new RTCSessionDescription(data.answer));
            }
            if (data.candidate && peerConnection) {
                await peerConnection.addIceCandidate(new RTCIceCandidate(data.candidate.candidate));
            }
            if (data.endCall) endCall();
        });
    }
    
    function listenForIncomingCalls() {
        db.ref('calls').orderByChild('timestamp').startAt(Date.now() - 60000).on('child_added', (snap) => {
            const call = snap.val();
            if (call.from !== currentUserId && call.active && !isCallActive && !call.rejected) {
                currentCallId = snap.key;
                document.getElementById('callerName').innerHTML = `📞 ${call.fromName} звонит...`;
                document.getElementById('incomingCallModal').style.display = 'block';
                window.answerCall = async (accept) => {
                    document.getElementById('incomingCallModal').style.display = 'none';
                    if (!accept) { await callsRef.child(currentCallId).update({ active: false, rejected: true }); return; }
                    if (!hasPermissions) { alert('Сначала разрешите доступ к камере!'); return; }
                    try {
                        localStream = await navigator.mediaDevices.getUserMedia({ video: true, audio: true });
                        document.getElementById('localVideo').srcObject = localStream;
                        peerConnection = new RTCPeerConnection(configuration);
                        localStream.getTracks().forEach(track => peerConnection.addTrack(track, localStream));
                        peerConnection.ontrack = (event) => { document.getElementById('remoteVideo').srcObject = event.streams[0]; };
                        peerConnection.onicecandidate = (event) => {
                            if (event.candidate) db.ref('calls/' + currentCallId + '/candidate').push({ candidate: event.candidate, from: currentUserId });
                        };
                        await peerConnection.setRemoteDescription(new RTCSessionDescription(call.offer));
                        const answer = await peerConnection.createAnswer();
                        await peerConnection.setLocalDescription(answer);
                        await callsRef.child(currentCallId).update({ type: 'answer', answer: { sdp: answer.sdp, type: answer.type } });
                        isCallActive = true;
                        document.getElementById('callOverlay').style.display = 'flex';
                        listenForCallResponse();
                    } catch(e) { console.error(e); alert('Ошибка ответа на звонок'); }
                };
            }
        });
    }
    
    function endCall() {
        if (peerConnection) peerConnection.close();
        if (localStream) localStream.getTracks().forEach(t => t.stop());
        if (currentCallId) db.ref('calls/' + currentCallId).update({ endCall: true, active: false });
        peerConnection = null; localStream = null; isCallActive = false; currentCallId = null;
        document.getElementById('callOverlay').style.display = 'none';
        document.getElementById('localVideo').srcObject = null;
        document.getElementById('remoteVideo').srcObject = null;
    }
    
    function setupCallHandlers() {
        document.getElementById('callBtn').onclick = () => startCall(true);
        document.getElementById('endCallBtn').onclick = endCall;
        document.getElementById('toggleMuteBtn').onclick = () => {
            if (localStream) {
                isMuted = !isMuted;
                localStream.getAudioTracks().forEach(t => t.enabled = !isMuted);
                document.getElementById('toggleMuteBtn').innerHTML = isMuted ? '<i class="fas fa-microphone-slash"></i>' : '<i class="fas fa-microphone"></i>';
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
    
    // Запуск
    initUser().then(() => {
        setupMessaging();
        setupCallHandlers();
        setupProfile();
        setupTheme();
        listenForIncomingCalls();
    });
</script>
</body>
</html>
