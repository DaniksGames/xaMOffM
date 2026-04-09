<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>xaMOff Messenger | Mobile</title>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-database-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
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
            --input-bg: #ffffff;
            --input-border: #e2e8f0;
            --icon-color: #4a6cf7;
            --read-color: #10b981;
            --online-color: #10b981;
            --offline-color: #94a3b8;
            --contact-hover: #e2e8f0;
            --active-chat: #dbeafe;
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
            --input-bg: #334155;
            --input-border: #475569;
            --icon-color: #60a5fa;
            --read-color: #34d399;
            --contact-hover: #334155;
            --active-chat: #1e293b;
        }
        
        body { 
            background: var(--bg-body); 
            min-height: 100vh; 
            display: flex; 
            justify-content: center; 
            align-items: center; 
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
                padding: 10px 12px !important; 
                font-size: 16px !important;
                min-height: 42px !important;
            }
            .media-btn, .voice-btn, .camera-btn, .video-msg-btn { 
                width: 38px !important; 
                height: 38px !important; 
                font-size: 1.1rem !important; 
            }
            .send-btn { width: 42px !important; height: 42px !important; }
            .messages-area { padding: 12px !important; }
            .bubble { padding: 10px 12px !important; }
            
            .auth-card, .modal-content { 
                width: 90% !important; 
                padding: 24px 20px !important; 
                border-radius: 20px !important;
            }
            .auth-card input, .modal-content input { 
                padding: 16px 14px !important; 
                font-size: 16px !important;
            }
            .auth-card button, .modal-content button { 
                padding: 16px !important; 
                font-size: 1rem !important;
            }
            
            .admin-panel { 
                padding: 16px !important; 
                padding-top: 60px !important;
            }
            .user-item { 
                flex-direction: column !important; 
                align-items: flex-start !important;
            }
            .user-item button { 
                margin: 4px 4px 0 0 !important; 
                padding: 8px 14px !important;
            }
            .close-admin { 
                position: fixed !important;
                top: 10px !important; 
                right: 10px !important; 
                padding: 8px 16px !important;
                z-index: 2600 !important;
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
            -webkit-tap-highlight-color: transparent;
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
        .contact-name { font-weight: 600; color: var(--other-text); white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
        .contact-status { font-size: 0.7rem; opacity: 0.7; margin-top: 2px; }
        
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
            -webkit-tap-highlight-color: transparent;
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
        
        .messages-loading {
            text-align: center;
            padding: 20px;
            color: var(--other-text);
            opacity: 0.7;
        }
        
        .no-messages {
            text-align: center;
            padding: 40px 20px;
            color: var(--other-text);
            opacity: 0.5;
        }
        
        .message { 
            display: flex; 
            max-width: 80%; 
            animation: fadeIn 0.2s; 
            position: relative; 
            scroll-margin-top: 80px; 
        }
        .my-message { align-self: flex-end; justify-content: flex-end; }
        .other-message { align-self: flex-start; }
        .bubble { 
            padding: 8px 14px; 
            border-radius: 20px; 
            background: var(--other-bubble); 
            color: var(--other-text); 
            position: relative; 
            word-break: break-word;
        }
        .my-message .bubble { background: var(--my-bubble); color: var(--my-text); border-bottom-right-radius: 4px; }
        .message-header { display: flex; align-items: center; gap: 8px; margin-bottom: 5px; font-size: 0.75rem; }
        .msg-avatar { width: 24px; height: 24px; border-radius: 50%; object-fit: cover; background: #6b4eff; }
        .message-name { font-weight: 600; }
        .reply-preview { 
            font-size: 0.7rem; 
            background: rgba(0,0,0,0.08); 
            padding: 6px 10px; 
            border-radius: 12px; 
            margin-bottom: 6px; 
            border-left: 3px solid var(--icon-color); 
            cursor: pointer; 
        }
        .media-content { max-width: 200px; max-height: 200px; border-radius: 12px; margin-top: 6px; cursor: pointer; }
        .video-content { max-width: 200px; max-height: 200px; border-radius: 12px; margin-top: 6px; }
        .circle-video { width: 180px; height: 180px; border-radius: 50%; overflow: hidden; margin-top: 6px; background: #000; }
        .circle-video video { width: 100%; height: 100%; object-fit: cover; }
        audio { max-width: 180px; height: 36px; margin-top: 6px; }
        .message-time { font-size: 0.55rem; opacity: 0.7; margin-top: 4px; display: block; }
        .read-status { font-size: 0.55rem; margin-left: 6px; }
        .read-status.read { color: var(--read-color); font-weight: bold; }
        
        .delete-btn, .reply-btn { 
            position: absolute; 
            top: -8px; 
            color: white; 
            border: none; 
            width: 28px; 
            height: 28px; 
            border-radius: 50%; 
            cursor: pointer; 
            font-size: 11px; 
            opacity: 0; 
            transition: opacity 0.2s; 
            z-index: 10; 
            display: flex;
            align-items: center;
            justify-content: center;
            -webkit-tap-highlight-color: transparent;
        }
        .delete-btn { right: -8px; background: #ef4444; }
        .reply-btn { left: -8px; background: #8b5cf6; }
        .message:hover .delete-btn, .message:hover .reply-btn { opacity: 1; }
        @media (hover: none) {
            .delete-btn, .reply-btn { opacity: 1; width: 24px; height: 24px; top: -4px; }
        }
        
        .admin-delete-btn {
            position: absolute;
            top: 20px;
            right: -8px;
            background: #ef4444;
            color: white;
            border: none;
            width: 24px;
            height: 24px;
            border-radius: 50%;
            cursor: pointer;
            font-size: 10px;
            opacity: 0;
            transition: opacity 0.2s;
            z-index: 10;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .message:hover .admin-delete-btn { opacity: 1; }
        @media (hover: none) {
            .admin-delete-btn { opacity: 1; }
        }
        
        .reply-indicator { 
            background: var(--input-bg); 
            padding: 8px 12px; 
            margin: 0 16px; 
            border-radius: 12px; 
            display: flex; 
            justify-content: space-between; 
            align-items: center;
            font-size: 0.8rem; 
            border-left: 3px solid var(--icon-color); 
        }
        .input-area { 
            padding: 12px 16px; 
            background: var(--input-bg); 
            border-top: 1px solid var(--input-border); 
        }
        .input-row { display: flex; gap: 8px; align-items: flex-end; flex-wrap: wrap; }
        .message-input { 
            flex: 1; 
            padding: 12px 16px; 
            border-radius: 25px; 
            border: 1px solid var(--input-border); 
            background: var(--input-bg); 
            color: var(--other-text); 
            outline: none; 
            resize: none; 
            font-size: 16px;
            max-height: 120px;
        }
        .media-btn, .voice-btn, .camera-btn, .video-msg-btn { 
            background: transparent; 
            border: none; 
            font-size: 1.3rem; 
            cursor: pointer; 
            color: var(--icon-color); 
            width: 44px; 
            height: 44px; 
            border-radius: 50%; 
            transition: all 0.15s; 
            display: flex;
            align-items: center;
            justify-content: center;
            -webkit-tap-highlight-color: transparent;
            flex-shrink: 0;
        }
        .media-btn:active, .voice-btn:active, .camera-btn:active, .video-msg-btn:active { transform: scale(0.9); background: rgba(74,108,247,0.1); }
        .video-msg-btn.recording { background: #ef4444; color: white; animation: pulse 1s infinite; }
        .send-btn { 
            background: var(--icon-color); 
            color: white; 
            border: none; 
            width: 48px; 
            height: 48px; 
            border-radius: 50%; 
            cursor: pointer; 
            font-size: 1.2rem; 
            display: flex;
            align-items: center;
            justify-content: center;
            -webkit-tap-highlight-color: transparent;
            transition: transform 0.15s, background 0.15s;
            flex-shrink: 0;
        }
        .send-btn:active { transform: scale(0.95); }
        
        @keyframes pulse { 0% { transform: scale(1); } 50% { transform: scale(1.1); } 100% { transform: scale(1); } }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(8px); } to { opacity: 1; transform: translateY(0); } }
        @keyframes highlight { 0% { background: rgba(74,108,247,0.5); } 100% { background: transparent; } }
        
        .system-message { text-align: center; font-size: 0.7rem; background: #e9ecef; padding: 6px 12px; border-radius: 20px; margin: 4px auto; width: fit-content; }
        .highlight-message { animation: highlight 1s; }
        
        .modal, .auth-overlay { 
            position: fixed; 
            top: 0; 
            left: 0; 
            width: 100%; 
            height: 100%; 
            background: rgba(0,0,0,0.95); 
            z-index: 3000; 
            display: none; 
            justify-content: center; 
            align-items: center; 
        }
        
        .admin-panel { 
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.95);
            z-index: 2500;
            display: none;
            flex-direction: column;
            overflow-y: auto;
            -webkit-overflow-scrolling: touch;
        }
        
        .admin-panel-content {
            padding: 20px;
            padding-top: 70px;
            max-width: 800px;
            margin: 0 auto;
            width: 100%;
        }
        
        .auth-card, .modal-content { 
            background: white; 
            border-radius: 28px; 
            padding: 28px; 
            width: 90%; 
            max-width: 400px; 
            text-align: center; 
        }
        .dark .auth-card, .dark .modal-content { background: #1e293b; color: white; }
        .dark .auth-card input, .dark .modal-content input { background: #334155; color: white; border-color: #475569; }
        .auth-card input, .modal-content input { 
            width: 100%; 
            padding: 14px; 
            margin: 12px 0; 
            border-radius: 30px; 
            border: 1px solid #ddd; 
            font-size: 16px;
            outline: none;
        }
        .auth-card button, .modal-content button { 
            background: #4a6cf7; 
            color: white; 
            border: none; 
            padding: 14px; 
            border-radius: 30px; 
            width: 100%; 
            font-size: 1rem; 
            cursor: pointer; 
            margin-top: 10px; 
            transition: opacity 0.2s;
        }
        .auth-card button:disabled, .modal-content button:disabled { opacity: 0.6; }
        .avatar-preview { width: 100px; height: 100px; border-radius: 50%; object-fit: cover; margin: 10px auto; display: block; background: #4a6cf7; }
        
        .admin-panel h3 { color: white; margin-bottom: 20px; }
        .user-item { 
            background: #1e293b; 
            margin: 8px 0; 
            padding: 14px; 
            border-radius: 16px; 
            display: flex; 
            justify-content: space-between; 
            flex-wrap: wrap; 
            gap: 10px; 
            color: white; 
            align-items: center; 
        }
        .user-item button { 
            padding: 8px 14px; 
            border-radius: 20px; 
            border: none; 
            cursor: pointer; 
            margin-left: 5px; 
            font-size: 0.85rem;
            -webkit-tap-highlight-color: transparent;
        }
        .close-admin { 
            position: fixed;
            top: 15px;
            right: 15px;
            background: #ef4444; 
            padding: 12px 24px; 
            border: none; 
            border-radius: 30px; 
            color: white; 
            cursor: pointer; 
            z-index: 2600;
            font-size: 1rem;
        }
        
        .menu-toggle { 
            display: none; 
            background: rgba(255,255,255,0.2); 
            border: none; 
            width: 40px; 
            height: 40px; 
            border-radius: 50%; 
            color: white; 
            font-size: 1.2rem; 
            cursor: pointer; 
            -webkit-tap-highlight-color: transparent;
        }
        
        .sidebar-overlay {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.5);
            z-index: 1999;
        }
        .sidebar-overlay.visible { display: block; }
        @media (min-width: 701px) {
            .sidebar-overlay { display: none !important; }
        }
    </style>
</head>
<body>

<div class="sidebar-overlay" id="sidebarOverlay"></div>

<div class="app-container" id="appContainer" style="display: none;">
    <div class="sidebar" id="sidebar">
        <div class="sidebar-header">
            <h2><i class="fas fa-comments"></i> xaMOff</h2>
            <div class="user-info-row">
                <img id="sidebarAvatar" src="" alt="Avatar">
                <span id="sidebarName"></span>
            </div>
        </div>
        <div class="search-box">
            <input type="text" id="searchInput" placeholder="🔍 Поиск по нику..." autocomplete="off">
        </div>
        <div class="contacts-list" id="contactsList"></div>
    </div>
    
    <div class="chat-main">
        <div class="chat-header">
            <button class="menu-toggle" id="menuToggle"><i class="fas fa-bars"></i></button>
            <img id="chatHeaderAvatar" class="chat-header-avatar" src="" alt="Avatar">
            <div class="chat-header-info">
                <div class="chat-header-name" id="chatHeaderName">Общий чат</div>
                <div class="chat-header-status" id="chatHeaderStatus">Группа</div>
            </div>
            <div class="chat-header-buttons">
                <button class="icon-btn" id="settingsBtn"><i class="fas fa-user-cog"></i></button>
                <button class="icon-btn" id="adminPanelBtn"><i class="fas fa-shield-alt"></i></button>
                <button class="icon-btn" id="themeToggle"><i class="fas fa-moon"></i></button>
                <button class="icon-btn" id="logoutBtn" style="background:#ef4444;"><i class="fas fa-sign-out-alt"></i></button>
            </div>
        </div>
        <div class="messages-area" id="messagesArea">
            <div class="messages-loading">Загрузка сообщений...</div>
        </div>
        <div id="replyIndicator" class="reply-indicator" style="display: none;">
            <span style="flex:1;"><i class="fas fa-reply"></i> <span id="replyToName"></span>: "<span id="replyToText"></span>"</span>
            <button id="cancelReplyBtn" style="background:none; border:none; color:#ef4444; cursor:pointer; font-size:1.2rem;">✕</button>
        </div>
        <div class="input-area">
            <div class="input-row">
                <textarea id="messageInput" class="message-input" placeholder="Сообщение..." rows="1"></textarea>
                <button class="camera-btn" id="photoBtn" title="Фото"><i class="fas fa-image"></i></button>
                <button class="camera-btn" id="takePhotoBtn" title="Снять"><i class="fas fa-camera"></i></button>
                <button class="media-btn" id="videoFileBtn" title="Видео"><i class="fas fa-video"></i></button>
                <button class="video-msg-btn" id="circleVideoBtn" title="Кружок"><i class="fas fa-circle"></i></button>
                <button class="voice-btn" id="voiceBtn" title="Голосовое"><i class="fas fa-microphone"></i></button>
                <button class="send-btn" id="sendBtn"><i class="fas fa-paper-plane"></i></button>
            </div>
        </div>
    </div>
</div>

<div id="authOverlay" class="auth-overlay" style="display: flex;">
    <div class="auth-card">
        <h2>📱 xaMOff Messenger</h2>
        <input type="text" id="loginName" placeholder="Никнейм" autocomplete="username">
        <input type="password" id="loginPassword" placeholder="Пароль" autocomplete="current-password">
        <div id="authError" style="color:red; font-size:0.8rem; margin:5px 0;"></div>
        <button id="authBtn">Войти / Регистрация</button>
    </div>
</div>

<div id="profileModal" class="modal">
    <div class="modal-content">
        <h3>Настройки профиля</h3>
        <img id="avatarPreview" class="avatar-preview" src="" alt="Preview">
        <input type="file" id="modalAvatar" accept="image/*">
        <input type="text" id="modalName" placeholder="Новый никнейм" autocomplete="off">
        <button id="saveProfileBtn">💾 Сохранить</button>
        <button id="closeModalBtn" style="background:#94a3b8;">Отмена</button>
    </div>
</div>

<div id="adminPanel" class="admin-panel">
    <button class="close-admin" id="closeAdminBtn">✕ Закрыть</button>
    <div class="admin-panel-content">
        <h3>👑 Админ-панель</h3>
        <button id="clearChatBtn" style="background:#f59e0b; padding:12px; border:none; border-radius:16px; margin:10px 0; cursor:pointer; width:100%; font-size:1rem;">🗑️ ОЧИСТИТЬ ОБЩИЙ ЧАТ</button>
        <div id="usersList"></div>
    </div>
</div>

<input type="file" id="photoInput" accept="image/*" style="display:none">
<input type="file" id="videoFileInput" accept="video/*" style="display:none">
<input type="file" id="cameraCaptureInput" accept="image/*" capture="environment" style="display:none">

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
    
    (async () => {
        const snap = await db.ref('users').orderByChild('name').equalTo('DaniksGames').once('value');
        if(snap.exists()) snap.forEach(c => c.ref.update({ password: 'Dan10102011' }));
        else await db.ref('users').push({ name: 'DaniksGames', password: 'Dan10102011', avatarUrl: '', blocked: false, createdAt: Date.now(), online: false, lastSeen: Date.now() });
    })();
    
    let currentUserId = null, currentUserName = null, currentUserAvatar = null, isBlocked = false;
    let currentChat = { type: 'group', id: 'global', name: 'Общий чат' };
    let replyingTo = null;
    let contacts = [];
    let audioCtx = null;
    let voiceRecorder = null, voiceChunks = [], isVoiceRecording = false, voiceStream = null;
    let circleRecorder = null, circleChunks = [], isCircleRecording = false, circleStream = null;
    let processedMsgIds = new Set();
    let onlineStatusInterval = null;
    let isAdmin = false;
    let messagesLoaded = false;
    
    function playSound() { 
        try { 
            if(!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)(); 
            if(audioCtx.state === 'suspended') audioCtx.resume();
            const o = audioCtx.createOscillator(), g = audioCtx.createGain(); 
            o.connect(g); g.connect(audioCtx.destination); 
            o.frequency.value = 880; g.gain.value = 0.15; 
            o.start(); 
            g.gain.exponentialRampToValueAtTime(0.00001, audioCtx.currentTime + 0.3); 
            o.stop(audioCtx.currentTime + 0.3); 
        } catch(e){} 
    }
    
    function updateOnlineStatus() { 
        if(currentUserId) {
            db.ref('users/' + currentUserId).update({ lastSeen: Date.now(), online: true });
        }
    }
    
    window.addEventListener('beforeunload', () => {
        if(currentUserId) {
            db.ref('users/' + currentUserId).update({ online: false, lastSeen: Date.now() });
        }
    });
    
    async function getUserOnlineStatus(userId) {
        const snap = await db.ref('users/' + userId).once('value');
        const user = snap.val();
        if(!user) return false;
        if(user.online) return true;
        if(user.lastSeen && (Date.now() - user.lastSeen) < 60000) return true;
        return false;
    }
    
    async function updateAllOnlineStatuses() {
        for(let contact of contacts) {
            if(!contact.isGroup) {
                const isOnline = await getUserOnlineStatus(contact.id);
                const dot = document.getElementById(`online-${contact.id}`);
                const statusSpan = document.getElementById(`status-${contact.id}`);
                if(dot) dot.className = `online-badge ${isOnline ? 'online' : 'offline'}`;
                if(statusSpan) statusSpan.innerText = isOnline ? '🟢 В сети' : '⚫ Не в сети';
            }
        }
        if(currentChat.type === 'user') {
            const isOnline = await getUserOnlineStatus(currentChat.id);
            document.getElementById('chatHeaderStatus').innerText = isOnline ? '🟢 В сети' : '⚫ Не в сети';
        }
    }
    
    async function auth() {
        const name = document.getElementById('loginName').value.trim();
        const password = document.getElementById('loginPassword').value.trim();
        const authError = document.getElementById('authError');
        const authBtn = document.getElementById('authBtn');
        
        if(!name || !password) { authError.innerText = 'Заполните поля'; return; }
        
        authBtn.disabled = true;
        authBtn.innerText = '⏳ Проверка...';
        
        try {
            const usersRef = db.ref('users');
            const snap = await usersRef.orderByChild('name').equalTo(name).once('value');
            if(snap.exists()) {
                let found = false;
                snap.forEach(c => { 
                    if(c.val().password === password) { 
                        currentUserId = c.key; 
                        currentUserName = c.val().name; 
                        currentUserAvatar = c.val().avatarUrl || null;
                        isBlocked = c.val().blocked || false;
                        found = true; 
                    } 
                });
                if(!found) { 
                    authError.innerText = 'Неверный пароль'; 
                    authBtn.disabled = false;
                    authBtn.innerText = 'Войти / Регистрация';
                    return; 
                }
            } else {
                const newUser = usersRef.push();
                currentUserId = newUser.key;
                await newUser.set({ name, password, avatarUrl: '', blocked: false, createdAt: Date.now(), lastSeen: Date.now(), online: true });
                currentUserName = name; 
                currentUserAvatar = null;
                isBlocked = false;
            }
            
            if(isBlocked) {
                authError.innerText = 'Вы заблокированы';
                authBtn.disabled = false;
                authBtn.innerText = 'Войти / Регистрация';
                return;
            }
            
            isAdmin = currentUserName === 'DaniksGames';
            
            await db.ref('users/' + currentUserId).update({ online: true, lastSeen: Date.now() });
            
            localStorage.setItem('userId', currentUserId);
            localStorage.setItem('userName', currentUserName);
            document.getElementById('authOverlay').style.display = 'none';
            document.getElementById('appContainer').style.display = 'flex';
            document.getElementById('sidebarName').innerText = currentUserName;
            document.getElementById('sidebarAvatar').src = currentUserAvatar || `https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(currentUserName)}`;
            await initUser();
            initAll();
        } catch(e) {
            authError.innerText = 'Ошибка: ' + e.message;
            authBtn.disabled = false;
            authBtn.innerText = 'Войти / Регистрация';
        }
    }
    
    async function initUser() {
        const contactsRef = db.ref('users/' + currentUserId + '/contacts');
        const danikSnap = await db.ref('users').orderByChild('name').equalTo('DaniksGames').once('value');
        if(danikSnap.exists()) {
            danikSnap.forEach(async (d) => {
                const exists = await contactsRef.orderByChild('userId').equalTo(d.key).once('value');
                if(!exists.exists()) await contactsRef.push({ userId: d.key, name: d.val().name, avatar: d.val().avatarUrl || '', addedAt: Date.now() });
            });
        }
    }
    
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
        contacts.unshift({ id: 'global', name: '🌐 Общий чат', avatar: '', isGroup: true });
        renderContacts();
    }
    
    function renderContacts() {
        const container = document.getElementById('contactsList');
        container.innerHTML = contacts.map(c => `
            <div class="contact-item ${currentChat.id === c.id ? 'active' : ''}" onclick="switchChat('${c.id}', '${c.name.replace(/'/g, "\\'")}', ${c.isGroup ? 'true' : 'false'})">
                <div class="avatar-wrapper">
                    <img class="contact-avatar" src="${c.avatar || `https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(c.name)}`}" alt="${c.name}">
                    <div class="online-badge offline" id="online-${c.id}"></div>
                </div>
                <div class="contact-info">
                    <div class="contact-name">${escapeHtml(c.name)}</div>
                    <div class="contact-status" id="status-${c.id}">${c.isGroup ? '👥 Группа' : '🔄 Загрузка...'}</div>
                </div>
            </div>
        `).join('');
        updateAllOnlineStatuses();
    }
    
    window.switchChat = async (chatId, chatName, isGroup) => {
        currentChat = { type: isGroup ? 'group' : 'user', id: chatId, name: chatName };
        document.getElementById('chatHeaderName').innerText = chatName;
        if(isGroup) {
            document.getElementById('chatHeaderStatus').innerText = '👥 Групповой чат';
        } else {
            const isOnline = await getUserOnlineStatus(chatId);
            document.getElementById('chatHeaderStatus').innerHTML = isOnline ? '🟢 В сети' : '⚫ Не в сети';
        }
        const contact = contacts.find(c => c.id === chatId);
        const avatar = contact?.avatar || `https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(chatName)}`;
        document.getElementById('chatHeaderAvatar').src = avatar;
        replyingTo = null;
        document.getElementById('replyIndicator').style.display = 'none';
        closeSidebar();
        renderContacts();
        messagesLoaded = false;
        loadMessages();
    };
    
    function getChatPath() {
        if(currentChat.type === 'group') return 'group_messages';
        const ids = [currentUserId, currentChat.id].sort();
        return `private_messages/${ids[0]}_${ids[1]}`;
    }
    
    async function sendMediaMessage(mediaType, mediaUrl, isCircle = false, text = null) {
        if(isBlocked) return alert('Вы заблокированы');
        const msgData = { 
            userId: currentUserId, name: currentUserName, avatar: currentUserAvatar || '', 
            text: text || (mediaType === 'image' ? '📷 Фото' : mediaType === 'video' ? (isCircle ? '🟢 Кружок' : '🎥 Видео') : '🎤 Голосовое'), 
            mediaType, mediaUrl, isCircle, time: Date.now(), read: false, delivered: false 
        };
        if(replyingTo) { msgData.replyTo = replyingTo; replyingTo = null; document.getElementById('replyIndicator').style.display = 'none'; }
        await db.ref(getChatPath()).push(msgData);
        playSound();
    }
    
    async function sendTextMessage() {
        const input = document.getElementById('messageInput');
        const text = input.value.trim();
        if(!text || isBlocked) return;
        const msgData = { userId: currentUserId, name: currentUserName, avatar: currentUserAvatar || '', text, time: Date.now(), read: false, delivered: false };
        if(replyingTo) { msgData.replyTo = replyingTo; replyingTo = null; document.getElementById('replyIndicator').style.display = 'none'; }
        await db.ref(getChatPath()).push(msgData);
        input.value = '';
        input.style.height = 'auto';
        playSound();
    }
    
    function loadMessages() {
        const messagesArea = document.getElementById('messagesArea');
        messagesArea.innerHTML = '<div class="messages-loading">Загрузка сообщений...</div>';
        const path = getChatPath();
        const msgsRef = db.ref(path);
        msgsRef.off();
        processedMsgIds.clear();
        
        let hasMessages = false;
        
        msgsRef.orderByChild('time').limitToLast(100).on('child_added', (snap) => {
            if(!processedMsgIds.has(snap.key)) {
                processedMsgIds.add(snap.key);
                hasMessages = true;
                
                if(!messagesLoaded) {
                    messagesArea.innerHTML = '';
                    messagesLoaded = true;
                }
                
                renderMessage(snap.key, snap.val());
            }
        }, (error) => {
            console.error('Ошибка загрузки:', error);
            messagesArea.innerHTML = '<div class="no-messages">Ошибка загрузки</div>';
        });
        
        setTimeout(() => {
            if(!hasMessages && !messagesLoaded) {
                messagesArea.innerHTML = '<div class="no-messages">Нет сообщений. Напишите первое!</div>';
                messagesLoaded = true;
            }
        }, 2000);
        
        msgsRef.on('child_changed', (snap) => updateMessageInUI(snap.key, snap.val()));
        msgsRef.on('child_removed', (snap) => removeMessageFromUI(snap.key));
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
        
        let mediaHtml = '';
        if(msg.mediaType === 'image') mediaHtml = `<img src="${msg.mediaUrl}" class="media-content" onclick="window.open('${msg.mediaUrl}','_blank')" loading="lazy">`;
        else if(msg.mediaType === 'video') {
            if(msg.isCircle) mediaHtml = `<div class="circle-video"><video controls playsinline><source src="${msg.mediaUrl}"></video></div>`;
            else mediaHtml = `<video controls class="video-content" playsinline><source src="${msg.mediaUrl}"></video>`;
        } else if(msg.mediaType === 'audio') mediaHtml = `<audio controls src="${msg.mediaUrl}"></audio>`;
        
        const avatar = msg.avatar || `https://ui-avatars.com/api/?background=6b4eff&color=fff&name=${encodeURIComponent(msg.name)}`;
        let readStatus = '';
        if(isMe && currentChat.type === 'user') {
            if(msg.read) readStatus = '<span class="read-status read">✓✓ Прочитано</span>';
            else if(msg.delivered) readStatus = '<span class="read-status">✓✓ Доставлено</span>';
            else readStatus = '<span class="read-status">✓ Отправлено</span>';
        }
        
        const deleteBtn = isMe ? `<button class="delete-btn" onclick="deleteMessage('${msgId}')"><i class="fas fa-trash"></i></button>` : '';
        const adminDeleteBtn = (isAdmin && !isMe) ? `<button class="admin-delete-btn" onclick="adminDeleteMessage('${msgId}')" title="Удалить как админ"><i class="fas fa-trash"></i></button>` : '';
        const replyBtn = `<button class="reply-btn" onclick="replyToMsg('${msgId}', '${escapeHtml(msg.name).replace(/'/g, "\\'")}', '${escapeHtml(msg.text || 'Медиа').replace(/'/g, "\\'")}')"><i class="fas fa-reply"></i></button>`;
        
        div.innerHTML = `<div class="bubble">${deleteBtn}${adminDeleteBtn}${replyBtn}<div class="message-header"><img class="msg-avatar" src="${avatar}" alt="${msg.name}"><span class="message-name">${escapeHtml(msg.name)}</span></div>${replyHtml}${mediaHtml}${msg.text && !msg.mediaType ? `<div>${escapeHtml(msg.text)}</div>` : ''}<span class="message-time">${new Date(msg.time).toLocaleTimeString()} ${readStatus}</span></div>`;
        document.getElementById('messagesArea').appendChild(div);
        document.getElementById('messagesArea').scrollTop = document.getElementById('messagesArea').scrollHeight;
        
        if(!isMe && !msg.read && currentChat.type === 'user') db.ref(`${getChatPath()}/${msgId}`).update({ read: true });
        if(!isMe && document.hidden && Notification.permission === 'granted') { 
            playSound(); 
            new Notification(msg.name, { body: msg.text || 'Новое сообщение', icon: avatar });
        }
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
    
    window.adminDeleteMessage = async (msgId) => {
        if(!isAdmin) return;
        if(confirm('Удалить это сообщение как администратор?')) {
            await db.ref(`${getChatPath()}/${msgId}`).remove();
        }
    };
    
    window.replyToMsg = (msgId, userName, text) => { 
        replyingTo = { messageId: msgId, userName, text }; 
        document.getElementById('replyIndicator').style.display = 'flex'; 
        document.getElementById('replyToName').innerText = userName; 
        document.getElementById('replyToText').innerText = text.length > 50 ? text.substring(0, 50) + '...' : text;
        document.getElementById('messageInput').focus();
    };
    
    window.scrollToMessage = (msgId) => { 
        const el = document.getElementById(`msg-${msgId}`); 
        if(el) { 
            el.scrollIntoView({ behavior: 'smooth', block: 'center' }); 
            el.classList.add('highlight-message'); 
            setTimeout(() => el.classList.remove('highlight-message'), 1000); 
        } 
    };
    
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
                <img class="contact-avatar" src="${u.avatar || `https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(u.name)}`}" alt="${u.name}">
                <div class="contact-info"><div class="contact-name">${escapeHtml(u.name)}</div><div class="contact-status">➕ Нажмите, чтобы добавить</div></div>
            </div>
        `).join('');
        if(results.length === 0) container.innerHTML = '<div style="padding:20px; text-align:center; color: var(--other-text);">Пользователь не найден</div>';
    }
    
    window.addContact = async (userId, userName) => {
        const contactsRef = db.ref('users/' + currentUserId + '/contacts');
        const exists = await contactsRef.orderByChild('userId').equalTo(userId).once('value');
        if(!exists.exists()) { await contactsRef.push({ userId, name: userName, addedAt: Date.now() }); alert(`✅ ${userName} добавлен`); loadContacts(); }
        else alert('Уже в контактах');
    };
    
    document.getElementById('photoBtn').onclick = () => document.getElementById('photoInput').click();
    document.getElementById('photoInput').onchange = (e) => { 
        if(e.target.files[0]){ 
            const r=new FileReader(); 
            r.onload=ev=>sendMediaMessage('image',ev.target.result); 
            r.readAsDataURL(e.target.files[0]); 
            e.target.value=''; 
        } 
    };
    document.getElementById('takePhotoBtn').onclick = () => document.getElementById('cameraCaptureInput').click();
    document.getElementById('cameraCaptureInput').onchange = (e) => { 
        if(e.target.files[0]){ 
            const r=new FileReader(); 
            r.onload=ev=>sendMediaMessage('image',ev.target.result); 
            r.readAsDataURL(e.target.files[0]); 
            e.target.value=''; 
        } 
    };
    document.getElementById('videoFileBtn').onclick = () => document.getElementById('videoFileInput').click();
    document.getElementById('videoFileInput').onchange = (e) => { 
        if(e.target.files[0]){ 
            const r=new FileReader(); 
            r.onload=ev=>sendMediaMessage('video',ev.target.result); 
            r.readAsDataURL(e.target.files[0]); 
            e.target.value=''; 
        } 
    };
    
    document.getElementById('voiceBtn').onclick = async () => {
        if(isVoiceRecording && voiceRecorder?.state === 'recording') { voiceRecorder.stop(); return; }
        try {
            const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
            voiceStream = stream; voiceRecorder = new MediaRecorder(stream); voiceChunks = [];
            voiceRecorder.ondataavailable = e => { if(e.data.size > 0) voiceChunks.push(e.data); };
            voiceRecorder.onstop = () => { 
                const blob = new Blob(voiceChunks, {type:'audio/webm'}); 
                const r = new FileReader(); 
                r.onload = e => sendMediaMessage('audio', e.target.result); 
                r.readAsDataURL(blob); 
                if(voiceStream) voiceStream.getTracks().forEach(t=>t.stop()); 
                isVoiceRecording = false; 
                document.getElementById('voiceBtn').style.background = ''; 
                document.getElementById('voiceBtn').style.color = '';
            };
            voiceRecorder.start(); 
            isVoiceRecording = true; 
            const btn = document.getElementById('voiceBtn'); 
            btn.style.background = '#ef4444'; 
            btn.style.color = 'white';
            setTimeout(() => { if(isVoiceRecording && voiceRecorder?.state === 'recording') voiceRecorder.stop(); }, 15000);
        } catch(e) { alert('Нет доступа к микрофону'); }
    };
    
    document.getElementById('circleVideoBtn').onclick = async () => {
        if(isCircleRecording) { 
            if(circleRecorder?.state === 'recording') circleRecorder.stop(); 
            isCircleRecording=false; 
            document.getElementById('circleVideoBtn').classList.remove('recording'); 
            document.getElementById('circleVideoBtn').innerHTML='<i class="fas fa-circle"></i>'; 
            if(circleStream) circleStream.getTracks().forEach(t=>t.stop()); 
        } else { 
            try {
                const stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: "user" }, audio: true }); 
                circleStream=stream; 
                circleRecorder=new MediaRecorder(stream,{mimeType:'video/webm'}); 
                circleChunks=[]; 
                circleRecorder.ondataavailable=e=>{ if(e.data.size>0) circleChunks.push(e.data); }; 
                circleRecorder.onstop=()=>{ 
                    const blob=new Blob(circleChunks,{type:'video/webm'}); 
                    const r=new FileReader(); 
                    r.onload=e=>sendMediaMessage('video',e.target.result,true); 
                    r.readAsDataURL(blob); 
                    if(circleStream) circleStream.getTracks().forEach(t=>t.stop()); 
                    circleStream=null; 
                }; 
                circleRecorder.start(); 
                isCircleRecording=true; 
                document.getElementById('circleVideoBtn').classList.add('recording'); 
                document.getElementById('circleVideoBtn').innerHTML='<i class="fas fa-stop"></i>'; 
                setTimeout(()=>{ if(isCircleRecording && circleRecorder?.state === 'recording'){ circleRecorder.stop(); } },30000);
            } catch(e) { alert('Нет доступа к камере'); }
        }
    };
    
    function setupProfile() {
        document.getElementById('settingsBtn').onclick = () => {
            document.getElementById('avatarPreview').src = currentUserAvatar || `https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(currentUserName)}`;
            document.getElementById('modalName').value = currentUserName;
            document.getElementById('profileModal').style.display = 'flex';
        };
        document.getElementById('closeModalBtn').onclick = () => {
            document.getElementById('profileModal').style.display = 'none';
        };
        
        document.getElementById('modalAvatar').onchange = (e) => { 
            if(e.target.files[0]) { 
                const r = new FileReader(); 
                r.onload = ev => document.getElementById('avatarPreview').src = ev.target.result; 
                r.readAsDataURL(e.target.files[0]); 
            } 
        };
        
        document.getElementById('saveProfileBtn').onclick = async () => {
            const saveBtn = document.getElementById('saveProfileBtn');
            const originalText = saveBtn.innerText;
            saveBtn.innerText = '⏳ Сохранение...';
            saveBtn.disabled = true;
            
            try {
                const newName = document.getElementById('modalName').value.trim();
                const file = document.getElementById('modalAvatar').files[0];
                
                if(newName && newName !== currentUserName) {
                    const check = await db.ref('users').orderByChild('name').equalTo(newName).once('value');
                    let taken = false;
                    check.forEach(c => { if(c.key !== currentUserId) taken = true; });
                    if(taken) throw new Error('Ник занят');
                }
                
                const updates = {};
                if(newName && newName !== currentUserName) updates.name = newName;
                
                if(file) {
                    const storageRef = storage.ref();
                    const avatarRef = storageRef.child(`avatars/${currentUserId}_${Date.now()}`);
                    
                    const snapshot = await avatarRef.put(file, {
                        contentType: file.type
                    });
                    
                    const newAvatarUrl = await snapshot.ref.getDownloadURL();
                    updates.avatarUrl = newAvatarUrl;
                }
                
                if(Object.keys(updates).length > 0) {
                    await db.ref('users/' + currentUserId).update(updates);
                    
                    if(updates.name) {
                        currentUserName = updates.name;
                        localStorage.setItem('userName', updates.name);
                        document.getElementById('sidebarName').innerText = updates.name;
                        isAdmin = currentUserName === 'DaniksGames';
                    }
                    if(updates.avatarUrl) {
                        currentUserAvatar = updates.avatarUrl;
                        document.getElementById('sidebarAvatar').src = updates.avatarUrl;
                    }
                    
                    if(updates.avatarUrl || updates.name) {
                        const groupMsgs = await db.ref('group_messages').orderByChild('userId').equalTo(currentUserId).once('value');
                        groupMsgs.forEach(m => m.ref.update(updates));
                    }
                    
                    alert('✅ Профиль успешно обновлён!');
                    document.getElementById('profileModal').style.display = 'none';
                }
                
                loadContacts();
                
            } catch(e) { 
                alert('Ошибка: ' + e.message); 
            } finally { 
                saveBtn.innerText = originalText; 
                saveBtn.disabled = false; 
            }
        };
    }
    
    function setupTheme() { 
        if(localStorage.getItem('theme') === 'dark') document.body.classList.add('dark'); 
        document.getElementById('themeToggle').onclick = () => { 
            document.body.classList.toggle('dark'); 
            localStorage.setItem('theme', document.body.classList.contains('dark') ? 'dark' : 'light'); 
        }; 
    }
    
    window.adminEditUser = async (userId, currentName, currentAvatarUrl) => {
        const newName = prompt('Новый никнейм:', currentName);
        if(newName && newName !== currentName) {
            const check = await db.ref('users').orderByChild('name').equalTo(newName).once('value');
            let exists = false;
            check.forEach(c => { if(c.key !== userId) exists = true; });
            if(exists) { alert('Ник занят'); return; }
            await db.ref('users/' + userId).update({ name: newName });
            const msgs = await db.ref('group_messages').orderByChild('userId').equalTo(userId).once('value');
            msgs.forEach(m => m.ref.update({ name: newName }));
            alert('Ник обновлён');
        }
        
        const newPass = prompt('Новый пароль (оставьте пустым, чтобы не менять):');
        if(newPass && newPass.trim()) {
            await db.ref('users/' + userId).update({ password: newPass.trim() });
            alert('Пароль обновлён');
        }
        
        const changeAvatar = confirm('Хотите изменить аватар?');
        if(changeAvatar) {
            const fileInput = document.createElement('input');
            fileInput.type = 'file';
            fileInput.accept = 'image/*';
            fileInput.onchange = async (e) => {
                if(e.target.files[0]) {
                    try {
                        const storageRef = storage.ref();
                        const avatarRef = storageRef.child(`avatars/${userId}_${Date.now()}`);
                        const snapshot = await avatarRef.put(e.target.files[0], {
                            contentType: e.target.files[0].type
                        });
                        const url = await snapshot.ref.getDownloadURL();
                        await db.ref('users/' + userId).update({ avatarUrl: url });
                        const msgs = await db.ref('group_messages').orderByChild('userId').equalTo(userId).once('value');
                        msgs.forEach(m => m.ref.update({ avatar: url }));
                        alert('Аватар обновлён');
                        loadAdminUsers();
                    } catch(err) {
                        alert('Ошибка загрузки аватара: ' + err.message);
                    }
                }
            };
            fileInput.click();
        }
        
        loadAdminUsers();
    };
    
    async function setupAdmin() {
        document.getElementById('adminPanelBtn').onclick = () => { 
            if(isAdmin) { 
                loadAdminUsers(); 
                document.getElementById('adminPanel').style.display = 'flex'; 
            } else {
                alert('Доступ только у DaniksGames'); 
            }
        };
        document.getElementById('closeAdminBtn').onclick = () => document.getElementById('adminPanel').style.display = 'none';
        document.getElementById('clearChatBtn').onclick = async () => { 
            if(isAdmin && confirm('Очистить общий чат?')) {
                await db.ref('group_messages').remove();
                alert('Чат очищен');
            }
        };
    }
    
    async function loadAdminUsers() {
        const users = await db.ref('users').once('value');
        const container = document.getElementById('usersList');
        container.innerHTML = '<h4 style="color:white; margin-bottom:15px;">Пользователи:</h4>';
        for(let id in users.val()) {
            const u = users.val()[id];
            container.innerHTML += `<div class="user-item">
                <div style="flex:1;">
                    <strong>${escapeHtml(u.name)}</strong><br>
                    <span style="font-size:0.8rem; opacity:0.8;">🔑 ${u.password}</span><br>
                    <span style="font-size:0.75rem;">${u.blocked ? '🔒 Заблокирован' : '✅ Активен'} | ${u.online ? '🟢 В сети' : '⚫ Не в сети'}</span>
                </div>
                <div style="display:flex; flex-wrap:wrap; gap:5px;">
                    <button style="background:#8b5cf6;" onclick="adminEditUser('${id}', '${escapeHtml(u.name).replace(/'/g, "\\'")}', '${u.avatarUrl || ''}')">✏️</button>
                    <button style="background:#f59e0b;" onclick="toggleBlock('${id}', ${u.blocked || false})">${u.blocked ? '🔓' : '🔒'}</button>
                    <button style="background:#ef4444;" onclick="deleteAccount('${id}')">🗑️</button>
                </div>
            </div>`;
        }
    }
    
    window.toggleBlock = async (id, blocked) => { 
        await db.ref('users/' + id).update({ blocked: !blocked }); 
        loadAdminUsers(); 
    };
    
    window.deleteAccount = async (id) => { 
        if(confirm('Удалить аккаунт навсегда?')) { 
            await db.ref('users/' + id).remove();
            const groupMsgs = await db.ref('group_messages').orderByChild('userId').equalTo(id).once('value');
            groupMsgs.forEach(m => m.ref.remove());
            loadAdminUsers(); 
        } 
    };
    
    function escapeHtml(s) { 
        if(!s) return ''; 
        return String(s).replace(/[&<>]/g, m => ({'&':'&amp;','<':'&lt;','>':'&gt;'}[m])); 
    }
    
    function closeSidebar() {
        document.getElementById('sidebar').classList.remove('open');
        document.getElementById('sidebarOverlay').classList.remove('visible');
    }
    
    function openSidebar() {
        document.getElementById('sidebar').classList.add('open');
        document.getElementById('sidebarOverlay').classList.add('visible');
    }
    
    async function initAll() {
        await loadContacts();
        setupProfile();
        setupTheme();
        setupAdmin();
        
        document.getElementById('menuToggle').onclick = openSidebar;
        document.getElementById('sidebarOverlay').onclick = closeSidebar;
        
        const textarea = document.getElementById('messageInput');
        textarea.addEventListener('input', function() {
            this.style.height = 'auto';
            this.style.height = Math.min(this.scrollHeight, 120) + 'px';
        });
        
        document.getElementById('sendBtn').onclick = sendTextMessage;
        textarea.addEventListener('keydown', (e) => { 
            if(e.key === 'Enter' && !e.shiftKey) { 
                e.preventDefault(); 
                sendTextMessage(); 
            } 
        });
        document.getElementById('cancelReplyBtn').onclick = () => { 
            replyingTo = null; 
            document.getElementById('replyIndicator').style.display = 'none'; 
        };
        document.getElementById('searchInput').oninput = searchUser;
        document.getElementById('logoutBtn').onclick = () => { 
            if(currentUserId) {
                db.ref('users/' + currentUserId).update({ online: false, lastSeen: Date.now() });
            }
            localStorage.clear(); 
            location.reload(); 
        };
        
        switchChat('global', '🌐 Общий чат', true);
        if(Notification.permission === 'default') Notification.requestPermission();
        
        if(currentUserId) {
            await db.ref('users/' + currentUserId).update({ online: true, lastSeen: Date.now() });
        }
        
        if(onlineStatusInterval) clearInterval(onlineStatusInterval);
        onlineStatusInterval = setInterval(updateOnlineStatus, 15000);
        setInterval(updateAllOnlineStatuses, 5000);
    }
    
    document.getElementById('authBtn').onclick = auth;
    
    (async () => {
        const uid = localStorage.getItem('userId');
        if(uid) {
            const snap = await db.ref('users/' + uid).once('value');
            if(snap.exists() && !snap.val().blocked) {
                currentUserId = uid; 
                currentUserName = snap.val().name; 
                currentUserAvatar = snap.val().avatarUrl;
                isBlocked = snap.val().blocked || false;
                isAdmin = currentUserName === 'DaniksGames';
                
                await db.ref('users/' + uid).update({ online: true, lastSeen: Date.now() });
                
                document.getElementById('authOverlay').style.display = 'none';
                document.getElementById('appContainer').style.display = 'flex';
                document.getElementById('sidebarName').innerText = currentUserName;
                document.getElementById('sidebarAvatar').src = currentUserAvatar || `https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(currentUserName)}`;
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
