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
            --muted-color: #94a3b8;
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
            --muted-color: #64748b;
        }
        
        body { background: var(--bg-body); min-height: 100vh; display: flex; justify-content: center; align-items: center; }
        
        @keyframes fadeInScale { from { opacity: 0; transform: scale(0.95); } to { opacity: 1; transform: scale(1); } }
        @keyframes slideInUp { from { opacity: 0; transform: translateY(30px); } to { opacity: 1; transform: translateY(0); } }
        @keyframes slideInLeft { from { opacity: 0; transform: translateX(-30px); } to { opacity: 1; transform: translateX(0); } }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(8px); } to { opacity: 1; transform: translateY(0); } }
        @keyframes highlight { 0% { background: rgba(74,108,247,0.5); } 100% { background: transparent; } }
        
        .modal, .auth-overlay, .admin-panel, .forward-modal, .read-by-modal, .profile-view-modal, .group-info-modal, .create-group-modal {
            animation: fadeInScale 0.2s ease-out;
        }
        .message { animation: slideInLeft 0.2s ease-out; }
        .my-message { animation: slideInUp 0.2s ease-out; }
        .contact-item { transition: all 0.2s ease; }
        .sidebar { transition: left 0.3s cubic-bezier(0.4, 0, 0.2, 1); }
        .selection-bar { transition: all 0.2s ease; }
        
        .drop-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(74,108,247,0.9); display: none; justify-content: center; align-items: center; z-index: 9999; color: white; font-size: 2rem; flex-direction: column; gap: 20px; pointer-events: none; }
        .drop-overlay.visible { display: flex; }
        
        .unread-badge { background: var(--unread-badge); color: white; border-radius: 50%; min-width: 20px; height: 20px; padding: 0 6px; font-size: 0.7rem; font-weight: bold; display: inline-flex; align-items: center; justify-content: center; margin-left: 8px; }
        .contact-name-wrapper { display: flex; align-items: center; justify-content: space-between; width: 100%; }
        .contact-actions { display: flex; gap: 5px; }
        .contact-delete { background: rgba(239,68,68,0.8); border: none; color: white; width: 28px; height: 28px; border-radius: 50%; cursor: pointer; font-size: 0.7rem; display: flex; align-items: center; justify-content: center; }
        .group-actions { display: flex; gap: 5px; margin-left: auto; }
        .group-action-btn { background: rgba(0,0,0,0.1); border: none; border-radius: 50%; width: 28px; height: 28px; cursor: pointer; color: var(--other-text); display: flex; align-items: center; justify-content: center; }
        .muted-badge { color: var(--muted-color); margin-left: 5px; font-size: 0.7rem; }
        .read-stats { font-size: 0.6rem; cursor: pointer; margin-left: 8px; color: var(--read-color); }
        
        @media (max-width: 700px) {
            body { padding: 0; }
            .app-container { border-radius: 0 !important; height: 100vh !important; width: 100% !important; max-width: 100% !important; }
            .sidebar { position: fixed !important; left: -100% !important; top: 0 !important; height: 100vh !important; width: 85% !important; max-width: 320px !important; z-index: 2000 !important; transition: left 0.3s cubic-bezier(0.4,0,0.2,1) !important; box-shadow: 2px 0 10px rgba(0,0,0,0.1) !important; }
            .sidebar.open { left: 0 !important; }
            .chat-main { width: 100% !important; }
            .message { max-width: 90% !important; }
            .menu-toggle { display: flex !important; background: rgba(255,255,255,0.2); border: none; width: 40px; height: 40px; border-radius: 50%; color: white; font-size: 1.2rem; cursor: pointer; align-items: center; justify-content: center; }
            .contact-actions { flex-direction: column; gap: 3px; }
            .contact-delete { width: 24px; height: 24px; font-size: 0.6rem; }
            .attach-menu { bottom: 70px !important; left: 10px !important; right: 10px !important; }
        }
        
        .app-container { width: 100%; max-width: 1300px; height: 95vh; background: var(--chat-bg); border-radius: 24px; display: flex; overflow: hidden; box-shadow: 0 20px 60px rgba(0,0,0,0.3); }
        .sidebar { width: 320px; background: var(--sidebar-bg); border-right: 1px solid var(--input-border); display: flex; flex-direction: column; }
        .sidebar-header { background: var(--header-bg); color: white; padding: 16px; }
        .sidebar-header h2 { font-size: 1.2rem; display: flex; align-items: center; gap: 8px; }
        .user-info-row { display: flex; align-items: center; gap: 10px; margin-top: 12px; padding-top: 8px; border-top: 1px solid rgba(255,255,255,0.2); cursor: pointer; }
        .user-info-row img { width: 40px; height: 40px; border-radius: 50%; object-fit: cover; }
        .search-box { padding: 12px; border-bottom: 1px solid var(--input-border); }
        .search-box input { width: 100%; padding: 10px 14px; border-radius: 20px; border: 1px solid var(--input-border); background: var(--input-bg); color: var(--other-text); outline: none; font-size: 16px; }
        .contacts-list { flex: 1; overflow-y: auto; }
        .contact-item { display: flex; align-items: center; gap: 12px; padding: 12px 16px; cursor: pointer; transition: background 0.15s; border-bottom: 1px solid var(--input-border); }
        .contact-item.active { background: var(--active-chat); border-left: 3px solid var(--icon-color); }
        .contact-avatar { width: 48px; height: 48px; border-radius: 50%; object-fit: cover; }
        .avatar-wrapper { position: relative; }
        .online-badge { position: absolute; bottom: 2px; right: 2px; width: 12px; height: 12px; border-radius: 50%; border: 2px solid var(--sidebar-bg); }
        .online-badge.online { background: var(--online-color); }
        .online-badge.offline { background: var(--offline-color); }
        .contact-info { flex: 1; min-width: 0; }
        .contact-name { font-weight: 600; color: var(--other-text); }
        .contact-status { font-size: 0.7rem; opacity: 0.7; margin-top: 2px; }
        
        .chat-main { flex: 1; display: flex; flex-direction: column; background: var(--chat-bg); }
        .chat-header { background: var(--header-bg); color: white; padding: 12px 16px; display: flex; align-items: center; gap: 12px; }
        .chat-header-avatar { width: 44px; height: 44px; border-radius: 50%; object-fit: cover; cursor: pointer; }
        .chat-header-info { flex: 1; min-width: 0; cursor: pointer; }
        .chat-header-name { font-weight: 600; font-size: 1.1rem; }
        .chat-header-status { font-size: 0.7rem; opacity: 0.8; }
        .chat-header-buttons { display: flex; gap: 6px; }
        .icon-btn { background: rgba(255,255,255,0.2); border: none; width: 38px; height: 38px; border-radius: 50%; cursor: pointer; color: white; font-size: 1rem; display: flex; align-items: center; justify-content: center; }
        
        .messages-area { flex: 1; overflow-y: auto; padding: 16px; display: flex; flex-direction: column; gap: 10px; background: var(--message-area-bg); }
        .message { display: flex; max-width: 80%; position: relative; scroll-margin-top: 80px; }
        .message.selected { background: var(--selected-message); border-radius: 12px; }
        .my-message { align-self: flex-end; justify-content: flex-end; }
        .other-message { align-self: flex-start; }
        .system-message { align-self: center; max-width: 90%; }
        .bubble { padding: 8px 14px; border-radius: 20px; background: var(--other-bubble); color: var(--other-text); word-break: break-word; }
        .system-message .bubble { background: var(--system-bubble); color: var(--system-text); text-align: center; font-size: 0.85rem; }
        .my-message .bubble { background: var(--my-bubble); color: var(--my-text); border-bottom-right-radius: 4px; }
        .message-header { display: flex; align-items: center; gap: 8px; margin-bottom: 5px; font-size: 0.75rem; cursor: pointer; }
        .msg-avatar { width: 24px; height: 24px; border-radius: 50%; object-fit: cover; cursor: pointer; }
        .message-name { font-weight: 600; cursor: pointer; }
        .reply-preview { font-size: 0.7rem; background: rgba(0,0,0,0.08); padding: 6px 10px; border-radius: 12px; margin-bottom: 6px; border-left: 3px solid var(--icon-color); cursor: pointer; }
        .media-content { max-width: 200px; max-height: 200px; border-radius: 12px; margin-top: 6px; cursor: pointer; }
        .video-content { max-width: 200px; max-height: 200px; border-radius: 12px; margin-top: 6px; }
        .circle-video { width: 180px; height: 180px; border-radius: 50%; overflow: hidden; margin-top: 6px; background: #000; }
        .circle-video video { width: 100%; height: 100%; object-fit: cover; }
        audio { max-width: 180px; height: 36px; margin-top: 6px; }
        .file-attachment { display: flex; align-items: center; gap: 10px; padding: 10px 14px; background: rgba(0,0,0,0.05); border-radius: 12px; margin-top: 6px; cursor: pointer; }
        .file-attachment i { font-size: 1.5rem; color: var(--icon-color); }
        .contact-card { display: flex; align-items: center; gap: 12px; padding: 8px 12px; background: var(--input-bg); border-radius: 16px; margin-top: 8px; cursor: pointer; border: 1px solid var(--input-border); }
        .contact-card img { width: 40px; height: 40px; border-radius: 50%; }
        .message-time { font-size: 0.55rem; opacity: 0.7; margin-top: 4px; display: flex; align-items: center; gap: 4px; }
        .read-status { font-size: 0.55rem; margin-left: 6px; }
        .read-status.read { color: var(--read-color); font-weight: bold; }
        
        .delete-btn, .reply-btn, .forward-btn { position: absolute; top: -8px; color: white; border: none; width: 28px; height: 28px; border-radius: 50%; cursor: pointer; font-size: 11px; opacity: 0; transition: opacity 0.2s; z-index: 10; display: flex; align-items: center; justify-content: center; }
        .delete-btn { right: -8px; background: #ef4444; }
        .reply-btn { left: -8px; background: #8b5cf6; }
        .forward-btn { left: 24px; background: #10b981; }
        @media (hover: hover) { .message:hover .delete-btn, .message:hover .reply-btn, .message:hover .forward-btn { opacity: 1; } }
        
        .message-checkbox { position: absolute; left: -30px; top: 50%; transform: translateY(-50%); width: 20px; height: 20px; cursor: pointer; opacity: 0; transition: opacity 0.2s; accent-color: var(--icon-color); }
        .message:hover .message-checkbox, .message.selected .message-checkbox { opacity: 1; }
        
        .selection-bar { position: fixed; bottom: 20px; left: 50%; transform: translateX(-50%); background: var(--header-bg); color: white; padding: 10px 20px; border-radius: 40px; display: none; align-items: center; gap: 15px; z-index: 1500; }
        .selection-bar.visible { display: flex; animation: slideInUp 0.2s ease-out; }
        .selection-bar button { background: rgba(255,255,255,0.2); border: none; color: white; padding: 8px 16px; border-radius: 20px; cursor: pointer; display: flex; align-items: center; gap: 6px; }
        .selection-bar .cancel-selection { background: #ef4444; }
        
        .reply-indicator { background: var(--input-bg); padding: 8px 12px; margin: 0 16px; border-radius: 12px; display: flex; justify-content: space-between; align-items: center; font-size: 0.8rem; border-left: 3px solid var(--icon-color); }
        .media-preview { background: var(--input-bg); padding: 8px 12px; margin: 0 16px 8px; border-radius: 12px; display: none; align-items: center; gap: 10px; }
        .media-preview.visible { display: flex; }
        .input-area { padding: 12px 16px; background: var(--input-bg); border-top: 1px solid var(--input-border); position: relative; }
        .input-row { display: flex; gap: 8px; align-items: flex-end; }
        .message-input { flex: 1; padding: 12px 16px; border-radius: 25px; border: 1px solid var(--input-border); background: var(--input-bg); color: var(--other-text); outline: none; resize: none; font-size: 16px; max-height: 150px; min-height: 50px; }
        .attach-btn { background: transparent; border: none; font-size: 1.3rem; cursor: pointer; color: var(--icon-color); width: 44px; height: 44px; border-radius: 50%; display: flex; align-items: center; justify-content: center; }
        .send-btn { background: var(--icon-color); color: white; border: none; width: 50px; height: 50px; border-radius: 50%; cursor: pointer; font-size: 1.2rem; display: flex; align-items: center; justify-content: center; }
        .attach-menu { position: absolute; bottom: 80px; left: 16px; background: var(--input-bg); border: 1px solid var(--input-border); border-radius: 16px; padding: 8px; display: none; grid-template-columns: repeat(3, 1fr); gap: 4px; z-index: 100; }
        .attach-menu.visible { display: grid; animation: fadeInScale 0.15s ease-out; }
        .attach-menu-btn { background: transparent; border: none; color: var(--other-text); padding: 10px 12px; border-radius: 12px; cursor: pointer; display: flex; flex-direction: column; align-items: center; gap: 4px; font-size: 0.75rem; }
        .attach-menu-btn i { font-size: 1.2rem; color: var(--icon-color); }
        
        .recording-indicator { position: fixed; bottom: 100px; left: 50%; transform: translateX(-50%); background: rgba(239,68,68,0.9); color: white; padding: 12px 24px; border-radius: 40px; display: none; align-items: center; gap: 10px; z-index: 1000; animation: pulse 1.5s infinite; }
        .recording-indicator.visible { display: flex; }
        @keyframes pulse { 0% { transform: translateX(-50%) scale(1); } 50% { transform: translateX(-50%) scale(1.05); } 100% { transform: translateX(-50%) scale(1); } }
        
        .camera-preview { position: fixed; bottom: 100px; right: 20px; width: 120px; height: 160px; border-radius: 12px; overflow: hidden; z-index: 1000; border: 2px solid white; display: none; }
        .camera-preview.visible { display: block; }
        .camera-preview video { width: 100%; height: 100%; object-fit: cover; }
        
        .forward-modal, .read-by-modal, .profile-view-modal, .group-info-modal, .create-group-modal { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.95); z-index: 3500; display: none; justify-content: center; align-items: center; }
        .forward-content, .read-by-content, .profile-view-content, .group-info-content, .create-group-content { background: var(--chat-bg); border-radius: 28px; padding: 24px; width: 90%; max-width: 400px; max-height: 80vh; overflow-y: auto; }
        .forward-contact { display: flex; align-items: center; gap: 12px; padding: 12px; cursor: pointer; border-radius: 12px; }
        .forward-contact:hover { background: var(--contact-hover); }
        .forward-contact img { width: 44px; height: 44px; border-radius: 50%; }
        .forward-cancel { background: #94a3b8; color: white; border: none; padding: 12px; border-radius: 30px; width: 100%; margin-top: 16px; cursor: pointer; }
        
        .profile-view-avatar { width: 120px; height: 120px; border-radius: 50%; object-fit: cover; margin: 0 auto 16px; display: block; }
        .profile-view-buttons { display: flex; gap: 12px; justify-content: center; margin-top: 16px; }
        .send-msg-btn { background: var(--icon-color); color: white; border: none; padding: 10px 20px; border-radius: 30px; cursor: pointer; }
        .close-profile-btn { background: #94a3b8; color: white; border: none; padding: 10px 20px; border-radius: 30px; cursor: pointer; }
        
        .member-item { display: flex; align-items: center; gap: 10px; padding: 8px; cursor: pointer; border-radius: 12px; }
        .member-item:hover { background: var(--contact-hover); }
        .member-item input { width: 20px; margin: 0; }
        .member-item img { width: 36px; height: 36px; border-radius: 50%; }
        .member-item-admin { display: flex; align-items: center; gap: 12px; padding: 10px; border-bottom: 1px solid var(--input-border); }
        .member-item-admin img { width: 40px; height: 40px; border-radius: 50%; }
        .member-info { flex: 1; }
        .member-name { font-weight: 600; color: var(--other-text); }
        .member-role { font-size: 0.7rem; opacity: 0.7; }
        .kick-btn { background: #ef4444; border: none; color: white; padding: 6px 12px; border-radius: 20px; cursor: pointer; }
        .leave-group-btn, .delete-group-btn, .mute-btn { background: var(--icon-color); color: white; border: none; padding: 12px; border-radius: 30px; width: 100%; margin-top: 10px; cursor: pointer; }
        .delete-group-btn { background: #dc2626; }
        .create-group-btn { background: var(--icon-color); color: white; border: none; padding: 12px; border-radius: 30px; width: 100%; margin-top: 12px; cursor: pointer; }
        .add-group-btn { background: var(--icon-color); color: white; border: none; padding: 8px 16px; border-radius: 20px; margin: 8px 12px; cursor: pointer; display: flex; align-items: center; justify-content: center; gap: 8px; }
        
        .auth-card, .modal-content { background: white; border-radius: 28px; padding: 28px; width: 90%; max-width: 400px; text-align: center; }
        .dark .auth-card, .dark .modal-content { background: #1e293b; color: white; }
        .dark .auth-card input, .dark .modal-content input, .dark .modal-content textarea { background: #334155; color: white; border-color: #475569; }
        .auth-card input, .modal-content input, .modal-content textarea { width: 100%; padding: 14px; margin: 12px 0; border-radius: 30px; border: 1px solid #ddd; font-size: 16px; outline: none; }
        .auth-card button, .modal-content button { background: #4a6cf7; color: white; border: none; padding: 14px; border-radius: 30px; width: 100%; font-size: 1rem; cursor: pointer; margin-top: 10px; }
        .avatar-preview { width: 100px; height: 100px; border-radius: 50%; object-fit: cover; margin: 10px auto; display: block; }
        
        .admin-panel { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.95); z-index: 2500; display: none; flex-direction: column; overflow-y: auto; }
        .admin-panel-content { padding: 20px; padding-top: 70px; max-width: 800px; margin: 0 auto; }
        .close-admin { position: fixed; top: 15px; right: 15px; background: #ef4444; padding: 12px 24px; border: none; border-radius: 30px; color: white; cursor: pointer; z-index: 2600; }
        .user-item { background: #1e293b; margin: 8px 0; padding: 14px; border-radius: 16px; display: flex; justify-content: space-between; flex-wrap: wrap; gap: 10px; color: white; align-items: center; }
        .user-item button { padding: 8px 14px; border-radius: 20px; border: none; cursor: pointer; margin-left: 5px; }
        
        .sidebar-overlay { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.5); z-index: 1999; }
        .sidebar-overlay.visible { display: block; }
        @media (min-width: 701px) { .sidebar-overlay { display: none !important; } }
        
        .highlight-message { animation: highlight 1s; }
        .menu-toggle { display: none; }
    </style>
</head>
<body>

<div class="drop-overlay" id="dropOverlay"><i class="fas fa-cloud-upload-alt"></i><span>Отпустите для загрузки</span></div>
<div class="sidebar-overlay" id="sidebarOverlay"></div>
<div id="recordingIndicator" class="recording-indicator"><i class="fas fa-microphone"></i><span id="recordingTimer" class="recording-timer">00:00</span><span>Идёт запись...</span></div>
<div id="cameraPreview" class="camera-preview"><video id="previewVideo" autoplay muted playsinline></video></div>

<div class="selection-bar" id="selectionBar">
    <span id="selectedCount">0 сообщений</span>
    <button id="forwardSelectedBtn"><i class="fas fa-share"></i> Переслать</button>
    <button id="deleteSelectedBtn" style="background:#ef4444;"><i class="fas fa-trash"></i> Удалить</button>
    <button id="cancelSelectionBtn" class="cancel-selection"><i class="fas fa-times"></i> Отмена</button>
</div>

<div id="forwardModal" class="forward-modal"><div class="forward-content"><h3><i class="fas fa-share"></i> Переслать</h3><div id="forwardContactsList"></div><button class="forward-cancel" onclick="closeForwardModal()">Отмена</button></div></div>
<div id="readByModal" class="read-by-modal"><div class="read-by-content"><h3><i class="fas fa-check-circle"></i> Прочитали:</h3><div id="readByList"></div><button class="read-by-close" onclick="closeReadByModal()">Закрыть</button></div></div>
<div id="profileViewModal" class="profile-view-modal"><div class="profile-view-content"><img id="profileViewAvatar" class="profile-view-avatar"><div class="profile-view-name" id="profileViewName"></div><div class="profile-view-bio" id="profileViewBio"></div><div class="profile-view-status" id="profileViewStatus"></div><div class="profile-view-buttons"><button class="send-msg-btn" id="profileViewSendMsg">💬 Написать</button><button class="close-profile-btn" onclick="closeProfileView()">Закрыть</button></div></div></div>
<div id="groupInfoModal" class="group-info-modal"><div class="group-info-content"><h3 id="groupInfoTitle"></h3><div id="groupMembersListAdmin"></div><button id="leaveGroupBtn" class="leave-group-btn">🚪 Покинуть группу</button><button id="deleteGroupBtn" class="delete-group-btn" style="display:none;">🗑️ Удалить группу</button><button id="muteGroupBtn" class="mute-btn">🔇 Выключить звук</button><button class="forward-cancel" onclick="closeGroupInfo()">Закрыть</button></div></div>
<div id="createGroupModal" class="create-group-modal"><div class="create-group-content"><h3><i class="fas fa-users"></i> Создать группу</h3><input type="text" id="groupName" placeholder="Название группы"><textarea id="groupDescription" placeholder="Описание группы"></textarea><div id="memberSelectList"></div><button id="createGroupBtn" class="create-group-btn">✅ Создать группу</button><button class="forward-cancel" onclick="closeCreateGroupModal()">Отмена</button></div></div>

<div class="app-container" id="appContainer" style="display: none;">
    <div class="sidebar" id="sidebar">
        <div class="sidebar-header">
            <h2><i class="fas fa-comments"></i> xaMOff</h2>
            <div class="user-info-row" onclick="showMyProfile()"><img id="sidebarAvatar"><span id="sidebarName"></span></div>
        </div>
        <div class="search-box"><input type="text" id="searchInput" placeholder="🔍 Поиск по нику..."></div>
        <button class="add-group-btn" id="createGroupBtnSidebar"><i class="fas fa-plus-circle"></i> Создать группу</button>
        <div class="contacts-list" id="contactsList"></div>
    </div>
    <div class="chat-main">
        <div class="chat-header">
            <button class="menu-toggle" id="menuToggle"><i class="fas fa-bars"></i></button>
            <img id="chatHeaderAvatar" class="chat-header-avatar" onclick="showCurrentChatProfile()">
            <div class="chat-header-info" onclick="showCurrentChatProfile()">
                <div class="chat-header-name" id="chatHeaderName"></div>
                <div class="chat-header-status" id="chatHeaderStatus"></div>
            </div>
            <div class="chat-header-buttons">
                <button class="icon-btn" id="settingsBtn"><i class="fas fa-user-cog"></i></button>
                <button class="icon-btn" id="adminPanelBtn"><i class="fas fa-shield-alt"></i></button>
                <button class="icon-btn" id="themeToggle"><i class="fas fa-moon"></i></button>
                <button class="icon-btn" id="logoutBtn" style="background:#ef4444;"><i class="fas fa-sign-out-alt"></i></button>
            </div>
        </div>
        <div class="messages-area" id="messagesArea"><div class="messages-loading">Загрузка...</div></div>
        <div id="replyIndicator" class="reply-indicator" style="display:none;"><span style="flex:1;"><i class="fas fa-reply"></i> <span id="replyToName"></span>: "<span id="replyToText"></span>"</span><button id="cancelReplyBtn">✕</button></div>
        <div id="mediaPreview" class="media-preview"><img id="previewImage"><div class="media-preview-info"><div class="media-preview-name" id="previewName"></div><input type="text" id="mediaCaption" placeholder="Подпись..."></div><button id="cancelMediaPreview">✕</button></div>
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
    <div class="auth-card"><h2>📱 xaMOff Messenger</h2><input type="text" id="loginName" placeholder="Никнейм"><input type="password" id="loginPassword" placeholder="Пароль"><div id="authError" style="color:red;"></div><button id="authBtn">Войти / Регистрация</button></div>
</div>

<div id="profileModal" class="modal">
    <div class="modal-content"><h3>Настройки профиля</h3><img id="avatarPreview" class="avatar-preview"><input type="file" id="modalAvatar" accept="image/*"><input type="text" id="modalName" placeholder="Новый никнейм"><textarea id="modalBio" placeholder="Описание профиля" rows="3"></textarea><button id="saveProfileBtn">💾 Сохранить</button><button id="closeModalBtn" style="background:#94a3b8;">Отмена</button></div>
</div>

<div id="adminPanel" class="admin-panel">
    <button class="close-admin" id="closeAdminBtn">✕ Закрыть</button>
    <div class="admin-panel-content">
        <h3>👑 Админ-панель</h3>
        <div style="background:#1e293b; border-radius:16px; padding:16px; margin:15px 0;">
            <h4 style="color:white;"><i class="fas fa-bullhorn"></i> Системные уведомления</h4>
            <div style="display:flex; gap:8px; flex-wrap:wrap; margin:12px 0;">
                <button class="preset-btn update" onclick="sendSystemNotification('update')" style="background:#3b82f6; color:white; padding:10px 16px; border-radius:20px; border:none;">🔄 Установка обновления</button>
                <button class="preset-btn success" onclick="sendSystemNotification('success')" style="background:#10b981; color:white; padding:10px 16px; border-radius:20px; border:none;">✅ Обновление установлено</button>
                <button class="preset-btn error" onclick="sendSystemNotification('error')" style="background:#ef4444; color:white; padding:10px 16px; border-radius:20px; border:none;">❌ Ошибка обновления</button>
            </div>
            <div style="display:flex; gap:8px;"><input type="text" id="customNotifyText" placeholder="Своё уведомление..." style="flex:1; padding:10px; border-radius:20px; border:none;"><button onclick="sendCustomNotification()" style="background:#8b5cf6; color:white; border:none; padding:10px 20px; border-radius:20px;">📢 Отправить</button></div>
        </div>
        <button id="clearGlobalChatBtn" style="background:#f59e0b; padding:12px; border:none; border-radius:16px; margin:10px 0; cursor:pointer; width:100%;">🗑️ ОЧИСТИТЬ ОБЩИЙ ЧАТ</button>
        <div id="usersList"></div>
    </div>
</div>

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

// ==================== ГЛОБАЛЬНЫЕ ПЕРЕМЕННЫЕ ====================
let currentUserId = null, currentUserName = null, currentUserAvatar = null, currentUserBio = null, isBlocked = false;
let currentChat = { type: 'group', id: 'global', name: 'Общий чат' };
let replyingTo = null;
let contacts = [];
let groups = [];
let unreadCounts = new Map();
let mutedChats = new Set();
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

// ==================== ВСПОМОГАТЕЛЬНЫЕ ====================
function formatFileSize(bytes) { if(bytes<1024) return bytes+' B'; if(bytes<1048576) return (bytes/1024).toFixed(1)+' KB'; return (bytes/1048576).toFixed(1)+' MB'; }
function playSound() { try { if(!audioCtx) audioCtx=new AudioContext(); if(audioCtx.state==='suspended') audioCtx.resume(); const o=audioCtx.createOscillator(), g=audioCtx.createGain(); o.connect(g); g.connect(audioCtx.destination); o.frequency.value=880; g.gain.value=0.15; o.start(); g.gain.exponentialRampToValueAtTime(0.00001, audioCtx.currentTime+0.3); o.stop(audioCtx.currentTime+0.3); } catch(e){} }
function escapeHtml(s) { if(!s) return ''; return String(s).replace(/[&<>]/g, m=>({'&':'&amp;','<':'&lt;','>':'&gt;'}[m])); }
function getAvatarUrl(name, avatar) { if(avatar) return avatar; return `https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(name)}`; }

// ==================== ЗАГРУЗКА ДАННЫХ ====================
async function loadAllUsers() {
    const snap = await db.ref('users').once('value');
    allUsers = [];
    for(let id in snap.val()) { const u=snap.val()[id]; if(!u.blocked && id!==currentUserId) allUsers.push({id,name:u.name,avatar:u.avatarUrl||''}); }
}

async function loadGroups() {
    const snap = await db.ref('groups').once('value');
    const groupsData = snap.val() || {};
    groups = [];
    for(let id in groupsData) {
        const group = groupsData[id];
        if(group.members && group.members[currentUserId]) {
            groups.push({ id, name: group.name, description: group.description, creator: group.creator, members: group.members, createdAt: group.createdAt });
        }
    }
}

async function createGroup(name, description, selectedMembers) {
    const checkSnap = await db.ref('groups').orderByChild('name').equalTo(name).once('value');
    if(checkSnap.exists()) throw new Error('Группа с таким названием уже существует');
    const groupId = Date.now().toString();
    const members = { [currentUserId]: true };
    selectedMembers.forEach(m => { members[m] = true; });
    await db.ref('groups/' + groupId).set({ id: groupId, name, description: description || '', creator: currentUserId, members, createdAt: Date.now() });
    await loadGroups();
    await loadContacts();
    return groupId;
}

async function deleteGroup(groupId) {
    const group = groups.find(g => g.id === groupId);
    if(!group || group.creator !== currentUserId) { alert('Только создатель может удалить группу'); return false; }
    if(confirm(`Удалить группу "${group.name}"? Все сообщения будут потеряны.`)) {
        await db.ref('groups/' + groupId).remove();
        await db.ref(`group_messages/${groupId}`).remove();
        await loadGroups();
        await loadContacts();
        if(currentChat.id === groupId && contacts.length) switchChat(contacts[0].id, contacts[0].name, contacts[0].isGroup);
        return true;
    }
    return false;
}

async function leaveGroup(groupId) {
    const group = groups.find(g => g.id === groupId);
    if(!group) return;
    if(group.creator === currentUserId) { alert('Создатель не может покинуть группу. Удалите её.'); return; }
    if(confirm(`Покинуть группу "${group.name}"?`)) {
        await db.ref('groups/' + groupId + '/members/' + currentUserId).remove();
        await loadGroups();
        await loadContacts();
        if(currentChat.id === groupId && contacts.length) switchChat(contacts[0].id, contacts[0].name, contacts[0].isGroup);
    }
}

async function kickFromGroup(groupId, userId) {
    const group = groups.find(g => g.id === groupId);
    if(!group || group.creator !== currentUserId) { alert('Только создатель может исключать участников'); return; }
    if(userId === group.creator) return;
    await db.ref('groups/' + groupId + '/members/' + userId).remove();
    await loadGroups();
    await loadContacts();
}

function toggleMute(chatId) {
    if(mutedChats.has(chatId)) mutedChats.delete(chatId);
    else mutedChats.add(chatId);
    localStorage.setItem('mutedChats', JSON.stringify([...mutedChats]));
    renderContacts();
    if(currentChat.id === chatId) document.getElementById('chatHeaderStatus').innerHTML = currentChat.type==='group' ? '👥 Группа' + (mutedChats.has(chatId) ? ' 🔇' : '') : document.getElementById('chatHeaderStatus').innerHTML;
}
function isMuted(chatId) { return mutedChats.has(chatId); }

async function loadContacts() {
    const contactsRef = db.ref('users/' + currentUserId + '/contacts');
    const snapshot = await contactsRef.once('value');
    const contactsData = snapshot.val() || {};
    const contactsMap = new Map();
    for(let id in contactsData) {
        const userSnap = await db.ref('users/' + id).once('value');
        if(userSnap.exists() && !userSnap.val().blocked) {
            contactsMap.set(id, { id, name: userSnap.val().name, avatar: userSnap.val().avatarUrl || '', isGroup: false });
        }
    }
    contacts = Array.from(contactsMap.values());
    for(let group of groups) {
        contacts.unshift({ id: group.id, name: '👥 ' + group.name, avatar: '', isGroup: true, groupData: group });
    }
    renderContacts();
}

function renderContacts() {
    const container = document.getElementById('contactsList');
    container.innerHTML = contacts.map(c => `
        <div class="contact-item ${currentChat.id === c.id ? 'active' : ''}" onclick="switchChat('${c.id}', '${c.name.replace(/'/g, "\\'")}', ${c.isGroup})">
            <div class="avatar-wrapper"><img class="contact-avatar" src="${getAvatarUrl(c.name, c.avatar)}"><div class="online-badge offline" id="online-${c.id}"></div></div>
            <div class="contact-info"><div class="contact-name-wrapper"><span class="contact-name">${escapeHtml(c.name)}${isMuted(c.id) ? ' <i class="fas fa-volume-mute muted-badge"></i>' : ''}</span>${unreadCounts.get(c.id) > 0 ? `<span class="unread-badge">${unreadCounts.get(c.id)}</span>` : ''}</div><div class="contact-status" id="status-${c.id}">${c.isGroup ? '👥 Группа' : 'Загрузка...'}</div></div>
            ${!c.isGroup ? `<div class="contact-actions"><button class="contact-delete" onclick="event.stopPropagation(); clearChatWithContact('${c.id}')" title="Очистить чат"><i class="fas fa-trash-alt"></i></button><button class="contact-delete" onclick="event.stopPropagation(); deleteContact('${c.id}')" title="Удалить контакт"><i class="fas fa-user-minus"></i></button><button class="contact-delete" onclick="event.stopPropagation(); toggleMute('${c.id}')" title="${isMuted(c.id) ? 'Включить звук' : 'Выключить звук'}"><i class="fas fa-volume-${isMuted(c.id) ? 'up' : 'mute'}"></i></button></div>` : `<div class="group-actions"><button class="group-action-btn" onclick="event.stopPropagation(); toggleMute('${c.id}')" title="${isMuted(c.id) ? 'Включить звук' : 'Выключить звук'}"><i class="fas fa-volume-${isMuted(c.id) ? 'up' : 'mute'}"></i></button></div>`}
        </div>
    `).join('');
    updateAllOnlineStatuses();
}

window.deleteContact = async (contactId) => {
    if(confirm('Удалить контакт?')) {
        await db.ref('users/' + currentUserId + '/contacts/' + contactId).remove();
        await loadContacts();
        if(currentChat.id === contactId && contacts.length) switchChat(contacts[0].id, contacts[0].name, contacts[0].isGroup);
    }
};

window.clearChatWithContact = async (contactId) => {
    if(confirm('Очистить весь чат?')) {
        const ids = [currentUserId, contactId].sort();
        await db.ref(`private_messages/${ids[0]}_${ids[1]}`).remove();
        if(currentChat.id === contactId) loadMessages();
        alert('Чат очищен');
    }
};

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
        document.getElementById('chatHeaderStatus').innerHTML = isOnline ? '🟢 В сети' : '⚫ Не в сети';
    }
}

window.switchChat = async (chatId, chatName, isGroup) => {
    currentChat = { type: isGroup ? 'group' : 'user', id: chatId, name: chatName.replace('👥 ', '') };
    document.getElementById('chatHeaderName').innerText = currentChat.name;
    if(isGroup) document.getElementById('chatHeaderStatus').innerHTML = '👥 Группа' + (isMuted(chatId) ? ' 🔇' : '');
    else { const isOnline = await getUserOnlineStatus(chatId); document.getElementById('chatHeaderStatus').innerHTML = isOnline ? '🟢 В сети' : '⚫ Не в сети'; }
    const contact = contacts.find(c => c.id === chatId);
    document.getElementById('chatHeaderAvatar').src = contact?.avatar || getAvatarUrl(currentChat.name, '');
    replyingTo = null; document.getElementById('replyIndicator').style.display = 'none';
    clearSelection(); closeSidebar();
    if(unreadCounts.has(chatId)) { unreadCounts.set(chatId, 0); renderContacts(); }
    renderContacts();
    messagesLoaded = false;
    loadMessages();
};

function getChatPath() {
    if(currentChat.type === 'group') return `group_messages/${currentChat.id}`;
    const ids = [currentUserId, currentChat.id].sort();
    return `private_messages/${ids[0]}_${ids[1]}`;
}

// ==================== ОТПРАВКА СООБЩЕНИЙ ====================
async function sendMessageWithMedia(mediaType, mediaUrl, caption, fileData = null, isCircle = false, contactData = null) {
    if(isBlocked) return alert('Вы заблокированы');
    const msgData = { userId: currentUserId, name: currentUserName, avatar: currentUserAvatar || '', text: caption || '', mediaType, mediaUrl, time: Date.now(), read: false, delivered: false, readBy: { [currentUserId]: true } };
    if(isCircle) msgData.isCircle = true;
    if(fileData) { msgData.fileName = fileData.name; msgData.fileSize = fileData.size; msgData.fileType = fileData.type; }
    if(contactData) { msgData.contactData = contactData; msgData.mediaType = 'contact'; }
    if(replyingTo) { msgData.replyTo = replyingTo; replyingTo = null; document.getElementById('replyIndicator').style.display = 'none'; }
    await db.ref(getChatPath()).push(msgData);
    if(!isMuted(currentChat.id)) playSound();
}

async function sendTextMessage() {
    const input = document.getElementById('messageInput');
    const text = input.value.trim();
    if(!text || isBlocked) return;
    const msgData = { userId: currentUserId, name: currentUserName, avatar: currentUserAvatar || '', text, time: Date.now(), read: false, delivered: false, readBy: { [currentUserId]: true } };
    if(replyingTo) { msgData.replyTo = replyingTo; replyingTo = null; document.getElementById('replyIndicator').style.display = 'none'; }
    await db.ref(getChatPath()).push(msgData);
    input.value = ''; input.style.height = 'auto';
    if(!isMuted(currentChat.id)) playSound();
}

// ==================== ЗАГРУЗКА СООБЩЕНИЙ ====================
function loadMessages() {
    const messagesArea = document.getElementById('messagesArea');
    messagesArea.innerHTML = '<div class="messages-loading">Загрузка...</div>';
    const path = getChatPath();
    const msgsRef = db.ref(path);
    if(!processedMsgIds.get(path)) processedMsgIds.set(path, new Set());
    const processed = processedMsgIds.get(path);
    msgsRef.off();
    let hasMessages = false;
    msgsRef.orderByChild('time').limitToLast(50).on('child_added', (snap) => {
        if(!processed.has(snap.key)) {
            processed.add(snap.key);
            hasMessages = true;
            if(!messagesLoaded) { messagesArea.innerHTML = ''; messagesLoaded = true; }
            renderMessage(snap.key, snap.val(), path);
        }
    });
    setTimeout(() => { if(!hasMessages && !messagesLoaded) messagesArea.innerHTML = '<div class="no-messages">💬 Нет сообщений</div>'; messagesLoaded = true; }, 1500);
    msgsRef.on('child_changed', (snap) => updateMessageInUI(snap.key, snap.val()));
    msgsRef.on('child_removed', (snap) => removeMessageFromUI(snap.key));
}

function renderMessage(msgId, msg, chatPath) {
    if(!msg || document.getElementById(`msg-${msgId}`)) return;
    const isMe = msg.userId === currentUserId;
    const isSystem = msg.isSystem || msg.userId === 'system';
    const div = document.createElement('div');
    div.className = `message ${isSystem ? 'system-message' : (isMe ? 'my-message' : 'other-message')}`;
    div.id = `msg-${msgId}`;
    let replyHtml = '', forwardedHtml = '', mediaHtml = '';
    if(msg.replyTo) replyHtml = `<div class="reply-preview" onclick="scrollToMessage('${msg.replyTo.messageId}')"><i class="fas fa-reply"></i> <strong>${escapeHtml(msg.replyTo.userName)}</strong>: ${escapeHtml(msg.replyTo.text)}</div>`;
    if(msg.forwardedFrom) forwardedHtml = `<div style="font-size:0.65rem; opacity:0.7; margin-bottom:4px;"><i class="fas fa-share"></i> Переслано из ${escapeHtml(msg.forwardedFrom.chatName)}</div>`;
    if(msg.mediaType === 'image') mediaHtml = `<img src="${msg.mediaUrl}" class="media-content" onclick="window.open('${msg.mediaUrl}')" loading="lazy">`;
    else if(msg.mediaType === 'video') mediaHtml = msg.isCircle ? `<div class="circle-video"><video controls><source src="${msg.mediaUrl}"></video></div>` : `<video controls class="video-content"><source src="${msg.mediaUrl}"></video>`;
    else if(msg.mediaType === 'audio') mediaHtml = `<audio controls src="${msg.mediaUrl}"></audio>`;
    else if(msg.mediaType === 'file') mediaHtml = `<div class="file-attachment" onclick="window.open('${msg.mediaUrl}')"><i class="fas fa-file"></i><div><div>${escapeHtml(msg.fileName)}</div><div>${formatFileSize(msg.fileSize)}</div></div></div>`;
    else if(msg.mediaType === 'contact' && msg.contactData) mediaHtml = `<div class="contact-card" onclick="showUserProfile('${msg.contactData.userId}','${escapeHtml(msg.contactData.name)}')"><img src="${getAvatarUrl(msg.contactData.name, msg.contactData.avatar)}"><div><div class="contact-card-name">${escapeHtml(msg.contactData.name)}</div><div>Контакт</div></div><i class="fas fa-chevron-right"></i></div>`;
    const avatar = msg.avatar || getAvatarUrl(msg.name, '');
    const readByCount = msg.readBy ? Object.keys(msg.readBy).length - 1 : 0;
    const readStatsHtml = (isMe && currentChat.type === 'group' && readByCount > 0) ? `<span class="read-stats" onclick="showReadBy('${msgId}')">👁 ${readByCount}</span>` : '';
    let readStatus = '';
    if(isMe && currentChat.type === 'user' && !isSystem) readStatus = msg.read ? '<span class="read-status read">✓✓ Прочитано</span>' : (msg.delivered ? '<span class="read-status">✓✓ Доставлено</span>' : '<span class="read-status">✓ Отправлено</span>');
    const checkbox = `<input type="checkbox" class="message-checkbox" onchange="toggleMessageSelection('${msgId}', this.checked)">`;
    const deleteBtn = (isMe && !isSystem) ? `<button class="delete-btn" onclick="deleteMessage('${msgId}')"><i class="fas fa-trash"></i></button>` : '';
    const replyBtn = !isSystem ? `<button class="reply-btn" onclick="replyToMsg('${msgId}', '${escapeHtml(msg.name).replace(/'/g, "\\'")}', '${escapeHtml(msg.text || 'Медиа').replace(/'/g, "\\'")}')"><i class="fas fa-reply"></i></button>` : '';
    const forwardBtn = !isSystem ? `<button class="forward-btn" onclick="forwardSingleMessage('${msgId}')"><i class="fas fa-share"></i></button>` : '';
    const nameClick = `onclick="showUserProfile('${msg.userId}', '${escapeHtml(msg.name).replace(/'/g, "\\'")}')"`;
    if(isSystem) div.innerHTML = `<div class="bubble">${msg.text}<span class="message-time">${new Date(msg.time).toLocaleTimeString()}</span></div>`;
    else div.innerHTML = `<div class="bubble">${checkbox}${deleteBtn}${replyBtn}${forwardBtn}<div class="message-header"><img class="msg-avatar" src="${avatar}" ${nameClick}><span class="message-name" ${nameClick}>${escapeHtml(msg.name)}</span></div>${forwardedHtml}${replyHtml}${mediaHtml}${msg.text && !msg.mediaType ? `<div>${escapeHtml(msg.text)}</div>` : ''}<span class="message-time">${new Date(msg.time).toLocaleTimeString()} ${readStatus} ${readStatsHtml}</span></div>`;
    document.getElementById('messagesArea').appendChild(div);
    const isScrolledToBottom = document.getElementById('messagesArea').scrollHeight - document.getElementById('messagesArea').scrollTop - document.getElementById('messagesArea').clientHeight < 100;
    if(isScrolledToBottom) document.getElementById('messagesArea').scrollTop = document.getElementById('messagesArea').scrollHeight;
    if(!isMe && !msg.read && currentChat.type === 'user' && !isSystem) db.ref(`${chatPath}/${msgId}`).update({ read: true, [`readBy/${currentUserId}`]: true });
    else if(!isMe && currentChat.type === 'group' && !isSystem && !msg.readBy?.[currentUserId]) db.ref(`${chatPath}/${msgId}/readBy/${currentUserId}`).set(true);
    if(!isMe && document.hidden && Notification.permission==='granted' && !isSystem && !isMuted(currentChat.id)) { playSound(); new Notification(msg.name, { body: msg.text || 'Новое сообщение', icon: avatar }); }
    if(!isMe && currentChat.id !== (currentChat.type === 'group' ? currentChat.id : msg.userId)) {
        const chatId = currentChat.type === 'group' ? currentChat.id : msg.userId;
        unreadCounts.set(chatId, (unreadCounts.get(chatId)||0)+1);
        renderContacts();
    }
}

function updateMessageInUI(msgId, msg) { const el = document.getElementById(`msg-${msgId}`); if(el && msg.userId===currentUserId && currentChat.type==='user') { const s=el.querySelector('.read-status'); if(s) s.innerHTML=msg.read?'✓✓ Прочитано':(msg.delivered?'✓✓ Доставлено':'✓ Отправлено'); } }
function removeMessageFromUI(msgId) { const el=document.getElementById(`msg-${msgId}`); if(el) el.remove(); selectedMessages.delete(msgId); updateSelectionBar(); }

// ==================== ВЫДЕЛЕНИЕ СООБЩЕНИЙ ====================
window.toggleMessageSelection = function(msgId, checked) { if(checked) { selectedMessages.add(msgId); document.getElementById(`msg-${msgId}`).classList.add('selected'); } else { selectedMessages.delete(msgId); document.getElementById(`msg-${msgId}`).classList.remove('selected'); } updateSelectionBar(); };
function updateSelectionBar() { const bar=document.getElementById('selectionBar'); const c=selectedMessages.size; if(c>0) { bar.classList.add('visible'); document.getElementById('selectedCount').innerText=`${c} ${getMessageWord(c)}`; } else bar.classList.remove('visible'); }
function getMessageWord(c) { if(c%10===1 && c%100!==11) return 'сообщение'; if([2,3,4].includes(c%10) && ![12,13,14].includes(c%100)) return 'сообщения'; return 'сообщений'; }
function clearSelection() { selectedMessages.forEach(id=>{ const el=document.getElementById(`msg-${id}`); if(el){ el.classList.remove('selected'); const cb=el.querySelector('.message-checkbox'); if(cb) cb.checked=false; } }); selectedMessages.clear(); updateSelectionBar(); }
window.deleteSelectedMessages = async function() { if(selectedMessages.size===0) return; if(confirm(`Удалить ${selectedMessages.size} ${getMessageWord(selectedMessages.size)}?`)) { for(let id of selectedMessages) await db.ref(`${getChatPath()}/${id}`).remove(); clearSelection(); } };
window.forwardSelectedMessages = function() { if(selectedMessages.size) showForwardModal(Array.from(selectedMessages)); };
window.forwardSingleMessage = function(msgId) { showForwardModal([msgId]); };

async function forwardMessages(messageIds, targetId, isGroup) {
    for(let msgId of messageIds) {
        const msg = await db.ref(getChatPath()+'/'+msgId).once('value');
        const msgData=msg.val();
        if(msgData) {
            const forwardData={...msgData, userId:currentUserId, name:currentUserName, avatar:currentUserAvatar, time:Date.now(), read:false, delivered:false, readBy:{[currentUserId]:true}, forwardedFrom:{chatId:currentChat.id, chatName:currentChat.name}};
            delete forwardData.readBy; forwardData.readBy={[currentUserId]:true};
            const targetPath=isGroup ? `group_messages/${targetId}` : `private_messages/${[currentUserId, targetId].sort().join('_')}`;
            await db.ref(targetPath).push(forwardData);
        }
    }
    if(!isMuted(currentChat.id)) playSound();
}

async function showForwardModal(messageIds) {
    const targets=contacts.filter(c=>!c.isGroup);
    document.getElementById('forwardContactsList').innerHTML=targets.map(t=>`<div class="forward-contact" onclick="forwardTo('${t.id}', false, ${JSON.stringify(messageIds)})"><img src="${getAvatarUrl(t.name,t.avatar)}"><div><div class="forward-contact-name">${escapeHtml(t.name)}</div></div></div>`).join('')||'<div>Нет контактов</div>';
    window.forwardTo = async (targetId, isGroup, mids) => { await forwardMessages(mids, targetId, isGroup); closeForwardModal(); clearSelection(); alert('Переслано'); };
    document.getElementById('forwardModal').classList.add('visible');
}
window.closeForwardModal = () => document.getElementById('forwardModal').classList.remove('visible');
window.deleteMessage = async (msgId) => { if(confirm('Удалить?')) await db.ref(`${getChatPath()}/${msgId}`).remove(); };
window.replyToMsg = (msgId, userName, text) => { replyingTo={messageId:msgId, userName, text}; document.getElementById('replyIndicator').style.display='flex'; document.getElementById('replyToName').innerText=userName; document.getElementById('replyToText').innerText=text.length>50?text.substring(0,50)+'...':text; document.getElementById('messageInput').focus(); };
window.scrollToMessage = (msgId) => { const el=document.getElementById(`msg-${msgId}`); if(el){ el.scrollIntoView({behavior:'smooth', block:'center'}); el.classList.add('highlight-message'); setTimeout(()=>el.classList.remove('highlight-message'),1000); } };
window.showReadBy = async function(msgId) {
    const msg=await db.ref(`${getChatPath()}/${msgId}`).once('value');
    const msgData=msg.val();
    if(!msgData||!msgData.readBy) return;
    const users=Object.keys(msgData.readBy).filter(id=>id!==currentUserId);
    let html='';
    for(let uid of users) {
        const u=await db.ref('users/'+uid).once('value');
        if(u.exists()) html+=`<div class="read-by-user"><img src="${getAvatarUrl(u.val().name, u.val().avatarUrl)}"><span>${escapeHtml(u.val().name)}</span></div>`;
    }
    document.getElementById('readByList').innerHTML=html||'<p>Никто не прочитал</p>';
    document.getElementById('readByModal').classList.add('visible');
};
window.closeReadByModal = () => document.getElementById('readByModal').classList.remove('visible');

// ==================== ПРОФИЛИ ====================
window.showUserProfile = async (userId, userName) => {
    const u=await db.ref('users/'+userId).once('value');
    if(!u.exists()) return;
    const user=u.val();
    document.getElementById('profileViewAvatar').src=getAvatarUrl(user.name, user.avatarUrl);
    document.getElementById('profileViewName').innerText=user.name;
    document.getElementById('profileViewBio').innerText=user.bio||'Нет описания';
    document.getElementById('profileViewStatus').innerHTML=user.online?'🟢 В сети':'⚫ Не в сети';
    document.getElementById('profileViewModal').dataset.userId=userId;
    document.getElementById('profileViewModal').dataset.userName=user.name;
    document.getElementById('profileViewModal').style.display='flex';
};
window.showMyProfile = () => showUserProfile(currentUserId, currentUserName);
window.showCurrentChatProfile = () => { if(currentChat.type==='user') showUserProfile(currentChat.id, currentChat.name); else showGroupInfo(currentChat.id); };
window.closeProfileView = () => document.getElementById('profileViewModal').style.display='none';
document.getElementById('profileViewSendMsg').onclick = () => { const uid=document.getElementById('profileViewModal').dataset.userId; const name=document.getElementById('profileViewModal').dataset.userName; closeProfileView(); switchChat(uid, name, false); };

window.showGroupInfo = async (groupId) => {
    const group=groups.find(g=>g.id===groupId);
    if(!group) return;
    document.getElementById('groupInfoTitle').innerHTML=escapeHtml(group.name)+(group.creator===currentUserId?' 👑':'');
    const membersList=Object.keys(group.members||{});
    let html='';
    for(let mid of membersList) {
        const u=await db.ref('users/'+mid).once('value');
        if(u.exists()) {
            html+=`<div class="member-item-admin"><img src="${getAvatarUrl(u.val().name, u.val().avatarUrl)}"><div class="member-info"><div class="member-name">${escapeHtml(u.val().name)}</div><div class="member-role">${mid===group.creator?'Создатель':'Участник'}</div></div>${group.creator===currentUserId && mid!==currentUserId ? `<button class="kick-btn" onclick="kickFromGroup('${groupId}','${mid}'); closeGroupInfo();">Исключить</button>` : ''}</div>`;
        }
    }
    document.getElementById('groupMembersListAdmin').innerHTML=html;
    const leaveBtn=document.getElementById('leaveGroupBtn');
    const deleteBtn=document.getElementById('deleteGroupBtn');
    leaveBtn.onclick=()=>{ leaveGroup(groupId); closeGroupInfo(); };
    if(group.creator===currentUserId) { deleteBtn.style.display='block'; deleteBtn.onclick=async()=>{ await deleteGroup(groupId); closeGroupInfo(); }; }
    else deleteBtn.style.display='none';
    const muteBtn=document.getElementById('muteGroupBtn');
    muteBtn.innerHTML=isMuted(groupId)?'🔊 Включить звук':'🔇 Выключить звук';
    muteBtn.onclick=()=>{ toggleMute(groupId); closeGroupInfo(); };
    document.getElementById('groupInfoModal').style.display='flex';
};
window.closeGroupInfo = () => document.getElementById('groupInfoModal').style.display='none';

// ==================== ПОИСК ====================
async function searchUser() {
    const query=document.getElementById('searchInput').value.trim().toLowerCase();
    if(!query) { loadContacts(); return; }
    const snap=await db.ref('users').once('value');
    const usersData=snap.val();
    const results=[]; const existing=new Set(contacts.map(c=>c.id));
    for(let id in usersData) { const u=usersData[id]; if(u.name.toLowerCase().includes(query) && u.name!==currentUserName && !u.blocked && !existing.has(id)) results.push({id,name:u.name,avatar:u.avatarUrl||''}); }
    const container=document.getElementById('contactsList');
    if(results.length===0) container.innerHTML='<div style="padding:20px;text-align:center;">👤 Пользователь не найден</div>';
    else container.innerHTML=results.map(u=>`<div class="contact-item" onclick="addContact('${u.id}','${u.name.replace(/'/g,"\\'")}')"><img class="contact-avatar" src="${getAvatarUrl(u.name,u.avatar)}"><div class="contact-info"><div class="contact-name">${escapeHtml(u.name)}</div><div class="contact-status">➕ Добавить</div></div></div>`).join('');
}
window.addContact = async (userId, userName) => {
    const exists=await db.ref('users/'+currentUserId+'/contacts/'+userId).once('value');
    if(!exists.exists()) { const u=await db.ref('users/'+userId).once('value'); await db.ref('users/'+currentUserId+'/contacts/'+userId).set({userId, name:u.val().name, avatar:u.val().avatarUrl||'', addedAt:Date.now()}); alert(`✅ ${userName} добавлен`); await loadContacts(); await loadAllUsers(); }
    else alert('Уже в контактах');
};

// ==================== АВТОРИЗАЦИЯ ====================
async function initUser() {
    await loadAllUsers(); await loadGroups();
    const savedMuted=localStorage.getItem('mutedChats'); if(savedMuted) mutedChats=new Set(JSON.parse(savedMuted));
    const danik=await db.ref('users').orderByChild('name').equalTo('DaniksGames').once('value');
    if(danik.exists()) danik.forEach(async d=>{ const exists=await db.ref('users/'+currentUserId+'/contacts/'+d.key).once('value'); if(!exists.exists()) await db.ref('users/'+currentUserId+'/contacts/'+d.key).set({userId:d.key, name:d.val().name, avatar:d.val().avatarUrl||'', addedAt:Date.now()}); });
    db.ref('private_messages').on('child_added', async snap=>{ const [id1,id2]=snap.key.split('_'); const other=id1===currentUserId?id2:id1; if(other && other!==currentUserId){ const u=await db.ref('users/'+other).once('value'); if(u.exists() && !u.val().blocked){ const exists=await db.ref('users/'+currentUserId+'/contacts/'+other).once('value'); if(!exists.exists()) await db.ref('users/'+currentUserId+'/contacts/'+other).set({userId:other, name:u.val().name, avatar:u.val().avatarUrl||'', addedAt:Date.now()}); await loadContacts(); } } });
}

function setupProfile() {
    document.getElementById('settingsBtn').onclick=()=>{ document.getElementById('avatarPreview').src=getAvatarUrl(currentUserName,currentUserAvatar); document.getElementById('modalName').value=currentUserName; document.getElementById('modalBio').value=currentUserBio||''; document.getElementById('profileModal').style.display='flex'; };
    document.getElementById('closeModalBtn').onclick=()=>document.getElementById('profileModal').style.display='none';
    document.getElementById('modalAvatar').onchange=e=>{ if(e.target.files[0]){ const r=new FileReader(); r.onload=ev=>document.getElementById('avatarPreview').src=ev.target.result; r.readAsDataURL(e.target.files[0]); } };
    document.getElementById('saveProfileBtn').onclick=async()=>{
        const newName=document.getElementById('modalName').value.trim(), newBio=document.getElementById('modalBio').value.trim(), file=document.getElementById('modalAvatar').files[0];
        const updates={};
        if(newName && newName!==currentUserName){ const check=await db.ref('users').orderByChild('name').equalTo(newName).once('value'); let taken=false; check.forEach(c=>{if(c.key!==currentUserId) taken=true;}); if(taken) throw new Error('Ник занят'); updates.name=newName; }
        if(newBio!==currentUserBio) updates.bio=newBio;
        if(file){ const reader=new FileReader(); const avatarBase64=await new Promise((resolve,reject)=>{ reader.onload=e=>resolve(e.target.result); reader.onerror=reject; reader.readAsDataURL(file); }); updates.avatarUrl=avatarBase64; currentUserAvatar=avatarBase64; }
        if(Object.keys(updates).length){ await db.ref('users/'+currentUserId).update(updates); if(updates.name){ currentUserName=updates.name; localStorage.setItem('userName',updates.name); document.getElementById('sidebarName').innerText=updates.name; isAdmin=currentUserName==='DaniksGames'; } if(updates.bio) currentUserBio=updates.bio; if(updates.avatarUrl) document.getElementById('sidebarAvatar').src=updates.avatarUrl; alert('✅ Профиль обновлён'); document.getElementById('profileModal').style.display='none'; await loadContacts(); }
    };
}

window.showCreateGroupModal = async () => {
    await loadAllUsers();
    const container=document.getElementById('memberSelectList');
    container.innerHTML='<div style="padding:8px;">Выберите участников:</div>';
    for(let u of allUsers) if(u.id!==currentUserId) container.innerHTML+=`<div class="member-item" onclick="toggleMemberSelect('${u.id}')"><input type="checkbox" id="member_${u.id}"><img src="${getAvatarUrl(u.name,u.avatar)}"><span>${escapeHtml(u.name)}</span></div>`;
    document.getElementById('createGroupModal').style.display='flex';
};
window.toggleMemberSelect=(uid)=>{ const cb=document.getElementById(`member_${uid}`); if(cb) cb.checked=!cb.checked; };
window.closeCreateGroupModal=()=>{ document.getElementById('createGroupModal').style.display='none'; document.getElementById('groupName').value=''; document.getElementById('groupDescription').value=''; };
document.getElementById('createGroupBtn').onclick=async()=>{
    const name=document.getElementById('groupName').value.trim();
    if(!name){ alert('Введите название'); return; }
    const selected=[]; document.querySelectorAll('#memberSelectList input:checked').forEach(cb=>selected.push(cb.value));
    try{ await createGroup(name, document.getElementById('groupDescription').value, selected); alert('Группа создана'); closeCreateGroupModal(); await loadContacts(); } catch(e){ alert(e.message); }
};

// ==================== МЕДИА ====================
function setupAttachments() {
    const attachBtn=document.getElementById('attachBtn'), attachMenu=document.getElementById('attachMenu');
    attachBtn.onclick=e=>{ e.stopPropagation(); attachMenu.classList.toggle('visible'); };
    document.addEventListener('click',e=>{ if(!attachMenu.contains(e.target) && e.target!==attachBtn) attachMenu.classList.remove('visible'); });
    document.getElementById('attachPhotoBtn').onclick=()=>{ attachMenu.classList.remove('visible'); document.getElementById('photoInput').click(); };
    document.getElementById('attachCameraBtn').onclick=()=>{ attachMenu.classList.remove('visible'); document.getElementById('cameraCaptureInput').click(); };
    document.getElementById('attachVideoBtn').onclick=()=>{ attachMenu.classList.remove('visible'); document.getElementById('videoFileInput').click(); };
    document.getElementById('attachFileBtn').onclick=()=>{ attachMenu.classList.remove('visible'); document.getElementById('fileInput').click(); };
    document.getElementById('attachContactBtn').onclick=async()=>{ attachMenu.classList.remove('visible'); await loadAllUsers(); const list=allUsers.filter(u=>u.id!==currentUserId); let html='<div style="padding:10px;"><h4>Выберите контакт:</h4>'; list.forEach(c=>{ html+=`<div class="forward-contact" onclick="sendContact('${c.id}','${c.name.replace(/'/g,"\\'")}','${c.avatar}'); closeForwardModal();"><img src="${getAvatarUrl(c.name,c.avatar)}"><div><div class="forward-contact-name">${escapeHtml(c.name)}</div></div></div>`; }); html+='<button class="forward-cancel" onclick="closeForwardModal()">Отмена</button></div>'; document.getElementById('forwardContactsList').innerHTML=html; document.getElementById('forwardModal').classList.add('visible'); window.sendContact=async(uid,uname,uavatar)=>{ await sendMessageWithMedia('contact','','',null,false,{userId:uid,name:uname,avatar:uavatar}); closeForwardModal(); }; };
    document.getElementById('attachCircleBtn').onclick=()=>{ attachMenu.classList.remove('visible'); startCircleRecording(); };
    document.getElementById('attachVoiceBtn').onclick=()=>{ attachMenu.classList.remove('visible'); startVoiceRecording(); };
    document.getElementById('cancelMediaPreview').onclick=()=>{ document.getElementById('mediaPreview').style.display='none'; pendingMedia=null; };
}

function setupFileInputs() {
    document.getElementById('photoInput').onchange=e=>{ if(e.target.files[0]) handleFiles([e.target.files[0]]); e.target.value=''; };
    document.getElementById('cameraCaptureInput').onchange=e=>{ if(e.target.files[0]) handleFiles([e.target.files[0]]); e.target.value=''; };
    document.getElementById('videoFileInput').onchange=e=>{ if(e.target.files[0]) handleFiles([e.target.files[0]]); e.target.value=''; };
    document.getElementById('fileInput').onchange=e=>{ if(e.target.files[0]) handleFiles([e.target.files[0]]); e.target.value=''; };
}

function handleFiles(files) { for(let file of files){ const reader=new FileReader(); reader.onload=e=>{ const isImage=file.type.startsWith('image/'), isVideo=file.type.startsWith('video/'); pendingMedia={data:e.target.result, type:isImage?'image':(isVideo?'video':'file'), file}; showMediaPreview(file, isImage?e.target.result:null); }; reader.readAsDataURL(file); } }
function showMediaPreview(file, previewUrl){ const preview=document.getElementById('mediaPreview'), previewImage=document.getElementById('previewImage'), previewName=document.getElementById('previewName'); if(file.type.startsWith('image/')){ previewImage.src=previewUrl; previewImage.style.display='block'; } else previewImage.style.display='none'; previewName.textContent=file.name; preview.style.display='flex'; }

async function startVoiceRecording(){ if(isVoiceRecording && voiceRecorder?.state==='recording'){ voiceRecorder.stop(); return; } try{ const stream=await navigator.mediaDevices.getUserMedia({audio:true}); voiceStream=stream; voiceRecorder=new MediaRecorder(stream); voiceChunks=[]; voiceRecorder.ondataavailable=e=>{ if(e.data.size>0) voiceChunks.push(e.data); }; voiceRecorder.onstop=()=>{ const blob=new Blob(voiceChunks,{type:'audio/webm'}); const r=new FileReader(); r.onload=e=>sendMessageWithMedia('audio',e.target.result,''); r.readAsDataURL(blob); if(voiceStream) voiceStream.getTracks().forEach(t=>t.stop()); isVoiceRecording=false; document.getElementById('recordingIndicator').classList.remove('visible'); stopRecordingTimer(); }; voiceRecorder.start(); isVoiceRecording=true; document.getElementById('recordingIndicator').classList.add('visible'); startRecordingTimer(); setTimeout(()=>{ if(isVoiceRecording && voiceRecorder?.state==='recording') voiceRecorder.stop(); },15000); } catch(e){ alert('Нет доступа к микрофону'); } }
async function startCircleRecording(){ if(isCircleRecording){ if(circleRecorder?.state==='recording') circleRecorder.stop(); isCircleRecording=false; if(circleStream) circleStream.getTracks().forEach(t=>t.stop()); document.getElementById('cameraPreview').classList.remove('visible'); document.getElementById('recordingIndicator').classList.remove('visible'); stopRecordingTimer(); } else { try{ const stream=await navigator.mediaDevices.getUserMedia({video:{facingMode:"user"}, audio:true}); circleStream=stream; document.getElementById('previewVideo').srcObject=stream; document.getElementById('cameraPreview').classList.add('visible'); circleRecorder=new MediaRecorder(stream,{mimeType:'video/webm'}); circleChunks=[]; circleRecorder.ondataavailable=e=>{ if(e.data.size>0) circleChunks.push(e.data); }; circleRecorder.onstop=()=>{ const blob=new Blob(circleChunks,{type:'video/webm'}); const r=new FileReader(); r.onload=e=>sendMessageWithMedia('video',e.target.result,'',null,true); r.readAsDataURL(blob); if(circleStream) circleStream.getTracks().forEach(t=>t.stop()); circleStream=null; document.getElementById('cameraPreview').classList.remove('visible'); document.getElementById('recordingIndicator').classList.remove('visible'); stopRecordingTimer(); }; circleRecorder.start(); isCircleRecording=true; document.getElementById('recordingIndicator').classList.add('visible'); document.querySelector('#recordingIndicator i').className='fas fa-video'; startRecordingTimer(); setTimeout(()=>{ if(isCircleRecording && circleRecorder?.state==='recording') circleRecorder.stop(); },30000); } catch(e){ alert('Нет доступа к камере'); } } }
function startRecordingTimer(){ recordingSeconds=0; document.getElementById('recordingTimer').textContent='00:00'; recordingTimerInterval=setInterval(()=>{ recordingSeconds++; document.getElementById('recordingTimer').textContent=Math.floor(recordingSeconds/60).toString().padStart(2,'0')+':'+(recordingSeconds%60).toString().padStart(2,'0'); },1000); }
function stopRecordingTimer(){ if(recordingTimerInterval){ clearInterval(recordingTimerInterval); recordingTimerInterval=null; } }

// ==================== АДМИН ====================
function setupAdmin() {
    document.getElementById('adminPanelBtn').onclick=()=>{ if(isAdmin){ loadAdminUsers(); document.getElementById('adminPanel').style.display='flex'; } else alert('Доступ только у DaniksGames'); };
    document.getElementById('closeAdminBtn').onclick=()=>document.getElementById('adminPanel').style.display='none';
    document.getElementById('clearGlobalChatBtn').onclick=async()=>{ if(isAdmin && confirm('Очистить общий чат?')){ await db.ref('group_messages/global').remove(); alert('Чат очищен'); } };
}
async function loadAdminUsers(){ const users=await db.ref('users').once('value'); const container=document.getElementById('usersList'); container.innerHTML='<h4 style="color:white;">Пользователи:</h4>'; for(let id in users.val()){ const u=users.val()[id]; container.innerHTML+=`<div class="user-item"><div><strong>${escapeHtml(u.name)}</strong><br><span style="font-size:0.8rem;">🔑 ${escapeHtml(u.password)}</span><br><span>${u.blocked?'🔒 Заблокирован':'✅ Активен'} | ${u.online?'🟢 В сети':'⚫ Не в сети'}</span></div><div><button onclick="adminEditUser('${id}','${escapeHtml(u.name).replace(/'/g,"\\'")}')">✏️</button><button onclick="toggleBlock('${id}',${u.blocked||false})">${u.blocked?'🔓':'🔒'}</button><button onclick="deleteAccount('${id}')">🗑️</button></div></div>`; } }
window.adminEditUser=async(id,name)=>{ const newName=prompt('Новый ник:',name); if(newName && newName!==name){ const check=await db.ref('users').orderByChild('name').equalTo(newName).once('value'); let exists=false; check.forEach(c=>{if(c.key!==id) exists=true;}); if(exists){ alert('Ник занят'); return; } await db.ref('users/'+id).update({name:newName}); alert('Ник обновлён'); } const newPass=prompt('Новый пароль:'); if(newPass && newPass.trim()) await db.ref('users/'+id).update({password:newPass.trim()}); loadAdminUsers(); };
window.toggleBlock=async(id,blocked)=>{ await db.ref('users/'+id).update({blocked:!blocked}); loadAdminUsers(); };
window.deleteAccount=async(id)=>{ if(confirm('Удалить аккаунт?')){ await db.ref('users/'+id).remove(); await db.ref('users/'+currentUserId+'/contacts/'+id).remove(); loadAdminUsers(); await loadContacts(); } };
window.sendSystemNotification=async(type)=>{ if(!isAdmin) return; let text=''; if(type==='update') text='🔄 Установка обновления...'; else if(type==='success') text='✅ Обновление успешно установлено!'; else text='❌ Ошибка установки обновления.'; await db.ref('group_messages/global').push({userId:'system', name:'Система', avatar:'', text, time:Date.now(), isSystem:true}); playSound(); };
window.sendCustomNotification=async()=>{ if(!isAdmin) return; const input=document.getElementById('customNotifyText'); const text=input.value.trim(); if(!text) return; await db.ref('group_messages/global').push({userId:'system', name:'Система', avatar:'', text:'📢 '+text, time:Date.now(), isSystem:true}); input.value=''; playSound(); };

function setupTheme(){ if(localStorage.getItem('theme')==='dark') document.body.classList.add('dark'); document.getElementById('themeToggle').onclick=()=>{ document.body.classList.toggle('dark'); localStorage.setItem('theme',document.body.classList.contains('dark')?'dark':'light'); }; }
function closeSidebar(){ document.getElementById('sidebar').classList.remove('open'); document.getElementById('sidebarOverlay').classList.remove('visible'); }
function openSidebar(){ document.getElementById('sidebar').classList.add('open'); document.getElementById('sidebarOverlay').classList.add('visible'); }

// ==================== ИНИЦИАЛИЗАЦИЯ ====================
async function initAll() {
    await loadContacts();
    setupProfile(); setupTheme(); setupAdmin(); setupAttachments(); setupFileInputs();
    document.getElementById('menuToggle').onclick=openSidebar;
    document.getElementById('sidebarOverlay').onclick=closeSidebar;
    document.getElementById('createGroupBtnSidebar').onclick=showCreateGroupModal;
    const ta=document.getElementById('messageInput');
    ta.addEventListener('input',function(){ this.style.height='auto'; this.style.height=Math.min(this.scrollHeight,150)+'px'; });
    document.getElementById('sendBtn').onclick=async()=>{ if(pendingMedia){ const cap=document.getElementById('mediaCaption').value; const fd={name:pendingMedia.file.name, size:pendingMedia.file.size, type:pendingMedia.file.type}; await sendMessageWithMedia(pendingMedia.type, pendingMedia.data, cap, fd); document.getElementById('mediaPreview').style.display='none'; pendingMedia=null; } else sendTextMessage(); };
    ta.addEventListener('keydown',e=>{ if(e.key==='Enter' && !e.shiftKey){ e.preventDefault(); document.getElementById('sendBtn').click(); } });
    document.getElementById('cancelReplyBtn').onclick=()=>{ replyingTo=null; document.getElementById('replyIndicator').style.display='none'; };
    document.getElementById('searchInput').oninput=searchUser;
    document.getElementById('logoutBtn').onclick=()=>{ if(currentUserId) db.ref('users/'+currentUserId).update({online:false,lastSeen:Date.now()}); localStorage.clear(); location.reload(); };
    document.getElementById('forwardSelectedBtn').onclick=forwardSelectedMessages;
    document.getElementById('deleteSelectedBtn').onclick=deleteSelectedMessages;
    document.getElementById('cancelSelectionBtn').onclick=clearSelection;
    if(contacts.length) switchChat(contacts[0].id, contacts[0].name, contacts[0].isGroup);
    if(Notification.permission==='default') Notification.requestPermission();
    if(currentUserId) await db.ref('users/'+currentUserId).update({online:true,lastSeen:Date.now()});
    if(onlineStatusInterval) clearInterval(onlineStatusInterval);
    onlineStatusInterval=setInterval(()=>{ if(currentUserId) db.ref('users/'+currentUserId).update({lastSeen:Date.now(),online:true}); },30000);
    setInterval(updateAllOnlineStatuses,10000);
}

async function auth() {
    const name=document.getElementById('loginName').value.trim(), pass=document.getElementById('loginPassword').value.trim();
    if(!name||!pass){ document.getElementById('authError').innerText='Заполните поля'; return; }
    const btn=document.getElementById('authBtn'); btn.disabled=true; btn.innerText='⏳...';
    try{
        const snap=await db.ref('users').orderByChild('name').equalTo(name).once('value');
        if(snap.exists()){ let found=false; snap.forEach(c=>{ if(c.val().password===pass){ currentUserId=c.key; currentUserName=c.val().name; currentUserAvatar=c.val().avatarUrl||null; currentUserBio=c.val().bio||''; isBlocked=c.val().blocked||false; found=true; } }); if(!found){ document.getElementById('authError').innerText='Неверный пароль'; btn.disabled=false; btn.innerText='Войти'; return; } }
        else{ const newUser=db.ref('users').push(); currentUserId=newUser.key; await newUser.set({name, password:pass, avatarUrl:'', bio:'', blocked:false, createdAt:Date.now(), lastSeen:Date.now(), online:true}); currentUserName=name; currentUserAvatar=null; currentUserBio=''; isBlocked=false; }
        if(isBlocked){ document.getElementById('authError').innerText='Вы заблокированы'; btn.disabled=false; btn.innerText='Войти'; return; }
        isAdmin=currentUserName==='DaniksGames';
        await db.ref('users/'+currentUserId).update({online:true,lastSeen:Date.now()});
        localStorage.setItem('userId',currentUserId);
        localStorage.setItem('userName',currentUserName);
        document.getElementById('authOverlay').style.display='none';
        document.getElementById('appContainer').style.display='flex';
        document.getElementById('sidebarName').innerText=currentUserName;
        document.getElementById('sidebarAvatar').src=getAvatarUrl(currentUserName,currentUserAvatar);
        await initUser();
        initAll();
    } catch(e){ document.getElementById('authError').innerText='Ошибка'; btn.disabled=false; btn.innerText='Войти'; }
}

document.getElementById('authBtn').onclick=auth;
(async()=>{ const uid=localStorage.getItem('userId'); if(uid){ const snap=await db.ref('users/'+uid).once('value'); if(snap.exists() && !snap.val().blocked){ currentUserId=uid; currentUserName=snap.val().name; currentUserAvatar=snap.val().avatarUrl; currentUserBio=snap.val().bio||''; isBlocked=snap.val().blocked||false; isAdmin=currentUserName==='DaniksGames'; await db.ref('users/'+uid).update({online:true,lastSeen:Date.now()}); document.getElementById('authOverlay').style.display='none'; document.getElementById('appContainer').style.display='flex'; document.getElementById('sidebarName').innerText=currentUserName; document.getElementById('sidebarAvatar').src=getAvatarUrl(currentUserName,currentUserAvatar); await initUser(); initAll(); return; } } document.getElementById('authOverlay').style.display='flex'; })();
</script>
</body>
</html>
