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
            --unread-badge: #ef4444;
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
            --unread-badge: #ef4444;
        }
        
        body { 
            background: var(--bg-body); 
            min-height: 100vh; 
            display: flex; 
            justify-content: center; 
            align-items: center; 
        }
        
        /* Анимации */
        @keyframes fadeInScale {
            from { opacity: 0; transform: scale(0.95); }
            to { opacity: 1; transform: scale(1); }
        }
        @keyframes slideInUp {
            from { opacity: 0; transform: translateY(30px); }
            to { opacity: 1; transform: translateY(0); }
        }
        @keyframes slideInLeft {
            from { opacity: 0; transform: translateX(-30px); }
            to { opacity: 1; transform: translateX(0); }
        }
        @keyframes pulse { 
            0% { transform: translateX(-50%) scale(1); } 
            50% { transform: translateX(-50%) scale(1.05); } 
            100% { transform: translateX(-50%) scale(1); } 
        }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(8px); } to { opacity: 1; transform: translateY(0); } }
        @keyframes highlight { 0% { background: rgba(74,108,247,0.5); } 100% { background: transparent; } }
        
        .modal, .auth-overlay, .admin-panel, .forward-modal, .read-by-modal, .profile-view-modal {
            animation: fadeInScale 0.2s ease-out;
        }
        .message {
            animation: slideInLeft 0.2s ease-out;
        }
        .my-message {
            animation: slideInUp 0.2s ease-out;
        }
        .contact-item {
            transition: all 0.2s ease;
        }
        .sidebar {
            transition: left 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        }
        .selection-bar {
            transition: all 0.2s ease;
        }
        
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
            animation: fadeInScale 0.2s;
        }
        
        .drop-overlay.visible {
            display: flex;
        }
        
        .drop-overlay i {
            font-size: 5rem;
        }
        
        .unread-badge {
            background: var(--unread-badge);
            color: white;
            border-radius: 50%;
            min-width: 20px;
            height: 20px;
            padding: 0 6px;
            font-size: 0.7rem;
            font-weight: bold;
            display: inline-flex;
            align-items: center;
            justify-content: center;
            margin-left: 8px;
        }
        
        .contact-name-wrapper {
            display: flex;
            align-items: center;
            justify-content: space-between;
            width: 100%;
        }
        
        .contact-actions {
            display: flex;
            gap: 5px;
        }
        
        .contact-delete {
            background: rgba(239, 68, 68, 0.8);
            border: none;
            color: white;
            width: 28px;
            height: 28px;
            border-radius: 50%;
            cursor: pointer;
            font-size: 0.7rem;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: all 0.15s;
        }
        
        .contact-delete:active {
            transform: scale(0.9);
        }
        
        .group-badge {
            font-size: 0.7rem;
            opacity: 0.7;
            margin-left: 5px;
        }
        
        /* Профиль пользователя */
        .profile-view-modal {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.95);
            z-index: 3500;
            display: none;
            justify-content: center;
            align-items: center;
        }
        .profile-view-content {
            background: var(--chat-bg);
            border-radius: 28px;
            padding: 28px;
            width: 90%;
            max-width: 380px;
            text-align: center;
        }
        .profile-view-avatar {
            width: 120px;
            height: 120px;
            border-radius: 50%;
            object-fit: cover;
            margin: 0 auto 16px;
            display: block;
            background: var(--icon-color);
        }
        .profile-view-name {
            font-size: 1.5rem;
            font-weight: bold;
            color: var(--other-text);
            margin-bottom: 8px;
        }
        .profile-view-bio {
            color: var(--other-text);
            opacity: 0.8;
            margin-bottom: 16px;
            padding: 0 16px;
            word-break: break-word;
        }
        .profile-view-status {
            font-size: 0.85rem;
            color: var(--other-text);
            opacity: 0.7;
            margin-bottom: 20px;
        }
        .profile-view-buttons {
            display: flex;
            gap: 12px;
            justify-content: center;
        }
        .profile-view-buttons button {
            padding: 10px 20px;
            border-radius: 30px;
            border: none;
            cursor: pointer;
            font-size: 0.9rem;
        }
        .send-msg-btn {
            background: var(--icon-color);
            color: white;
        }
        .close-profile-btn {
            background: #94a3b8;
            color: white;
        }
        
        /* Группы */
        .create-group-modal {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.95);
            z-index: 3500;
            display: none;
            justify-content: center;
            align-items: center;
        }
        .create-group-content {
            background: var(--chat-bg);
            border-radius: 28px;
            padding: 24px;
            width: 90%;
            max-width: 400px;
            max-height: 80vh;
            overflow-y: auto;
        }
        .create-group-content h3 {
            color: var(--other-text);
            margin-bottom: 16px;
        }
        .create-group-content input, .create-group-content textarea {
            width: 100%;
            padding: 12px;
            margin: 8px 0;
            border-radius: 20px;
            border: 1px solid var(--input-border);
            background: var(--input-bg);
            color: var(--other-text);
            font-size: 1rem;
        }
        .create-group-content textarea {
            min-height: 60px;
            resize: vertical;
        }
        .member-select-list {
            max-height: 200px;
            overflow-y: auto;
            margin: 12px 0;
            border: 1px solid var(--input-border);
            border-radius: 16px;
            padding: 8px;
        }
        .member-item {
            display: flex;
            align-items: center;
            gap: 10px;
            padding: 8px;
            cursor: pointer;
            border-radius: 12px;
            transition: background 0.15s;
        }
        .member-item:hover {
            background: var(--contact-hover);
        }
        .member-item input {
            width: 20px;
            margin: 0;
        }
        .member-item img {
            width: 36px;
            height: 36px;
            border-radius: 50%;
        }
        .create-group-btn {
            background: var(--icon-color);
            color: white;
            border: none;
            padding: 12px;
            border-radius: 30px;
            width: 100%;
            margin-top: 12px;
            cursor: pointer;
        }
        
        .contact-card {
            display: flex;
            align-items: center;
            gap: 12px;
            padding: 8px 12px;
            background: var(--input-bg);
            border-radius: 16px;
            margin-top: 8px;
            border: 1px solid var(--input-border);
        }
        .contact-card img {
            width: 40px;
            height: 40px;
            border-radius: 50%;
        }
        .contact-card-info {
            flex: 1;
        }
        .contact-card-name {
            font-weight: 600;
            color: var(--other-text);
        }
        .contact-card-phone {
            font-size: 0.7rem;
            opacity: 0.7;
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
                transition: left 0.3s cubic-bezier(0.4, 0, 0.2, 1) !important;
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
            .selection-bar button {
                padding: 8px 12px !important;
                font-size: 0.8rem !important;
            }
            .contact-actions {
                flex-direction: column;
                gap: 3px;
            }
            .contact-delete {
                width: 24px;
                height: 24px;
                font-size: 0.6rem;
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
        .user-info-row { display: flex; align-items: center; gap: 10px; margin-top: 12px; padding-top: 8px; border-top: 1px solid rgba(255,255,255,0.2); cursor: pointer; }
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
        .contact-name { font-weight: 600; color: var(--other-text); }
        .contact-status { font-size: 0.7rem; opacity: 0.7; margin-top: 2px; }
        
        .chat-main { flex: 1; display: flex; flex-direction: column; background: var(--chat-bg); }
        .chat-header { background: var(--header-bg); color: white; padding: 12px 16px; display: flex; align-items: center; gap: 12px; }
        .chat-header-avatar { width: 44px; height: 44px; border-radius: 50%; object-fit: cover; background: #4a6cf7; cursor: pointer; }
        .chat-header-info { flex: 1; min-width: 0; cursor: pointer; }
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
            will-change: transform;
        }
        
        .message {
            transform: translateZ(0);
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
            transition: background 0.15s;
        }
        .message.selected {
            background: var(--selected-message);
            border-radius: 12px;
        }
        .my-message { align-self: flex-end; justify-content: flex-end; }
        .other-message { align-self: flex-start; }
        .system-message { align-self: center; max-width: 90%; }
        .bubble { 
            padding: 8px 14px; 
            border-radius: 20px; 
            background: var(--other-bubble); 
            color: var(--other-text); 
            position: relative; 
            word-break: break-word;
        }
        .system-message .bubble {
            background: var(--system-bubble);
            color: var(--system-text);
            text-align: center;
            font-size: 0.85rem;
        }
        .my-message .bubble { background: var(--my-bubble); color: var(--my-text); border-bottom-right-radius: 4px; }
        .message-header { display: flex; align-items: center; gap: 8px; margin-bottom: 5px; font-size: 0.75rem; cursor: pointer; }
        .msg-avatar { width: 24px; height: 24px; border-radius: 50%; object-fit: cover; background: #6b4eff; cursor: pointer; }
        .message-name { font-weight: 600; cursor: pointer; }
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
        
        .file-attachment {
            display: flex;
            align-items: center;
            gap: 10px;
            padding: 10px 14px;
            background: rgba(0,0,0,0.05);
            border-radius: 12px;
            margin-top: 6px;
            cursor: pointer;
        }
        .file-attachment i {
            font-size: 1.5rem;
            color: var(--icon-color);
        }
        .file-info {
            flex: 1;
            min-width: 0;
        }
        .file-name {
            font-weight: 500;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
        }
        .file-size {
            font-size: 0.7rem;
            opacity: 0.7;
        }
        
        .message-time { 
            font-size: 0.55rem; 
            opacity: 0.7; 
            margin-top: 4px; 
            display: flex;
            align-items: center;
            gap: 4px;
        }
        .read-status { font-size: 0.55rem; margin-left: 6px; }
        .read-status.read { color: var(--read-color); font-weight: bold; }
        
        .read-by-info {
            font-size: 0.6rem;
            opacity: 0.7;
            cursor: pointer;
            margin-left: 4px;
        }
        .read-by-info:hover {
            opacity: 1;
            text-decoration: underline;
        }
        
        .delete-btn, .reply-btn, .forward-btn { 
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
        .forward-btn { left: 24px; background: #10b981; }
        
        @media (hover: hover) {
            .message:hover .delete-btn, 
            .message:hover .reply-btn,
            .message:hover .forward-btn,
            .message:hover .admin-delete-btn { 
                opacity: 1; 
            }
        }
        
        @media (hover: none) {
            .delete-btn, .reply-btn, .forward-btn { 
                opacity: 0; 
                width: 24px; 
                height: 24px; 
                top: -4px; 
            }
            .message:active .delete-btn,
            .message:active .reply-btn,
            .message:active .forward-btn,
            .message:active .admin-delete-btn {
                opacity: 1;
            }
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
        
        .message-checkbox {
            position: absolute;
            left: -30px;
            top: 50%;
            transform: translateY(-50%);
            width: 20px;
            height: 20px;
            cursor: pointer;
            opacity: 0;
            transition: opacity 0.2s;
            accent-color: var(--icon-color);
        }
        .message:hover .message-checkbox,
        .message.selected .message-checkbox {
            opacity: 1;
        }
        
        .selection-bar {
            position: fixed;
            bottom: 20px;
            left: 50%;
            transform: translateX(-50%);
            background: var(--header-bg);
            color: white;
            padding: 10px 20px;
            border-radius: 40px;
            display: none;
            align-items: center;
            gap: 15px;
            box-shadow: 0 4px 20px rgba(0,0,0,0.3);
            z-index: 1500;
        }
        .selection-bar.visible {
            display: flex;
            animation: slideInUp 0.2s ease-out;
        }
        .selection-bar button {
            background: rgba(255,255,255,0.2);
            border: none;
            color: white;
            padding: 8px 16px;
            border-radius: 20px;
            cursor: pointer;
            display: flex;
            align-items: center;
            gap: 6px;
            font-size: 0.9rem;
        }
        .selection-bar button:active {
            background: rgba(255,255,255,0.3);
        }
        .selection-bar .cancel-selection {
            background: #ef4444;
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
        
        .media-preview {
            background: var(--input-bg);
            padding: 8px 12px;
            margin: 0 16px 8px;
            border-radius: 12px;
            display: none;
            align-items: center;
            gap: 10px;
            border: 1px solid var(--input-border);
        }
        .media-preview.visible {
            display: flex;
        }
        .media-preview img {
            width: 50px;
            height: 50px;
            object-fit: cover;
            border-radius: 8px;
        }
        .media-preview-info {
            flex: 1;
            min-width: 0;
        }
        .media-preview-name {
            font-weight: 500;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
            color: var(--other-text);
        }
        .media-preview-size {
            font-size: 0.7rem;
            opacity: 0.7;
            color: var(--other-text);
        }
        .media-preview-cancel {
            background: none;
            border: none;
            color: #ef4444;
            cursor: pointer;
            font-size: 1.2rem;
        }
        .media-caption-input {
            flex: 1;
            padding: 8px 12px;
            border-radius: 20px;
            border: 1px solid var(--input-border);
            background: var(--input-bg);
            color: var(--other-text);
            font-size: 0.9rem;
            outline: none;
        }
        
        .input-area { 
            padding: 12px 16px; 
            background: var(--input-bg); 
            border-top: 1px solid var(--input-border);
            position: relative;
        }
        .input-row { 
            display: flex; 
            gap: 8px; 
            align-items: flex-end;
        }
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
            max-height: 150px;
            min-height: 50px;
            line-height: 1.4;
        }
        
        .attach-btn {
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
        .attach-btn:active { transform: scale(0.9); background: rgba(74,108,247,0.1); }
        
        .attach-menu {
            position: absolute;
            bottom: 80px;
            left: 16px;
            background: var(--input-bg);
            border: 1px solid var(--input-border);
            border-radius: 16px;
            padding: 8px;
            display: none;
            grid-template-columns: repeat(3, 1fr);
            gap: 4px;
            box-shadow: 0 4px 20px rgba(0,0,0,0.15);
            z-index: 100;
        }
        
        .attach-menu.visible {
            display: grid;
            animation: fadeInScale 0.15s ease-out;
        }
        
        .attach-menu-btn {
            background: transparent;
            border: none;
            color: var(--other-text);
            padding: 10px 12px;
            border-radius: 12px;
            cursor: pointer;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 4px;
            font-size: 0.75rem;
            transition: background 0.15s;
            white-space: nowrap;
        }
        
        .attach-menu-btn i {
            font-size: 1.2rem;
            color: var(--icon-color);
        }
        
        .attach-menu-btn:active {
            background: var(--contact-hover);
        }
        
        .send-btn { 
            background: var(--icon-color); 
            color: white; 
            border: none; 
            width: 50px; 
            height: 50px; 
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
        
        .recording-indicator {
            position: fixed;
            bottom: 100px;
            left: 50%;
            transform: translateX(-50%);
            background: rgba(239, 68, 68, 0.9);
            color: white;
            padding: 12px 24px;
            border-radius: 40px;
            display: none;
            align-items: center;
            gap: 10px;
            z-index: 1000;
            animation: pulse 1.5s infinite;
            box-shadow: 0 4px 15px rgba(0,0,0,0.3);
        }
        .recording-indicator.visible {
            display: flex;
        }
        
        .recording-indicator i {
            font-size: 1.2rem;
        }
        
        .recording-timer {
            font-weight: bold;
            font-size: 1.1rem;
        }
        
        .camera-preview {
            position: fixed;
            bottom: 100px;
            right: 20px;
            width: 120px;
            height: 160px;
            border-radius: 12px;
            overflow: hidden;
            box-shadow: 0 4px 15px rgba(0,0,0,0.3);
            z-index: 1000;
            border: 2px solid white;
            display: none;
        }
        .camera-preview.visible {
            display: block;
        }
        
        .camera-preview video {
            width: 100%;
            height: 100%;
            object-fit: cover;
        }
        
        .forward-modal {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.95);
            z-index: 3500;
            display: none;
            justify-content: center;
            align-items: center;
        }
        .forward-modal.visible {
            display: flex;
        }
        .forward-content {
            background: var(--chat-bg);
            border-radius: 24px;
            padding: 24px;
            width: 90%;
            max-width: 400px;
            max-height: 80vh;
            overflow-y: auto;
        }
        .forward-content h3 {
            color: var(--other-text);
            margin-bottom: 16px;
        }
        .forward-contact {
            display: flex;
            align-items: center;
            gap: 12px;
            padding: 12px;
            cursor: pointer;
            border-radius: 12px;
            transition: background 0.15s;
        }
        .forward-contact:hover {
            background: var(--contact-hover);
        }
        .forward-contact img {
            width: 44px;
            height: 44px;
            border-radius: 50%;
            object-fit: cover;
        }
        .forward-contact-info {
            flex: 1;
        }
        .forward-contact-name {
            font-weight: 600;
            color: var(--other-text);
        }
        .forward-cancel {
            background: #94a3b8;
            color: white;
            border: none;
            padding: 12px;
            border-radius: 30px;
            width: 100%;
            margin-top: 16px;
            cursor: pointer;
        }
        
        .read-by-modal {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.5);
            z-index: 3500;
            display: none;
            justify-content: center;
            align-items: center;
        }
        .read-by-modal.visible {
            display: flex;
        }
        .read-by-content {
            background: var(--chat-bg);
            border-radius: 24px;
            padding: 24px;
            width: 90%;
            max-width: 350px;
            max-height: 70vh;
            overflow-y: auto;
        }
        .read-by-content h3 {
            color: var(--other-text);
            margin-bottom: 16px;
        }
        .read-by-user {
            display: flex;
            align-items: center;
            gap: 12px;
            padding: 10px 0;
            border-bottom: 1px solid var(--input-border);
        }
        .read-by-user img {
            width: 36px;
            height: 36px;
            border-radius: 50%;
            object-fit: cover;
        }
        .read-by-user span {
            color: var(--other-text);
        }
        .read-by-close {
            background: var(--icon-color);
            color: white;
            border: none;
            padding: 12px;
            border-radius: 30px;
            width: 100%;
            margin-top: 16px;
            cursor: pointer;
        }
        
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
        .dark .auth-card input, .dark .modal-content input, .dark .modal-content textarea { 
            background: #334155; 
            color: white; 
            border-color: #475569; 
        }
        .auth-card input, .modal-content input, .modal-content textarea { 
            width: 100%; 
            padding: 14px; 
            margin: 12px 0; 
            border-radius: 30px; 
            border: 1px solid #ddd; 
            font-size: 16px;
            outline: none;
        }
        .modal-content textarea {
            border-radius: 20px;
            resize: vertical;
            min-height: 80px;
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
        
        .system-notify-section {
            background: #1e293b;
            border-radius: 16px;
            padding: 16px;
            margin: 15px 0;
        }
        
        .system-notify-section h4 {
            color: white;
            margin-bottom: 12px;
            font-size: 1rem;
        }
        
        .preset-buttons {
            display: flex;
            gap: 8px;
            flex-wrap: wrap;
            margin-bottom: 12px;
        }
        
        .preset-btn {
            background: #334155;
            color: white;
            border: none;
            padding: 10px 16px;
            border-radius: 20px;
            cursor: pointer;
            font-size: 0.85rem;
            flex: 1;
            min-width: 120px;
        }
        
        .preset-btn.update { background: #3b82f6; }
        .preset-btn.success { background: #10b981; }
        .preset-btn.error { background: #ef4444; }
        
        .custom-notify-input {
            display: flex;
            gap: 8px;
            margin-top: 10px;
        }
        
        .custom-notify-input input {
            flex: 1;
            padding: 12px;
            border-radius: 20px;
            border: none;
            font-size: 0.9rem;
        }
        
        .custom-notify-input button {
            background: #8b5cf6;
            color: white;
            border: none;
            padding: 12px 20px;
            border-radius: 20px;
            cursor: pointer;
            font-size: 0.9rem;
        }
        
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
            .attach-menu {
                left: 50%;
                transform: translateX(-50%);
                min-width: 400px;
            }
        }
        
        .highlight-message { animation: highlight 1s; }
        
        .add-group-btn {
            background: var(--icon-color);
            color: white;
            border: none;
            padding: 8px 16px;
            border-radius: 20px;
            margin: 8px 12px;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
            font-size: 0.9rem;
        }
    </style>
</head>
<body>

<div class="drop-overlay" id="dropOverlay">
    <i class="fas fa-cloud-upload-alt"></i>
    <span>Отпустите для загрузки</span>
</div>

<div class="sidebar-overlay" id="sidebarOverlay"></div>

<div id="recordingIndicator" class="recording-indicator">
    <i class="fas fa-microphone"></i>
    <span id="recordingTimer" class="recording-timer">00:00</span>
    <span>Идёт запись...</span>
</div>

<div id="cameraPreview" class="camera-preview">
    <video id="previewVideo" autoplay muted playsinline></video>
</div>

<div class="selection-bar" id="selectionBar">
    <span id="selectedCount">0 сообщений</span>
    <button id="forwardSelectedBtn"><i class="fas fa-share"></i> Переслать</button>
    <button id="deleteSelectedBtn" style="background:#ef4444;"><i class="fas fa-trash"></i> Удалить</button>
    <button id="cancelSelectionBtn" class="cancel-selection"><i class="fas fa-times"></i> Отмена</button>
</div>

<div id="forwardModal" class="forward-modal">
    <div class="forward-content">
        <h3><i class="fas fa-share"></i> Переслать сообщения</h3>
        <div id="forwardContactsList"></div>
        <button class="forward-cancel" onclick="closeForwardModal()">Отмена</button>
    </div>
</div>

<div id="readByModal" class="read-by-modal">
    <div class="read-by-content">
        <h3><i class="fas fa-check-circle"></i> Прочитали:</h3>
        <div id="readByList"></div>
        <button class="read-by-close" onclick="closeReadByModal()">Закрыть</button>
    </div>
</div>

<div id="profileViewModal" class="profile-view-modal">
    <div class="profile-view-content">
        <img id="profileViewAvatar" class="profile-view-avatar" src="" alt="Avatar">
        <div class="profile-view-name" id="profileViewName"></div>
        <div class="profile-view-bio" id="profileViewBio"></div>
        <div class="profile-view-status" id="profileViewStatus"></div>
        <div class="profile-view-buttons">
            <button class="send-msg-btn" id="profileViewSendMsg">💬 Написать</button>
            <button class="close-profile-btn" onclick="closeProfileView()">Закрыть</button>
        </div>
    </div>
</div>

<div id="createGroupModal" class="create-group-modal">
    <div class="create-group-content">
        <h3><i class="fas fa-users"></i> Создать группу</h3>
        <input type="text" id="groupName" placeholder="Название группы">
        <textarea id="groupDescription" placeholder="Описание группы (необязательно)"></textarea>
        <div class="member-select-list" id="memberSelectList"></div>
        <button class="create-group-btn" id="createGroupBtn">✅ Создать группу</button>
        <button class="forward-cancel" onclick="closeCreateGroupModal()" style="margin-top:8px;">Отмена</button>
    </div>
</div>

<div class="app-container" id="appContainer" style="display: none;">
    <div class="sidebar" id="sidebar">
        <div class="sidebar-header">
            <h2><i class="fas fa-comments"></i> xaMOff</h2>
            <div class="user-info-row" onclick="showMyProfile()">
                <img id="sidebarAvatar" src="" alt="Avatar">
                <span id="sidebarName"></span>
            </div>
        </div>
        <div class="search-box">
            <input type="text" id="searchInput" placeholder="🔍 Поиск по нику..." autocomplete="off">
        </div>
        <button class="add-group-btn" id="createGroupBtnSidebar"><i class="fas fa-plus-circle"></i> Создать группу</button>
        <div class="contacts-list" id="contactsList"></div>
    </div>
    
    <div class="chat-main">
        <div class="chat-header">
            <button class="menu-toggle" id="menuToggle"><i class="fas fa-bars"></i></button>
            <img id="chatHeaderAvatar" class="chat-header-avatar" src="" alt="Avatar" onclick="showCurrentChatProfile()">
            <div class="chat-header-info" onclick="showCurrentChatProfile()">
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
        <div id="mediaPreview" class="media-preview">
            <img id="previewImage" src="" style="display: none;">
            <i id="previewFileIcon" class="fas fa-file" style="font-size: 2rem; display: none;"></i>
            <div class="media-preview-info">
                <div class="media-preview-name" id="previewName"></div>
                <div class="media-preview-size" id="previewSize"></div>
                <input type="text" id="mediaCaption" class="media-caption-input" placeholder="Добавить подпись...">
            </div>
            <button class="media-preview-cancel" id="cancelMediaPreview"><i class="fas fa-times"></i></button>
        </div>
        <div class="input-area">
            <div class="attach-menu" id="attachMenu">
                <button class="attach-menu-btn" id="attachPhotoBtn"><i class="fas fa-image"></i><span>Фото</span></button>
                <button class="attach-menu-btn" id="attachCameraBtn"><i class="fas fa-camera"></i><span>Снять</span></button>
                <button class="attach-menu-btn" id="attachVideoBtn"><i class="fas fa-video"></i><span>Видео</span></button>
                <button class="attach-menu-btn" id="attachFileBtn"><i class="fas fa-file"></i><span>Файл</span></button>
                <button class="attach-menu-btn" id="attachContactBtn"><i class="fas fa-address-card"></i><span>Контакт</span></button>
                <button class="attach-menu-btn" id="attachCircleBtn"><i class="fas fa-circle"></i><span>Кружок</span></button>
                <button class="attach-menu-btn" id="attachVoiceBtn"><i class="fas fa-microphone"></i><span>Голос</span></button>
            </div>
            <div class="input-row">
                <textarea id="messageInput" class="message-input" placeholder="Сообщение..." rows="1"></textarea>
                <button class="attach-btn" id="attachBtn"><i class="fas fa-paperclip"></i></button>
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
        <textarea id="modalBio" placeholder="Описание профиля" rows="3"></textarea>
        <button id="saveProfileBtn">💾 Сохранить</button>
        <button id="closeModalBtn" style="background:#94a3b8;">Отмена</button>
    </div>
</div>

<div id="adminPanel" class="admin-panel">
    <button class="close-admin" id="closeAdminBtn">✕ Закрыть</button>
    <div class="admin-panel-content">
        <h3>👑 Админ-панель</h3>
        
        <div class="system-notify-section">
            <h4><i class="fas fa-bullhorn"></i> Системные уведомления</h4>
            <div class="preset-buttons">
                <button class="preset-btn update" onclick="sendSystemNotification('update')">
                    <i class="fas fa-sync-alt"></i> Установка обновления
                </button>
                <button class="preset-btn success" onclick="sendSystemNotification('success')">
                    <i class="fas fa-check-circle"></i> Обновление установлено
                </button>
                <button class="preset-btn error" onclick="sendSystemNotification('error')">
                    <i class="fas fa-exclamation-circle"></i> Ошибка обновления
                </button>
            </div>
            <div class="custom-notify-input">
                <input type="text" id="customNotifyText" placeholder="Своё уведомление...">
                <button onclick="sendCustomNotification()"><i class="fas fa-paper-plane"></i> Отправить</button>
            </div>
        </div>
        
        <div style="background: #1e293b; border-radius: 16px; padding: 16px; margin: 15px 0;">
            <h4 style="color: white; margin-bottom: 12px;"><i class="fas fa-users"></i> Участники группы</h4>
            <div id="groupMembersList"></div>
            <div style="margin-top: 12px;">
                <select id="addUserToGroupSelect" style="width: 100%; padding: 10px; border-radius: 12px; background: #334155; color: white; border: none;">
                    <option value="">-- Выберите пользователя --</option>
                </select>
                <button id="addUserToGroupBtn" style="width: 100%; margin-top: 8px; background: #10b981; color: white; border: none; padding: 10px; border-radius: 12px;">➕ Добавить в группу</button>
            </div>
        </div>
        
        <button id="clearChatBtn" style="background:#f59e0b; padding:12px; border:none; border-radius:16px; margin:10px 0; cursor:pointer; width:100%; font-size:1rem;">🗑️ ОЧИСТИТЬ ОБЩИЙ ЧАТ</button>
        <div id="usersList"></div>
    </div>
</div>

<input type="file" id="photoInput" accept="image/*" style="display:none">
<input type="file" id="videoFileInput" accept="video/*" style="display:none">
<input type="file" id="fileInput" accept="*/*" style="display:none">
<input type="file" id="cameraCaptureInput" accept="image/*" capture="environment" style="display:none">

<script>
// ==================== ИНИЦИАЛИЗАЦИЯ FIREBASE ====================
const firebaseConfig = {
    apiKey: "AIzaSyD4a16-r1nwCVDAu5_DBnirq0e8Lu9pBZw",
    authDomain: "daniksgames-d46b2.firebaseapp.com",
    databaseURL: "https://daniksgames-d46b2-default-rtdb.firebaseio.com",
    projectId: "daniksgames-d46b2",
    storageBucket: "daniksgames-d46b2.firebasestorage.app"
};
firebase.initializeApp(firebaseConfig);
const db = firebase.database();

const userCache = new Map();
const avatarCache = new Map();

// Создаём учётку DaniksGames
(async () => {
    const snap = await db.ref('users').orderByChild('name').equalTo('DaniksGames').once('value');
    if(snap.exists()) snap.forEach(c => c.ref.update({ password: 'Dan10102011', bio: 'Создатель xaMOff Messenger' }));
    else await db.ref('users').push({ name: 'DaniksGames', password: 'Dan10102011', avatarUrl: '', bio: 'Создатель xaMOff Messenger', blocked: false, createdAt: Date.now(), online: false, lastSeen: Date.now() });
})();

// ==================== ГЛОБАЛЬНЫЕ ПЕРЕМЕННЫЕ ====================
let currentUserId = null, currentUserName = null, currentUserAvatar = null, currentUserBio = null, isBlocked = false;
let currentChat = { type: 'group', id: 'global', name: 'Общий чат' };
let replyingTo = null;
let contacts = [];
let groups = [];
let unreadCounts = new Map();
let audioCtx = null;
let voiceRecorder = null, voiceChunks = [], isVoiceRecording = false, voiceStream = null;
let circleRecorder = null, circleChunks = [], isCircleRecording = false, circleStream = null;
let processedMsgIds = new Map();
let onlineStatusInterval = null;
let isAdmin = false;
let messagesLoaded = false;
let recordingTimerInterval = null;
let recordingSeconds = 0;
let pendingMedia = null;
let selectedMessages = new Set();
let allUsers = [];
let groupMembers = new Map(); // groupId -> Set of userIds
let userGroups = new Map(); // userId -> Set of groupIds

// ==================== ВСПОМОГАТЕЛЬНЫЕ ФУНКЦИИ ====================
function formatFileSize(bytes) {
    if(bytes < 1024) return bytes + ' B';
    if(bytes < 1024 * 1024) return (bytes / 1024).toFixed(1) + ' KB';
    return (bytes / (1024 * 1024)).toFixed(1) + ' MB';
}

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

function escapeHtml(s) { 
    if(!s) return ''; 
    return String(s).replace(/[&<>]/g, m => ({'&':'&amp;','<':'&lt;','>':'&gt;'}[m])); 
}

function formatTime(seconds) {
    const mins = Math.floor(seconds / 60);
    const secs = seconds % 60;
    return `${mins.toString().padStart(2, '0')}:${secs.toString().padStart(2, '0')}`;
}

function startRecordingTimer() {
    recordingSeconds = 0;
    document.getElementById('recordingTimer').textContent = formatTime(0);
    recordingTimerInterval = setInterval(() => {
        recordingSeconds++;
        document.getElementById('recordingTimer').textContent = formatTime(recordingSeconds);
    }, 1000);
}

function stopRecordingTimer() {
    if(recordingTimerInterval) {
        clearInterval(recordingTimerInterval);
        recordingTimerInterval = null;
    }
}

function getAvatarUrl(name, avatar) {
    if(avatar) return avatar;
    if(avatarCache.has(name)) return avatarCache.get(name);
    const url = `https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(name)}`;
    avatarCache.set(name, url);
    return url;
}

// ==================== ПРОФИЛЬ ПОЛЬЗОВАТЕЛЯ ====================
window.showUserProfile = async (userId, userName) => {
    const userSnap = await db.ref('users/' + userId).once('value');
    const user = userSnap.val();
    if(!user) return;
    
    document.getElementById('profileViewAvatar').src = getAvatarUrl(user.name, user.avatarUrl);
    document.getElementById('profileViewName').innerText = user.name;
    document.getElementById('profileViewBio').innerText = user.bio || 'Нет описания';
    document.getElementById('profileViewStatus').innerHTML = user.online ? '🟢 В сети' : '⚫ Не в сети';
    
    document.getElementById('profileViewModal').style.display = 'flex';
    document.getElementById('profileViewModal').dataset.userId = userId;
    document.getElementById('profileViewModal').dataset.userName = user.name;
};

window.showMyProfile = () => {
    showUserProfile(currentUserId, currentUserName);
};

window.showCurrentChatProfile = () => {
    if(currentChat.type === 'user') {
        showUserProfile(currentChat.id, currentChat.name);
    } else if(currentChat.type === 'group') {
        showGroupInfo(currentChat.id);
    }
};

window.closeProfileView = () => {
    document.getElementById('profileViewModal').style.display = 'none';
};

document.getElementById('profileViewSendMsg').onclick = () => {
    const userId = document.getElementById('profileViewModal').dataset.userId;
    const userName = document.getElementById('profileViewModal').dataset.userName;
    closeProfileView();
    switchChat(userId, userName, false);
};

// ==================== ГРУППЫ ====================
async function loadGroups() {
    const groupsRef = db.ref('groups');
    const snap = await groupsRef.once('value');
    const groupsData = snap.val() || {};
    groups = [];
    
    for(let id in groupsData) {
        const group = groupsData[id];
        if(group.members && group.members[currentUserId]) {
            groups.push({
                id: id,
                name: group.name,
                description: group.description,
                creator: group.creator,
                members: group.members,
                createdAt: group.createdAt
            });
            groupMembers.set(id, new Set(Object.keys(group.members || {})));
        }
    }
}

async function createGroup(name, description, selectedMembers) {
    const groupId = Date.now().toString();
    const members = { [currentUserId]: true };
    selectedMembers.forEach(m => { members[m] = true; });
    
    await db.ref('groups/' + groupId).set({
        id: groupId,
        name: name,
        description: description || '',
        creator: currentUserId,
        members: members,
        createdAt: Date.now()
    });
    
    await loadGroups();
    await loadContacts();
    return groupId;
}

window.showCreateGroupModal = async () => {
    await loadAllUsers();
    const container = document.getElementById('memberSelectList');
    container.innerHTML = '<div style="padding: 8px; color: var(--other-text);">Выберите участников:</div>';
    
    for(let user of allUsers) {
        if(user.id !== currentUserId) {
            container.innerHTML += `
                <div class="member-item" onclick="toggleMemberSelect('${user.id}')">
                    <input type="checkbox" id="member_${user.id}" value="${user.id}">
                    <img src="${getAvatarUrl(user.name, user.avatar)}" alt="${user.name}">
                    <span>${escapeHtml(user.name)}</span>
                </div>
            `;
        }
    }
    
    document.getElementById('createGroupModal').style.display = 'flex';
};

window.toggleMemberSelect = (userId) => {
    const cb = document.getElementById(`member_${userId}`);
    if(cb) cb.checked = !cb.checked;
};

window.closeCreateGroupModal = () => {
    document.getElementById('createGroupModal').style.display = 'none';
    document.getElementById('groupName').value = '';
    document.getElementById('groupDescription').value = '';
};

document.getElementById('createGroupBtn').onclick = async () => {
    const name = document.getElementById('groupName').value.trim();
    if(!name) {
        alert('Введите название группы');
        return;
    }
    
    const selectedMembers = [];
    document.querySelectorAll('#memberSelectList input:checked').forEach(cb => {
        selectedMembers.push(cb.value);
    });
    
    const groupId = await createGroup(name, document.getElementById('groupDescription').value, selectedMembers);
    alert('✅ Группа создана!');
    closeCreateGroupModal();
    await loadContacts();
    switchChat(groupId, name, true);
};

window.showGroupInfo = async (groupId) => {
    const groupSnap = await db.ref('groups/' + groupId).once('value');
    const group = groupSnap.val();
    if(!group) return;
    
    const membersList = Object.keys(group.members || {});
    let membersHtml = '';
    for(let memberId of membersList) {
        const userSnap = await db.ref('users/' + memberId).once('value');
        if(userSnap.exists()) {
            const user = userSnap.val();
            membersHtml += `<div class="read-by-user"><img src="${getAvatarUrl(user.name, user.avatarUrl)}"><span>${escapeHtml(user.name)}${memberId === group.creator ? ' 👑' : ''}</span></div>`;
        }
    }
    
    document.getElementById('readByList').innerHTML = `
        <h4 style="margin-bottom:12px;">${escapeHtml(group.name)}</h4>
        <p style="margin-bottom:12px; opacity:0.7;">${group.description || 'Нет описания'}</p>
        <p style="margin-bottom:8px;"><strong>Участники (${membersList.length}):</strong></p>
        ${membersHtml}
    `;
    document.getElementById('readByModal').classList.add('visible');
};

// ==================== УПРАВЛЕНИЕ СТАТУСОМ ОНЛАЙН ====================
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

// ==================== АУТЕНТИФИКАЦИЯ ====================
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
                    currentUserBio = c.val().bio || '';
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
            await newUser.set({ name, password, avatarUrl: '', bio: '', blocked: false, createdAt: Date.now(), lastSeen: Date.now(), online: true });
            currentUserName = name; 
            currentUserAvatar = null;
            currentUserBio = '';
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
        document.getElementById('sidebarAvatar').src = getAvatarUrl(currentUserName, currentUserAvatar);
        await initUser();
        initAll();
    } catch(e) {
        authError.innerText = 'Ошибка: ' + e.message;
        authBtn.disabled = false;
        authBtn.innerText = 'Войти / Регистрация';
    }
}

// ==================== ЗАГРУЗКА ПОЛЬЗОВАТЕЛЕЙ И КОНТАКТОВ ====================
async function loadAllUsers() {
    if(userCache.has('allUsers')) {
        allUsers = userCache.get('allUsers');
        return;
    }
    const snap = await db.ref('users').once('value');
    allUsers = [];
    for(let id in snap.val()) {
        const u = snap.val()[id];
        if(!u.blocked && id !== currentUserId) {
            allUsers.push({ id, name: u.name, avatar: u.avatarUrl || '' });
        }
    }
    userCache.set('allUsers', allUsers);
}

async function initUser() {
    await loadAllUsers();
    await loadGroups();
    
    // Загружаем контакты
    const contactsRef = db.ref('users/' + currentUserId + '/contacts');
    const contactsSnap = await contactsRef.once('value');
    const savedContacts = contactsSnap.val() || {};
    
    // Добавляем DaniksGames автоматически
    const danikSnap = await db.ref('users').orderByChild('name').equalTo('DaniksGames').once('value');
    if(danikSnap.exists()) {
        danikSnap.forEach(async (d) => {
            if(!savedContacts[d.key]) {
                await contactsRef.child(d.key).set({ userId: d.key, name: d.val().name, avatar: d.val().avatarUrl || '', addedAt: Date.now() });
            }
        });
    }
    
    // Подписываемся на входящие сообщения от незнакомцев
    const privateMessagesRef = db.ref('private_messages');
    privateMessagesRef.on('child_added', async (snap) => {
        const [id1, id2] = snap.key.split('_');
        const otherId = id1 === currentUserId ? id2 : id1;
        if(otherId && otherId !== currentUserId) {
            const userSnap = await db.ref('users/' + otherId).once('value');
            if(userSnap.exists() && !userSnap.val().blocked) {
                const contactExists = await db.ref('users/' + currentUserId + '/contacts/' + otherId).once('value');
                if(!contactExists.exists()) {
                    await db.ref('users/' + currentUserId + '/contacts/' + otherId).set({
                        userId: otherId,
                        name: userSnap.val().name,
                        avatar: userSnap.val().avatarUrl || '',
                        addedAt: Date.now()
                    });
                    await loadContacts();
                }
            }
        }
    });
}

async function loadContacts() {
    const contactsRef = db.ref('users/' + currentUserId + '/contacts');
    const snapshot = await contactsRef.once('value');
    const contactsData = snapshot.val() || {};
    
    const contactsMap = new Map();
    
    for(let id in contactsData) {
        if(!contactsMap.has(id)) {
            const userSnap = await db.ref('users/' + id).once('value');
            if(userSnap.exists() && !userSnap.val().blocked) {
                contactsMap.set(id, { 
                    id, 
                    name: userSnap.val().name, 
                    avatar: userSnap.val().avatarUrl || '', 
                    isGroup: false 
                });
            }
        }
    }
    
    contacts = Array.from(contactsMap.values());
    
    // Добавляем группы
    for(let group of groups) {
        contacts.unshift({ id: group.id, name: '👥 ' + group.name, avatar: '', isGroup: true, groupData: group });
    }
    
    renderContacts();
}

function renderContacts() {
    const container = document.getElementById('contactsList');
    container.innerHTML = contacts.map(c => `
        <div class="contact-item ${currentChat.id === c.id ? 'active' : ''}" onclick="switchChat('${c.id}', '${c.name.replace(/'/g, "\\'")}', ${c.isGroup ? 'true' : 'false'})">
            <div class="avatar-wrapper">
                <img class="contact-avatar" src="${getAvatarUrl(c.name, c.avatar)}" alt="${c.name}">
                <div class="online-badge offline" id="online-${c.id}"></div>
            </div>
            <div class="contact-info">
                <div class="contact-name-wrapper">
                    <span class="contact-name">${escapeHtml(c.name)}</span>
                    ${unreadCounts.get(c.id) > 0 ? `<span class="unread-badge">${unreadCounts.get(c.id)}</span>` : ''}
                </div>
                <div class="contact-status" id="status-${c.id}">${c.isGroup ? '👥 Группа' : '🔄 Загрузка...'}</div>
            </div>
            ${!c.isGroup ? `
                <div class="contact-actions">
                    <button class="contact-delete" onclick="event.stopPropagation(); clearChatWithContact('${c.id}')" title="Очистить чат">
                        <i class="fas fa-trash-alt"></i>
                    </button>
                    <button class="contact-delete" onclick="event.stopPropagation(); deleteContact('${c.id}')" title="Удалить контакт">
                        <i class="fas fa-user-minus"></i>
                    </button>
                </div>
            ` : ''}
        </div>
    `).join('');
    updateAllOnlineStatuses();
}

window.deleteContact = async (contactId) => {
    if(confirm('Удалить контакт?')) {
        await db.ref('users/' + currentUserId + '/contacts/' + contactId).remove();
        await loadContacts();
        if(currentChat.id === contactId) {
            if(contacts.length > 0) {
                switchChat(contacts[0].id, contacts[0].name, contacts[0].isGroup);
            }
        }
    }
};

window.clearChatWithContact = async (contactId) => {
    if(confirm('Очистить весь чат?')) {
        const ids = [currentUserId, contactId].sort();
        const chatPath = `private_messages/${ids[0]}_${ids[1]}`;
        await db.ref(chatPath).remove();
        if(processedMsgIds.has(chatPath)) {
            processedMsgIds.get(chatPath).clear();
        }
        if(currentChat.id === contactId) {
            loadMessages();
        }
        alert('✅ Чат очищен');
    }
};

window.switchChat = async (chatId, chatName, isGroup) => {
    currentChat = { type: isGroup ? 'group' : 'user', id: chatId, name: chatName.replace('👥 ', '') };
    document.getElementById('chatHeaderName').innerText = currentChat.name;
    if(isGroup) {
        document.getElementById('chatHeaderStatus').innerText = '👥 Групповой чат';
    } else {
        const isOnline = await getUserOnlineStatus(chatId);
        document.getElementById('chatHeaderStatus').innerHTML = isOnline ? '🟢 В сети' : '⚫ Не в сети';
    }
    const contact = contacts.find(c => c.id === chatId);
    const avatar = contact?.avatar || getAvatarUrl(currentChat.name, '');
    document.getElementById('chatHeaderAvatar').src = avatar;
    replyingTo = null;
    document.getElementById('replyIndicator').style.display = 'none';
    clearSelection();
    closeSidebar();
    
    if(unreadCounts.has(chatId)) {
        unreadCounts.set(chatId, 0);
        renderContacts();
    }
    
    renderContacts();
    messagesLoaded = false;
    loadMessages();
};

async function searchUser() {
    const query = document.getElementById('searchInput').value.trim().toLowerCase();
    if(!query) { 
        loadContacts(); 
        return; 
    }
    
    let usersData;
    if (userCache.has('allUsersForSearch')) {
        usersData = userCache.get('allUsersForSearch');
    } else {
        const snap = await db.ref('users').once('value');
        usersData = snap.val();
        userCache.set('allUsersForSearch', usersData);
    }
    
    const results = [];
    const existingContactIds = new Set(contacts.map(c => c.id));
    
    for(let id in usersData) {
        const user = usersData[id];
        if(user.name.toLowerCase().includes(query) && 
           user.name !== currentUserName && 
           !user.blocked && 
           !existingContactIds.has(id)) {
            results.push({ id, name: user.name, avatar: user.avatarUrl || '' });
        }
    }
    
    const container = document.getElementById('contactsList');
    if(results.length === 0) {
        container.innerHTML = '<div style="padding:20px; text-align:center; color: var(--other-text);">👤 Пользователь не найден</div>';
    } else {
        container.innerHTML = results.map(u => `
            <div class="contact-item" onclick="addContact('${u.id}', '${u.name.replace(/'/g, "\\'")}')">
                <img class="contact-avatar" src="${getAvatarUrl(u.name, u.avatar)}" alt="${u.name}">
                <div class="contact-info">
                    <div class="contact-name">${escapeHtml(u.name)}</div>
                    <div class="contact-status">➕ Нажмите, чтобы добавить</div>
                </div>
            </div>
        `).join('');
    }
}

window.addContact = async (userId, userName) => {
    const contactsRef = db.ref('users/' + currentUserId + '/contacts');
    const exists = await contactsRef.child(userId).once('value');
    if(!exists.exists()) { 
        const userSnap = await db.ref('users/' + userId).once('value');
        await contactsRef.child(userId).set({ userId, name: userSnap.val().name, avatar: userSnap.val().avatarUrl || '', addedAt: Date.now() }); 
        alert(`✅ ${userName} добавлен`); 
        userCache.delete('allUsersForSearch');
        await loadContacts(); 
        await loadAllUsers();
    } else alert('Уже в контактах');
};

// ==================== ОТПРАВКА СООБЩЕНИЙ ====================
function getChatPath() {
    if(currentChat.type === 'group') return `group_messages/${currentChat.id}`;
    const ids = [currentUserId, currentChat.id].sort();
    return `private_messages/${ids[0]}_${ids[1]}`;
}

async function sendMessageWithMedia(mediaType, mediaUrl, caption, fileData = null, isCircle = false, contactData = null) {
    if(isBlocked) return alert('Вы заблокированы');
    
    const msgData = { 
        userId: currentUserId, 
        name: currentUserName, 
        avatar: currentUserAvatar || '', 
        text: caption || '',
        mediaType, 
        mediaUrl, 
        time: Date.now(), 
        read: false, 
        delivered: false,
        readBy: { [currentUserId]: true }
    };
    
    if(isCircle) msgData.isCircle = true;
    if(fileData) {
        msgData.fileName = fileData.name;
        msgData.fileSize = fileData.size;
        msgData.fileType = fileData.type;
    }
    if(contactData) {
        msgData.contactData = contactData;
        msgData.mediaType = 'contact';
    }
    if(replyingTo) { msgData.replyTo = replyingTo; replyingTo = null; document.getElementById('replyIndicator').style.display = 'none'; }
    
    await db.ref(getChatPath()).push(msgData);
    playSound();
}

async function sendTextMessage() {
    const input = document.getElementById('messageInput');
    const text = input.value.trim();
    if(!text || isBlocked) return;
    
    const msgData = { 
        userId: currentUserId, 
        name: currentUserName, 
        avatar: currentUserAvatar || '', 
        text, 
        time: Date.now(), 
        read: false, 
        delivered: false,
        readBy: { [currentUserId]: true }
    };
    
    if(replyingTo) { msgData.replyTo = replyingTo; replyingTo = null; document.getElementById('replyIndicator').style.display = 'none'; }
    
    await db.ref(getChatPath()).push(msgData);
    input.value = '';
    input.style.height = 'auto';
    playSound();
}

async function sendContact(contactUserId, contactName, contactAvatar) {
    const contactData = {
        userId: contactUserId,
        name: contactName,
        avatar: contactAvatar
    };
    await sendMessageWithMedia('contact', '', '', null, false, contactData);
}

// ==================== ЗАГРУЗКА И ОТОБРАЖЕНИЕ СООБЩЕНИЙ ====================
function loadMessages() {
    const messagesArea = document.getElementById('messagesArea');
    messagesArea.innerHTML = '<div class="messages-loading">📨 Загрузка сообщений...</div>';
    const path = getChatPath();
    const msgsRef = db.ref(path);
    
    if(!processedMsgIds.get(path)) processedMsgIds.set(path, new Set());
    const processed = processedMsgIds.get(path);
    
    msgsRef.off();
    
    let hasMessages = false;
    let messageCount = 0;
    
    msgsRef.orderByChild('time').limitToLast(50).on('child_added', (snap) => {
        if(!processed.has(snap.key)) {
            processed.add(snap.key);
            hasMessages = true;
            messageCount++;
            
            if(!messagesLoaded) {
                messagesArea.innerHTML = '';
                messagesLoaded = true;
            }
            
            renderMessage(snap.key, snap.val(), path);
        }
    });
    
    setTimeout(() => {
        if(!hasMessages && !messagesLoaded) {
            messagesArea.innerHTML = '<div class="no-messages">💬 Нет сообщений. Напишите первое!</div>';
            messagesLoaded = true;
        }
    }, 1500);
    
    msgsRef.on('child_changed', (snap) => updateMessageInUI(snap.key, snap.val()));
    msgsRef.on('child_removed', (snap) => removeMessageFromUI(snap.key));
}

function renderMessage(msgId, msg, chatPath) {
    if(!msg) return;
    if(document.getElementById(`msg-${msgId}`)) return;
    
    const isMe = msg.userId === currentUserId;
    const isSystem = msg.isSystem || msg.userId === 'system';
    const div = document.createElement('div');
    div.className = `message ${isSystem ? 'system-message' : (isMe ? 'my-message' : 'other-message')}`;
    div.id = `msg-${msgId}`;
    div.dataset.msgId = msgId;
    
    let replyHtml = '';
    if(msg.replyTo) {
        replyHtml = `<div class="reply-preview" onclick="scrollToMessage('${msg.replyTo.messageId}')"><i class="fas fa-reply"></i> <strong>${escapeHtml(msg.replyTo.userName)}</strong>: ${escapeHtml(msg.replyTo.text)}</div>`;
    }
    
    let forwardedHtml = '';
    if(msg.forwardedFrom) {
        forwardedHtml = `<div style="font-size:0.65rem; opacity:0.7; margin-bottom:4px;"><i class="fas fa-share"></i> Переслано из ${escapeHtml(msg.forwardedFrom.chatName)}</div>`;
    }
    
    let mediaHtml = '';
    if(msg.mediaType === 'image') {
        mediaHtml = `<img src="${msg.mediaUrl}" class="media-content" onclick="window.open('${msg.mediaUrl}','_blank')" loading="lazy">`;
    } else if(msg.mediaType === 'video') {
        if(msg.isCircle) {
            mediaHtml = `<div class="circle-video"><video controls playsinline><source src="${msg.mediaUrl}"></video></div>`;
        } else {
            mediaHtml = `<video controls class="video-content" playsinline><source src="${msg.mediaUrl}"></video>`;
        }
    } else if(msg.mediaType === 'audio') {
        mediaHtml = `<audio controls src="${msg.mediaUrl}"></audio>`;
    } else if(msg.mediaType === 'file') {
        mediaHtml = `
            <div class="file-attachment" onclick="window.open('${msg.mediaUrl}','_blank')">
                <i class="fas fa-file"></i>
                <div class="file-info">
                    <div class="file-name">${escapeHtml(msg.fileName || 'Файл')}</div>
                    <div class="file-size">${formatFileSize(msg.fileSize || 0)}</div>
                </div>
            </div>
        `;
    } else if(msg.mediaType === 'contact' && msg.contactData) {
        mediaHtml = `
            <div class="contact-card" onclick="showUserProfile('${msg.contactData.userId}', '${escapeHtml(msg.contactData.name)}')">
                <img src="${getAvatarUrl(msg.contactData.name, msg.contactData.avatar)}" alt="${msg.contactData.name}">
                <div class="contact-card-info">
                    <div class="contact-card-name">${escapeHtml(msg.contactData.name)}</div>
                    <div class="contact-card-phone">Контакт</div>
                </div>
                <i class="fas fa-chevron-right" style="opacity:0.5;"></i>
            </div>
        `;
    }
    
    const avatar = msg.avatar || getAvatarUrl(msg.name, '');
    
    const readByCount = msg.readBy ? Object.keys(msg.readBy).length - 1 : 0;
    const readByInfo = isMe && readByCount > 0 && currentChat.type === 'group' ? 
        `<span class="read-by-info" onclick="showReadBy('${msgId}')">👁 ${readByCount}</span>` : '';
    
    let readStatus = '';
    if(isMe && currentChat.type === 'user' && !isSystem) {
        if(msg.read) readStatus = '<span class="read-status read">✓✓ Прочитано</span>';
        else if(msg.delivered) readStatus = '<span class="read-status">✓✓ Доставлено</span>';
        else readStatus = '<span class="read-status">✓ Отправлено</span>';
    }
    
    const checkbox = `<input type="checkbox" class="message-checkbox" onchange="toggleMessageSelection('${msgId}', this.checked)">`;
    const deleteBtn = (isMe && !isSystem) ? `<button class="delete-btn" onclick="deleteMessage('${msgId}')"><i class="fas fa-trash"></i></button>` : '';
    const adminDeleteBtn = (isAdmin && !isMe && !isSystem && currentChat.type === 'group') ? `<button class="admin-delete-btn" onclick="adminDeleteMessage('${msgId}')" title="Удалить как админ"><i class="fas fa-trash"></i></button>` : '';
    const replyBtn = !isSystem ? `<button class="reply-btn" onclick="replyToMsg('${msgId}', '${escapeHtml(msg.name).replace(/'/g, "\\'")}', '${escapeHtml(msg.text || 'Медиа').replace(/'/g, "\\'")}')"><i class="fas fa-reply"></i></button>` : '';
    const forwardBtn = !isSystem ? `<button class="forward-btn" onclick="forwardSingleMessage('${msgId}')"><i class="fas fa-share"></i></button>` : '';
    
    const nameClick = `onclick="event.stopPropagation(); showUserProfile('${msg.userId}', '${escapeHtml(msg.name).replace(/'/g, "\\'")}')"`;
    
    if(isSystem) {
        div.innerHTML = `<div class="bubble">${msg.text}<span class="message-time">${new Date(msg.time).toLocaleTimeString()}</span></div>`;
    } else {
        div.innerHTML = `<div class="bubble">${checkbox}${deleteBtn}${adminDeleteBtn}${replyBtn}${forwardBtn}<div class="message-header"><img class="msg-avatar" src="${avatar}" alt="${msg.name}" ${nameClick}><span class="message-name" ${nameClick}>${escapeHtml(msg.name)}</span></div>${forwardedHtml}${replyHtml}${mediaHtml}${msg.text && !msg.mediaType ? `<div>${escapeHtml(msg.text)}</div>` : ''}<span class="message-time">${new Date(msg.time).toLocaleTimeString()} ${readStatus} ${readByInfo}</span></div>`;
    }
    
    requestAnimationFrame(() => {
        const messagesArea = document.getElementById('messagesArea');
        messagesArea.appendChild(div);
        
        const isScrolledToBottom = messagesArea.scrollHeight - messagesArea.scrollTop - messagesArea.clientHeight < 100;
        if(isScrolledToBottom) {
            messagesArea.scrollTop = messagesArea.scrollHeight;
        }
    });
    
    if(!isMe && !msg.read && currentChat.type === 'user' && !isSystem) {
        const updates = { read: true };
        if(!msg.readBy) msg.readBy = {};
        msg.readBy[currentUserId] = true;
        updates.readBy = msg.readBy;
        db.ref(`${chatPath}/${msgId}`).update(updates);
    } else if(!isMe && currentChat.type === 'group' && !isSystem) {
        if(!msg.readBy) msg.readBy = {};
        if(!msg.readBy[currentUserId]) {
            msg.readBy[currentUserId] = true;
            db.ref(`${chatPath}/${msgId}/readBy/${currentUserId}`).set(true);
        }
    }
    
    if(!isMe && document.hidden && Notification.permission === 'granted' && !isSystem) { 
        playSound(); 
        new Notification(msg.name, { body: msg.text || 'Новое сообщение', icon: avatar, tag: 'msg' });
    }
    
    if(!isMe && (currentChat.id !== (currentChat.type === 'group' ? currentChat.id : msg.userId))) {
        const chatId = currentChat.type === 'group' ? currentChat.id : msg.userId;
        const current = unreadCounts.get(chatId) || 0;
        unreadCounts.set(chatId, current + 1);
        renderContacts();
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
    selectedMessages.delete(msgId);
    updateSelectionBar();
}

// ==================== ВЫДЕЛЕНИЕ СООБЩЕНИЙ ====================
window.toggleMessageSelection = function(msgId, checked) {
    if(checked) {
        selectedMessages.add(msgId);
        document.getElementById(`msg-${msgId}`).classList.add('selected');
    } else {
        selectedMessages.delete(msgId);
        document.getElementById(`msg-${msgId}`).classList.remove('selected');
    }
    updateSelectionBar();
};

function updateSelectionBar() {
    const bar = document.getElementById('selectionBar');
    const countSpan = document.getElementById('selectedCount');
    const count = selectedMessages.size;
    
    if(count > 0) {
        bar.classList.add('visible');
        countSpan.textContent = `${count} ${getMessageWord(count)}`;
    } else {
        bar.classList.remove('visible');
    }
}

function getMessageWord(count) {
    if(count % 10 === 1 && count % 100 !== 11) return 'сообщение';
    if([2, 3, 4].includes(count % 10) && ![12, 13, 14].includes(count % 100)) return 'сообщения';
    return 'сообщений';
}

function clearSelection() {
    selectedMessages.forEach(msgId => {
        const el = document.getElementById(`msg-${msgId}`);
        if(el) {
            el.classList.remove('selected');
            const cb = el.querySelector('.message-checkbox');
            if(cb) cb.checked = false;
        }
    });
    selectedMessages.clear();
    updateSelectionBar();
}

window.deleteSelectedMessages = async function() {
    if(selectedMessages.size === 0) return;
    if(!confirm(`Удалить ${selectedMessages.size} ${getMessageWord(selectedMessages.size)}?`)) return;
    
    for(let msgId of selectedMessages) {
        await db.ref(`${getChatPath()}/${msgId}`).remove();
    }
    clearSelection();
};

window.forwardSelectedMessages = function() {
    if(selectedMessages.size === 0) return;
    showForwardModal(Array.from(selectedMessages));
};

window.forwardSingleMessage = function(msgId) {
    showForwardModal([msgId]);
};

// ==================== ПЕРЕСЫЛКА ====================
async function forwardMessages(messageIds, targetChatId, targetChatName, isGroup) {
    for(let msgId of messageIds) {
        const msg = await db.ref(getChatPath() + '/' + msgId).once('value');
        const msgData = msg.val();
        if(msgData) {
            const forwardData = {
                ...msgData,
                userId: currentUserId,
                name: currentUserName,
                avatar: currentUserAvatar,
                time: Date.now(),
                read: false,
                delivered: false,
                readBy: { [currentUserId]: true },
                forwardedFrom: { chatId: currentChat.id, chatName: currentChat.name }
            };
            delete forwardData.readBy;
            forwardData.readBy = { [currentUserId]: true };
            
            const targetPath = isGroup ? `group_messages/${targetChatId}` : `private_messages/${[currentUserId, targetChatId].sort().join('_')}`;
            await db.ref(targetPath).push(forwardData);
        }
    }
    playSound();
}

async function showForwardModal(messageIds) {
    const targets = contacts.filter(c => !c.isGroup);
    
    const container = document.getElementById('forwardContactsList');
    container.innerHTML = targets.map(t => `
        <div class="forward-contact" onclick="forwardTo('${t.id}', '${t.name.replace(/'/g, "\\'")}', false, ${JSON.stringify(messageIds)})">
            <img src="${getAvatarUrl(t.name, t.avatar)}" alt="${t.name}">
            <div class="forward-contact-info">
                <div class="forward-contact-name">${escapeHtml(t.name)}</div>
            </div>
        </div>
    `).join('');
    
    if(targets.length === 0) {
        container.innerHTML = '<div style="padding:20px; text-align:center; color: var(--other-text);">📭 Нет контактов для пересылки</div>';
    }
    
    document.getElementById('forwardModal').classList.add('visible');
}

window.forwardTo = async function(targetId, targetName, isGroup, messageIds) {
    await forwardMessages(messageIds, targetId, targetName, isGroup);
    closeForwardModal();
    clearSelection();
    alert(`✅ Переслано в ${targetName}`);
};

window.closeForwardModal = function() {
    document.getElementById('forwardModal').classList.remove('visible');
};

// ==================== УДАЛЕНИЕ, ОТВЕТ, ПРОСМОТР ПРОЧИТАВШИХ ====================
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

window.showReadBy = async function(msgId) {
    const msg = await db.ref(`${getChatPath()}/${msgId}`).once('value');
    const msgData = msg.val();
    if(!msgData || !msgData.readBy) return;
    
    const readByUsers = Object.keys(msgData.readBy).filter(id => id !== currentUserId);
    const container = document.getElementById('readByList');
    
    if(readByUsers.length === 0) {
        container.innerHTML = '<p style="color: var(--other-text);">👀 Никто ещё не прочитал</p>';
    } else {
        let html = '';
        for(let userId of readByUsers) {
            const userSnap = await db.ref('users/' + userId).once('value');
            if(userSnap.exists()) {
                const user = userSnap.val();
                html += `
                    <div class="read-by-user">
                        <img src="${getAvatarUrl(user.name, user.avatarUrl)}" alt="${user.name}">
                        <span>${escapeHtml(user.name)}</span>
                    </div>
                `;
            }
        }
        container.innerHTML = html;
    }
    
    document.getElementById('readByModal').classList.add('visible');
};

window.closeReadByModal = function() {
    document.getElementById('readByModal').classList.remove('visible');
};

// ==================== DRAG & DROP ====================
function setupDragDrop() {
    const dropOverlay = document.getElementById('dropOverlay');
    
    document.addEventListener('dragover', (e) => {
        e.preventDefault();
        dropOverlay.classList.add('visible');
    });
    
    document.addEventListener('dragleave', (e) => {
        e.preventDefault();
        dropOverlay.classList.remove('visible');
    });
    
    document.addEventListener('drop', async (e) => {
        e.preventDefault();
        dropOverlay.classList.remove('visible');
        
        const files = e.dataTransfer.files;
        if(files.length > 0) {
            handleFiles(files);
        }
    });
    
    document.addEventListener('paste', async (e) => {
        const items = e.clipboardData.items;
        for(let item of items) {
            if(item.type.indexOf('image') !== -1) {
                const file = item.getAsFile();
                handleFiles([file]);
                break;
            }
        }
    });
}

function handleFiles(files) {
    for(let file of files) {
        const reader = new FileReader();
        reader.onload = (e) => {
            const isImage = file.type.startsWith('image/');
            const isVideo = file.type.startsWith('video/');
            const mediaType = isImage ? 'image' : (isVideo ? 'video' : 'file');
            
            pendingMedia = {
                data: e.target.result,
                type: mediaType,
                file: file
            };
            
            showMediaPreview(file, isImage ? e.target.result : null);
        };
        reader.readAsDataURL(file);
    }
}

function showMediaPreview(file, previewUrl) {
    const preview = document.getElementById('mediaPreview');
    const previewImage = document.getElementById('previewImage');
    const previewFileIcon = document.getElementById('previewFileIcon');
    const previewName = document.getElementById('previewName');
    const previewSize = document.getElementById('previewSize');
    const captionInput = document.getElementById('mediaCaption');
    
    if(file.type.startsWith('image/')) {
        previewImage.src = previewUrl;
        previewImage.style.display = 'block';
        previewFileIcon.style.display = 'none';
    } else {
        previewImage.style.display = 'none';
        previewFileIcon.style.display = 'block';
        if(file.type.startsWith('video/')) {
            previewFileIcon.className = 'fas fa-video';
        } else if(file.type.startsWith('audio/')) {
            previewFileIcon.className = 'fas fa-music';
        } else {
            previewFileIcon.className = 'fas fa-file';
        }
    }
    
    previewName.textContent = file.name;
    previewSize.textContent = formatFileSize(file.size);
    captionInput.value = '';
    preview.classList.add('visible');
}

// ==================== ЗАПИСЬ ====================
async function startVoiceRecording() {
    if(isVoiceRecording && voiceRecorder?.state === 'recording') { 
        voiceRecorder.stop(); 
        return; 
    }
    try {
        const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
        voiceStream = stream; 
        voiceRecorder = new MediaRecorder(stream); 
        voiceChunks = [];
        
        voiceRecorder.ondataavailable = e => { if(e.data.size > 0) voiceChunks.push(e.data); };
        voiceRecorder.onstop = () => { 
            const blob = new Blob(voiceChunks, {type:'audio/webm'}); 
            const r = new FileReader(); 
            r.onload = e => sendMessageWithMedia('audio', e.target.result, ''); 
            r.readAsDataURL(blob); 
            if(voiceStream) voiceStream.getTracks().forEach(t=>t.stop()); 
            isVoiceRecording = false; 
            document.getElementById('recordingIndicator').classList.remove('visible');
            stopRecordingTimer();
        };
        
        voiceRecorder.start(); 
        isVoiceRecording = true; 
        
        document.getElementById('recordingIndicator').classList.add('visible');
        document.querySelector('#recordingIndicator i').className = 'fas fa-microphone';
        startRecordingTimer();
        
        setTimeout(() => { 
            if(isVoiceRecording && voiceRecorder?.state === 'recording') {
                voiceRecorder.stop(); 
            }
        }, 15000);
    } catch(e) { 
        alert('Нет доступа к микрофону'); 
    }
}

async function startCircleRecording() {
    if(isCircleRecording) { 
        if(circleRecorder?.state === 'recording') circleRecorder.stop(); 
        isCircleRecording = false; 
        if(circleStream) circleStream.getTracks().forEach(t=>t.stop()); 
        document.getElementById('cameraPreview').classList.remove('visible');
        document.getElementById('recordingIndicator').classList.remove('visible');
        stopRecordingTimer();
    } else { 
        try {
            const stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: "user" }, audio: true }); 
            circleStream = stream; 
            
            const previewVideo = document.getElementById('previewVideo');
            previewVideo.srcObject = stream;
            document.getElementById('cameraPreview').classList.add('visible');
            
            circleRecorder = new MediaRecorder(stream, {mimeType: 'video/webm'}); 
            circleChunks = []; 
            
            circleRecorder.ondataavailable = e => { if(e.data.size > 0) circleChunks.push(e.data); }; 
            circleRecorder.onstop = () => { 
                const blob = new Blob(circleChunks, {type:'video/webm'}); 
                const r = new FileReader(); 
                r.onload = e => sendMessageWithMedia('video', e.target.result, '', null, true); 
                r.readAsDataURL(blob); 
                if(circleStream) circleStream.getTracks().forEach(t=>t.stop()); 
                circleStream = null; 
                document.getElementById('cameraPreview').classList.remove('visible');
                document.getElementById('recordingIndicator').classList.remove('visible');
                stopRecordingTimer();
            }; 
            
            circleRecorder.start(); 
            isCircleRecording = true; 
            
            document.getElementById('recordingIndicator').classList.add('visible');
            document.querySelector('#recordingIndicator i').className = 'fas fa-video';
            startRecordingTimer();
            
            setTimeout(() => { 
                if(isCircleRecording && circleRecorder?.state === 'recording') { 
                    circleRecorder.stop(); 
                } 
            }, 30000);
        } catch(e) { 
            alert('Нет доступа к камере'); 
        }
    }
}

// ==================== ПРОФИЛЬ ====================
function setupProfile() {
    document.getElementById('settingsBtn').onclick = () => {
        document.getElementById('avatarPreview').src = getAvatarUrl(currentUserName, currentUserAvatar);
        document.getElementById('modalName').value = currentUserName;
        document.getElementById('modalBio').value = currentUserBio || '';
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
            const newBio = document.getElementById('modalBio').value.trim();
            const file = document.getElementById('modalAvatar').files[0];
            
            if(newName && newName !== currentUserName) {
                const check = await db.ref('users').orderByChild('name').equalTo(newName).once('value');
                let taken = false;
                check.forEach(c => { if(c.key !== currentUserId) taken = true; });
                if(taken) throw new Error('Ник занят');
            }
            
            const updates = {};
            if(newName && newName !== currentUserName) updates.name = newName;
            if(newBio !== currentUserBio) updates.bio = newBio;
            
            if(file) {
                const reader = new FileReader();
                const avatarBase64 = await new Promise((resolve, reject) => {
                    reader.onload = (e) => resolve(e.target.result);
                    reader.onerror = reject;
                    reader.readAsDataURL(file);
                });
                updates.avatarUrl = avatarBase64;
                currentUserAvatar = avatarBase64;
            }
            
            if(Object.keys(updates).length > 0) {
                await db.ref('users/' + currentUserId).update(updates);
                
                if(updates.name) {
                    currentUserName = updates.name;
                    localStorage.setItem('userName', updates.name);
                    document.getElementById('sidebarName').innerText = updates.name;
                    isAdmin = currentUserName === 'DaniksGames';
                }
                if(updates.bio) currentUserBio = updates.bio;
                if(updates.avatarUrl) {
                    document.getElementById('sidebarAvatar').src = updates.avatarUrl;
                    avatarCache.delete(currentUserName);
                }
                
                alert('✅ Профиль успешно обновлён!');
                document.getElementById('profileModal').style.display = 'none';
            }
            
            await loadContacts();
            
        } catch(e) { 
            alert('Ошибка: ' + e.message); 
        } finally { 
            saveBtn.innerText = originalText; 
            saveBtn.disabled = false; 
        }
    };
}

// ==================== АДМИН-ПАНЕЛЬ ====================
window.sendSystemNotification = async function(type) {
    if(!isAdmin) return;
    let text = '';
    switch(type) {
        case 'update': text = '🔄 Установка обновления... Пожалуйста, подождите.'; break;
        case 'success': text = '✅ Обновление успешно установлено! Приятного использования!'; break;
        case 'error': text = '❌ Ошибка установки обновления. Попробуйте позже.'; break;
    }
    await db.ref('group_messages/global').push({
        userId: 'system', name: 'Система', avatar: '', text, time: Date.now(), isSystem: true
    });
    playSound();
};

window.sendCustomNotification = async function() {
    if(!isAdmin) return;
    const input = document.getElementById('customNotifyText');
    const text = input.value.trim();
    if(!text) return;
    await db.ref('group_messages/global').push({
        userId: 'system', name: 'Система', avatar: '', text: '📢 ' + text, time: Date.now(), isSystem: true
    });
    input.value = '';
    playSound();
};

async function loadAdminUsers() {
    const users = await db.ref('users').once('value');
    const container = document.getElementById('usersList');
    container.innerHTML = '<h4 style="color:white; margin-bottom:15px;">👥 Пользователи:</h4>';
    for(let id in users.val()) {
        const u = users.val()[id];
        container.innerHTML += `<div class="user-item">
            <div style="flex:1;">
                <strong>${escapeHtml(u.name)}</strong><br>
                <span style="font-size:0.8rem; opacity:0.8;">🔑 ${escapeHtml(u.password)}</span><br>
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

window.adminEditUser = async (userId, currentName, currentAvatarUrl) => {
    const newName = prompt('Новый никнейм:', currentName);
    if(newName && newName !== currentName) {
        const check = await db.ref('users').orderByChild('name').equalTo(newName).once('value');
        let exists = false;
        check.forEach(c => { if(c.key !== userId) exists = true; });
        if(exists) { alert('Ник занят'); return; }
        await db.ref('users/' + userId).update({ name: newName });
        alert('Ник обновлён');
    }
    
    const newPass = prompt('Новый пароль (оставьте пустым, чтобы не менять):');
    if(newPass && newPass.trim()) {
        await db.ref('users/' + userId).update({ password: newPass.trim() });
        alert('Пароль обновлён');
    }
    
    loadAdminUsers();
};

window.toggleBlock = async (id, blocked) => { 
    await db.ref('users/' + id).update({ blocked: !blocked }); 
    loadAdminUsers(); 
};

window.deleteAccount = async (id) => { 
    if(confirm('Удалить аккаунт навсегда?')) { 
        await db.ref('users/' + id).remove();
        await db.ref('users/' + currentUserId + '/contacts/' + id).remove();
        userCache.clear();
        loadAdminUsers(); 
        await loadContacts();
    } 
};

function setupAdmin() {
    document.getElementById('adminPanelBtn').onclick = async () => { 
        if(isAdmin) { 
            await loadAdminUsers(); 
            document.getElementById('adminPanel').style.display = 'flex'; 
        } else {
            alert('Доступ только у DaniksGames'); 
        }
    };
    document.getElementById('closeAdminBtn').onclick = () => document.getElementById('adminPanel').style.display = 'none';
    document.getElementById('clearChatBtn').onclick = async () => { 
        if(isAdmin && confirm('Очистить общий чат?')) {
            await db.ref('group_messages/global').remove();
            alert('Чат очищен');
        }
    };
}

// ==================== ТЕМА И ИНТЕРФЕЙС ====================
function setupTheme() { 
    if(localStorage.getItem('theme') === 'dark') document.body.classList.add('dark'); 
    document.getElementById('themeToggle').onclick = () => { 
        document.body.classList.toggle('dark'); 
        localStorage.setItem('theme', document.body.classList.contains('dark') ? 'dark' : 'light'); 
    }; 
}

function closeSidebar() {
    document.getElementById('sidebar').classList.remove('open');
    document.getElementById('sidebarOverlay').classList.remove('visible');
}

function openSidebar() {
    document.getElementById('sidebar').classList.add('open');
    document.getElementById('sidebarOverlay').classList.add('visible');
}

function setupAttachments() {
    const attachBtn = document.getElementById('attachBtn');
    const attachMenu = document.getElementById('attachMenu');
    
    attachBtn.onclick = (e) => {
        e.stopPropagation();
        attachMenu.classList.toggle('visible');
    };
    
    document.addEventListener('click', (e) => {
        if(!attachMenu.contains(e.target) && e.target !== attachBtn) {
            attachMenu.classList.remove('visible');
        }
    });
    
    document.getElementById('attachPhotoBtn').onclick = () => {
        attachMenu.classList.remove('visible');
        document.getElementById('photoInput').click();
    };
    document.getElementById('attachCameraBtn').onclick = () => {
        attachMenu.classList.remove('visible');
        document.getElementById('cameraCaptureInput').click();
    };
    document.getElementById('attachVideoBtn').onclick = () => {
        attachMenu.classList.remove('visible');
        document.getElementById('videoFileInput').click();
    };
    document.getElementById('attachFileBtn').onclick = () => {
        attachMenu.classList.remove('visible');
        document.getElementById('fileInput').click();
    };
    document.getElementById('attachContactBtn').onclick = async () => {
        attachMenu.classList.remove('visible');
        await loadAllUsers();
        const contactsList = allUsers.filter(u => u.id !== currentUserId);
        let contactHtml = '<div style="padding:10px;"><h4>Выберите контакт для отправки:</h4>';
        contactsList.forEach(c => {
            contactHtml += `<div class="forward-contact" onclick="sendContact('${c.id}', '${c.name.replace(/'/g, "\\'")}', '${c.avatar}'); closeForwardModal();">
                <img src="${getAvatarUrl(c.name, c.avatar)}">
                <div class="forward-contact-info"><div class="forward-contact-name">${escapeHtml(c.name)}</div></div>
            </div>`;
        });
        contactHtml += '<button class="forward-cancel" onclick="closeForwardModal()">Отмена</button></div>';
        document.getElementById('forwardContactsList').innerHTML = contactHtml;
        document.getElementById('forwardModal').classList.add('visible');
        window.forwardTo = null;
    };
    document.getElementById('attachCircleBtn').onclick = () => {
        attachMenu.classList.remove('visible');
        startCircleRecording();
    };
    document.getElementById('attachVoiceBtn').onclick = () => {
        attachMenu.classList.remove('visible');
        startVoiceRecording();
    };
    
    document.getElementById('cancelMediaPreview').onclick = () => {
        document.getElementById('mediaPreview').classList.remove('visible');
        pendingMedia = null;
    };
}

function setupFileInputs() {
    document.getElementById('photoInput').onchange = (e) => { 
        if(e.target.files[0]) handleFiles([e.target.files[0]]); 
        e.target.value = ''; 
    };
    document.getElementById('cameraCaptureInput').onchange = (e) => { 
        if(e.target.files[0]) handleFiles([e.target.files[0]]); 
        e.target.value = ''; 
    };
    document.getElementById('videoFileInput').onchange = (e) => { 
        if(e.target.files[0]) handleFiles([e.target.files[0]]); 
        e.target.value = ''; 
    };
    document.getElementById('fileInput').onchange = (e) => { 
        if(e.target.files[0]) handleFiles([e.target.files[0]]); 
        e.target.value = ''; 
    };
}

// ==================== ИНИЦИАЛИЗАЦИЯ ====================
async function initAll() {
    await loadContacts();
    setupProfile();
    setupTheme();
    setupAdmin();
    setupAttachments();
    setupFileInputs();
    setupDragDrop();
    
    document.getElementById('menuToggle').onclick = openSidebar;
    document.getElementById('sidebarOverlay').onclick = closeSidebar;
    document.getElementById('createGroupBtnSidebar').onclick = showCreateGroupModal;
    
    const textarea = document.getElementById('messageInput');
    textarea.addEventListener('input', function() {
        this.style.height = 'auto';
        this.style.height = Math.min(this.scrollHeight, 150) + 'px';
    });
    
    document.getElementById('sendBtn').onclick = async () => {
        if(pendingMedia) {
            const caption = document.getElementById('mediaCaption').value;
            const fileData = {
                name: pendingMedia.file.name,
                size: pendingMedia.file.size,
                type: pendingMedia.file.type
            };
            await sendMessageWithMedia(pendingMedia.type, pendingMedia.data, caption, fileData);
            document.getElementById('mediaPreview').classList.remove('visible');
            pendingMedia = null;
        } else {
            sendTextMessage();
        }
    };
    
    textarea.addEventListener('keydown', (e) => { 
        if(e.key === 'Enter' && !e.shiftKey) { 
            e.preventDefault(); 
            document.getElementById('sendBtn').click(); 
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
        userCache.clear();
        location.reload(); 
    };
    
    document.getElementById('forwardSelectedBtn').onclick = forwardSelectedMessages;
    document.getElementById('deleteSelectedBtn').onclick = deleteSelectedMessages;
    document.getElementById('cancelSelectionBtn').onclick = clearSelection;
    
    if(contacts.length > 0) {
        switchChat(contacts[0].id, contacts[0].name, contacts[0].isGroup);
    }
    
    if(Notification.permission === 'default') Notification.requestPermission();
    
    if(currentUserId) {
        await db.ref('users/' + currentUserId).update({ online: true, lastSeen: Date.now() });
    }
    
    if(onlineStatusInterval) clearInterval(onlineStatusInterval);
    onlineStatusInterval = setInterval(updateOnlineStatus, 30000);
    setInterval(updateAllOnlineStatuses, 10000);
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
            currentUserBio = snap.val().bio || '';
            isBlocked = snap.val().blocked || false;
            isAdmin = currentUserName === 'DaniksGames';
            
            await db.ref('users/' + uid).update({ online: true, lastSeen: Date.now() });
            
            document.getElementById('authOverlay').style.display = 'none';
            document.getElementById('appContainer').style.display = 'flex';
            document.getElementById('sidebarName').innerText = currentUserName;
            document.getElementById('sidebarAvatar').src = getAvatarUrl(currentUserName, currentUserAvatar);
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
