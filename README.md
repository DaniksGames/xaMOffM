<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>xaMOff Messenger | Полная версия</title>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-database-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, sans-serif; }
        
        :root {
            --bg-body: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            --chat-bg: #ffffff;
            --sidebar-bg: #f8fafc;
            --header-bg: linear-gradient(135deg, #4a6cf7, #6b4eff);
            --message-area-bg: #f8f9fc;
            --my-bubble: #4a6cf7;
            --my-text: #ffffff;
            --other-bubble: #ffffff;
            --other-text: #1e293b;
            --system-bubble: #e9ecef;
            --system-text: #64748b;
            --input-bg: #ffffff;
            --input-border: #e2e8f0;
            --icon-color: #4a6cf7;
            --read-color: #10b981;
            --online-color: #10b981;
            --offline-color: #94a3b8;
            --contact-hover: #e2e8f0;
            --active-chat: #dbeafe;
            --selected-message: rgba(74, 108, 247, 0.15);
            --safe-area-bottom: env(safe-area-inset-bottom, 0px);
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
            --system-bubble: #1e293b;
            --system-text: #94a3b8;
            --input-bg: #334155;
            --input-border: #475569;
            --icon-color: #60a5fa;
            --read-color: #34d399;
            --contact-hover: #334155;
            --active-chat: #1e293b;
            --selected-message: rgba(59, 130, 246, 0.2);
        }
        
        body { 
            background: var(--bg-body); 
            min-height: 100vh; 
            display: flex; 
            justify-content: center; 
            align-items: center; 
        }
        
        /* Drag & Drop оверлей */
        .drop-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(74, 108, 247, 0.9);
            display: none;
            justify-content: center;
            align-items: center;
            z-index: 9999;
            color: white;
            font-size: 2rem;
            flex-direction: column;
            gap: 20px;
            pointer-events: none;
        }
        
        .drop-overlay.visible {
            display: flex;
        }
        
        /* Плавные анимации для модалок */
        .modal, .auth-overlay, .admin-panel, .forward-modal, .read-by-modal {
            transition: opacity 0.25s ease, visibility 0.25s ease;
            opacity: 0;
            visibility: hidden;
        }
        .modal.visible, .auth-overlay.visible, .admin-panel.visible, .forward-modal.visible, .read-by-modal.visible {
            opacity: 1;
            visibility: visible;
        }
        
        .auth-card, .modal-content, .forward-content, .read-by-content, .admin-panel-content {
            transform: scale(0.95);
            transition: transform 0.25s cubic-bezier(0.2, 0.9, 0.4, 1.1);
        }
        .visible .auth-card, .visible .modal-content, .visible .forward-content, .visible .read-by-content, .visible .admin-panel-content {
            transform: scale(1);
        }
        
        @media (max-width: 700px) {
            body { padding: 0; }
            .app-container { 
                border-radius: 0 !important; 
                height: 100vh !important; 
                width: 100% !important;
                max-width: 100% !important;
            }
            .sidebar { 
                position: fixed !important; 
                left: -100% !important; 
                top: 0 !important; 
                height: 100vh !important; 
                width: 85% !important;
                max-width: 320px !important;
                z-index: 2000 !important; 
                transition: left 0.3s ease !important;
                box-shadow: 2px 0 10px rgba(0,0,0,0.1) !important;
            }
            .sidebar.open { left: 0 !important; }
            .chat-main { width: 100% !important; }
            .message { max-width: 90% !important; }
            .circle-video { width: 140px !important; height: 140px !important; }
            .menu-toggle { 
                display: flex !important; 
                align-items: center;
                justify-content: center;
            }
            .chat-header { padding: 8px 12px !important; }
            .chat-header-avatar { width: 38px !important; height: 38px !important; }
            .icon-btn { width: 36px !important; height: 36px !important; font-size: 0.9rem !important; }
            .chat-header-buttons { gap: 4px !important; }
            .input-area { 
                padding: 8px 12px !important; 
                padding-bottom: calc(8px + var(--safe-area-bottom)) !important;
            }
            .message-input { 
                padding: 12px 16px !important; 
                font-size: 16px !important;
                min-height: 50px !important;
                max-height: 150px !important;
            }
            .input-row { gap: 6px !important; }
            .send-btn { width: 50px !important; height: 50px !important; font-size: 1.3rem !important; }
            .attach-btn { width: 44px !important; height: 44px !important; font-size: 1.3rem !important; }
            .attach-menu { bottom: 70px !important; left: 10px !important; right: 10px !important; }
            .messages-area { padding: 12px !important; }
            .bubble { padding: 10px 12px !important; }
            .auth-card, .modal-content { 
                width: 90% !important; 
                padding: 24px 20px !important; 
                border-radius: 20px !important;
            }
            .selection-bar {
                flex-wrap: wrap !important;
                gap: 5px !important;
            }
            .contact-delete-btn {
                opacity: 1 !important;
                width: 28px !important;
                height: 28px !important;
            }
        }
        
        .app-container { 
            width: 100%; 
            max-width: 1300px; 
            height: 95vh; 
            background: var(--chat-bg); 
            border-radius: 24px; 
            display: flex; 
            overflow: hidden; 
            box-shadow: 0 20px 60px rgba(0,0,0,0.3); 
            transition: background 0.2s ease;
        }
        
        .sidebar { 
            width: 320px; 
            background: var(--sidebar-bg); 
            border-right: 1px solid var(--input-border); 
            display: flex; 
            flex-direction: column; 
        }
        .sidebar-header { background: var(--header-bg); color: white; padding: 16px; }
        .sidebar-header h2 { font-size: 1.2rem; display: flex; align-items: center; gap: 8px; }
        .user-info-row { display: flex; align-items: center; gap: 10px; margin-top: 12px; padding-top: 8px; border-top: 1px solid rgba(255,255,255,0.2); }
        .user-info-row img { width: 40px; height: 40px; border-radius: 50%; object-fit: cover; background: #4a6cf7; }
        .search-box { padding: 12px; border-bottom: 1px solid var(--input-border); }
        .search-box input { 
            width: 100%; 
            padding: 10px 14px; 
            border-radius: 20px; 
            border: 1px solid var(--input-border); 
            background: var(--input-bg); 
            color: var(--other-text); 
            outline: none; 
            font-size: 16px;
        }
        .contacts-list { flex: 1; overflow-y: auto; -webkit-overflow-scrolling: touch; }
        .contact-item { 
            display: flex; 
            align-items: center; 
            gap: 12px; 
            padding: 12px 16px; 
            cursor: pointer; 
            transition: background 0.15s ease; 
            border-bottom: 1px solid var(--input-border);
            position: relative;
        }
        .contact-item:active { background: var(--contact-hover); }
        .contact-item.active { background: var(--active-chat); border-left: 3px solid var(--icon-color); }
        .contact-avatar { width: 48px; height: 48px; border-radius: 50%; object-fit: cover; background: #4a6cf7; }
        .avatar-wrapper { position: relative; }
        .online-badge { 
            position: absolute; 
            bottom: 2px; 
            right: 2px; 
            width: 12px; 
            height: 12px; 
            border-radius: 50%; 
            border: 2px solid var(--sidebar-bg); 
        }
        .online-badge.online { background: var(--online-color); }
        .online-badge.offline { background: var(--offline-color); }
        .contact-info { flex: 1; min-width: 0; }
        .contact-name { font-weight: 600; color: var(--other-text); display: flex; align-items: center; gap: 8px; flex-wrap: wrap; }
        .contact-status { font-size: 0.7rem; opacity: 0.7; margin-top: 2px; }
        .unread-badge {
            background: #ef4444;
            color: white;
            border-radius: 20px;
            padding: 2px 8px;
            font-size: 0.7rem;
            font-weight: bold;
            margin-left: 6px;
        }
        .contact-delete-btn {
            background: rgba(239,68,68,0.1);
            border: none;
            color: #ef4444;
            width: 32px;
            height: 32px;
            border-radius: 50%;
            cursor: pointer;
            transition: all 0.15s;
            opacity: 0.6;
        }
        .contact-delete-btn:hover, .contact-delete-btn:active {
            opacity: 1;
            background: #ef4444;
            color: white;
        }
        
        .chat-main { flex: 1; display: flex; flex-direction: column; background: var(--chat-bg); }
        .chat-header { background: var(--header-bg); color: white; padding: 12px 16px; display: flex; align-items: center; gap: 12px; }
        .chat-header-avatar { width: 44px; height: 44px; border-radius: 50%; object-fit: cover; background: #4a6cf7; }
        .chat-header-info { flex: 1; min-width: 0; }
        .chat-header-name { font-weight: 600; font-size: 1.1rem; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
        .chat-header-status { font-size: 0.7rem; opacity: 0.8; }
        .chat-header-buttons { display: flex; gap: 6px; flex-wrap: wrap; }
        .icon-btn { 
            background: rgba(255,255,255,0.2); 
            border: none; 
            width: 38px; 
            height: 38px; 
            border-radius: 50%; 
            cursor: pointer; 
            color: white; 
            font-size: 1rem; 
            transition: transform 0.15s, background 0.15s;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .icon-btn:active { transform: scale(0.95); background: rgba(255,255,255,0.3); }
        
        .messages-area { 
            flex: 1; 
            overflow-y: auto; 
            padding: 16px; 
            display: flex; 
            flex-direction: column; 
            gap: 10px; 
            background: var(--message-area-bg);
            -webkit-overflow-scrolling: touch;
        }
        
        .message { 
            display: flex; 
            max-width: 80%; 
            animation: fadeIn 0.2s; 
            position: relative; 
            scroll-margin-top: 80px;
            transition: background 0.15s;
        }
        .message.selected { background: var(--selected-message); border-radius: 12px; }
        .my-message { align-self: flex-end; justify-content: flex-end; }
        .other-message { align-self: flex-start; }
        .system-message { align-self: center; max-width: 90%; }
        .bubble { padding: 8px 14px; border-radius: 20px; background: var(--other-bubble); color: var(--other-text); position: relative; word-break: break-word; }
        .system-message .bubble { background: var(--system-bubble); color: var(--system-text); text-align: center; font-size: 0.85rem; }
        .my-message .bubble { background: var(--my-bubble); color: var(--my-text); border-bottom-right-radius: 4px; }
        .delete-btn, .reply-btn, .forward-btn, .admin-delete-btn {
            position: absolute; top: -8px; width: 28px; height: 28px; border-radius: 50%; cursor: pointer; font-size: 11px; opacity: 0; transition: opacity 0.2s; z-index: 10; display: flex; align-items: center; justify-content: center;
        }
        .delete-btn { right: -8px; background: #ef4444; color:white; }
        .reply-btn { left: -8px; background: #8b5cf6; color:white; }
        .forward-btn { left: 24px; background: #10b981; color:white; }
        .admin-delete-btn { top: 20px; right: -8px; background: #ef4444; color:white; }
        @media (hover: hover) { .message:hover .delete-btn, .message:hover .reply-btn, .message:hover .forward-btn, .message:hover .admin-delete-btn { opacity: 1; } }
        @media (hover: none) { .delete-btn, .reply-btn, .forward-btn { opacity: 0; width: 24px; height: 24px; top: -4px; } .message:active .delete-btn, .message:active .reply-btn, .message:active .forward-btn { opacity: 1; } }
        
        .selection-bar { position: fixed; bottom: 20px; left: 50%; transform: translateX(-50%); background: var(--header-bg); color: white; padding: 10px 20px; border-radius: 40px; display: none; align-items: center; gap: 15px; box-shadow: 0 4px 20px rgba(0,0,0,0.3); z-index: 1500; }
        .selection-bar.visible { display: flex; }
        .input-area { padding: 12px 16px; background: var(--input-bg); border-top: 1px solid var(--input-border); position: relative; }
        .input-row { display: flex; gap: 8px; align-items: flex-end; }
        .message-input { flex: 1; padding: 12px 16px; border-radius: 25px; border: 1px solid var(--input-border); background: var(--input-bg); color: var(--other-text); outline: none; resize: none; font-size: 16px; max-height: 150px; min-height: 50px; }
        .send-btn { background: var(--icon-color); color: white; border: none; width: 50px; height: 50px; border-radius: 50%; cursor: pointer; font-size: 1.2rem; display: flex; align-items: center; justify-content: center; transition: transform 0.15s; }
        .attach-btn { background: transparent; border: none; font-size: 1.3rem; cursor: pointer; color: var(--icon-color); width: 44px; height: 44px; border-radius: 50%; display: flex; align-items: center; justify-content: center; }
        .attach-menu { position: absolute; bottom: 80px; left: 16px; background: var(--input-bg); border: 1px solid var(--input-border); border-radius: 16px; padding: 8px; display: none; grid-template-columns: repeat(3, 1fr); gap: 4px; z-index: 100; }
        .attach-menu.visible { display: grid; }
        .recording-indicator { position: fixed; bottom: 100px; left: 50%; transform: translateX(-50%); background: rgba(239,68,68,0.9); color: white; padding: 12px 24px; border-radius: 40px; display: none; align-items: center; gap: 10px; z-index: 1000; animation: pulse 1.5s infinite; }
        .recording-indicator.visible { display: flex; }
        .camera-preview { position: fixed; bottom: 100px; right: 20px; width: 120px; height: 160px; border-radius: 12px; overflow: hidden; box-shadow: 0 4px 15px rgba(0,0,0,0.3); z-index: 1000; border: 2px solid white; display: none; }
        .camera-preview.visible { display: block; }
        .camera-preview video { width: 100%; height: 100%; object-fit: cover; }
        .modal, .auth-overlay, .admin-panel, .forward-modal, .read-by-modal { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.85); z-index: 3000; display: flex; justify-content: center; align-items: center; }
        .auth-card, .modal-content, .forward-content, .read-by-content, .admin-panel-content { background: var(--chat-bg); border-radius: 28px; padding: 28px; width: 90%; max-width: 400px; text-align: center; color: var(--other-text); }
        .admin-panel-content { max-width: 800px; width: 95%; text-align: left; }
        .close-admin { position: fixed; top: 15px; right: 15px; background: #ef4444; padding: 12px 24px; border: none; border-radius: 30px; color: white; cursor: pointer; z-index: 3100; }
        .user-item { background: #1e293b; margin: 8px 0; padding: 14px; border-radius: 16px; display: flex; justify-content: space-between; flex-wrap: wrap; gap: 10px; color: white; align-items: center; }
        .menu-toggle { display: none; }
        .sidebar-overlay { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.5); z-index: 1999; }
        .sidebar-overlay.visible { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(8px); } to { opacity: 1; transform: translateY(0); } }
    </style>
</head>
<body>
<div class="drop-overlay" id="dropOverlay"><i class="fas fa-cloud-upload-alt"></i><span>Отпустите для загрузки</span></div>
<div class="sidebar-overlay" id="sidebarOverlay"></div>
<div id="recordingIndicator" class="recording-indicator"><i class="fas fa-microphone"></i><span id="recordingTimer">00:00</span><span>Идёт запись...</span></div>
<div id="cameraPreview" class="camera-preview"><video id="previewVideo" autoplay muted playsinline></video></div>
<div class="selection-bar" id="selectionBar"><span id="selectedCount">0 сообщений</span><button id="forwardSelectedBtn"><i class="fas fa-share"></i> Переслать</button><button id="deleteSelectedBtn" style="background:#ef4444;"><i class="fas fa-trash"></i> Удалить</button><button id="cancelSelectionBtn" class="cancel-selection"><i class="fas fa-times"></i> Отмена</button></div>
<div id="forwardModal" class="forward-modal"><div class="forward-content"><h3><i class="fas fa-share"></i> Переслать сообщения</h3><div id="forwardContactsList"></div><button class="forward-cancel" onclick="closeForwardModal()">Отмена</button></div></div>
<div id="readByModal" class="read-by-modal"><div class="read-by-content"><h3><i class="fas fa-check-circle"></i> Прочитали:</h3><div id="readByList"></div><button class="read-by-close" onclick="closeReadByModal()">Закрыть</button></div></div>
<div class="app-container" id="appContainer" style="display: none;">
    <div class="sidebar" id="sidebar"><div class="sidebar-header"><h2><i class="fas fa-comments"></i> xaMOff</h2><div class="user-info-row"><img id="sidebarAvatar" src=""><span id="sidebarName"></span></div></div><div class="search-box"><input type="text" id="searchInput" placeholder="🔍 Поиск по нику..."></div><div class="contacts-list" id="contactsList"></div></div>
    <div class="chat-main"><div class="chat-header"><button class="menu-toggle" id="menuToggle"><i class="fas fa-bars"></i></button><img id="chatHeaderAvatar" class="chat-header-avatar"><div class="chat-header-info"><div class="chat-header-name" id="chatHeaderName">Общий чат</div><div class="chat-header-status" id="chatHeaderStatus">Группа</div></div><div class="chat-header-buttons"><button class="icon-btn" id="settingsBtn"><i class="fas fa-user-cog"></i></button><button class="icon-btn" id="adminPanelBtn"><i class="fas fa-shield-alt"></i></button><button class="icon-btn" id="themeToggle"><i class="fas fa-moon"></i></button><button class="icon-btn" id="clearChatBtnHeader" style="background:#f59e0b; display:none;"><i class="fas fa-trash-alt"></i></button><button class="icon-btn" id="logoutBtn" style="background:#ef4444;"><i class="fas fa-sign-out-alt"></i></button></div></div>
        <div class="messages-area" id="messagesArea"><div class="messages-loading">Загрузка...</div></div>
        <div id="replyIndicator" class="reply-indicator" style="display:none;"><span><i class="fas fa-reply"></i> <span id="replyToName"></span>: "<span id="replyToText"></span>"</span><button id="cancelReplyBtn" style="background:none; border:none; color:#ef4444;">✕</button></div>
        <div id="mediaPreview" class="media-preview"><img id="previewImage" style="display:none;"><i id="previewFileIcon" class="fas fa-file" style="display:none;"></i><div class="media-preview-info"><div class="media-preview-name" id="previewName"></div><div class="media-preview-size" id="previewSize"></div><input type="text" id="mediaCaption" placeholder="Добавить подпись..."></div><button id="cancelMediaPreview" class="media-preview-cancel"><i class="fas fa-times"></i></button></div>
        <div class="input-area"><div class="attach-menu" id="attachMenu"><button class="attach-menu-btn" id="attachPhotoBtn"><i class="fas fa-image"></i><span>Фото</span></button><button class="attach-menu-btn" id="attachCameraBtn"><i class="fas fa-camera"></i><span>Снять</span></button><button class="attach-menu-btn" id="attachVideoBtn"><i class="fas fa-video"></i><span>Видео</span></button><button class="attach-menu-btn" id="attachFileBtn"><i class="fas fa-file"></i><span>Файл</span></button><button class="attach-menu-btn" id="attachCircleBtn"><i class="fas fa-circle"></i><span>Кружок</span></button><button class="attach-menu-btn" id="attachVoiceBtn"><i class="fas fa-microphone"></i><span>Голос</span></button></div><div class="input-row"><textarea id="messageInput" class="message-input" placeholder="Сообщение..." rows="1"></textarea><button class="attach-btn" id="attachBtn"><i class="fas fa-paperclip"></i></button><button class="send-btn" id="sendBtn"><i class="fas fa-paper-plane"></i></button></div></div>
    </div>
</div>
<div id="authOverlay" class="auth-overlay" style="display: flex;"><div class="auth-card"><h2>📱 xaMOff Messenger</h2><input type="text" id="loginName" placeholder="Никнейм"><input type="password" id="loginPassword" placeholder="Пароль"><div id="authError" style="color:red; font-size:0.8rem;"></div><button id="authBtn">Войти / Регистрация</button></div></div>
<div id="profileModal" class="modal"><div class="modal-content"><h3>Настройки профиля</h3><img id="avatarPreview" class="avatar-preview"><input type="file" id="modalAvatar" accept="image/*"><input type="text" id="modalName" placeholder="Новый никнейм"><button id="saveProfileBtn">💾 Сохранить</button><button id="closeModalBtn" style="background:#94a3b8;">Отмена</button></div></div>
<div id="adminPanel" class="admin-panel"><button class="close-admin" id="closeAdminBtn">✕ Закрыть</button><div class="admin-panel-content"><h3>👑 Админ-панель</h3><div class="system-notify-section"><h4><i class="fas fa-bullhorn"></i> Системные уведомления</h4><div class="preset-buttons"><button class="preset-btn update" onclick="sendSystemNotification('update')">Обновление</button><button class="preset-btn success" onclick="sendSystemNotification('success')">Успех</button><button class="preset-btn error" onclick="sendSystemNotification('error')">Ошибка</button></div><div class="custom-notify-input"><input type="text" id="customNotifyText" placeholder="Своё уведомление..."><button onclick="sendCustomNotification()">Отправить</button></div></div><button id="clearChatAdminBtn" style="background:#f59e0b; padding:12px; border-radius:16px; margin:10px 0; width:100%;">🗑️ ОЧИСТИТЬ ОБЩИЙ ЧАТ</button><div id="usersList"></div></div></div>
<input type="file" id="photoInput" accept="image/*" style="display:none"><input type="file" id="videoFileInput" accept="video/*" style="display:none"><input type="file" id="fileInput" accept="*/*" style="display:none"><input type="file" id="cameraCaptureInput" accept="image/*" capture="environment" style="display:none">
<script>
const firebaseConfig = { apiKey: "AIzaSyD4a16-r1nwCVDAu5_DBnirq0e8Lu9pBZw", authDomain: "daniksgames-d46b2.firebaseapp.com", databaseURL: "https://daniksgames-d46b2-default-rtdb.firebaseio.com", projectId: "daniksgames-d46b2", storageBucket: "daniksgames-d46b2.firebasestorage.app" };
firebase.initializeApp(firebaseConfig); const db = firebase.database();
(async () => { const snap = await db.ref('users').orderByChild('name').equalTo('DaniksGames').once('value'); if(snap.exists()) snap.forEach(c => c.ref.update({ password: 'Dan10102011' })); else await db.ref('users').push({ name: 'DaniksGames', password: 'Dan10102011', avatarUrl: '', blocked: false, createdAt: Date.now(), online: false, lastSeen: Date.now() }); })();

let currentUserId, currentUserName, currentUserAvatar, isBlocked = false, currentChat = { type: 'group', id: 'global', name: 'Общий чат' }, replyingTo = null, contacts = [], isAdmin = false, selectedMessages = new Set(), unreadCounts = {}, globalMsgListeners = [];

function escapeHtml(s) { return String(s || '').replace(/[&<>]/g, m => ({'&':'&amp;','<':'&lt;','>':'&gt;'}[m])); }
function formatFileSize(b) { if(b<1024) return b+' B'; if(b<1048576) return (b/1024).toFixed(1)+' KB'; return (b/1048576).toFixed(1)+' MB'; }
function playSound() { try { let a = new AudioContext?.(); if(a) { let o=a.createOscillator(), g=a.createGain(); o.connect(g); g.connect(a.destination); o.frequency.value=880; g.gain.value=0.1; o.start(); g.gain.exponentialRampToValueAtTime(0.00001, a.currentTime+0.3); o.stop(a.currentTime+0.3); a.resume(); } } catch(e){} }

async function getUserOnlineStatus(id) { let s=await db.ref('users/'+id).once('value'), u=s.val(); return u ? (u.online || (u.lastSeen && Date.now()-u.lastSeen<60000)) : false; }
async function updateAllOnlineStatuses() { for(let c of contacts) { if(!c.isGroup) { let on=await getUserOnlineStatus(c.id); let dot=document.getElementById(`online-${c.id}`), stat=document.getElementById(`status-${c.id}`); if(dot) dot.className=`online-badge ${on?'online':'offline'}`; if(stat) stat.innerText=on?'🟢 В сети':'⚫ Не в сети'; } } if(currentChat.type==='user') { let on=await getUserOnlineStatus(currentChat.id); document.getElementById('chatHeaderStatus').innerText=on?'🟢 В сети':'⚫ Не в сети'; } }

async function addContactIfNeeded(userId, userName, avatar) { if(userId===currentUserId) return; let exists=contacts.some(c=>c.id===userId); if(!exists) { let contactsRef=db.ref('users/'+currentUserId+'/contacts'); await contactsRef.push({ userId, name: userName, avatar: avatar||'', addedAt: Date.now() }); await loadContacts(); } }
async function loadContacts() { let snap=await db.ref('users/'+currentUserId+'/contacts').once('value'); contacts=[]; for(let key in snap.val()||{}) { let c=snap.val()[key]; let userSnap=await db.ref('users/'+c.userId).once('value'); if(userSnap.exists() && !userSnap.val().blocked) contacts.push({ id: c.userId, name: userSnap.val().name, avatar: userSnap.val().avatarUrl||'', contactKey: key }); } contacts.unshift({ id: 'global', name: '🌐 Общий чат', avatar: '', isGroup: true }); renderContacts(); }
function renderContacts() { let container=document.getElementById('contactsList'); container.innerHTML=contacts.map(c=>`<div class="contact-item ${currentChat.id===c.id?'active':''}" onclick="switchChat('${c.id}','${c.name.replace(/'/g,"\\'")}',${!!c.isGroup})"><div class="avatar-wrapper"><img class="contact-avatar" src="${c.avatar||`https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(c.name)}`}"><div class="online-badge offline" id="online-${c.id}"></div></div><div class="contact-info"><div class="contact-name">${escapeHtml(c.name)}${unreadCounts[c.id]?`<span class="unread-badge">${unreadCounts[c.id]}</span>`:''}</div><div class="contact-status" id="status-${c.id}">${c.isGroup?'👥 Группа':'🔄'}</div></div>${!c.isGroup?`<button class="contact-delete-btn" onclick="event.stopPropagation(); deleteContact('${c.id}','${c.contactKey}')"><i class="fas fa-trash"></i></button>`:''}</div>`).join(''); updateAllOnlineStatuses(); }
window.deleteContact=async(contactId,contactKey)=>{ if(confirm('Удалить контакт?')){ await db.ref('users/'+currentUserId+'/contacts/'+contactKey).remove(); await loadContacts(); if(currentChat.id===contactId) switchChat('global','🌐 Общий чат',true); } };
async function clearPrivateChat(userId){ let ids=[currentUserId,userId].sort(), path=`private_messages/${ids[0]}_${ids[1]}`; if(confirm(`Очистить чат с ${userId}?`)){ await db.ref(path).remove(); if(currentChat.id===userId) document.getElementById('messagesArea').innerHTML='<div class="no-messages">Чат очищен</div>'; } }

window.switchChat=async(chatId,chatName,isGroup)=>{ currentChat={type:isGroup?'group':'user',id:chatId,name:chatName}; document.getElementById('chatHeaderName').innerText=chatName; document.getElementById('chatHeaderStatus').innerText=isGroup?'👥 Групповой чат':(await getUserOnlineStatus(chatId)?'🟢 В сети':'⚫ Не в сети'); let contact=contacts.find(c=>c.id===chatId); document.getElementById('chatHeaderAvatar').src=contact?.avatar||`https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(chatName)}`; document.getElementById('clearChatBtnHeader').style.display=isGroup?'none':'flex'; replyingTo=null; document.getElementById('replyIndicator').style.display='none'; clearSelection(); renderContacts(); messagesLoaded=false; if(unreadCounts[chatId]){ unreadCounts[chatId]=0; renderContacts(); } loadMessages(); };
document.getElementById('clearChatBtnHeader').onclick=()=>{ if(currentChat.type==='user') clearPrivateChat(currentChat.id); };
function getChatPath(){ if(currentChat.type==='group') return 'group_messages'; let ids=[currentUserId,currentChat.id].sort(); return `private_messages/${ids[0]}_${ids[1]}`; }

async function sendMessageWithMedia(type,url,caption,fileData=null,isCircle=false){ if(isBlocked) return; let msg={ userId:currentUserId, name:currentUserName, avatar:currentUserAvatar||'', text:caption||'', mediaType:type, mediaUrl:url, time:Date.now(), read:false, delivered:false, readBy:{[currentUserId]:true} }; if(isCircle) msg.isCircle=true; if(fileData){ msg.fileName=fileData.name; msg.fileSize=fileData.size; } if(replyingTo){ msg.replyTo=replyingTo; replyingTo=null; document.getElementById('replyIndicator').style.display='none'; } await db.ref(getChatPath()).push(msg); playSound(); }
async function sendTextMessage(){ let input=document.getElementById('messageInput'), text=input.value.trim(); if(!text||isBlocked) return; let msg={ userId:currentUserId, name:currentUserName, avatar:currentUserAvatar||'', text, time:Date.now(), read:false, delivered:false, readBy:{[currentUserId]:true} }; if(replyingTo){ msg.replyTo=replyingTo; replyingTo=null; document.getElementById('replyIndicator').style.display='none'; } await db.ref(getChatPath()).push(msg); input.value=''; input.style.height='auto'; playSound(); }

let processedMsgIds=new Set(), messagesLoaded=false;
function loadMessages(){ let area=document.getElementById('messagesArea'); area.innerHTML='<div class="messages-loading">Загрузка...</div>'; let path=getChatPath(), ref=db.ref(path); ref.off(); processedMsgIds.clear(); let has=false; ref.orderByChild('time').limitToLast(100).on('child_added',snap=>{ if(!processedMsgIds.has(snap.key)){ processedMsgIds.add(snap.key); has=true; if(!messagesLoaded){ area.innerHTML=''; messagesLoaded=true; } renderMessage(snap.key,snap.val()); } }); setTimeout(()=>{ if(!has && !messagesLoaded) area.innerHTML='<div class="no-messages">Нет сообщений. Напишите первое!</div>'; messagesLoaded=true; },1500); ref.on('child_changed',snap=>updateMessageInUI(snap.key,snap.val())); ref.on('child_removed',snap=>removeMessageFromUI(snap.key)); }
function renderMessage(id,msg){ if(!msg) return; let isMe=msg.userId===currentUserId, isSystem=msg.isSystem||msg.userId==='system'; if(!isMe && !isSystem && currentChat.type==='user' && msg.userId!==currentUserId) addContactIfNeeded(msg.userId,msg.name,msg.avatar); let div=document.createElement('div'); div.className=`message ${isSystem?'system-message':(isMe?'my-message':'other-message')}`; div.id=`msg-${id}`; let replyHtml=msg.replyTo?`<div class="reply-preview" onclick="scrollToMessage('${msg.replyTo.messageId}')"><i class="fas fa-reply"></i> <strong>${escapeHtml(msg.replyTo.userName)}</strong>: ${escapeHtml(msg.replyTo.text)}</div>`:''; let mediaHtml=''; if(msg.mediaType==='image') mediaHtml=`<img src="${msg.mediaUrl}" class="media-content" onclick="window.open('${msg.mediaUrl}','_blank')">`; else if(msg.mediaType==='video') mediaHtml=msg.isCircle?`<div class="circle-video"><video controls playsinline><source src="${msg.mediaUrl}"></video></div>`:`<video controls class="video-content" playsinline><source src="${msg.mediaUrl}"></video>`; else if(msg.mediaType==='audio') mediaHtml=`<audio controls src="${msg.mediaUrl}"></audio>`; else if(msg.mediaType==='file') mediaHtml=`<div class="file-attachment" onclick="window.open('${msg.mediaUrl}','_blank')"><i class="fas fa-file"></i><div><div>${escapeHtml(msg.fileName||'Файл')}</div><div>${formatFileSize(msg.fileSize||0)}</div></div></div>`; let avatar=msg.avatar||`https://ui-avatars.com/api/?background=6b4eff&color=fff&name=${encodeURIComponent(msg.name)}`; let readByCount=msg.readBy?Object.keys(msg.readBy).length-1:0; let readByInfo=isMe&&readByCount>0?`<span class="read-by-info" onclick="showReadBy('${id}')">👁 ${readByCount}</span>`:''; let readStatus=''; if(isMe && currentChat.type==='user' && !isSystem) readStatus=msg.read?'<span class="read-status read">✓✓ Прочитано</span>':(msg.delivered?'<span class="read-status">✓✓ Доставлено</span>':'<span class="read-status">✓ Отправлено</span>'); let checkbox=`<input type="checkbox" class="message-checkbox" onchange="toggleMessageSelection('${id}', this.checked)">`; let delBtn=(isMe&&!isSystem)?`<button class="delete-btn" onclick="deleteMessage('${id}')"><i class="fas fa-trash"></i></button>`:''; let adminDel=(isAdmin&&!isMe&&!isSystem)?`<button class="admin-delete-btn" onclick="adminDeleteMessage('${id}')"><i class="fas fa-trash"></i></button>`:''; let replyBtn=!isSystem?`<button class="reply-btn" onclick="replyToMsg('${id}','${escapeHtml(msg.name).replace(/'/g,"\\'")}','${escapeHtml(msg.text||'Медиа').replace(/'/g,"\\'")}')"><i class="fas fa-reply"></i></button>`:''; let forwardBtn=!isSystem?`<button class="forward-btn" onclick="forwardSingleMessage('${id}')"><i class="fas fa-share"></i></button>`:''; if(isSystem) div.innerHTML=`<div class="bubble">${msg.text}<span class="message-time">${new Date(msg.time).toLocaleTimeString()}</span></div>`; else div.innerHTML=`<div class="bubble">${checkbox}${delBtn}${adminDel}${replyBtn}${forwardBtn}<div class="message-header"><img class="msg-avatar" src="${avatar}"><span class="message-name">${escapeHtml(msg.name)}</span></div>${replyHtml}${mediaHtml}${msg.text && !msg.mediaType?`<div>${escapeHtml(msg.text)}</div>`:''}<span class="message-time">${new Date(msg.time).toLocaleTimeString()} ${readStatus} ${readByInfo}</span></div>`; document.getElementById('messagesArea').appendChild(div); document.getElementById('messagesArea').scrollTop=document.getElementById('messagesArea').scrollHeight; if(!isMe && !msg.read && currentChat.type==='user' && !isSystem){ let updates={read:true}; if(!msg.readBy) msg.readBy={}; msg.readBy[currentUserId]=true; updates.readBy=msg.readBy; db.ref(`${getChatPath()}/${id}`).update(updates); } if(!isMe && document.hidden && Notification.permission==='granted' && !isSystem) new Notification(msg.name,{body:msg.text||'Новое сообщение',icon:avatar}); }
function updateMessageInUI(id,msg){ let el=document.getElementById(`msg-${id}`); if(el && msg.userId===currentUserId && currentChat.type==='user'){ let statusSpan=el.querySelector('.read-status'); if(statusSpan) statusSpan.innerHTML=msg.read?'✓✓ Прочитано':(msg.delivered?'✓✓ Доставлено':'✓ Отправлено'); let readByCount=msg.readBy?Object.keys(msg.readBy).length-1:0; let readBySpan=el.querySelector('.read-by-info'); if(readBySpan) readBySpan.innerHTML=`👁 ${readByCount}`; } }
function removeMessageFromUI(id){ let el=document.getElementById(`msg-${id}`); if(el) el.remove(); selectedMessages.delete(id); updateSelectionBar(); }
window.toggleMessageSelection=(id,ch)=>{ if(ch){ selectedMessages.add(id); document.getElementById(`msg-${id}`).classList.add('selected'); } else{ selectedMessages.delete(id); document.getElementById(`msg-${id}`).classList.remove('selected'); } updateSelectionBar(); };
function updateSelectionBar(){ let bar=document.getElementById('selectionBar'), cnt=selectedMessages.size; if(cnt>0){ bar.classList.add('visible'); document.getElementById('selectedCount').innerText=`${cnt} ${cnt%10===1&&cnt%100!==11?'сообщение':([2,3,4].includes(cnt%10)&&![12,13,14].includes(cnt%100)?'сообщения':'сообщений')}`; } else bar.classList.remove('visible'); }
function clearSelection(){ selectedMessages.forEach(id=>{ let el=document.getElementById(`msg-${id}`); if(el){ el.classList.remove('selected'); let cb=el.querySelector('.message-checkbox'); if(cb) cb.checked=false; } }); selectedMessages.clear(); updateSelectionBar(); }
window.deleteSelectedMessages=async()=>{ if(selectedMessages.size && confirm(`Удалить ${selectedMessages.size} сообщений?`)){ for(let id of selectedMessages) await db.ref(`${getChatPath()}/${id}`).remove(); clearSelection(); } };
window.forwardSelectedMessages=()=>{ if(selectedMessages.size) showForwardModal(Array.from(selectedMessages)); };
window.forwardSingleMessage=(id)=>showForwardModal([id]);
async function showForwardModal(msgIds){ await loadContacts(); let targets=contacts.filter(c=>c.id!==currentChat.id); let container=document.getElementById('forwardContactsList'); container.innerHTML=targets.map(t=>`<div class="forward-contact" onclick="executeForward(${JSON.stringify(msgIds)},'${t.id}','${t.name.replace(/'/g,"\\'")}',${!!t.isGroup})"><img src="${t.avatar||`https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(t.name)}`}"><div><div>${escapeHtml(t.name)}</div></div></div>`).join(''); document.getElementById('forwardModal').classList.add('visible'); }
window.executeForward=async(msgIds,targetId,targetName,isGroup)=>{ for(let mid of msgIds){ let snap=await db.ref(getChatPath()+'/'+mid).once('value'); let d=snap.val(); if(d){ let forwardData={...d, userId:currentUserId, name:currentUserName, avatar:currentUserAvatar, time:Date.now(), read:false, delivered:false, readBy:{[currentUserId]:true}, forwardedFrom:{chatId:currentChat.id,chatName:currentChat.name}}; let targetPath=isGroup?'group_messages':`private_messages/${[currentUserId,targetId].sort().join('_')}`; await db.ref(targetPath).push(forwardData); } } playSound(); closeForwardModal(); clearSelection(); alert(`Переслано в ${targetName}`); };
window.closeForwardModal=()=>document.getElementById('forwardModal').classList.remove('visible');
window.deleteMessage=async(id)=>{ if(confirm('Удалить?')) await db.ref(`${getChatPath()}/${id}`).remove(); };
window.adminDeleteMessage=async(id)=>{ if(isAdmin && confirm('Удалить как админ?')) await db.ref(`${getChatPath()}/${id}`).remove(); };
window.replyToMsg=(id,name,text)=>{ replyingTo={messageId:id,userName:name,text}; document.getElementById('replyIndicator').style.display='flex'; document.getElementById('replyToName').innerText=name; document.getElementById('replyToText').innerText=text.length>50?text.slice(0,50)+'...':text; };
window.showReadBy=async(id)=>{ let msg=(await db.ref(`${getChatPath()}/${id}`).once('value')).val(); if(msg?.readBy){ let users=Object.keys(msg.readBy).filter(uid=>uid!==currentUserId); let html=users.length?await Promise.all(users.map(async uid=>{ let u=(await db.ref('users/'+uid).once('value')).val(); return u?`<div><img src="${u.avatarUrl||`https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(u.name)}`}" width="36" height="36" style="border-radius:50%"><span>${escapeHtml(u.name)}</span></div>`:''; })).then(arr=>arr.join('')):'<p>Никто не прочитал</p>'; document.getElementById('readByList').innerHTML=html; document.getElementById('readByModal').classList.add('visible'); } };
window.closeReadByModal=()=>document.getElementById('readByModal').classList.remove('visible');
function setupDragDrop(){ let overlay=document.getElementById('dropOverlay'); document.addEventListener('dragover',e=>{e.preventDefault(); overlay.classList.add('visible');}); document.addEventListener('dragleave',()=>overlay.classList.remove('visible')); document.addEventListener('drop',async e=>{e.preventDefault(); overlay.classList.remove('visible'); if(e.dataTransfer.files.length) handleFiles(e.dataTransfer.files);}); document.addEventListener('paste',e=>{for(let item of e.clipboardData.items) if(item.type.indexOf('image')!==-1) handleFiles([item.getAsFile()]);}); }
function handleFiles(files){ for(let file of files){ let reader=new FileReader(); reader.onload=e=>{ let isImg=file.type.startsWith('image/'), isVid=file.type.startsWith('video/'); pendingMedia={data:e.target.result,type:isImg?'image':(isVid?'video':'file'),file}; showMediaPreview(file,isImg?e.target.result:null); }; reader.readAsDataURL(file); } }
let pendingMedia=null;
function showMediaPreview(file,previewUrl){ let p=document.getElementById('mediaPreview'); if(file.type.startsWith('image/')){ document.getElementById('previewImage').src=previewUrl; document.getElementById('previewImage').style.display='block'; document.getElementById('previewFileIcon').style.display='none'; }else{ document.getElementById('previewImage').style.display='none'; let icon=document.getElementById('previewFileIcon'); icon.className=file.type.startsWith('video/')?'fas fa-video':(file.type.startsWith('audio/')?'fas fa-music':'fas fa-file'); icon.style.display='block'; } document.getElementById('previewName').innerText=file.name; document.getElementById('previewSize').innerText=formatFileSize(file.size); document.getElementById('mediaCaption').value=''; p.classList.add('visible'); }
document.getElementById('cancelMediaPreview').onclick=()=>{ document.getElementById('mediaPreview').classList.remove('visible'); pendingMedia=null; };
async function startVoiceRecording(){ try{ let s=await navigator.mediaDevices.getUserMedia({audio:true}); voiceStream=s; voiceRecorder=new MediaRecorder(s); voiceChunks=[]; voiceRecorder.ondataavailable=e=>{if(e.data.size) voiceChunks.push(e.data);}; voiceRecorder.onstop=()=>{ let blob=new Blob(voiceChunks,{type:'audio/webm'}); let r=new FileReader(); r.onload=e=>sendMessageWithMedia('audio',e.target.result,''); r.readAsDataURL(blob); voiceStream?.getTracks().forEach(t=>t.stop()); isVoiceRecording=false; document.getElementById('recordingIndicator').classList.remove('visible'); stopRecordingTimer(); }; voiceRecorder.start(); isVoiceRecording=true; document.getElementById('recordingIndicator').classList.add('visible'); startRecordingTimer(); setTimeout(()=>{if(isVoiceRecording && voiceRecorder?.state==='recording') voiceRecorder.stop();},15000); }catch(e){alert('Нет микрофона');} }
let voiceRecorder,voiceChunks,isVoiceRecording,voiceStream,recordingTimerInterval,recordingSeconds;
function startRecordingTimer(){ recordingSeconds=0; document.getElementById('recordingTimer').innerText='00:00'; recordingTimerInterval=setInterval(()=>{ recordingSeconds++; let m=Math.floor(recordingSeconds/60), s=recordingSeconds%60; document.getElementById('recordingTimer').innerText=`${m.toString().padStart(2,'0')}:${s.toString().padStart(2,'0')}`; },1000); }
function stopRecordingTimer(){ if(recordingTimerInterval) clearInterval(recordingTimerInterval); }
function setupAttachments(){ let menu=document.getElementById('attachMenu'); document.getElementById('attachBtn').onclick=e=>{e.stopPropagation(); menu.classList.toggle('visible');}; document.addEventListener('click',e=>{if(!menu.contains(e.target)&&e.target!==document.getElementById('attachBtn')) menu.classList.remove('visible');}); document.getElementById('attachPhotoBtn').onclick=()=>{menu.classList.remove('visible'); document.getElementById('photoInput').click();}; document.getElementById('attachCameraBtn').onclick=()=>{menu.classList.remove('visible'); document.getElementById('cameraCaptureInput').click();}; document.getElementById('attachVideoBtn').onclick=()=>{menu.classList.remove('visible'); document.getElementById('videoFileInput').click();}; document.getElementById('attachFileBtn').onclick=()=>{menu.classList.remove('visible'); document.getElementById('fileInput').click();}; document.getElementById('attachCircleBtn').onclick=()=>{menu.classList.remove('visible'); startCircleRecording();}; document.getElementById('attachVoiceBtn').onclick=()=>{menu.classList.remove('visible'); startVoiceRecording();}; }
function startCircleRecording(){ if(window.circleRecorder?.state==='recording'){ circleRecorder.stop(); return; } navigator.mediaDevices.getUserMedia({video:{facingMode:"user"},audio:true}).then(stream=>{ circleStream=stream; document.getElementById('previewVideo').srcObject=stream; document.getElementById('cameraPreview').classList.add('visible'); circleRecorder=new MediaRecorder(stream,{mimeType:'video/webm'}); circleChunks=[]; circleRecorder.ondataavailable=e=>{if(e.data.size) circleChunks.push(e.data);}; circleRecorder.onstop=()=>{ let blob=new Blob(circleChunks,{type:'video/webm'}); let r=new FileReader(); r.onload=e=>sendMessageWithMedia('video',e.target.result,'',null,true); r.readAsDataURL(blob); circleStream?.getTracks().forEach(t=>t.stop()); document.getElementById('cameraPreview').classList.remove('visible'); document.getElementById('recordingIndicator').classList.remove('visible'); stopRecordingTimer(); }; circleRecorder.start(); document.getElementById('recordingIndicator').classList.add('visible'); document.querySelector('#recordingIndicator i').className='fas fa-video'; startRecordingTimer(); setTimeout(()=>{ if(circleRecorder?.state==='recording') circleRecorder.stop(); },30000); }).catch(()=>alert('Нет камеры')); }
function setupProfile(){ document.getElementById('settingsBtn').onclick=()=>{ document.getElementById('avatarPreview').src=currentUserAvatar||`https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(currentUserName)}`; document.getElementById('modalName').value=currentUserName; document.getElementById('profileModal').classList.add('visible'); }; document.getElementById('closeModalBtn').onclick=()=>document.getElementById('profileModal').classList.remove('visible'); document.getElementById('modalAvatar').onchange=e=>{if(e.target.files[0]){let r=new FileReader(); r.onload=ev=>document.getElementById('avatarPreview').src=ev.target.result; r.readAsDataURL(e.target.files[0]);}}; document.getElementById('saveProfileBtn').onclick=async()=>{ let newName=document.getElementById('modalName').value.trim(); let file=document.getElementById('modalAvatar').files[0]; if(newName && newName!==currentUserName){ let check=await db.ref('users').orderByChild('name').equalTo(newName).once('value'); let taken=false; check.forEach(c=>{if(c.key!==currentUserId) taken=true;}); if(taken){alert('Ник занят'); return;} } let updates={}; if(newName && newName!==currentUserName) updates.name=newName; if(file){ let reader=new FileReader(); let base64=await new Promise(r=>{reader.onload=e=>r(e.target.result); reader.readAsDataURL(file);}); updates.avatarUrl=base64; } if(Object.keys(updates).length){ await db.ref('users/'+currentUserId).update(updates); if(updates.name){ currentUserName=updates.name; localStorage.setItem('userName',updates.name); document.getElementById('sidebarName').innerText=updates.name; isAdmin=currentUserName==='DaniksGames'; } if(updates.avatarUrl) currentUserAvatar=updates.avatarUrl; let msgs=await db.ref('group_messages').orderByChild('userId').equalTo(currentUserId).once('value'); msgs.forEach(m=>m.ref.update(updates)); alert('Профиль обновлён'); } document.getElementById('profileModal').classList.remove('visible'); await loadContacts(); }; }
function setupAdmin(){ document.getElementById('adminPanelBtn').onclick=()=>{if(isAdmin){ loadAdminUsers(); document.getElementById('adminPanel').classList.add('visible'); }else alert('Только DaniksGames');}; document.getElementById('closeAdminBtn').onclick=()=>document.getElementById('adminPanel').classList.remove('visible'); document.getElementById('clearChatAdminBtn').onclick=async()=>{if(isAdmin && confirm('Очистить общий чат?')){ await db.ref('group_messages').remove(); alert('Чат очищен');}}; }
async function loadAdminUsers(){ let users=await db.ref('users').once('value'); let html='<h4>Пользователи:</h4>'; for(let id in users.val()){ let u=users.val()[id]; html+=`<div class="user-item"><div><strong>${escapeHtml(u.name)}</strong><br>🔑 ${escapeHtml(u.password)}<br>${u.blocked?'🔒 Заблокирован':'✅ Активен'} | ${u.online?'🟢 В сети':'⚫ Не в сети'}</div><div><button style="background:#8b5cf6;" onclick="adminEditUser('${id}','${escapeHtml(u.name).replace(/'/g,"\\'")}','${u.avatarUrl||''}')">✏️</button><button style="background:#f59e0b;" onclick="toggleBlock('${id}',${u.blocked||false})">${u.blocked?'🔓':'🔒'}</button><button style="background:#ef4444;" onclick="deleteAccount('${id}')">🗑️</button></div></div>`; } document.getElementById('usersList').innerHTML=html; }
window.adminEditUser=async(id,name,avatar)=>{ let newName=prompt('Новый ник',name); if(newName && newName!==name){ let check=await db.ref('users').orderByChild('name').equalTo(newName).once('value'); let exists=false; check.forEach(c=>{if(c.key!==id) exists=true;}); if(exists){alert('Занят'); return;} await db.ref('users/'+id).update({name:newName}); let msgs=await db.ref('group_messages').orderByChild('userId').equalTo(id).once('value'); msgs.forEach(m=>m.ref.update({name:newName})); alert('Ник обновлён'); } let newPass=prompt('Новый пароль'); if(newPass && newPass.trim()) await db.ref('users/'+id).update({password:newPass.trim()}); if(confirm('Сменить аватар?')){ let input=document.createElement('input'); input.type='file'; input.accept='image/*'; input.onchange=async(e)=>{ if(e.target.files[0]){ let r=new FileReader(); let base64=await new Promise(res=>{r.onload=ev=>res(ev.target.result); r.readAsDataURL(e.target.files[0]);}); await db.ref('users/'+id).update({avatarUrl:base64}); let msgs=await db.ref('group_messages').orderByChild('userId').equalTo(id).once('value'); msgs.forEach(m=>m.ref.update({avatar:base64})); alert('Аватар обновлён'); loadAdminUsers(); } }; input.click(); } loadAdminUsers(); };
window.toggleBlock=async(id,blocked)=> { await db.ref('users/'+id).update({blocked:!blocked}); loadAdminUsers(); };
window.deleteAccount=async(id)=>{ if(confirm('Удалить навсегда?')){ await db.ref('users/'+id).remove(); await db.ref('group_messages').orderByChild('userId').equalTo(id).once('value').then(s=>s.forEach(m=>m.ref.remove())); loadAdminUsers(); } };
function setupTheme(){ if(localStorage.getItem('theme')==='dark') document.body.classList.add('dark'); document.getElementById('themeToggle').onclick=()=>{ document.body.classList.toggle('dark'); localStorage.setItem('theme',document.body.classList.contains('dark')?'dark':'light');}; }
async function auth(){ let name=document.getElementById('loginName').value.trim(), pass=document.getElementById('loginPassword').value.trim(), err=document.getElementById('authError'); if(!name||!pass){err.innerText='Заполните поля';return;} let btn=document.getElementById('authBtn'); btn.disabled=true; btn.innerText='⏳...'; try{ let snap=await db.ref('users').orderByChild('name').equalTo(name).once('value'); if(snap.exists()){ let found=false; snap.forEach(c=>{if(c.val().password===pass){ currentUserId=c.key; currentUserName=c.val().name; currentUserAvatar=c.val().avatarUrl||null; isBlocked=c.val().blocked||false; found=true;}}); if(!found){err.innerText='Неверный пароль'; btn.disabled=false; btn.innerText='Войти'; return;} }else{ let newUser=db.ref('users').push(); currentUserId=newUser.key; await newUser.set({name,password:pass,avatarUrl:'',blocked:false,createdAt:Date.now(),lastSeen:Date.now(),online:true}); currentUserName=name; currentUserAvatar=null; isBlocked=false; } if(isBlocked){err.innerText='Вы заблокированы';return;} isAdmin=currentUserName==='DaniksGames'; await db.ref('users/'+currentUserId).update({online:true,lastSeen:Date.now()}); localStorage.setItem('userId',currentUserId); document.getElementById('authOverlay').classList.remove('visible'); document.getElementById('appContainer').style.display='flex'; document.getElementById('sidebarName').innerText=currentUserName; document.getElementById('sidebarAvatar').src=currentUserAvatar||`https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(currentUserName)}`; await initUser(); initAll(); }catch(e){err.innerText='Ошибка: '+e.message;}finally{btn.disabled=false; btn.innerText='Войти';} }
async function initUser(){ await loadContacts(); }
function initAll(){ loadContacts(); setupProfile(); setupTheme(); setupAdmin(); setupAttachments(); setupDragDrop(); document.getElementById('menuToggle').onclick=()=>{document.getElementById('sidebar').classList.add('open'); document.getElementById('sidebarOverlay').classList.add('visible');}; document.getElementById('sidebarOverlay').onclick=()=>{document.getElementById('sidebar').classList.remove('open'); document.getElementById('sidebarOverlay').classList.remove('visible');}; let textarea=document.getElementById('messageInput'); textarea.addEventListener('input',function(){this.style.height='auto'; this.style.height=Math.min(this.scrollHeight,150)+'px';}); document.getElementById('sendBtn').onclick=async()=>{ if(pendingMedia){ let cap=document.getElementById('mediaCaption').value; await sendMessageWithMedia(pendingMedia.type,pendingMedia.data,cap,{name:pendingMedia.file.name,size:pendingMedia.file.size,type:pendingMedia.file.type}); document.getElementById('mediaPreview').classList.remove('visible'); pendingMedia=null; }else sendTextMessage(); }; textarea.addEventListener('keydown',e=>{if(e.key==='Enter'&&!e.shiftKey){e.preventDefault(); document.getElementById('sendBtn').click();}}); document.getElementById('cancelReplyBtn').onclick=()=>{replyingTo=null; document.getElementById('replyIndicator').style.display='none';}; document.getElementById('searchInput').oninput=async()=>{ let q=document.getElementById('searchInput').value.trim().toLowerCase(); if(!q){loadContacts(); return;} let users=await db.ref('users').once('value'); let res=[]; for(let id in users.val()){ let u=users.val()[id]; if(u.name.toLowerCase().includes(q) && u.name!==currentUserName && !u.blocked) res.push({id,name:u.name,avatar:u.avatarUrl||''}); } let container=document.getElementById('contactsList'); container.innerHTML=res.map(u=>`<div class="contact-item" onclick="addContact('${u.id}','${u.name.replace(/'/g,"\\'")}')"><img class="contact-avatar" src="${u.avatar||`https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(u.name)}`}"><div><div>${escapeHtml(u.name)}</div><div>➕ Добавить</div></div></div>`).join(''); }; window.addContact=async(id,name)=>{ let ref=db.ref('users/'+currentUserId+'/contacts'); let exists=await ref.orderByChild('userId').equalTo(id).once('value'); if(!exists.exists()){ await ref.push({userId:id,name,addedAt:Date.now()}); await loadContacts(); alert(`✅ ${name} добавлен`); }else alert('Уже в контактах'); }; document.getElementById('logoutBtn').onclick=()=>{ db.ref('users/'+currentUserId).update({online:false,lastSeen:Date.now()}); localStorage.clear(); location.reload();}; document.getElementById('forwardSelectedBtn').onclick=forwardSelectedMessages; document.getElementById('deleteSelectedBtn').onclick=deleteSelectedMessages; document.getElementById('cancelSelectionBtn').onclick=clearSelection; switchChat('global','🌐 Общий чат',true); if(Notification.permission==='default') Notification.requestPermission(); setInterval(()=>{if(currentUserId) db.ref('users/'+currentUserId).update({online:true,lastSeen:Date.now()});},15000); setInterval(updateAllOnlineStatuses,5000); }
document.getElementById('authBtn').onclick=auth;
(async()=>{ let uid=localStorage.getItem('userId'); if(uid){ let snap=await db.ref('users/'+uid).once('value'); if(snap.exists() && !snap.val().blocked){ currentUserId=uid; currentUserName=snap.val().name; currentUserAvatar=snap.val().avatarUrl; isBlocked=snap.val().blocked||false; isAdmin=currentUserName==='DaniksGames'; await db.ref('users/'+uid).update({online:true,lastSeen:Date.now()}); document.getElementById('authOverlay').classList.remove('visible'); document.getElementById('appContainer').style.display='flex'; document.getElementById('sidebarName').innerText=currentUserName; document.getElementById('sidebarAvatar').src=currentUserAvatar||`https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(currentUserName)}`; await initUser(); initAll(); return; } } document.getElementById('authOverlay').classList.add('visible'); })();
</script>
</body>
</html>
