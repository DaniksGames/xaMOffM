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
        
        .recording-indicator {
            position: fixed;
            bottom: 100px;
            left: 50%;
            transform: translateX(-50%);
            background: rgba(239, 68, 68, 0.9);
            color: white;
            padding: 12px 24px;
            border-radius: 40px;
            display: flex;
            align-items: center;
            gap: 12px;
            z-index: 1000;
            animation: pulse 1.5s infinite;
            backdrop-filter: blur(10px);
            box-shadow: 0 4px 20px rgba(0,0,0,0.3);
        }
        
        .recording-indicator i {
            font-size: 1.2rem;
        }
        
        .recording-indicator .recording-time {
            font-family: monospace;
            font-size: 1.1rem;
            font-weight: 600;
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
        
        @keyframes pulse { 0% { transform: scale(1); } 50% { transform: scale(1.05); } 100% { transform: scale(1); } }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(8px); } to { opacity: 1; transform: translateY(0); } }
        @keyframes highlight { 0% { background: rgba(74,108,247,0.5); } 100% { background: transparent; } }
        
        .system-message { 
            text-align: center; 
            font-size: 0.75rem; 
            background: #e9ecef; 
            padding: 6px 12px; 
            border-radius: 20px; 
            margin: 4px auto; 
            width: fit-content;
            max-width: 90%;
            color: #475569;
        }
        .dark .system-message {
            background: #334155;
            color: #94a3b8;
        }
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
        <button id="updateStartBtn" style="background:#8b5cf6; padding:12px; border:none; border-radius:16px; margin:10px 0; cursor:pointer; width:100%; font-size:1rem;">🔔 НАЧАЛО ОБНОВЛЕНИЯ</button>
        <button id="updateSuccessBtn" style="background:#10b981; padding:12px; border:none; border-radius:16px; margin:10px 0; cursor:pointer; width:100%; font-size:1rem;">✅ УСПЕШНОЕ ОБНОВЛЕНИЕ</button>
        <button id="updateErrorBtn" style="background:#ef4444; padding:12px; border:none; border-radius:16px; margin:10px 0; cursor:pointer; width:100%; font-size:1rem;">❌ ОШИБКА ОБНОВЛЕНИЯ</button>
        <div id="usersList"></div>
    </div>
</div>

<input type="file" id="photoInput" accept="image/*" style="display:none">
<input type="file" id="videoFileInput" accept="video/*" style="display:none">
<input type="file" id="cameraCaptureInput" accept="image/*" capture="environment" style="display:none">

<div id="recordingIndicator" class="recording-indicator" style="display: none;">
    <i class="fas fa-circle" style="color: #ef4444;"></i>
    <span id="recordingType">Запись...</span>
    <span class="recording-time" id="recordingTime">00:00</span>
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
    let recordingTimer = null;
    let recordingSeconds = 0;
    
    function playSound() { 
        try { 
            if(!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)(); 
            if(audioCtx.state === 'suspended') audioCtx.resume();
            const o = audioCtx.createOscillator(), g = audioCtx.createGain(); 
            o.connect(g); g.connect(audioCtx.destination); 
            o.frequency.value = 880; g.gain.value = 0.15; 
            o.start(); 
            g.gain.exponentialRampToValueAtTime(0.00001, audioCtx.currentTime + 0.3); 
            o.stop(audioCtx.currentTime + 0.
