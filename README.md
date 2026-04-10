<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <meta name="theme-color" content="#4a6cf7">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <title>xaMOff Messenger | Полная версия</title>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-database-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, sans-serif; -webkit-tap-highlight-color: transparent; }
        
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
            --safe-area-top: env(safe-area-inset-top, 0px);
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
            position: fixed;
            width: 100%;
            height: 100%;
            overflow: hidden;
        }
        
        /* Анимации для модальных окон */
        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }
        
        @keyframes fadeOut {
            from { opacity: 1; }
            to { opacity: 0; }
        }
        
        @keyframes slideInUp {
            from {
                opacity: 0;
                transform: translateY(30px);
            }
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }
        
        @keyframes slideOutDown {
            from {
                opacity: 1;
                transform: translateY(0);
            }
            to {
                opacity: 0;
                transform: translateY(30px);
            }
        }
        
        @keyframes fadeInUp {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }
        
        @keyframes slideInLeft {
            from { transform: translateX(-100%); opacity: 0; }
            to { transform: translateX(0); opacity: 1; }
        }
        
        .modal, .auth-overlay, .forward-modal, .read-by-modal, .admin-panel {
            animation: fadeIn 0.25s ease-out;
        }
        
        .modal.closing, .auth-overlay.closing, .forward-modal.closing, .read-by-modal.closing, .admin-panel.closing {
            animation: fadeOut 0.2s ease-in;
        }
        
        .modal-content, .forward-content, .read-by-content, .auth-card {
            animation: slideInUp 0.3s cubic-bezier(0.2, 0.9, 0.4, 1.1);
        }
        
        .modal.closing .modal-content, 
        .forward-modal.closing .forward-content, 
        .read-by-modal.closing .read-by-content, 
        .auth-overlay.closing .auth-card {
            animation: slideOutDown 0.2s ease-in;
        }
        
        .message {
            animation: fadeInUp 0.2s ease;
        }
        
        .sidebar {
            transition: left 0.3s cubic-bezier(0.2, 0.9, 0.4, 1.1);
        }
        
        /* Мобильная оптимизация */
        @media (max-width: 768px) {
            body { padding: 0; align-items: flex-start; }
            .app-container { border-radius: 0 !important; height: 100vh !important; width: 100% !important; max-width: 100% !important; }
            .sidebar { position: fixed !important; left: -100% !important; top: 0 !important; height: 100vh !important; width: 85% !important; max-width: 320px !important; z-index: 2000 !important; transition: left 0.3s cubic-bezier(0.2, 0.9, 0.4, 1.1) !important; box-shadow: 2px 0 10px rgba(0,0,0,0.1) !important; }
            .sidebar.open { left: 0 !important; }
            .chat-main { width: 100% !important; }
            .message { max-width: 90% !important; }
            .circle-video { width: 140px !important; height: 140px !important; }
            .menu-toggle { display: flex !important; }
            .chat-header { padding: 12px !important; min-height: 60px; }
            .chat-header-avatar { width: 40px !important; height: 40px !important; }
            .icon-btn { width: 40px !important; height: 40px !important; font-size: 1rem !important; }
            .input-area { padding: 10px 12px !important; padding-bottom: calc(10px + var(--safe-area-bottom)) !important; }
            .message-input { padding: 12px 16px !important; font-size: 16px !important; min-height: 44px !important; max-height: 120px !important; }
            .send-btn, .attach-btn { width: 44px !important; height: 44px !important; font-size: 1.2rem !important; min-width: 44px; }
            .attach-menu { bottom: 70px !important; left: 10px !important; right: 10px !important; max-width: calc(100% - 20px); }
            .messages-area { padding: 12px !important; }
            .auth-card, .modal-content, .forward-content, .read-by-content { width: 90% !important; padding: 24px 20px !important; border-radius: 24px !important; margin: 20px; max-height: 80vh !important; overflow-y: auto !important; }
            .admin-panel-content { padding: 20px !important; padding-top: 70px !important; }
        }
        
        @media (min-width: 769px) {
            .menu-toggle { display: none !important; }
            .sidebar-overlay { display: none !important; }
            .attach-menu { left: 50%; transform: translateX(-50%); min-width: 400px; }
        }
        
        .app-container {
            width: 100%;
            max-width: 1300px;
            height: 100vh;
            background: var(--chat-bg);
            display: flex;
            overflow: hidden;
            box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
        }
        
        .sidebar {
            width: 320px;
            background: var(--sidebar-bg);
            border-right: 1px solid var(--input-border);
            display: flex;
            flex-direction: column;
        }
        
        .sidebar-header {
            background: var(--header-bg);
            color: white;
            padding: 20px 16px;
            padding-top: calc(20px + var(--safe-area-top));
        }
        
        .user-info-row {
            display: flex;
            align-items: center;
            gap: 12px;
            margin-top: 16px;
            padding-top: 12px;
            border-top: 1px solid rgba(255, 255, 255, 0.2);
        }
        
        .user-info-row img {
            width: 44px;
            height: 44px;
            border-radius: 50%;
            object-fit: cover;
        }
        
        .search-box {
            padding: 12px;
            border-bottom: 1px solid var(--input-border);
        }
        
        .search-box input {
            width: 100%;
            padding: 12px 16px;
            border-radius: 25px;
            border: 1px solid var(--input-border);
            background: var(--input-bg);
            color: var(--other-text);
            outline: none;
            font-size: 16px;
        }
        
        .contacts-list {
            flex: 1;
            overflow-y: auto;
            -webkit-overflow-scrolling: touch;
        }
        
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
        
        .contact-avatar {
            width: 52px;
            height: 52px;
            border-radius: 50%;
            object-fit: cover;
        }
        
        .avatar-wrapper { position: relative; }
        
        .online-badge {
            position: absolute;
            bottom: 2px;
            right: 2px;
            width: 14px;
            height: 14px;
            border-radius: 50%;
            border: 2px solid var(--sidebar-bg);
        }
        
        .online-badge.online { background: var(--online-color); }
        .online-badge.offline { background: var(--offline-color); }
        
        .contact-info {
            flex: 1;
            min-width: 0;
        }
        
        .contact-name {
            font-weight: 600;
            color: var(--other-text);
            display: flex;
            justify-content: space-between;
            align-items: center;
            font-size: 1rem;
        }
        
        .contact-status {
            font-size: 0.75rem;
            opacity: 0.8;
            margin-top: 4px;
            color: var(--other-text);
        }
        
        body.dark .contact-status {
            color: #e2e8f0;
        }
        
        .unread-badge {
            background: var(--unread-badge);
            color: white;
            border-radius: 20px;
            padding: 2px 8px;
            font-size: 0.75rem;
            font-weight: bold;
            min-width: 20px;
            text-align: center;
        }
        
        .contact-actions {
            display: flex;
            gap: 8px;
        }
        
        .contact-actions button {
            background: rgba(74, 108, 247, 0.1);
            border: none;
            color: var(--icon-color);
            cursor: pointer;
            padding: 6px;
            border-radius: 50%;
            width: 32px;
            height: 32px;
            font-size: 0.9rem;
            transition: all 0.15s;
        }
        
        .contact-actions button:active {
            background: rgba(74, 108, 247, 0.2);
            transform: scale(0.95);
        }
        
        .chat-main {
            flex: 1;
            display: flex;
            flex-direction: column;
            background: var(--chat-bg);
        }
        
        .chat-header {
            background: var(--header-bg);
            color: white;
            padding: 12px 16px;
            padding-top: calc(12px + var(--safe-area-top));
            display: flex;
            align-items: center;
            gap: 12px;
        }
        
        .chat-header-avatar {
            width: 44px;
            height: 44px;
            border-radius: 50%;
            object-fit: cover;
        }
        
        .chat-header-info {
            flex: 1;
            min-width: 0;
        }
        
        .chat-header-name {
            font-weight: 600;
            font-size: 1.1rem;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
        }
        
        .chat-header-status {
            font-size: 0.7rem;
            opacity: 0.8;
        }
        
        .chat-header-buttons {
            display: flex;
            gap: 8px;
        }
        
        .icon-btn {
            background: rgba(255, 255, 255, 0.2);
            border: none;
            width: 40px;
            height: 40px;
            border-radius: 50%;
            cursor: pointer;
            color: white;
            font-size: 1rem;
            transition: all 0.15s;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        
        .icon-btn:active {
            transform: scale(0.95);
            background: rgba(255, 255, 255, 0.3);
        }
        
        .messages-area {
            flex: 1;
            overflow-y: auto;
            padding: 16px;
            display: flex;
            flex-direction: column;
            gap: 12px;
            background: var(--message-area-bg);
            -webkit-overflow-scrolling: touch;
        }
        
        .message {
            display: flex;
            max-width: 80%;
            position: relative;
            scroll-margin-top: 80px;
        }
        
        .my-message { align-self: flex-end; justify-content: flex-end; }
        .other-message { align-self: flex-start; }
        .system-message { align-self: center; max-width: 90%; }
        
        .bubble {
            padding: 10px 14px;
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
        
        .my-message .bubble {
            background: var(--my-bubble);
            color: var(--my-text);
            border-bottom-right-radius: 4px;
        }
        
        .message-header {
            display: flex;
            align-items: center;
            gap: 8px;
            margin-bottom: 6px;
            font-size: 0.75rem;
        }
        
        .msg-avatar {
            width: 24px;
            height: 24px;
            border-radius: 50%;
            object-fit: cover;
        }
        
        .message-name { font-weight: 600; }
        
        .reply-preview {
            font-size: 0.7rem;
            background: rgba(0, 0, 0, 0.08);
            padding: 6px 10px;
            border-radius: 12px;
            margin-bottom: 6px;
            border-left: 3px solid var(--icon-color);
            cursor: pointer;
        }
        
        .media-content {
            max-width: 200px;
            max-height: 200px;
            border-radius: 12px;
            margin-top: 6px;
            cursor: pointer;
        }
        
        .video-content {
            max-width: 200px;
            max-height: 200px;
            border-radius: 12px;
            margin-top: 6px;
        }
        
        .circle-video {
            width: 180px;
            height: 180px;
            border-radius: 50%;
            overflow: hidden;
            margin-top: 6px;
            background: #000;
        }
        
        .circle-video video {
            width: 100%;
            height: 100%;
            object-fit: cover;
        }
        
        audio {
            max-width: 180px;
            height: 36px;
            margin-top: 6px;
        }
        
        .file-attachment {
            display: flex;
            align-items: center;
            gap: 10px;
            padding: 10px 14px;
            background: rgba(0, 0, 0, 0.05);
            border-radius: 12px;
            margin-top: 6px;
            cursor: pointer;
        }
        
        .message-time {
            font-size: 0.55rem;
            opacity: 0.7;
            margin-top: 4px;
            display: flex;
            align-items: center;
            gap: 4px;
        }
        
        .read-status.read {
            color: var(--read-color);
            font-weight: bold;
        }
        
        .read-by-info {
            font-size: 0.6rem;
            opacity: 0.7;
            cursor: pointer;
            margin-left: 4px;
        }
        
        .delete-btn, .reply-btn, .forward-btn, .admin-delete-btn {
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
        }
        
        .delete-btn { right: -8px; background: #ef4444; }
        .reply-btn { left: -8px; background: #8b5cf6; }
        .forward-btn { left: 24px; background: #10b981; }
        .admin-delete-btn { top: 20px; right: -8px; background: #dc2626; }
        
        @media (hover: hover) {
            .message:hover .delete-btn,
            .message:hover .reply-btn,
            .message:hover .forward-btn,
            .message:hover .admin-delete-btn { opacity: 1; }
        }
        
        @media (hover: none) {
            .message:active .delete-btn,
            .message:active .reply-btn,
            .message:active .forward-btn,
            .message:active .admin-delete-btn { opacity: 1; }
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
        .message.selected .message-checkbox { opacity: 1; }
        
        .message.selected {
            background: var(--selected-message);
            border-radius: 12px;
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
            box-shadow: 0 4px 20px rgba(0, 0, 0, 0.3);
            z-index: 1500;
        }
        
        .selection-bar.visible { display: flex; }
        
        .selection-bar button {
            background: rgba(255, 255, 255, 0.2);
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
            background: rgba(255, 255, 255, 0.3);
            transform: scale(0.95);
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
        
        .media-preview.visible { display: flex; }
        
        .input-area {
            padding: 12px 16px;
            background: var(--input-bg);
            border-top: 1px solid var(--input-border);
            position: relative;
        }
        
        .input-row {
            display: flex;
            gap: 10px;
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
            min-height: 44px;
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
            flex-shrink: 0;
        }
        
        .attach-btn:active {
            transform: scale(0.9);
            background: rgba(74, 108, 247, 0.1);
        }
        
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
            box-shadow: 0 4px 20px rgba(0, 0, 0, 0.15);
            z-index: 100;
        }
        
        .attach-menu.visible { display: grid; }
        
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
        
        .attach-menu-btn:active {
            background: var(--contact-hover);
            transform: scale(0.95);
        }
        
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
            transition: all 0.15s;
            flex-shrink: 0;
        }
        
        .send-btn:active { transform: scale(0.95); }
        
        /* Модальные окна */
        .modal, .auth-overlay, .forward-modal, .read-by-modal, .admin-panel {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.95);
            z-index: 3000;
            display: none;
            justify-content: center;
            align-items: center;
        }
        
        .admin-panel {
            display: none;
            flex-direction: column;
            overflow-y: auto !important;
            -webkit-overflow-scrolling: touch;
        }
        
        .admin-panel-content {
            padding: 20px;
            padding-top: 80px;
            padding-bottom: 40px;
            max-width: 800px;
            margin: 0 auto;
            width: 100%;
            color: white;
            overflow-y: visible;
        }
        
        .auth-card, .modal-content, .forward-content, .read-by-content {
            background: white;
            border-radius: 28px;
            padding: 28px;
            width: 90%;
            max-width: 400px;
            text-align: center;
            max-height: 80vh;
            overflow-y: auto;
        }
        
        .dark .auth-card, .dark .modal-content, .dark .forward-content, .dark .read-by-content {
            background: #1e293b;
            color: white;
        }
        
        .auth-card input, .modal-content input, .modal-content textarea {
            width: 100%;
            padding: 14px;
            margin: 12px 0;
            border-radius: 30px;
            border: 1px solid #ddd;
            font-size: 16px;
        }
        
        .dark .auth-card input, .dark .modal-content input {
            background: #334155;
            color: white;
            border-color: #475569;
        }
        
        .auth-card button, .modal-content button {
            background: #4a6cf7;
            color: white;
            border: none;
            padding: 14px;
            border-radius: 30px;
            width: 100%;
            cursor: pointer;
            margin-top: 10px;
            font-size: 1rem;
        }
        
        .user-item, .member-item {
            background: #1e293b;
            margin: 8px 0;
            padding: 14px;
            border-radius: 16px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            gap: 10px;
        }
        
        .system-notify-section {
            background: #1e293b;
            border-radius: 16px;
            padding: 16px;
            margin: 15px 0;
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
            min-width: 100px;
        }
        
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
        
        .menu-toggle {
            display: none;
            background: rgba(255, 255, 255, 0.2);
            border: none;
            width: 40px;
            height: 40px;
            border-radius: 50%;
            color: white;
            font-size: 1.2rem;
            cursor: pointer;
        }
        
        .sidebar-overlay {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.5);
            z-index: 1999;
        }
        
        .sidebar-overlay.visible { display: block; }
        
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
        
        .drop-overlay.visible { display: flex; }
        
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
        }
        
        @keyframes pulse {
            0% { transform: translateX(-50%) scale(1); }
            50% { transform: translateX(-50%) scale(1.05); }
            100% { transform: translateX(-50%) scale(1); }
        }
        
        .recording-indicator.visible { display: flex; }
        
        .camera-preview {
            position: fixed;
            bottom: 100px;
            right: 20px;
            width: 120px;
            height: 160px;
            border-radius: 12px;
            overflow: hidden;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.3);
            z-index: 1000;
            border: 2px solid white;
            display: none;
        }
        
        .camera-preview.visible { display: block; }
        .camera-preview video { width: 100%; height: 100%; object-fit: cover; }
        
        .forward-contact {
            display: flex;
            align-items: center;
            gap: 12px;
            padding: 12px;
            cursor: pointer;
            border-radius: 12px;
            transition: background 0.15s;
        }
        
        .forward-contact:active { background: var(--contact-hover); }
        .forward-contact img { width: 44px; height: 44px; border-radius: 50%; object-fit: cover; }
        
        .read-by-user {
            display: flex;
            align-items: center;
            gap: 12px;
            padding: 10px 0;
            border-bottom: 1px solid var(--input-border);
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
        
        .avatar-preview {
            width: 100px;
            height: 100px;
            border-radius: 50%;
            object-fit: cover;
            margin: 10px auto;
            display: block;
        }
        
        .highlight-message {
            animation: highlight 1s;
        }
        
        @keyframes highlight {
            0% { background: rgba(74, 108, 247, 0.5); }
            100% { background: transparent; }
        }
    </style>
</head>
<body>

<!-- Оверлеи -->
<div class="drop-overlay" id="dropOverlay"><i class="fas fa-cloud-upload-alt"></i><span>Отпустите для загрузки</span></div>
<div class="sidebar-overlay" id="sidebarOverlay"></div>

<!-- Индикаторы записи -->
<div id="recordingIndicator" class="recording-indicator"><i class="fas fa-microphone"></i><span id="recordingTimer">00:00</span><span>Запись...</span></div>
<div id="cameraPreview" class="camera-preview"><video id="previewVideo" autoplay muted playsinline></video></div>

<!-- Панель выделения -->
<div class="selection-bar" id="selectionBar"><span id="selectedCount">0</span><button id="forwardSelectedBtn"><i class="fas fa-share"></i> Переслать</button><button id="deleteSelectedBtn"><i class="fas fa-trash"></i> Удалить</button><button id="cancelSelectionBtn"><i class="fas fa-times"></i> Отмена</button></div>

<!-- Модальные окна -->
<div id="forwardModal" class="forward-modal"><div class="forward-content"><h3><i class="fas fa-share"></i> Переслать</h3><div id="forwardContactsList"></div><button class="forward-cancel" onclick="closeForwardModal()">Отмена</button></div></div>
<div id="readByModal" class="read-by-modal"><div class="read-by-content"><h3><i class="fas fa-check-circle"></i> Прочитали</h3><div id="readByList"></div><button onclick="closeReadByModal()">Закрыть</button></div></div>
<div id="profileModal" class="modal"><div class="modal-content"><h3>Настройки профиля</h3><img id="avatarPreview" class="avatar-preview"><input type="file" id="modalAvatar" accept="image/*"><input type="text" id="modalName" placeholder="Новый никнейм"><button id="saveProfileBtn">💾 Сохранить</button><button id="closeModalBtn">Отмена</button></div></div>
<div id="adminPanel" class="admin-panel"><button class="close-admin" id="closeAdminBtn">✕ Закрыть</button><div class="admin-panel-content"><h3>👑 Админ-панель</h3><div class="system-notify-section"><h4><i class="fas fa-bullhorn"></i> Системные уведомления</h4><div class="preset-buttons"><button class="preset-btn update" onclick="sendSystemNotification('update')">🔄 Обновление</button><button class="preset-btn success" onclick="sendSystemNotification('success')">✅ Успех</button><button class="preset-btn error" onclick="sendSystemNotification('error')">❌ Ошибка</button></div><div class="custom-notify-input"><input type="text" id="customNotifyText" placeholder="Своё уведомление..."><button onclick="sendCustomNotification()">📢 Отправить</button></div></div><div><h4>Участники группы</h4><div id="groupMembersList"></div><button id="addMemberBtn">➕ Добавить участника</button></div><button id="clearChatBtn">🗑️ ОЧИСТИТЬ ОБЩИЙ ЧАТ</button><div id="usersList"></div></div></div>

<!-- Основной интерфейс -->
<div class="app-container" id="appContainer" style="display: none;">
    <div class="sidebar" id="sidebar">
        <div class="sidebar-header"><h2><i class="fas fa-comments"></i> xaMOff</h2><div class="user-info-row"><img id="sidebarAvatar"><span id="sidebarName"></span></div></div>
        <div class="search-box"><input type="text" id="searchInput" placeholder="🔍 Поиск пользователей..."></div>
        <div class="contacts-list" id="contactsList"></div>
    </div>
    <div class="chat-main">
        <div class="chat-header"><button class="menu-toggle" id="menuToggle"><i class="fas fa-bars"></i></button><img id="chatHeaderAvatar" class="chat-header-avatar"><div class="chat-header-info"><div class="chat-header-name" id="chatHeaderName">Общий чат</div><div class="chat-header-status" id="chatHeaderStatus">Группа</div></div><div class="chat-header-buttons"><button class="icon-btn" id="settingsBtn"><i class="fas fa-user-cog"></i></button><button class="icon-btn" id="adminPanelBtn"><i class="fas fa-shield-alt"></i></button><button class="icon-btn" id="themeToggle"><i class="fas fa-moon"></i></button><button class="icon-btn" id="logoutBtn"><i class="fas fa-sign-out-alt"></i></button></div></div>
        <div class="messages-area" id="messagesArea"><div class="messages-loading">Загрузка сообщений...</div></div>
        <div id="replyIndicator" class="reply-indicator" style="display: none;"><span><i class="fas fa-reply"></i> <span id="replyToName"></span>: "<span id="replyToText"></span>"</span><button id="cancelReplyBtn">✕</button></div>
        <div id="mediaPreview" class="media-preview"><img id="previewImage"><div class="media-preview-info"><div class="media-preview-name" id="previewName"></div><div class="media-preview-size" id="previewSize"></div><input type="text" id="mediaCaption" placeholder="Подпись..."></div><button id="cancelMediaPreview">✕</button></div>
        <div class="input-area"><div class="attach-menu" id="attachMenu"><button class="attach-menu-btn" id="attachPhotoBtn"><i class="fas fa-image"></i><span>Фото</span></button><button class="attach-menu-btn" id="attachCameraBtn"><i class="fas fa-camera"></i><span>Снять</span></button><button class="attach-menu-btn" id="attachVideoBtn"><i class="fas fa-video"></i><span>Видео</span></button><button class="attach-menu-btn" id="attachFileBtn"><i class="fas fa-file"></i><span>Файл</span></button><button class="attach-menu-btn" id="attachCircleBtn"><i class="fas fa-circle"></i><span>Кружок</span></button><button class="attach-menu-btn" id="attachVoiceBtn"><i class="fas fa-microphone"></i><span>Голос</span></button></div><div class="input-row"><textarea id="messageInput" class="message-input" placeholder="Сообщение..." rows="1"></textarea><button class="attach-btn" id="attachBtn"><i class="fas fa-paperclip"></i></button><button class="send-btn" id="sendBtn"><i class="fas fa-paper-plane"></i></button></div></div>
    </div>
</div>

<!-- Авторизация -->
<div id="authOverlay" class="auth-overlay" style="display: flex;"><div class="auth-card"><h2>📱 xaMOff Messenger</h2><input type="text" id="loginName" placeholder="Никнейм"><input type="password" id="loginPassword" placeholder="Пароль"><div id="authError" style="color:red;"></div><button id="authBtn">Войти / Регистрация</button></div></div>

<!-- Скрытые инпуты -->
<input type="file" id="photoInput" accept="image/*" style="display:none">
<input type="file" id="videoFileInput" accept="video/*" style="display:none">
<input type="file" id="fileInput" accept="*/*" style="display:none">
<input type="file" id="cameraCaptureInput" accept="image/*" capture="environment" style="display:none">

<script>
// ==================== FIREBASE ====================
const firebaseConfig = {
    apiKey: "AIzaSyD4a16-r1nwCVDAu5_DBnirq0e8Lu9pBZw",
    authDomain: "daniksgames-d46b2.firebaseapp.com",
    databaseURL: "https://daniksgames-d46b2-default-rtdb.firebaseio.com",
    projectId: "daniksgames-d46b2",
    storageBucket: "daniksgames-d46b2.firebasestorage.app"
};
firebase.initializeApp(firebaseConfig);
const db = firebase.database();

// Создаём учётку DaniksGames
(async () => {
    const snap = await db.ref('users').orderByChild('name').equalTo('DaniksGames').once('value');
    if(snap.exists()) snap.forEach(c => c.ref.update({ password: 'Dan10102011' }));
    else await db.ref('users').push({ name: 'DaniksGames', password: 'Dan10102011', avatarUrl: '', blocked: false, createdAt: Date.now(), online: false, lastSeen: Date.now() });
    const members = await db.ref('group_members').once('value');
    if(!members.exists()) await db.ref('group_members/DaniksGames').set(true);
})();

// ==================== ГЛОБАЛЬНЫЕ ПЕРЕМЕННЫЕ ====================
let currentUserId = null, currentUserName = null, currentUserAvatar = null, isBlocked = false, isAdmin = false;
let currentChat = { type: 'group', id: 'global', name: 'Общий чат' };
let replyingTo = null;
let contactsMap = new Map();
let groupMembers = new Set();
let selectedMessages = new Set();
let pendingMedia = null;
let voiceRecorder = null, voiceChunks = [], isVoiceRecording = false, voiceStream = null;
let circleRecorder = null, circleChunks = [], isCircleRecording = false, circleStream = null;
let recordingTimerInterval = null, recordingSeconds = 0;
let currentMsgListener = null;

// ==================== ВСПОМОГАТЕЛЬНЫЕ ====================
function escapeHtml(s) { return String(s || '').replace(/[&<>]/g, m => ({'&':'&amp;','<':'&lt;','>':'&gt;'}[m])); }
function formatFileSize(b) { if(b<1024) return b+' B'; if(b<1048576) return (b/1024).toFixed(1)+' KB'; return (b/1048576).toFixed(1)+' MB'; }
function playSound() { try { new Audio('data:audio/wav;base64,U3RlYWx0aA==').play().catch(e=>{}); } catch(e){} }
function formatTime(sec) { return `${Math.floor(sec/60).toString().padStart(2,'0')}:${(sec%60).toString().padStart(2,'0')}`; }
function startRecordingTimer() { recordingSeconds=0; document.getElementById('recordingTimer').textContent='00:00'; recordingTimerInterval=setInterval(()=>{ recordingSeconds++; document.getElementById('recordingTimer').textContent=formatTime(recordingSeconds); },1000); }
function stopRecordingTimer() { if(recordingTimerInterval) clearInterval(recordingTimerInterval); recordingTimerInterval=null; }

// Функции для анимированного закрытия модальных окон
function closeModalWithAnimation(modalElement) {
    modalElement.classList.add('closing');
    setTimeout(() => {
        modalElement.style.display = 'none';
        modalElement.classList.remove('closing');
    }, 200);
}

// ==================== УПРАВЛЕНИЕ КОНТАКТАМИ ====================
async function addContactAuto(userId, userName, userAvatar) {
    const contactRef = db.ref(`users/${currentUserId}/contacts/${userId}`);
    const snap = await contactRef.once('value');
    if(!snap.exists()) {
        await contactRef.set({ userId, name: userName, avatar: userAvatar || '', addedAt: Date.now(), unreadCount: 0 });
        await loadContactsList();
    }
}

async function loadContactsList() {
    const contactsSnap = await db.ref(`users/${currentUserId}/contacts`).once('value');
    const contactsData = contactsSnap.val() || {};
    contactsMap.clear();
    for(let id in contactsData) {
        const c = contactsData[id];
        if(c.userId) {
            const userSnap = await db.ref(`users/${c.userId}`).once('value');
            if(userSnap.exists() && !userSnap.val().blocked) {
                contactsMap.set(c.userId, { name: userSnap.val().name, avatar: userSnap.val().avatarUrl || '', unread: c.unreadCount || 0, isGroup: false });
            }
        }
    }
    const isMember = groupMembers.has(currentUserId) || currentUserName === 'DaniksGames';
    if(isMember) contactsMap.set('global', { name: '🌐 Общий чат', avatar: '', unread: 0, isGroup: true });
    renderContacts();
}

function renderContacts() {
    const container = document.getElementById('contactsList');
    container.innerHTML = '';
    for(let [id, data] of contactsMap.entries()) {
        const unreadBadge = (data.unread > 0) ? `<span class="unread-badge">${data.unread}</span>` : '';
        const isActive = (currentChat.id === id);
        container.innerHTML += `
            <div class="contact-item ${isActive ? 'active' : ''}" onclick="switchChat('${id}', '${data.name.replace(/'/g, "\\'")}', ${data.isGroup})">
                <div class="avatar-wrapper"><img class="contact-avatar" src="${data.avatar || `https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(data.name)}`}"><div class="online-badge offline" id="online-${id}"></div></div>
                <div class="contact-info"><div class="contact-name">${escapeHtml(data.name)} ${unreadBadge}</div><div class="contact-status">${data.isGroup ? '👥 Группа' : '💬 Пользователь'}</div></div>
                ${!data.isGroup ? `<div class="contact-actions"><button onclick="event.stopPropagation(); deleteContact('${id}')"><i class="fas fa-user-minus"></i></button><button onclick="event.stopPropagation(); clearPrivateChat('${id}')"><i class="fas fa-trash-alt"></i></button></div>` : ''}
            </div>
        `;
    }
    updateAllOnlineStatuses();
}

window.deleteContact = async (contactId) => {
    if(confirm('Удалить контакт?')) {
        await db.ref(`users/${currentUserId}/contacts/${contactId}`).remove();
        if(currentChat.id === contactId) switchChat('global', 'Общий чат', true);
        await loadContactsList();
    }
};

window.clearPrivateChat = async (contactId) => {
    if(confirm('Очистить историю сообщений с этим контактом?')) {
        const pair = [currentUserId, contactId].sort().join('_');
        await db.ref(`private_messages/${pair}`).remove();
        if(currentChat.id === contactId) loadMessages();
    }
};

async function updateUnreadCount(chatId, delta) {
    if(chatId !== 'global') {
        const contactRef = db.ref(`users/${currentUserId}/contacts/${chatId}`);
        const snap = await contactRef.once('value');
        if(snap.exists()) {
            let unread = (snap.val().unreadCount || 0) + delta;
            unread = Math.max(0, unread);
            await contactRef.update({ unreadCount: unread });
            if(contactsMap.has(chatId)) contactsMap.get(chatId).unread = unread;
            renderContacts();
        }
    }
}

function resetUnread(chatId) {
    if(chatId === 'global') {
        db.ref(`user_group_read/${currentUserId}`).set(Date.now());
    } else {
        db.ref(`users/${currentUserId}/contacts/${chatId}`).update({ unreadCount: 0 }).then(() => {
            if(contactsMap.has(chatId)) contactsMap.get(chatId).unread = 0;
            renderContacts();
        });
    }
}

// ==================== УПРАВЛЕНИЕ ГРУППОЙ ====================
async function loadGroupMembers() {
    const snap = await db.ref('group_members').once('value');
    groupMembers.clear();
    const members = snap.val() || {};
    for(let id in members) if(members[id]) groupMembers.add(id);
    if(!groupMembers.has('DaniksGames')) groupMembers.add('DaniksGames');
    await db.ref('group_members/DaniksGames').set(true);
    await loadContactsList();
}

async function isGroupMember(userId) { return groupMembers.has(userId) || userId === 'DaniksGames'; }

// ==================== ПУТЬ К ЧАТУ ====================
function getChatPath() {
    if(currentChat.type === 'group') return 'group_messages';
    const ids = [currentUserId, currentChat.id].sort();
    return `private_messages/${ids[0]}_${ids[1]}`;
}

// ==================== ОТПРАВКА СООБЩЕНИЙ ====================
async function sendMessageWithMedia(mediaType, mediaUrl, caption, fileData = null, isCircle = false) {
    if(isBlocked) return alert('Вы заблокированы');
    if(currentChat.type === 'group' && !(await isGroupMember(currentUserId))) return alert('Вы не в группе');
    const msgData = {
        userId: currentUserId, name: currentUserName, avatar: currentUserAvatar || '', text: caption || '',
        mediaType, mediaUrl, time: Date.now(), read: false, delivered: false, readBy: { [currentUserId]: true }
    };
    if(isCircle) msgData.isCircle = true;
    if(fileData) { msgData.fileName = fileData.name; msgData.fileSize = fileData.size; msgData.fileType = fileData.type; }
    if(replyingTo) { msgData.replyTo = replyingTo; replyingTo = null; document.getElementById('replyIndicator').style.display = 'none'; }
    await db.ref(getChatPath()).push(msgData);
    playSound();
}

async function sendTextMessage() {
    const input = document.getElementById('messageInput');
    const text = input.value.trim();
    if(!text || isBlocked) return;
    const msgData = {
        userId: currentUserId, name: currentUserName, avatar: currentUserAvatar || '', text,
        time: Date.now(), read: false, delivered: false, readBy: { [currentUserId]: true }
    };
    if(replyingTo) { msgData.replyTo = replyingTo; replyingTo = null; document.getElementById('replyIndicator').style.display = 'none'; }
    await db.ref(getChatPath()).push(msgData);
    input.value = '';
    input.style.height = 'auto';
    playSound();
}

// ==================== ЗАГРУЗКА СООБЩЕНИЙ ====================
function loadMessages() {
    if(currentMsgListener) currentMsgListener.off();
    const area = document.getElementById('messagesArea');
    area.innerHTML = '<div class="messages-loading">Загрузка...</div>';
    const path = getChatPath();
    resetUnread(currentChat.id);
    const ref = db.ref(path);
    currentMsgListener = ref.orderByChild('time').limitToLast(100);
    currentMsgListener.on('child_added', snap => renderMessage(snap.key, snap.val()));
    currentMsgListener.on('child_changed', snap => updateMessageUI(snap.key, snap.val()));
    currentMsgListener.on('child_removed', snap => document.getElementById(`msg-${snap.key}`)?.remove());
}

function renderMessage(msgId, msg) {
    if(!msg) return;
    const isMe = msg.userId === currentUserId;
    const isSystem = msg.isSystem || msg.userId === 'system';
    const div = document.createElement('div');
    div.className = `message ${isSystem ? 'system-message' : (isMe ? 'my-message' : 'other-message')}`;
    div.id = `msg-${msgId}`;
    
    let replyHtml = '';
    if(msg.replyTo) replyHtml = `<div class="reply-preview" onclick="scrollToMessage('${msg.replyTo.messageId}')"><i class="fas fa-reply"></i> <strong>${escapeHtml(msg.replyTo.userName)}</strong>: ${escapeHtml(msg.replyTo.text)}</div>`;
    
    let mediaHtml = '';
    if(msg.mediaType === 'image') mediaHtml = `<img src="${msg.mediaUrl}" class="media-content" onclick="window.open('${msg.mediaUrl}')" loading="lazy">`;
    else if(msg.mediaType === 'video') mediaHtml = msg.isCircle ? `<div class="circle-video"><video controls playsinline><source src="${msg.mediaUrl}"></video></div>` : `<video controls class="media-content" src="${msg.mediaUrl}"></video>`;
    else if(msg.mediaType === 'audio') mediaHtml = `<audio controls src="${msg.mediaUrl}"></audio>`;
    else if(msg.mediaType === 'file') mediaHtml = `<div class="file-attachment" onclick="window.open('${msg.mediaUrl}')"><i class="fas fa-file"></i><div><strong>${escapeHtml(msg.fileName)}</strong><br><small>${formatFileSize(msg.fileSize)}</small></div></div>`;
    
    const avatar = msg.avatar || `https://ui-avatars.com/api/?background=6b4eff&color=fff&name=${encodeURIComponent(msg.name)}`;
    const readCount = msg.readBy ? Object.keys(msg.readBy).length - 1 : 0;
    const readInfo = (isMe && readCount > 0) ? `<span class="read-by-info" onclick="showReadBy('${msgId}')">👁 ${readCount}</span>` : '';
    let readStatus = '';
    if(isMe && currentChat.type === 'user' && !isSystem) readStatus = msg.read ? '<span class="read-status read">✓✓ Прочитано</span>' : (msg.delivered ? '<span class="read-status">✓✓ Доставлено</span>' : '<span class="read-status">✓ Отправлено</span>');
    
    const checkbox = `<input type="checkbox" class="message-checkbox" onchange="toggleMessageSelection('${msgId}', this.checked)">`;
    const deleteBtn = (isMe && !isSystem) ? `<button class="delete-btn" onclick="deleteMessage('${msgId}')"><i class="fas fa-trash"></i></button>` : '';
    const adminDeleteBtn = (isAdmin && !isMe && !isSystem) ? `<button class="admin-delete-btn" onclick="adminDeleteMessage('${msgId}')"><i class="fas fa-trash"></i></button>` : '';
    const replyBtn = !isSystem ? `<button class="reply-btn" onclick="replyToMsg('${msgId}', '${escapeHtml(msg.name).replace(/'/g, "\\'")}', '${escapeHtml(msg.text || 'Медиа').replace(/'/g, "\\'")}')"><i class="fas fa-reply"></i></button>` : '';
    const forwardBtn = !isSystem ? `<button class="forward-btn" onclick="forwardSingle('${msgId}')"><i class="fas fa-share"></i></button>` : '';
    
    if(isSystem) {
        div.innerHTML = `<div class="bubble">${msg.text}<div class="message-time">${new Date(msg.time).toLocaleTimeString()}</div></div>`;
    } else {
        div.innerHTML = `<div class="bubble">${checkbox}${deleteBtn}${adminDeleteBtn}${replyBtn}${forwardBtn}<div class="message-header"><img class="msg-avatar" src="${avatar}"><span class="message-name">${escapeHtml(msg.name)}</span></div>${replyHtml}${mediaHtml}${msg.text && !msg.mediaType ? `<div>${escapeHtml(msg.text)}</div>` : ''}<div class="message-time">${new Date(msg.time).toLocaleTimeString()} ${readStatus} ${readInfo}</div></div>`;
    }
    document.getElementById('messagesArea').appendChild(div);
    document.getElementById('messagesArea').scrollTop = document.getElementById('messagesArea').scrollHeight;
    
    if(!isMe && !msg.read && currentChat.type === 'user') {
        db.ref(`${getChatPath()}/${msgId}`).update({ read: true, [`readBy/${currentUserId}`]: true });
    }
    if(!isMe && currentChat.type === 'group' && !msg.readBy?.[currentUserId]) {
        db.ref(`${getChatPath()}/${msgId}/readBy/${currentUserId}`).set(true);
    }
    if(!isMe && currentChat.id !== msg.userId && currentChat.type !== 'group') {
        updateUnreadCount(msg.userId, 1);
    }
    if(!isMe && document.hidden && Notification.permission === 'granted' && !isSystem) {
        new Notification(msg.name, { body: msg.text || 'Новое сообщение', icon: avatar });
    }
}

function updateMessageUI(msgId, msg) {
    const el = document.getElementById(`msg-${msgId}`);
    if(el && msg.readBy) {
        const count = Object.keys(msg.readBy).length - 1;
        const readSpan = el.querySelector('.read-by-info');
        if(readSpan) readSpan.innerHTML = `👁 ${count}`;
    }
}

window.deleteMessage = async (id) => { if(confirm('Удалить?')) await db.ref(`${getChatPath()}/${id}`).remove(); };
window.adminDeleteMessage = async (id) => { if(isAdmin && confirm('Удалить как админ?')) await db.ref(`${getChatPath()}/${id}`).remove(); };
window.replyToMsg = (id, name, txt) => { replyingTo = { messageId: id, userName: name, text: txt }; document.getElementById('replyIndicator').style.display = 'flex'; document.getElementById('replyToName').innerText = name; document.getElementById('replyToText').innerText = txt.slice(0,50); document.getElementById('messageInput').focus(); };
window.scrollToMessage = (id) => { const el = document.getElementById(`msg-${id}`); if(el) { el.scrollIntoView({ behavior: 'smooth', block: 'center' }); el.classList.add('highlight-message'); setTimeout(() => el.classList.remove('highlight-message'), 1000); } };
window.showReadBy = async (msgId) => {
    const msg = (await db.ref(`${getChatPath()}/${msgId}`).once('value')).val();
    if(!msg?.readBy) return;
    let html = '';
    for(let uid in msg.readBy) {
        if(uid !== currentUserId) {
            const userSnap = await db.ref(`users/${uid}`).once('value');
            if(userSnap.exists()) {
                const u = userSnap.val();
                html += `<div class="read-by-user"><img src="${u.avatarUrl || `https://ui-avatars.com/api/?name=${u.name}`}"><span>${escapeHtml(u.name)}</span></div>`;
            }
        }
    }
    document.getElementById('readByList').innerHTML = html || '<p>Никто не прочитал</p>';
    document.getElementById('readByModal').style.display = 'flex';
};
window.closeReadByModal = () => closeModalWithAnimation(document.getElementById('readByModal'));

// ==================== ВЫДЕЛЕНИЕ СООБЩЕНИЙ ====================
window.toggleMessageSelection = (msgId, checked) => {
    if(checked) { selectedMessages.add(msgId); document.getElementById(`msg-${msgId}`).classList.add('selected'); }
    else { selectedMessages.delete(msgId); document.getElementById(`msg-${msgId}`).classList.remove('selected'); }
    updateSelectionBar();
};
function updateSelectionBar() {
    const bar = document.getElementById('selectionBar');
    const count = selectedMessages.size;
    if(count > 0) { bar.classList.add('visible'); document.getElementById('selectedCount').innerText = `${count} сообщ.`; }
    else bar.classList.remove('visible');
}
function clearSelection() { selectedMessages.forEach(id => { const el = document.getElementById(`msg-${id}`); if(el) { el.classList.remove('selected'); const cb = el.querySelector('.message-checkbox'); if(cb) cb.checked = false; } }); selectedMessages.clear(); updateSelectionBar(); }
window.deleteSelectedMessages = async () => { if(selectedMessages.size && confirm('Удалить выбранные?')) { for(let id of selectedMessages) await db.ref(`${getChatPath()}/${id}`).remove(); clearSelection(); } };
window.forwardSingle = (msgId) => showForwardModal([msgId]);
window.forwardSelectedMessages = () => showForwardModal(Array.from(selectedMessages));

// ==================== ПЕРЕСЫЛКА ====================
async function showForwardModal(msgIds) {
    const contacts = Array.from(contactsMap.entries()).filter(([id]) => id !== currentUserId && id !== 'global');
    const container = document.getElementById('forwardContactsList');
    container.innerHTML = contacts.map(([id, data]) => `
        <div class="forward-contact" onclick="forwardToChat('${id}', '${data.name}', ${JSON.stringify(msgIds)})">
            <img src="${data.avatar || `https://ui-avatars.com/api/?name=${data.name}`}">
            <div><strong>${escapeHtml(data.name)}</strong></div>
        </div>
    `).join('');
    document.getElementById('forwardModal').style.display = 'flex';
}
window.forwardToChat = async (targetId, targetName, msgIds) => {
    for(let msgId of msgIds) {
        const snap = await db.ref(`${getChatPath()}/${msgId}`).once('value');
        const msg = snap.val();
        if(msg) {
            const forwardData = { ...msg, userId: currentUserId, name: currentUserName, avatar: currentUserAvatar, time: Date.now(), read: false, delivered: false, readBy: { [currentUserId]: true }, forwardedFrom: { chatId: currentChat.id, chatName: currentChat.name } };
            delete forwardData.id;
            const pair = [currentUserId, targetId].sort().join('_');
            await db.ref(`private_messages/${pair}`).push(forwardData);
        }
    }
    closeForwardModal();
    clearSelection();
    alert(`Переслано ${targetName}`);
};
window.closeForwardModal = () => closeModalWithAnimation(document.getElementById('forwardModal'));

// ==================== ПЕРЕКЛЮЧЕНИЕ ЧАТА ====================
window.switchChat = async (chatId, chatName, isGroup) => {
    currentChat = { type: isGroup ? 'group' : 'user', id: chatId, name: chatName };
    document.getElementById('chatHeaderName').innerText = chatName;
    document.getElementById('chatHeaderStatus').innerText = isGroup ? '👥 Групповой чат' : '💬 Приватный чат';
    const avatar = contactsMap.get(chatId)?.avatar || `https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(chatName)}`;
    document.getElementById('chatHeaderAvatar').src = avatar;
    replyingTo = null;
    document.getElementById('replyIndicator').style.display = 'none';
    clearSelection();
    loadMessages();
    renderContacts();
    closeSidebar();
};

// ==================== АВТОМАТИЧЕСКОЕ ДОБАВЛЕНИЕ ====================
function listenIncomingPrivate() {
    db.ref('private_messages').on('child_added', async (snap) => {
        const ids = snap.key.split('_');
        const otherId = ids[0] === currentUserId ? ids[1] : (ids[1] === currentUserId ? ids[0] : null);
        if(otherId && snap.val()) {
            const msgs = snap.val();
            for(let key in msgs) {
                const msg = msgs[key];
                if(msg.userId !== currentUserId && msg.userId === otherId) {
                    const userSnap = await db.ref(`users/${otherId}`).once('value');
                    if(userSnap.exists()) {
                        await addContactAuto(otherId, userSnap.val().name, userSnap.val().avatarUrl);
                        if(currentChat.id !== otherId) await updateUnreadCount(otherId, 1);
                    }
                    break;
                }
            }
        }
    });
}

// ==================== ЗАПИСЬ ГОЛОСА И КРУЖКОВ ====================
async function startVoiceRecording() {
    if(isVoiceRecording && voiceRecorder?.state === 'recording') { voiceRecorder.stop(); return; }
    try {
        const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
        voiceStream = stream;
        voiceRecorder = new MediaRecorder(stream);
        voiceChunks = [];
        voiceRecorder.ondataavailable = e => { if(e.data.size > 0) voiceChunks.push(e.data); };
        voiceRecorder.onstop = () => {
            const blob = new Blob(voiceChunks, {type:'audio/webm'});
            const reader = new FileReader();
            reader.onload = e => sendMessageWithMedia('audio', e.target.result, '');
            reader.readAsDataURL(blob);
            if(voiceStream) voiceStream.getTracks().forEach(t=>t.stop());
            isVoiceRecording = false;
            document.getElementById('recordingIndicator').classList.remove('visible');
            stopRecordingTimer();
        };
        voiceRecorder.start();
        isVoiceRecording = true;
        document.getElementById('recordingIndicator').classList.add('visible');
        startRecordingTimer();
        setTimeout(() => { if(isVoiceRecording && voiceRecorder?.state === 'recording') voiceRecorder.stop(); }, 15000);
    } catch(e) { alert('Нет микрофона'); }
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
            document.getElementById('previewVideo').srcObject = stream;
            document.getElementById('cameraPreview').classList.add('visible');
            circleRecorder = new MediaRecorder(stream, {mimeType: 'video/webm'});
            circleChunks = [];
            circleRecorder.ondataavailable = e => { if(e.data.size > 0) circleChunks.push(e.data); };
            circleRecorder.onstop = () => {
                const blob = new Blob(circleChunks, {type:'video/webm'});
                const reader = new FileReader();
                reader.onload = e => sendMessageWithMedia('video', e.target.result, '', null, true);
                reader.readAsDataURL(blob);
                if(circleStream) circleStream.getTracks().forEach(t=>t.stop());
                document.getElementById('cameraPreview').classList.remove('visible');
                document.getElementById('recordingIndicator').classList.remove('visible');
                stopRecordingTimer();
            };
            circleRecorder.start();
            isCircleRecording = true;
            document.getElementById('recordingIndicator').classList.add('visible');
            document.querySelector('#recordingIndicator i').className = 'fas fa-video';
            startRecordingTimer();
            setTimeout(() => { if(isCircleRecording && circleRecorder?.state === 'recording') circleRecorder.stop(); }, 30000);
        } catch(e) { alert('Нет камеры'); }
    }
}

// ==================== DRAG & DROP ====================
function setupDragDrop() {
    const dropOverlay = document.getElementById('dropOverlay');
    document.addEventListener('dragover', e => { e.preventDefault(); dropOverlay.classList.add('visible'); });
    document.addEventListener('dragleave', e => { e.preventDefault(); dropOverlay.classList.remove('visible'); });
    document.addEventListener('drop', async e => { e.preventDefault(); dropOverlay.classList.remove('visible'); if(e.dataTransfer.files.length) handleFiles(e.dataTransfer.files); });
    document.addEventListener('paste', e => { for(let item of e.clipboardData.items) if(item.type.indexOf('image')!==-1) handleFiles([item.getAsFile()]); });
}

function handleFiles(files) {
    for(let file of files) {
        const reader = new FileReader();
        reader.onload = (ev) => {
            const isImage = file.type.startsWith('image/');
            const mediaType = isImage ? 'image' : 'file';
            pendingMedia = { type: mediaType, data: ev.target.result, fileData: { name: file.name, size: file.size, type: file.type } };
            showMediaPreview(file, isImage ? ev.target.result : null);
        };
        reader.readAsDataURL(file);
    }
}

function showMediaPreview(file, previewUrl) {
    const preview = document.getElementById('mediaPreview');
    const previewImage = document.getElementById('previewImage');
    if(previewUrl) { previewImage.src = previewUrl; previewImage.style.display = 'block'; }
    document.getElementById('previewName').innerText = file.name;
    document.getElementById('previewSize').innerText = formatFileSize(file.size);
    document.getElementById('mediaCaption').value = '';
    preview.classList.add('visible');
}

// ==================== ПРОФИЛЬ ====================
function setupProfile() {
    document.getElementById('settingsBtn').onclick = () => { 
        document.getElementById('avatarPreview').src = currentUserAvatar || `https://ui-avatars.com/api/?name=${currentUserName}`; 
        document.getElementById('modalName').value = currentUserName; 
        document.getElementById('profileModal').style.display = 'flex'; 
    };
    document.getElementById('closeModalBtn').onclick = () => closeModalWithAnimation(document.getElementById('profileModal'));
    document.getElementById('saveProfileBtn').onclick = async () => {
        const newName = document.getElementById('modalName').value.trim();
        const file = document.getElementById('modalAvatar').files[0];
        let updates = {};
        if(newName && newName !== currentUserName) {
            const check = await db.ref('users').orderByChild('name').equalTo(newName).once('value');
            let taken = false;
            check.forEach(c => { if(c.key !== currentUserId) taken = true; });
            if(taken) return alert('Ник занят');
            updates.name = newName;
        }
        if(file) {
            const reader = new FileReader();
            const base64 = await new Promise(r => { reader.onload = e => r(e.target.result); reader.readAsDataURL(file); });
            updates.avatarUrl = base64;
        }
        if(Object.keys(updates).length) await db.ref(`users/${currentUserId}`).update(updates);
        if(updates.name) { currentUserName = updates.name; isAdmin = currentUserName === 'DaniksGames'; document.getElementById('sidebarName').innerText = currentUserName; }
        if(updates.avatarUrl) currentUserAvatar = updates.avatarUrl;
        closeModalWithAnimation(document.getElementById('profileModal'));
        await loadContactsList();
    };
}

// ==================== АДМИН-ПАНЕЛЬ ====================
async function loadAdminUsers() {
    const users = await db.ref('users').once('value');
    const container = document.getElementById('usersList');
    container.innerHTML = '<h4>Все пользователи:</h4>';
    for(let id in users.val()) {
        const u = users.val()[id];
        container.innerHTML += `<div class="user-item"><div><strong>${escapeHtml(u.name)}</strong><br><small>Пароль: ${escapeHtml(u.password)}</small><br>${u.blocked ? '🔒 Заблокирован' : '✅ Активен'}</div><div><button onclick="adminEditUser('${id}', '${escapeHtml(u.name)}')">✏️</button><button onclick="toggleBlock('${id}', ${u.blocked || false})">${u.blocked ? '🔓' : '🔒'}</button><button onclick="deleteAccount('${id}')">🗑️</button></div></div>`;
    }
}
window.adminEditUser = async (userId, currentName) => {
    const newName = prompt('Новый ник:', currentName);
    if(newName && newName !== currentName) {
        const check = await db.ref('users').orderByChild('name').equalTo(newName).once('value');
        let exists = false;
        check.forEach(c => { if(c.key !== userId) exists = true; });
        if(exists) return alert('Ник занят');
        await db.ref(`users/${userId}`).update({ name: newName });
    }
    const newPass = prompt('Новый пароль:');
    if(newPass && newPass.trim()) await db.ref(`users/${userId}`).update({ password: newPass.trim() });
    loadAdminUsers();
};
window.toggleBlock = async (id, blocked) => { await db.ref(`users/${id}`).update({ blocked: !blocked }); loadAdminUsers(); };
window.deleteAccount = async (id) => { if(confirm('Удалить аккаунт?')) { await db.ref(`users/${id}`).remove(); loadAdminUsers(); } };
window.sendSystemNotification = async (type) => { if(!isAdmin) return; let text = type==='update'?'🔄 Установка обновления...':(type==='success'?'✅ Обновление установлено!':'❌ Ошибка обновления'); await db.ref('group_messages').push({ userId:'system', name:'Система', text, time:Date.now(), isSystem:true }); playSound(); };
window.sendCustomNotification = async () => { if(!isAdmin) return; const txt = document.getElementById('customNotifyText').value.trim(); if(txt) await db.ref('group_messages').push({ userId:'system', name:'Система', text:'📢 '+txt, time:Date.now(), isSystem:true }); playSound(); };
async function loadGroupMembersAdmin() {
    const membersDiv = document.getElementById('groupMembersList');
    const allUsersSnap = await db.ref('users').once('value');
    const allUsersData = allUsersSnap.val() || {};
    membersDiv.innerHTML = '<h4>Участники группы:</h4>';
    for(let uid of groupMembers) {
        const user = allUsersData[uid];
        if(user) membersDiv.innerHTML += `<div class="member-item"><span>${escapeHtml(user.name)}</span><button onclick="removeGroupMember('${uid}')">Удалить</button></div>`;
    }
    document.getElementById('addMemberBtn').onclick = async () => {
        const candidates = Object.entries(allUsersData).filter(([id,u]) => !groupMembers.has(id) && !u.blocked);
        const name = prompt('Введите ник:\n' + candidates.map(([id,u])=>u.name).join(', '));
        if(name) {
            const found = candidates.find(([id,u]) => u.name === name);
            if(found) { await db.ref(`group_members/${found[0]}`).set(true); await loadGroupMembers(); loadGroupMembersAdmin(); }
            else alert('Не найден');
        }
    };
}
window.removeGroupMember = async (uid) => { if(uid === 'DaniksGames') return alert('Нельзя удалить создателя'); await db.ref(`group_members/${uid}`).remove(); await loadGroupMembers(); loadGroupMembersAdmin(); if(currentChat.type==='group' && !groupMembers.has(currentUserId)) switchChat('global','Общий чат',true); };
document.getElementById('clearChatBtn').onclick = async () => { if(isAdmin && confirm('Очистить общий чат?')) await db.ref('group_messages').remove(); };
function setupAdmin() {
    document.getElementById('adminPanelBtn').onclick = () => { if(isAdmin) { loadAdminUsers(); loadGroupMembersAdmin(); document.getElementById('adminPanel').style.display = 'flex'; } else alert('Только DaniksGames'); };
    document.getElementById('closeAdminBtn').onclick = () => closeModalWithAnimation(document.getElementById('adminPanel'));
}

// ==================== ТЕМА, ОНЛАЙН ====================
function setupTheme() { if(localStorage.getItem('theme')==='dark') document.body.classList.add('dark'); document.getElementById('themeToggle').onclick = () => { document.body.classList.toggle('dark'); localStorage.setItem('theme', document.body.classList.contains('dark') ? 'dark' : 'light'); }; }
async function updateOnlineStatus() { if(currentUserId) await db.ref(`users/${currentUserId}`).update({ online: true, lastSeen: Date.now() }); }
async function getUserOnlineStatus(uid) { const snap = await db.ref(`users/${uid}`).once('value'); const u = snap.val(); return u ? (u.online || (Date.now() - (u.lastSeen||0) < 60000)) : false; }
async function updateAllOnlineStatuses() { for(let [id] of contactsMap) { if(id !== 'global') { const online = await getUserOnlineStatus(id); const dot = document.getElementById(`online-${id}`); if(dot) dot.className = `online-badge ${online ? 'online' : 'offline'}`; } } }
function closeSidebar() { document.getElementById('sidebar').classList.remove('open'); document.getElementById('sidebarOverlay').classList.remove('visible'); }
function openSidebar() { document.getElementById('sidebar').classList.add('open'); document.getElementById('sidebarOverlay').classList.add('visible'); }

// ==================== ИНИЦИАЛИЗАЦИЯ ====================
async function init() {
    await loadGroupMembers();
    await loadContactsList();
    setupProfile();
    setupTheme();
    setupAdmin();
    setupDragDrop();
    listenIncomingPrivate();
    
    document.getElementById('menuToggle').onclick = openSidebar;
    document.getElementById('sidebarOverlay').onclick = closeSidebar;
    document.getElementById('sendBtn').onclick = async () => { if(pendingMedia) { await sendMessageWithMedia(pendingMedia.type, pendingMedia.data, document.getElementById('mediaCaption').value, pendingMedia.fileData); pendingMedia = null; document.getElementById('mediaPreview').classList.remove('visible'); } else sendTextMessage(); };
    document.getElementById('attachBtn').onclick = (e) => { e.stopPropagation(); document.getElementById('attachMenu').classList.toggle('visible'); };
    document.getElementById('attachPhotoBtn').onclick = () => { document.getElementById('attachMenu').classList.remove('visible'); document.getElementById('photoInput').click(); };
    document.getElementById('attachCameraBtn').onclick = () => { document.getElementById('attachMenu').classList.remove('visible'); document.getElementById('cameraCaptureInput').click(); };
    document.getElementById('attachVideoBtn').onclick = () => { document.getElementById('attachMenu').classList.remove('visible'); document.getElementById('videoFileInput').click(); };
    document.getElementById('attachFileBtn').onclick = () => { document.getElementById('attachMenu').classList.remove('visible'); document.getElementById('fileInput').click(); };
    document.getElementById('attachCircleBtn').onclick = () => { document.getElementById('attachMenu').classList.remove('visible'); startCircleRecording(); };
    document.getElementById('attachVoiceBtn').onclick = () => { document.getElementById('attachMenu').classList.remove('visible'); startVoiceRecording(); };
    document.getElementById('cancelMediaPreview').onclick = () => { pendingMedia = null; document.getElementById('mediaPreview').classList.remove('visible'); };
    document.getElementById('cancelReplyBtn').onclick = () => { replyingTo = null; document.getElementById('replyIndicator').style.display = 'none'; };
    document.getElementById('searchInput').oninput = async (e) => { const q = e.target.value.toLowerCase(); if(!q) { loadContactsList(); return; } const users = (await db.ref('users').once('value')).val(); const results = Object.entries(users).filter(([id,u]) => u.name.toLowerCase().includes(q) && id!==currentUserId && !u.blocked); const container=document.getElementById('contactsList'); container.innerHTML = results.map(([id,u]) => `<div class="contact-item" onclick="addContactAuto('${id}','${u.name}','${u.avatarUrl||''}')"><img class="contact-avatar" src="${u.avatarUrl||`https://ui-avatars.com/api/?name=${u.name}`}"><div><strong>${escapeHtml(u.name)}</strong><br><small>➕ Добавить</small></div></div>`).join(''); };
    document.getElementById('forwardSelectedBtn').onclick = forwardSelectedMessages;
    document.getElementById('deleteSelectedBtn').onclick = deleteSelectedMessages;
    document.getElementById('cancelSelectionBtn').onclick = clearSelection;
    document.getElementById('logoutBtn').onclick = () => { db.ref(`users/${currentUserId}`).update({ online: false, lastSeen: Date.now() }); localStorage.clear(); location.reload(); };
    
    document.getElementById('photoInput').onchange = e => { if(e.target.files[0]) handleFiles([e.target.files[0]]); e.target.value=''; };
    document.getElementById('videoFileInput').onchange = e => { if(e.target.files[0]) handleFiles([e.target.files[0]]); e.target.value=''; };
    document.getElementById('fileInput').onchange = e => { if(e.target.files[0]) handleFiles([e.target.files[0]]); e.target.value=''; };
    document.getElementById('cameraCaptureInput').onchange = e => { if(e.target.files[0]) handleFiles([e.target.files[0]]); e.target.value=''; };
    
    const textarea = document.getElementById('messageInput');
    textarea.addEventListener('input', function() { this.style.height = 'auto'; this.style.height = Math.min(this.scrollHeight, 120) + 'px'; });
    textarea.addEventListener('keydown', e => { if(e.key === 'Enter' && !e.shiftKey) { e.preventDefault(); document.getElementById('sendBtn').click(); } });
    
    switchChat('global', 'Общий чат', true);
    if(Notification.permission === 'default') Notification.requestPermission();
    setInterval(updateOnlineStatus, 15000);
    setInterval(updateAllOnlineStatuses, 5000);
}

// ==================== АВТОРИЗАЦИЯ ====================
document.getElementById('authBtn').onclick = async () => {
    const name = document.getElementById('loginName').value.trim();
    const pass = document.getElementById('loginPassword').value.trim();
    if(!name || !pass) return;
    const usersRef = db.ref('users');
    const snap = await usersRef.orderByChild('name').equalTo(name).once('value');
    if(snap.exists()) {
        let found = false;
        snap.forEach(c => { if(c.val().password === pass) { currentUserId = c.key; currentUserName = c.val().name; currentUserAvatar = c.val().avatarUrl; isBlocked = c.val().blocked; found = true; } });
        if(!found) { document.getElementById('authError').innerText = 'Неверный пароль'; return; }
    } else {
        const newRef = usersRef.push();
        currentUserId = newRef.key;
        await newRef.set({ name, password: pass, avatarUrl: '', blocked: false, createdAt: Date.now(), lastSeen: Date.now(), online: true });
        currentUserName = name; currentUserAvatar = null;
        await db.ref(`users/${currentUserId}/contacts/DaniksGames`).set({ userId: 'DaniksGames', name: 'DaniksGames', avatar: '', addedAt: Date.now() });
    }
    if(isBlocked) { alert('Вы заблокированы'); return; }
    isAdmin = currentUserName === 'DaniksGames';
    localStorage.setItem('userId', currentUserId);
    document.getElementById('authOverlay').style.display = 'none';
    document.getElementById('appContainer').style.display = 'flex';
    document.getElementById('sidebarName').innerText = currentUserName;
    document.getElementById('sidebarAvatar').src = currentUserAvatar || `https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(currentUserName)}`;
    await init();
};

(async () => {
    const uid = localStorage.getItem('userId');
    if(uid) {
        const snap = await db.ref(`users/${uid}`).once('value');
        if(snap.exists() && !snap.val().blocked) {
            currentUserId = uid; currentUserName = snap.val().name; currentUserAvatar = snap.val().avatarUrl; isAdmin = currentUserName === 'DaniksGames';
            document.getElementById('authOverlay').style.display = 'none';
            document.getElementById('appContainer').style.display = 'flex';
            document.getElementById('sidebarName').innerText = currentUserName;
            document.getElementById('sidebarAvatar').src = currentUserAvatar || `https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(currentUserName)}`;
            await init();
            return;
        }
    }
    document.getElementById('authOverlay').style.display = 'flex';
})();
</script>
</body>
</html>
