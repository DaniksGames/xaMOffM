<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>xaMOff Messenger</title>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-database-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, sans-serif; }
        
        :root {
            --bg-body: #f0f2f5;
            --chat-bg: #ffffff;
            --chat-bg-image: none;
            --sidebar-bg: #ffffff;
            --header-bg: #ffffff;
            --header-text: #1a1a2e;
            --message-area-bg: #f8f9fc;
            --my-bubble: #4a6cf7;
            --my-text: #ffffff;
            --other-bubble: #e9ecef;
            --other-text: #1a1a2e;
            --system-bubble: #e9ecef;
            --system-text: #666666;
            --input-bg: #ffffff;
            --input-border: #e2e8f0;
            --icon-color: #4a6cf7;
            --read-color: #10b981;
            --online-color: #10b981;
            --offline-color: #94a3b8;
            --contact-hover: #f1f5f9;
            --active-chat: #eef2ff;
            --selected-message: rgba(74, 108, 247, 0.2);
            --unread-badge: #ef4444;
            --reaction-bg: rgba(0,0,0,0.05);
            --shadow: 0 4px 20px rgba(0,0,0,0.08);
            --safe-area-bottom: env(safe-area-inset-bottom, 0px);
            --transition-smooth: cubic-bezier(0.34, 1.2, 0.64, 1);
        }
        
        body.dark {
            --bg-body: #121212;
            --chat-bg: #1e1e2e;
            --sidebar-bg: #1e1e2e;
            --header-bg: #1e1e2e;
            --header-text: #e2e8f0;
            --message-area-bg: #1a1a2e;
            --my-bubble: #4a6cf7;
            --other-bubble: #2d2d3d;
            --other-text: #e2e8f0;
            --system-bubble: #2d2d3d;
            --system-text: #a0aec0;
            --input-bg: #2d2d3d;
            --input-border: #3d3d4d;
            --icon-color: #60a5fa;
            --contact-hover: #2d2d3d;
            --active-chat: #2d2d4d;
            --selected-message: rgba(96, 165, 250, 0.25);
            --reaction-bg: rgba(255,255,255,0.1);
        }
        
        body { background: var(--bg-body); min-height: 100vh; transition: background 0.3s var(--transition-smooth); }
        body.no-animations * { animation: none !important; transition: none !important; }
        
        /* Анимации */
        @keyframes floatIn {
            0% { opacity: 0; transform: translateY(40px) scale(0.9); filter: blur(10px); }
            100% { opacity: 1; transform: translateY(0) scale(1); filter: blur(0); }
        }
        @keyframes slideIn3D {
            0% { opacity: 0; transform: translateX(100%) rotateY(-30deg); }
            100% { opacity: 1; transform: translateX(0) rotateY(0); }
        }
        @keyframes morphIn {
            0% { opacity: 0; border-radius: 100px; transform: scale(0.7); background: var(--icon-color); }
            100% { opacity: 1; border-radius: 28px; transform: scale(1); background: var(--chat-bg); }
        }
        @keyframes messageAppear {
            0% { opacity: 0; transform: translateX(-30px) scale(0.85); filter: blur(6px); }
            100% { opacity: 1; transform: translateX(0) scale(1); filter: blur(0); }
        }
        @keyframes messageAppearMy {
            0% { opacity: 0; transform: translateX(30px) scale(0.85); filter: blur(6px); }
            100% { opacity: 1; transform: translateX(0) scale(1); filter: blur(0); }
        }
        @keyframes glowPulse {
            0%, 100% { box-shadow: 0 0 0 0 rgba(74, 108, 247, 0.4); }
            50% { box-shadow: 0 0 0 12px rgba(74, 108, 247, 0); }
        }
        @keyframes bounceReaction {
            0% { transform: scale(0); opacity: 0; }
            50% { transform: scale(1.4); }
            100% { transform: scale(1); opacity: 1; }
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        @keyframes loadingLogo {
            0% { transform: scale(1) rotate(0deg); opacity: 0; }
            20% { opacity: 1; }
            80% { opacity: 1; }
            100% { transform: scale(1.2) rotate(360deg); opacity: 0; }
        }
        @keyframes loadingText {
            0%, 100% { opacity: 0.3; transform: translateY(0); }
            50% { opacity: 1; transform: translateY(-5px); }
        }
        @keyframes ripple {
            0% { transform: scale(0); opacity: 0.6; }
            100% { transform: scale(4); opacity: 0; }
        }
        
        /* Экран загрузки */
        .loading-screen {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            z-index: 10000;
            transition: opacity 0.8s cubic-bezier(0.34, 1.2, 0.64, 1), visibility 0.8s;
        }
        .loading-screen.hide {
            opacity: 0;
            visibility: hidden;
        }
        .loading-logo {
            font-size: 4rem;
            color: white;
            margin-bottom: 20px;
            animation: loadingLogo 2s ease-in-out infinite;
        }
        .loading-dots {
            display: flex;
            gap: 12px;
            margin-top: 30px;
        }
        .loading-dots span {
            width: 12px;
            height: 12px;
            background: white;
            border-radius: 50%;
            animation: loadingText 1.4s ease-in-out infinite;
        }
        .loading-dots span:nth-child(1) { animation-delay: 0s; }
        .loading-dots span:nth-child(2) { animation-delay: 0.2s; }
        .loading-dots span:nth-child(3) { animation-delay: 0.4s; }
        .loading-dots span:nth-child(4) { animation-delay: 0.6s; }
        
        .app-container { display: flex; flex-direction: column; height: 100vh; max-width: 600px; margin: 0 auto; background: var(--chat-bg); background-image: var(--chat-bg-image); background-size: cover; background-position: center; position: relative; overflow: hidden; transition: background 0.3s var(--transition-smooth); }
        
        .app-header { background: var(--header-bg); padding: 12px 16px; display: flex; align-items: center; justify-content: space-between; border-bottom: 1px solid var(--input-border); backdrop-filter: blur(10px); }
        .app-header h1 { font-size: 1.4rem; color: var(--header-text); background: linear-gradient(135deg, var(--icon-color), #a855f7); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
        .menu-btn { background: none; border: none; font-size: 1.5rem; color: var(--icon-color); cursor: pointer; padding: 8px; border-radius: 50%; width: 44px; height: 44px; transition: all 0.2s; }
        .menu-btn:active { transform: scale(0.85) rotate(90deg); background: var(--contact-hover); }
        
        .side-menu { position: fixed; top: 0; left: -280px; width: 280px; height: 100vh; background: var(--sidebar-bg); z-index: 200; transition: left 0.4s cubic-bezier(0.34, 1.2, 0.64, 1); box-shadow: 2px 0 30px rgba(0,0,0,0.2); backdrop-filter: blur(20px); }
        .side-menu.open { left: 0; }
        .side-menu-header { padding: 20px; border-bottom: 1px solid var(--input-border); display: flex; align-items: center; gap: 12px; cursor: pointer; }
        .side-menu-header:active { background: var(--contact-hover); transform: scale(0.98); }
        .side-menu-header img { width: 50px; height: 50px; border-radius: 50%; object-fit: cover; }
        .side-menu-header-info h3 { color: var(--other-text); }
        .side-menu-header-info p { color: var(--system-text); font-size: 0.8rem; }
        .side-menu-item { display: flex; align-items: center; gap: 16px; padding: 14px 20px; cursor: pointer; color: var(--other-text); border-bottom: 1px solid var(--input-border); }
        .side-menu-item:active { background: var(--contact-hover); transform: translateX(10px); }
        .side-menu-item i { width: 24px; font-size: 1.2rem; color: var(--icon-color); }
        .menu-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.5); z-index: 199; display: none; backdrop-filter: blur(8px); }
        .menu-overlay.visible { display: block; animation: fadeIn 0.3s; }
        
        .chats-list { flex: 1; overflow-y: auto; background: var(--chat-bg); background-image: var(--chat-bg-image); background-size: cover; }
        .chat-item { display: flex; align-items: center; gap: 12px; padding: 12px 16px; cursor: pointer; border-bottom: 1px solid var(--input-border); transition: all 0.2s; }
        .chat-item:active { background: var(--contact-hover); transform: scale(0.98); }
        .chat-item.selected { background: var(--selected-message); outline: 2px solid var(--icon-color); outline-offset: -1px; }
        .chat-avatar { width: 52px; height: 52px; border-radius: 50%; object-fit: cover; position: relative; }
        .online-dot { position: absolute; bottom: 2px; right: 2px; width: 12px; height: 12px; border-radius: 50%; border: 2px solid var(--chat-bg); }
        .online-dot.online { background: var(--online-color); animation: glowPulse 2s infinite; }
        .chat-info { flex: 1; min-width: 0; }
        .chat-name { font-weight: 600; color: var(--other-text); display: flex; align-items: center; gap: 6px; flex-wrap: wrap; }
        .chat-preview { font-size: 0.8rem; color: var(--system-text); white-space: nowrap; overflow: hidden; text-overflow: ellipsis; margin-top: 4px; }
        .chat-meta { text-align: right; }
        .chat-time { font-size: 0.7rem; color: var(--system-text); }
        .unread-count { background: var(--unread-badge); color: white; border-radius: 50%; min-width: 20px; height: 20px; padding: 0 6px; font-size: 0.7rem; font-weight: bold; display: inline-flex; align-items: center; justify-content: center; margin-top: 4px; animation: bounceReaction 0.3s; }
        
        .chat-screen { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: var(--message-area-bg); background-image: var(--chat-bg-image); background-size: cover; z-index: 100; display: flex; flex-direction: column; transform: translateX(100%); transition: transform 0.5s cubic-bezier(0.34, 1.2, 0.64, 1); }
        .chat-screen.open { transform: translateX(0); }
        .chat-header { background: var(--header-bg); padding: 12px 16px; display: flex; align-items: center; gap: 12px; border-bottom: 1px solid var(--input-border); backdrop-filter: blur(10px); }
        .back-btn { background: none; border: none; font-size: 1.3rem; color: var(--icon-color); cursor: pointer; padding: 8px; border-radius: 50%; width: 40px; height: 40px; }
        .back-btn:active { transform: translateX(-5px) scale(0.9); }
        .chat-header-avatar { width: 44px; height: 44px; border-radius: 50%; object-fit: cover; cursor: pointer; }
        .chat-header-info { flex: 1; cursor: pointer; }
        .chat-header-name { font-weight: 600; color: var(--other-text); }
        .chat-header-status { font-size: 0.7rem; color: var(--system-text); }
        .chat-action-btn { background: none; border: none; font-size: 1.2rem; color: var(--icon-color); cursor: pointer; padding: 8px; border-radius: 50%; width: 40px; height: 40px; }
        .chat-action-btn:active { transform: rotate(90deg) scale(0.9); }
        
        .messages-area { flex: 1; overflow-y: auto; padding: 16px; display: flex; flex-direction: column; gap: 8px; }
        .message { display: flex; max-width: 85%; padding: 4px; border-radius: 12px; position: relative; transition: all 0.2s; }
        .message.selected { background: var(--selected-message); outline: 2px solid var(--icon-color); outline-offset: 2px; }
        .my-message { align-self: flex-end; justify-content: flex-end; animation: messageAppearMy 0.3s ease-out; }
        .other-message { align-self: flex-start; animation: messageAppear 0.3s ease-out; }
        .system-message { align-self: center; max-width: 90%; }
        .bubble { padding: 10px 14px; border-radius: 20px; background: var(--other-bubble); color: var(--other-text); word-break: break-word; }
        .my-message .bubble { background: var(--my-bubble); color: var(--my-text); border-bottom-right-radius: 4px; }
        .system-message .bubble { background: var(--system-bubble); color: var(--system-text); text-align: center; font-size: 0.8rem; }
        .message-header { display: flex; align-items: center; gap: 8px; margin-bottom: 5px; font-size: 0.7rem; }
        .msg-avatar { width: 20px; height: 20px; border-radius: 50%; object-fit: cover; cursor: pointer; }
        .message-name { font-weight: 600; cursor: pointer; color: inherit; }
        .reply-preview { font-size: 0.7rem; background: rgba(0,0,0,0.08); padding: 6px 10px; border-radius: 12px; margin-bottom: 6px; border-left: 3px solid var(--icon-color); cursor: pointer; }
        .media-content { max-width: 200px; max-height: 200px; border-radius: 12px; margin-top: 6px; cursor: pointer; }
        .circle-video { width: 150px; height: 150px; border-radius: 50%; overflow: hidden; margin-top: 6px; background: #000; }
        .circle-video video { width: 100%; height: 100%; object-fit: cover; }
        audio { max-width: 180px; height: 36px; margin-top: 6px; border-radius: 20px; }
        .file-attachment { display: flex; align-items: center; gap: 10px; padding: 8px 12px; background: rgba(0,0,0,0.05); border-radius: 12px; margin-top: 6px; cursor: pointer; }
        .contact-card { display: flex; align-items: center; gap: 12px; padding: 8px 12px; background: var(--input-bg); border-radius: 16px; margin-top: 8px; cursor: pointer; border: 1px solid var(--input-border); }
        .message-time { font-size: 0.55rem; opacity: 0.7; margin-top: 4px; display: flex; align-items: center; gap: 4px; flex-wrap: wrap; }
        .read-status { font-size: 0.55rem; }
        .read-status.read { color: var(--read-color); }
        .read-stats { font-size: 0.6rem; cursor: pointer; margin-left: 8px; color: var(--read-color); }
        
        .reactions { display: flex; gap: 6px; margin-top: 8px; flex-wrap: wrap; }
        .reaction { background: var(--reaction-bg); border-radius: 20px; padding: 3px 10px; font-size: 0.75rem; cursor: pointer; display: inline-flex; align-items: center; gap: 5px; transition: all 0.2s; animation: bounceReaction 0.2s; }
        .reaction:hover { transform: scale(1.08); background: var(--contact-hover); }
        .reaction-count { font-size: 0.65rem; opacity: 0.8; }
        .add-reaction { background: var(--reaction-bg); border-radius: 20px; padding: 3px 10px; font-size: 0.75rem; cursor: pointer; opacity: 0.5; transition: all 0.2s; }
        .message:hover .add-reaction { opacity: 1; }
        .add-reaction:hover { opacity: 1 !important; transform: scale(1.05); background: var(--contact-hover); }
        
        .reaction-picker { position: fixed; background: var(--chat-bg); border-radius: 50px; padding: 10px 20px; display: flex; gap: 15px; box-shadow: var(--shadow); z-index: 500; animation: floatIn 0.2s; backdrop-filter: blur(10px); }
        .reaction-emoji { font-size: 1.6rem; cursor: pointer; padding: 5px; border-radius: 50%; transition: all 0.2s; }
        .reaction-emoji:hover { transform: scale(1.3) rotate(10deg); background: var(--contact-hover); }
        
        .selection-bar { position: fixed; bottom: 80px; left: 16px; right: 16px; background: var(--icon-color); color: white; padding: 12px 20px; border-radius: 40px; display: none; align-items: center; justify-content: space-between; z-index: 150; box-shadow: var(--shadow); }
        .selection-bar.visible { display: flex; animation: morphIn 0.3s forwards; }
        .selection-actions { display: flex; gap: 16px; }
        .selection-actions button { background: none; border: none; color: white; font-size: 1.2rem; cursor: pointer; padding: 8px; border-radius: 50%; transition: all 0.2s; }
        .selection-actions button:active { transform: scale(0.85) rotate(360deg); background: rgba(255,255,255,0.2); }
        
        .input-area { padding: 12px 16px; background: var(--input-bg); border-top: 1px solid var(--input-border); padding-bottom: calc(12px + var(--safe-area-bottom)); position: relative; }
        .reply-indicator { background: var(--input-bg); padding: 8px 12px; margin-bottom: 8px; border-radius: 12px; display: flex; justify-content: space-between; align-items: center; font-size: 0.8rem; border-left: 3px solid var(--icon-color); }
        .input-row { display: flex; gap: 8px; align-items: flex-end; }
        .message-input { flex: 1; padding: 12px 16px; border-radius: 25px; border: 1px solid var(--input-border); background: var(--input-bg); color: var(--other-text); outline: none; resize: none; font-size: 16px; max-height: 120px; min-height: 44px; }
        .message-input:focus { border-color: var(--icon-color); box-shadow: 0 0 0 3px rgba(74,108,247,0.2); }
        .attach-btn, .voice-btn, .circle-btn { background: none; border: none; font-size: 1.3rem; color: var(--icon-color); cursor: pointer; width: 44px; height: 44px; border-radius: 50%; transition: all 0.2s; }
        .attach-btn:active, .voice-btn:active, .circle-btn:active { transform: scale(0.85) rotate(15deg); background: var(--contact-hover); }
        .send-btn { background: var(--icon-color); color: white; border: none; width: 44px; height: 44px; border-radius: 50%; cursor: pointer; font-size: 1.2rem; transition: all 0.2s; }
        .send-btn:active { transform: scale(0.85); animation: glowPulse 0.5s; }
        .send-btn.disabled { opacity: 0.5; pointer-events: none; }
        
        .attach-menu { position: absolute; bottom: 70px; left: 16px; background: var(--input-bg); border-radius: 24px; padding: 12px; display: none; grid-template-columns: repeat(3, 1fr); gap: 12px; box-shadow: var(--shadow); z-index: 160; animation: morphIn 0.2s forwards; transform-origin: bottom left; }
        .attach-menu.visible { display: grid; }
        .attach-menu-btn { background: none; border: none; padding: 12px; border-radius: 16px; display: flex; flex-direction: column; align-items: center; gap: 6px; color: var(--other-text); font-size: 0.7rem; cursor: pointer; transition: all 0.2s; }
        .attach-menu-btn i { font-size: 1.4rem; color: var(--icon-color); transition: all 0.2s; }
        .attach-menu-btn:active { transform: scale(0.92); background: var(--contact-hover); }
        .attach-menu-btn:active i { transform: rotate(360deg); }
        
        .fab { position: fixed; bottom: 80px; right: 16px; width: 56px; height: 56px; background: var(--icon-color); border-radius: 50%; display: flex; align-items: center; justify-content: center; color: white; font-size: 1.5rem; cursor: pointer; box-shadow: var(--shadow); z-index: 90; border: none; transition: all 0.3s cubic-bezier(0.34, 1.2, 0.64, 1); animation: glowPulse 2s infinite; }
        .fab:active { transform: scale(0.85) rotate(90deg); }
        
        .modal-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.8); backdrop-filter: blur(12px); z-index: 300; display: none; align-items: center; justify-content: center; }
        .modal-overlay.visible { display: flex; animation: fadeIn 0.3s; }
        .modal-content { background: var(--chat-bg); border-radius: 32px; padding: 24px; width: 90%; max-width: 350px; max-height: 80vh; overflow-y: auto; animation: morphIn 0.3s; box-shadow: var(--shadow); }
        .modal-content h3 { color: var(--other-text); margin-bottom: 16px; text-align: center; }
        .modal-content input, .modal-content textarea, .modal-content select { width: 100%; padding: 12px; margin: 8px 0; border-radius: 20px; border: 1px solid var(--input-border); background: var(--input-bg); color: var(--other-text); font-size: 1rem; }
        .modal-content input:focus, .modal-content textarea:focus, .modal-content select:focus { border-color: var(--icon-color); outline: none; box-shadow: 0 0 0 3px rgba(74,108,247,0.2); }
        .modal-content button { background: var(--icon-color); color: white; border: none; padding: 12px; border-radius: 30px; width: 100%; margin-top: 12px; cursor: pointer; font-size: 1rem; transition: all 0.2s; }
        .modal-content button:active { transform: scale(0.98); }
        .close-modal { background: #94a3b8 !important; margin-top: 8px !important; }
        
        .setting-row { display: flex; justify-content: space-between; align-items: center; padding: 14px 0; border-bottom: 1px solid var(--input-border); }
        .setting-label { color: var(--other-text); font-size: 1rem; }
        .toggle-switch { width: 50px; height: 26px; background: var(--input-border); border-radius: 13px; cursor: pointer; position: relative; transition: all 0.2s; }
        .toggle-switch.active { background: var(--icon-color); }
        .toggle-switch::after { content: ''; position: absolute; width: 22px; height: 22px; background: white; border-radius: 50%; top: 2px; left: 2px; transition: all 0.2s; }
        .toggle-switch.active::after { left: 26px; }
        
        .color-picker { display: flex; gap: 12px; flex-wrap: wrap; margin: 12px 0; }
        .color-option { width: 44px; height: 44px; border-radius: 50%; cursor: pointer; transition: all 0.2s; border: 2px solid transparent; }
        .color-option:hover { transform: scale(1.1); }
        .color-option.selected { border-color: var(--other-text); transform: scale(1.1); }
        
        .avatar-preview { width: 100px; height: 100px; border-radius: 50%; object-fit: cover; margin: 0 auto 16px; display: block; }
        .contact-list-item { display: flex; align-items: center; gap: 12px; padding: 12px; cursor: pointer; border-radius: 16px; transition: all 0.2s; }
        .contact-list-item:active { background: var(--contact-hover); transform: translateX(5px); }
        .contact-list-item img { width: 44px; height: 44px; border-radius: 50%; }
        .contact-list-item-name { font-weight: 500; color: var(--other-text); }
        
        .member-item { display: flex; align-items: center; gap: 12px; padding: 10px; cursor: pointer; border-radius: 12px; }
        .member-item:active { background: var(--contact-hover); transform: scale(0.98); }
        .member-item img { width: 40px; height: 40px; border-radius: 50%; }
        .member-item span { color: var(--other-text); }
        .group-member-item { display: flex; align-items: center; gap: 12px; padding: 10px; border-bottom: 1px solid var(--input-border); }
        .group-member-item img { width: 40px; height: 40px; border-radius: 50%; object-fit: cover; }
        .group-member-item .member-name { flex: 1; color: var(--other-text); }
        .kick-btn { background: #ef4444; border: none; color: white; padding: 6px 12px; border-radius: 20px; cursor: pointer; }
        .read-by-user { display: flex; align-items: center; gap: 12px; padding: 10px; border-bottom: 1px solid var(--input-border); }
        .read-by-user img { width: 36px; height: 36px; border-radius: 50%; }
        .read-by-user span { color: var(--other-text); }
        .admin-user-card { background: var(--input-bg); border-radius: 20px; padding: 12px; margin-bottom: 12px; }
        .admin-user-name { font-weight: bold; margin-bottom: 8px; color: var(--other-text); }
        .admin-user-detail { font-size: 0.8rem; opacity: 0.8; margin-bottom: 4px; color: var(--system-text); }
        .admin-user-actions { display: flex; flex-wrap: wrap; gap: 8px; margin-top: 8px; }
        .admin-user-actions button { padding: 6px 12px; border-radius: 20px; border: none; cursor: pointer; font-size: 0.7rem; color: white; transition: all 0.2s; }
        .admin-user-actions button:active { transform: scale(0.95); }
        
        .recording-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.95); backdrop-filter: blur(20px); z-index: 400; display: none; flex-direction: column; align-items: center; justify-content: center; animation: fadeIn 0.3s; }
        .recording-overlay.visible { display: flex; }
        .recording-preview { width: 250px; height: 250px; border-radius: 50%; overflow: hidden; background: #000; margin-bottom: 30px; box-shadow: 0 0 30px var(--icon-color); animation: glowPulse 2s infinite; }
        .recording-preview video { width: 100%; height: 100%; object-fit: cover; }
        .recording-wave { width: 80%; height: 100px; display: flex; align-items: center; justify-content: center; gap: 4px; margin-bottom: 30px; }
        .wave-bar { width: 4px; background: var(--icon-color); border-radius: 2px; transition: height 0.05s; animation: wave 0.5s ease-in-out infinite; }
        .recording-progress { width: 80%; height: 4px; background: rgba(255,255,255,0.3); border-radius: 2px; margin-bottom: 30px; overflow: hidden; }
        .recording-progress-fill { width: 0%; height: 100%; background: var(--icon-color); transition: width 0.1s linear; }
        .recording-controls { display: flex; gap: 30px; }
        .recording-controls button { background: none; border: none; font-size: 2rem; color: white; cursor: pointer; width: 70px; height: 70px; border-radius: 50%; display: flex; align-items: center; justify-content: center; transition: all 0.2s; }
        .recording-controls button:active { transform: scale(0.85); }
        .recording-controls .stop-rec { background: #ef4444; }
        .recording-controls .send-rec { background: var(--icon-color); }
        .recording-controls .delete-rec { background: #64748b; }
        .recording-controls .pause-rec { background: #f59e0b; }
        .flip-camera { position: absolute; top: 20px; right: 20px; background: rgba(0,0,0,0.5); border: none; color: white; font-size: 1.5rem; width: 50px; height: 50px; border-radius: 50%; cursor: pointer; transition: all 0.2s; }
        .flip-camera:active { transform: rotate(180deg) scale(0.9); }
        .recording-timer { position: absolute; top: 20px; left: 20px; color: white; font-size: 1.2rem; background: rgba(0,0,0,0.5); padding: 8px 16px; border-radius: 30px; font-family: monospace; }
        
        .toast { position: fixed; bottom: 100px; left: 50%; transform: translateX(-50%); background: rgba(0,0,0,0.9); color: white; padding: 12px 24px; border-radius: 40px; font-size: 0.9rem; z-index: 500; display: none; backdrop-filter: blur(10px); animation: floatIn 0.2s; }
        
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        @keyframes wave { 0%, 100% { transform: scaleY(1); } 50% { transform: scaleY(1.8); } }
        
        .highlight-message { animation: fadeIn 0.3s; background: var(--selected-message); }
        
        .group-avatar-large { width: 80px; height: 80px; border-radius: 50%; object-fit: cover; margin: 0 auto 16px; display: block; }
    </style>
</head>
<body>

<div class="loading-screen" id="loadingScreen">
    <div class="loading-logo"><i class="fas fa-comments"></i></div>
    <div class="loading-dots">
        <span></span><span></span><span></span><span></span>
    </div>
</div>

<div class="app-container" id="appContainer" style="display: none;">
    <div class="app-header">
        <button class="menu-btn" id="menuBtn"><i class="fas fa-bars"></i></button>
        <h1>xaMOff</h1>
        <div style="width: 44px;"></div>
    </div>
    
    <div class="chats-list" id="chatsList"></div>
    
    <button class="fab" id="fabBtn"><i class="fas fa-plus"></i></button>
    
    <div class="side-menu" id="sideMenu">
        <div class="side-menu-header" id="profileMenuBtn">
            <img id="menuAvatar" src="">
            <div class="side-menu-header-info">
                <h3 id="menuName"></h3>
                <p>Нажмите для редактирования</p>
            </div>
        </div>
        <div class="side-menu-item" id="favoritesMenuItem"><i class="fas fa-star"></i><span>Избранное</span></div>
        <div class="side-menu-item" id="searchChannelsMenuItem"><i class="fas fa-search"></i><span>Поиск каналов</span></div>
        <div class="side-menu-item" id="settingsMenuItem"><i class="fas fa-cog"></i><span>Настройки</span></div>
        <div class="side-menu-item" id="themeMenuItem"><i class="fas fa-palette"></i><span>Тема</span></div>
        <div class="side-menu-item" id="adminMenuItem" style="display:none;"><i class="fas fa-shield-alt"></i><span>Админ панель</span></div>
        <div class="side-menu-item" id="logoutMenuItem"><i class="fas fa-sign-out-alt"></i><span>Выйти</span></div>
    </div>
    <div class="menu-overlay" id="menuOverlay"></div>
    
    <div class="chat-screen" id="chatScreen">
        <div class="chat-header">
            <button class="back-btn" id="backBtn"><i class="fas fa-arrow-left"></i></button>
            <img class="chat-header-avatar" id="chatAvatar" onclick="showChatProfile()">
            <div class="chat-header-info" onclick="showChatProfile()">
                <div class="chat-header-name" id="chatName"></div>
                <div class="chat-header-status" id="chatStatus"></div>
            </div>
            <div class="chat-actions">
                <button class="chat-action-btn" id="chatInfoBtn"><i class="fas fa-info-circle"></i></button>
            </div>
        </div>
        <div class="messages-area" id="messagesArea"></div>
        <div id="replyIndicator" class="reply-indicator" style="display:none;">
            <span><i class="fas fa-reply"></i> Ответ <span id="replyToName"></span>: "<span id="replyToText"></span>"</span>
            <button id="cancelReplyBtn">✕</button>
        </div>
        <div class="input-area">
            <div class="attach-menu" id="attachMenu">
                <button class="attach-menu-btn" id="attachPhotoBtn"><i class="fas fa-image"></i><span>Фото</span></button>
                <button class="attach-menu-btn" id="attachCameraBtn"><i class="fas fa-camera"></i><span>Камера</span></button>
                <button class="attach-menu-btn" id="attachVideoBtn"><i class="fas fa-video"></i><span>Видео</span></button>
                <button class="attach-menu-btn" id="attachFileBtn"><i class="fas fa-file"></i><span>Файл</span></button>
                <button class="attach-menu-btn" id="attachContactBtn"><i class="fas fa-address-card"></i><span>Контакт</span></button>
            </div>
            <div class="input-row">
                <button class="attach-btn" id="attachBtn"><i class="fas fa-paperclip"></i></button>
                <textarea id="messageInput" class="message-input" placeholder="Сообщение..." rows="1"></textarea>
                <button class="voice-btn" id="voiceBtn"><i class="fas fa-microphone"></i></button>
                <button class="circle-btn" id="circleBtn"><i class="fas fa-circle"></i></button>
                <button class="send-btn" id="sendBtn"><i class="fas fa-paper-plane"></i></button>
            </div>
        </div>
    </div>
    
    <div class="selection-bar" id="selectionBar">
        <span id="selectedCount">0</span>
        <div class="selection-actions">
            <button id="replySelectedBtn"><i class="fas fa-reply"></i></button>
            <button id="forwardSelectedBtn"><i class="fas fa-share"></i></button>
            <button id="deleteSelectedBtn"><i class="fas fa-trash"></i></button>
            <button id="cancelSelectionBtn"><i class="fas fa-times"></i></button>
        </div>
    </div>
    
    <!-- Модальные окна -->
    <div id="profileModal" class="modal-overlay">
        <div class="modal-content">
            <h3>Профиль</h3>
            <img id="profileAvatar" class="avatar-preview">
            <input type="file" id="avatarInput" accept="image/*">
            <input type="text" id="profileName" placeholder="Никнейм">
            <input type="text" id="profilePhone" placeholder="Телефон">
            <input type="date" id="profileBirthday">
            <textarea id="profileBio" placeholder="О себе" rows="3"></textarea>
            <button id="saveProfileBtn">Сохранить</button>
            <button class="close-modal" onclick="closeModal('profileModal')">Отмена</button>
        </div>
    </div>
    
    <div id="viewProfileModal" class="modal-overlay">
        <div class="modal-content">
            <img id="viewProfileAvatar" class="avatar-preview">
            <h3 id="viewProfileName"></h3>
            <p id="viewProfileBio" style="color: var(--system-text); margin: 8px 0;"></p>
            <p id="viewProfilePhone" style="color: var(--system-text); font-size: 0.9rem;"></p>
            <p id="viewProfileBirthday" style="color: var(--system-text); font-size: 0.9rem;"></p>
            <button id="viewProfileSendMsg" class="close-modal" style="background: var(--icon-color);">💬 Написать</button>
            <button class="close-modal" onclick="closeModal('viewProfileModal')">Закрыть</button>
        </div>
    </div>
    
    <div id="settingsModal" class="modal-overlay">
        <div class="modal-content">
            <h3>Настройки</h3>
            <div class="setting-row">
                <span class="setting-label">Показывать секунды</span>
                <div class="toggle-switch" id="showSecondsToggle"></div>
            </div>
            <div class="setting-row">
                <span class="setting-label">Отключить анимации</span>
                <div class="toggle-switch" id="disableAnimationsToggle"></div>
            </div>
            <div class="setting-row">
                <span class="setting-label">Уведомления</span>
                <div class="toggle-switch" id="notificationsToggle"></div>
            </div>
            <div class="setting-row">
                <span class="setting-label">Звук уведомлений</span>
                <div class="toggle-switch" id="soundToggle"></div>
            </div>
            <button class="close-modal" onclick="closeModal('settingsModal')">Закрыть</button>
        </div>
    </div>
    
    <div id="themeModal" class="modal-overlay">
        <div class="modal-content">
            <h3>Тема</h3>
            <div class="setting-row">
                <span class="setting-label">Тёмная тема</span>
                <div class="toggle-switch" id="darkThemeToggle"></div>
            </div>
            <div class="setting-row">
                <span class="setting-label">Цвет акцента</span>
                <div class="color-picker" id="accentColorPicker">
                    <div class="color-option" style="background: #4a6cf7;" data-color="#4a6cf7"></div>
                    <div class="color-option" style="background: #10b981;" data-color="#10b981"></div>
                    <div class="color-option" style="background: #ef4444;" data-color="#ef4444"></div>
                    <div class="color-option" style="background: #f59e0b;" data-color="#f59e0b"></div>
                    <div class="color-option" style="background: #8b5cf6;" data-color="#8b5cf6"></div>
                    <div class="color-option" style="background: #ec4899;" data-color="#ec4899"></div>
                </div>
            </div>
            <div class="setting-row">
                <span class="setting-label">Фон чата</span>
                <button id="uploadBgBtn">Загрузить изображение</button>
                <button id="resetBgBtn">Сбросить фон</button>
            </div>
            <input type="file" id="bgImageInput" accept="image/*" style="display:none">
            <button class="close-modal" onclick="closeModal('themeModal')">Закрыть</button>
        </div>
    </div>
    
    <div id="createGroupModal" class="modal-overlay">
        <div class="modal-content">
            <h3>Создать группу</h3>
            <input type="text" id="groupName" placeholder="Название группы">
            <textarea id="groupDesc" placeholder="Описание"></textarea>
            <input type="file" id="groupAvatarInput" accept="image/*">
            <div id="membersList"></div>
            <button id="createGroupBtn">Создать</button>
            <button class="close-modal" onclick="closeModal('createGroupModal')">Отмена</button>
        </div>
    </div>
    
    <div id="createChannelModal" class="modal-overlay">
        <div class="modal-content">
            <h3>Создать канал</h3>
            <input type="text" id="channelName" placeholder="Название канала">
            <textarea id="channelDesc" placeholder="Описание"></textarea>
            <input type="file" id="channelAvatarInput" accept="image/*">
            <button id="createChannelBtn">Создать</button>
            <button class="close-modal" onclick="closeModal('createChannelModal')">Отмена</button>
        </div>
    </div>
    
    <div id="searchChannelsModal" class="modal-overlay">
        <div class="modal-content">
            <h3>Поиск каналов</h3>
            <input type="text" id="channelSearchInput" class="search-input" placeholder="Название канала...">
            <div id="channelsSearchList"></div>
            <button class="close-modal" onclick="closeModal('searchChannelsModal')">Закрыть</button>
        </div>
    </div>
    
    <div id="addContactModal" class="modal-overlay">
        <div class="modal-content">
            <h3>Добавить контакт</h3>
            <div id="allUsersList"></div>
            <button class="close-modal" onclick="closeModal('addContactModal')">Закрыть</button>
        </div>
    </div>
    
    <div id="forwardModal" class="modal-overlay">
        <div class="modal-content">
            <h3>Переслать</h3>
            <div id="forwardList"></div>
            <button class="close-modal" onclick="closeModal('forwardModal')">Отмена</button>
        </div>
    </div>
    
    <div id="readByModal" class="modal-overlay">
        <div class="modal-content">
            <h3>Прочитали</h3>
            <div id="readByList"></div>
            <button class="close-modal" onclick="closeModal('readByModal')">Закрыть</button>
        </div>
    </div>
    
    <div id="groupInfoModal" class="modal-overlay">
        <div class="modal-content">
            <img id="groupInfoAvatar" class="group-avatar-large">
            <h3 id="groupInfoTitle"></h3>
            <p id="groupInfoDesc" style="color: var(--system-text); margin: 8px 0;"></p>
            <div id="groupMembersList"></div>
            <div id="addMemberToGroup" style="margin-top: 16px; display: none;">
                <select id="addMemberSelect" style="width:100%; padding:10px; border-radius:20px; margin-bottom:8px;"></select>
                <button id="addMemberBtn" style="background: var(--icon-color);">➕ Добавить участника</button>
            </div>
            <button id="leaveGroupBtn" style="background:#ef4444; margin-top:12px;">Покинуть группу</button>
            <button id="deleteGroupBtn" style="background:#dc2626; display:none; margin-top:12px;">Удалить группу</button>
            <button class="close-modal" onclick="closeModal('groupInfoModal')">Закрыть</button>
        </div>
    </div>
    
    <div id="adminModal" class="modal-overlay">
        <div class="modal-content">
            <h3>Админ панель</h3>
            <div id="usersAdminList"></div>
            <button class="close-modal" onclick="closeModal('adminModal')">Закрыть</button>
        </div>
    </div>
    
    <div id="recordingOverlay" class="recording-overlay">
        <div class="recording-timer" id="recTimer">00:00</div>
        <div class="recording-preview" id="recPreview"><video id="recVideo" autoplay muted playsinline></video></div>
        <div class="recording-wave" id="recWave" style="display:none;"></div>
        <div class="recording-progress"><div class="recording-progress-fill" id="recProgress"></div></div>
        <div class="recording-controls">
            <button id="pauseRecBtn" class="pause-rec"><i class="fas fa-pause"></i></button>
            <button id="stopRecBtn" class="stop-rec"><i class="fas fa-stop"></i></button>
            <button id="deleteRecBtn" class="delete-rec"><i class="fas fa-trash"></i></button>
            <button id="sendRecBtn" class="send-rec"><i class="fas fa-paper-plane"></i></button>
        </div>
        <button id="flipCameraBtn" class="flip-camera" style="display:none;"><i class="fas fa-sync-alt"></i></button>
    </div>
    
    <div id="toast" class="toast"></div>
</div>

<div id="authOverlay" class="modal-overlay" style="display: flex; background: var(--bg-body);">
    <div class="modal-content">
        <h2>📱 xaMOff</h2>
        <input type="text" id="loginName" placeholder="Никнейм">
        <input type="password" id="loginPassword" placeholder="Пароль">
        <div id="authError" style="color:red; font-size:0.8rem;"></div>
        <button id="authBtn">Войти / Регистрация</button>
        <button id="registerBtn" style="background: #10b981; margin-top: 8px;">Создать аккаунт</button>
    </div>
</div>

<div id="registerModal" class="modal-overlay">
    <div class="modal-content">
        <h3>Создание аккаунта</h3>
        <input type="text" id="regName" placeholder="Никнейм">
        <input type="password" id="regPassword" placeholder="Пароль">
        <input type="text" id="regPhone" placeholder="Телефон">
        <input type="date" id="regBirthday">
        <textarea id="regBio" placeholder="О себе" rows="3"></textarea>
        <input type="file" id="regAvatar" accept="image/*">
        <button id="registerSubmitBtn">Зарегистрироваться</button>
        <button class="close-modal" onclick="closeModal('registerModal')">Отмена</button>
    </div>
</div>

<input type="file" id="photoInput" accept="image/*" style="display:none">
<input type="file" id="videoInput" accept="video/*" style="display:none">
<input type="file" id="fileInput" accept="*/*" style="display:none">
<input type="file" id="cameraInput" accept="image/*" capture="environment" style="display:none">

<script>
// Firebase
const firebaseConfig = {
    apiKey: "AIzaSyD4a16-r1nwCVDAu5_DBnirq0e8Lu9pBZw",
    authDomain: "daniksgames-d46b2.firebaseapp.com",
    databaseURL: "https://daniksgames-d46b2-default-rtdb.firebaseio.com",
    projectId: "daniksgames-d46b2",
    storageBucket: "daniksgames-d46b2.firebasestorage.app"
};
firebase.initializeApp(firebaseConfig);
const db = firebase.database();

// Глобальные переменные
let currentUserId = null, currentUserName = null, currentUserAvatar = null, currentUserBio = null, currentUserPhone = null, currentUserBirthday = null;
let currentChat = { id: null, name: null, isGroup: false, isChannel: false };
let chats = [];
let groups = [];
let channels = [];
let unreadCounts = new Map();
let mutedChats = new Set();
let replyingTo = null;
let selectedMessages = new Set();
let selectedChats = new Set();
let allUsers = [];
let contactsList = [];
let isAdmin = false;
let isSuperAdmin = false;
let processedMsgIds = new Map();
let messageListeners = new Map();
let showSeconds = false;
let animationsEnabled = true;
let notificationsEnabled = true;
let soundEnabled = true;
let currentAccentColor = "#4a6cf7";
let isSending = false;

// Переменные для записи
let mediaRecorder = null;
let audioChunks = [];
let videoChunks = [];
let isRecording = false;
let recordType = null;
let recordStream = null;
let recordTimer = null;
let recordSeconds = 0;
let audioContext = null;
let analyser = null;
let animationId = null;
let currentFacing = "user";
let isPaused = false;

// Загрузка настроек
function loadSavedSettings() {
    const saved = localStorage.getItem('messenger_settings');
    if(saved) {
        try {
            const settings = JSON.parse(saved);
            showSeconds = settings.showSeconds || false;
            animationsEnabled = settings.animationsEnabled !== false;
            notificationsEnabled = settings.notificationsEnabled !== false;
            soundEnabled = settings.soundEnabled !== false;
            currentAccentColor = settings.accentColor || "#4a6cf7";
            if(settings.darkTheme) document.body.classList.add('dark');
            if(settings.chatBgImage) {
                document.documentElement.style.setProperty('--chat-bg-image', `url(${settings.chatBgImage})`);
            }
            if(!animationsEnabled) document.body.classList.add('no-animations');
            document.documentElement.style.setProperty('--icon-color', currentAccentColor);
            document.documentElement.style.setProperty('--my-bubble', currentAccentColor);
        } catch(e) {}
    }
}

function saveSettings() {
    const settings = {
        showSeconds,
        animationsEnabled,
        notificationsEnabled,
        soundEnabled,
        accentColor: currentAccentColor,
        darkTheme: document.body.classList.contains('dark'),
        chatBgImage: getComputedStyle(document.documentElement).getPropertyValue('--chat-bg-image').replace(/url\(["']?|["']?\)/g, '')
    };
    localStorage.setItem('messenger_settings', JSON.stringify(settings));
}

// Вспомогательные функции
function showToast(text) { 
    const t = document.getElementById('toast'); 
    t.innerText = text; 
    t.style.display = 'block'; 
    setTimeout(() => {
        t.style.animation = 'fadeOut 0.2s';
        setTimeout(() => { t.style.display = 'none'; t.style.animation = ''; }, 200);
    }, 2000); 
}

function escapeHtml(s) { 
    if(!s) return ''; 
    return String(s).replace(/[&<>]/g, m => ({'&':'&amp;','<':'&lt;','>':'&gt;'}[m])); 
}

function getAvatarUrl(name, avatar) { 
    if(avatar && avatar.startsWith('data:image')) return avatar;
    return avatar || `https://ui-avatars.com/api/?background=${currentAccentColor.substring(1)}&color=fff&name=${encodeURIComponent(name)}`; 
}

function closeModal(id) { 
    const modal = document.getElementById(id);
    modal.classList.add('closing');
    setTimeout(() => modal.classList.remove('visible', 'closing'), 300);
}

function playSound() { 
    if(!soundEnabled) return;
    try { 
        const ctx = new (window.AudioContext || window.webkitAudioContext)(); 
        const o = ctx.createOscillator(); 
        const g = ctx.createGain(); 
        o.connect(g); 
        g.connect(ctx.destination); 
        o.frequency.value = 880; 
        g.gain.value = 0.1; 
        o.start(); 
        g.gain.exponentialRampToValueAtTime(0.00001, ctx.currentTime + 0.2); 
        o.stop(ctx.currentTime + 0.2); 
        ctx.resume(); 
    } catch(e){} 
}

function formatTime(timestamp) {
    const date = new Date(timestamp);
    if(showSeconds) {
        return date.toLocaleTimeString([], {hour:'2-digit', minute:'2-digit', second:'2-digit'});
    }
    return date.toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'});
}

// Загрузка данных
async function loadAllUsers() {
    const snap = await db.ref('users').once('value');
    allUsers = [];
    for(let id in snap.val()) {
        const u = snap.val()[id];
        if(id !== currentUserId && !u.blocked) {
            allUsers.push({id, name: u.name, avatar: u.avatarUrl || '', password: u.password, bio: u.bio, phone: u.phone, birthday: u.birthday, isAdmin: u.isAdmin || false});
        }
    }
}

async function loadContacts() {
    const contactsSnap = await db.ref('users/' + currentUserId + '/contacts').once('value');
    contactsList = [];
    for(let id in contactsSnap.val() || {}) {
        const u = await db.ref('users/' + id).once('value');
        if(u.exists() && !u.val().blocked) {
            contactsList.push({id, name: u.val().name, avatar: u.val().avatarUrl});
        }
    }
}

async function loadGroups() {
    const snap = await db.ref('groups').once('value');
    groups = [];
    for(let id in snap.val()) {
        const g = snap.val()[id];
        if(g.members && g.members[currentUserId]) {
            groups.push({id, name: g.name, description: g.description, creator: g.creator, members: g.members, avatar: g.avatar});
        }
    }
}

async function loadChannels() {
    const snap = await db.ref('channels').once('value');
    channels = [];
    for(let id in snap.val()) {
        const c = snap.val()[id];
        if(c.subscribers && c.subscribers[currentUserId]) {
            channels.push({id, name: c.name, description: c.description, creator: c.creator, subscribers: c.subscribers, avatar: c.avatar});
        }
    }
}

async function loadLastMessages() {
    for(let chat of chats) {
        const path = chat.isGroup ? `group_messages/${chat.id}` : (chat.isChannel ? `channel_messages/${chat.id}` : `private_messages/${[currentUserId, chat.id].sort().join('_')}`);
        const snap = await db.ref(path).orderByChild('time').limitToLast(1).once('value');
        const msgs = snap.val();
        if(msgs) {
            const lastMsg = Object.values(msgs)[0];
            chat.lastMessage = lastMsg.text ? (lastMsg.text.length > 30 ? lastMsg.text.substring(0,30)+'...' : lastMsg.text) : (lastMsg.mediaType === 'image' ? '📷 Фото' : (lastMsg.mediaType === 'video' ? '🎥 Видео' : (lastMsg.mediaType === 'audio' ? '🎤 Голосовое' : (lastMsg.mediaType === 'file' ? '📎 Файл' : (lastMsg.mediaType === 'contact' ? '👤 Контакт' : 'Медиа')))));
            chat.lastTime = lastMsg.time;
        }
    }
    renderChats();
}

async function loadChats() {
    await loadGroups();
    await loadChannels();
    const contactsSnap = await db.ref('users/' + currentUserId + '/contacts').once('value');
    const contacts = contactsSnap.val() || {};
    chats = [];
    for(let id in contacts) {
        const u = await db.ref('users/' + id).once('value');
        if(u.exists() && !u.val().blocked) {
            chats.push({id, name: u.val().name, avatar: u.val().avatarUrl, isGroup: false, isChannel: false, lastMessage: '', lastTime: 0});
        }
    }
    for(let g of groups) {
        chats.push({id: g.id, name: g.name, avatar: g.avatar, isGroup: true, isChannel: false, lastMessage: '', lastTime: 0, creator: g.creator, description: g.description});
    }
    for(let c of channels) {
        chats.push({id: c.id, name: '📢 ' + c.name, avatar: c.avatar, isGroup: false, isChannel: true, lastMessage: '', lastTime: 0, creator: c.creator, description: c.description});
    }
    await loadLastMessages();
}

function renderChats() {
    const container = document.getElementById('chatsList');
    if(!container) return;
    container.innerHTML = chats.map(c => `
        <div class="chat-item ${selectedChats.has(c.id) ? 'selected' : ''}" data-chat-id="${c.id}" data-is-group="${c.isGroup}" data-is-channel="${c.isChannel}" data-name="${escapeHtml(c.name).replace(/'/g,"\\'")}">
            <div style="position:relative;">
                <img class="chat-avatar" src="${getAvatarUrl(c.name, c.avatar)}">
                ${!c.isGroup && !c.isChannel ? `<div class="online-dot offline" id="online-${c.id}"></div>` : ''}
            </div>
            <div class="chat-info">
                <div class="chat-name">${escapeHtml(c.name)}${mutedChats.has(c.id) ? ' <i class="fas fa-volume-mute" style="font-size:0.7rem;"></i>' : ''}${c.isChannel ? ' <i class="fas fa-broadcast-tower" style="font-size:0.7rem;"></i>' : ''}</div>
                <div class="chat-preview">${c.lastMessage || 'Нет сообщений'}</div>
            </div>
            <div class="chat-meta">
                <div class="chat-time">${c.lastTime ? formatTime(c.lastTime) : ''}</div>
                ${unreadCounts.get(c.id) > 0 ? `<div class="unread-count">${unreadCounts.get(c.id)}</div>` : ''}
            </div>
        </div>
    `).join('');
    
    document.querySelectorAll('.chat-item').forEach(el => {
        const chatId = el.dataset.chatId;
        const isGroup = el.dataset.isGroup === 'true';
        const isChannel = el.dataset.isChannel === 'true';
        const name = el.dataset.name;
        
        let pressTimer;
        el.addEventListener('touchstart', (e) => {
            pressTimer = setTimeout(() => {
                toggleChatSelection(chatId);
            }, 500);
        });
        el.addEventListener('touchend', () => clearTimeout(pressTimer));
        el.addEventListener('touchmove', () => clearTimeout(pressTimer));
        el.addEventListener('click', (e) => {
            if(!selectedChats.has(chatId)) {
                openChat(chatId, name, isGroup, isChannel);
            }
        });
    });
    updateOnlineStatuses();
}

function toggleChatSelection(chatId) {
    if(selectedChats.has(chatId)) {
        selectedChats.delete(chatId);
        document.querySelector(`.chat-item[data-chat-id="${chatId}"]`)?.classList.remove('selected');
    } else {
        selectedChats.add(chatId);
        document.querySelector(`.chat-item[data-chat-id="${chatId}"]`)?.classList.add('selected');
    }
    if(selectedChats.size > 0) {
        document.getElementById('selectionBar').classList.add('visible');
        document.getElementById('selectedCount').innerHTML = `${selectedChats.size}`;
    } else {
        document.getElementById('selectionBar').classList.remove('visible');
    }
}

async function deleteSelectedChats() {
    if(selectedChats.size === 0) return;
    if(confirm(`Удалить ${selectedChats.size} чатов?`)) {
        for(let chatId of selectedChats) {
            const chat = chats.find(c => c.id === chatId);
            if(chat && !chat.isGroup && !chat.isChannel) {
                await db.ref('users/' + currentUserId + '/contacts/' + chatId).remove();
            } else if(chat && chat.isGroup) {
                await db.ref('groups/' + chatId + '/members/' + currentUserId).remove();
            } else if(chat && chat.isChannel) {
                await db.ref('channels/' + chatId + '/subscribers/' + currentUserId).remove();
            }
        }
        selectedChats.clear();
        document.getElementById('selectionBar').classList.remove('visible');
        await loadChats();
        showToast('Чаты удалены');
    }
}

async function updateOnlineStatuses() {
    for(let c of chats) {
        if(!c.isGroup && !c.isChannel) {
            const u = await db.ref('users/' + c.id).once('value');
            const isOnline = u.val()?.online || (u.val()?.lastSeen && Date.now() - u.val().lastSeen < 60000);
            const dot = document.getElementById(`online-${c.id}`);
            if(dot) dot.className = `online-dot ${isOnline ? 'online' : 'offline'}`;
        }
    }
}

// Чат
function getChatPath() {
    if(currentChat.isGroup) return `group_messages/${currentChat.id}`;
    if(currentChat.isChannel) return `channel_messages/${currentChat.id}`;
    const ids = [currentUserId, currentChat.id].sort();
    return `private_messages/${ids[0]}_${ids[1]}`;
}

window.openChat = async function(id, name, isGroup, isChannel) {
    currentChat = { id, name: name.replace('📢 ', ''), isGroup, isChannel };
    document.getElementById('chatName').innerText = currentChat.name;
    const chat = chats.find(c => c.id === id);
    document.getElementById('chatAvatar').src = getAvatarUrl(currentChat.name, chat?.avatar);
    if(isChannel) {
        document.getElementById('chatStatus').innerHTML = '📢 Канал';
    } else if(isGroup) {
        document.getElementById('chatStatus').innerHTML = '👥 Группа';
    } else {
        const u = await db.ref('users/' + id).once('value');
        document.getElementById('chatStatus').innerHTML = u.val()?.online ? '🟢 В сети' : '⚫ Не в сети';
    }
    unreadCounts.set(id, 0);
    renderChats();
    replyingTo = null;
    document.getElementById('replyIndicator').style.display = 'none';
    clearSelection();
    document.getElementById('selectionBar').classList.remove('visible');
    loadMessages();
    document.getElementById('chatScreen').classList.add('open');
};

function loadMessages() {
    const area = document.getElementById('messagesArea');
    area.innerHTML = '<div class="loading-spinner"></div>';
    const path = getChatPath();
    
    if(messageListeners.has(path)) {
        messageListeners.get(path).off();
    }
    
    if(!processedMsgIds.get(path)) processedMsgIds.set(path, new Set());
    const processed = processedMsgIds.get(path);
    processed.clear();
    
    const msgsRef = db.ref(path);
    let hasMessages = false;
    
    msgsRef.orderByChild('time').limitToLast(50).once('value', (snap) => {
        const msgs = snap.val();
        if(msgs) {
            hasMessages = Object.keys(msgs).length > 0;
            if(area.innerHTML === '<div class="loading-spinner"></div>') area.innerHTML = '';
            const sortedMsgs = Object.entries(msgs).sort((a,b) => a[1].time - b[1].time);
            for(let [id, msg] of sortedMsgs) {
                if(!processed.has(id)) {
                    processed.add(id);
                    renderMessage(id, msg, path);
                }
            }
        }
        if(!hasMessages && area.innerHTML === '<div class="loading-spinner"></div>') {
            area.innerHTML = '<div class="system-message"><div class="bubble">💬 Нет сообщений</div></div>';
        }
    });
    
    msgsRef.on('child_added', (snap) => {
        if(!processed.has(snap.key)) {
            processed.add(snap.key);
            if(area.innerHTML === '<div class="loading-spinner"></div>') area.innerHTML = '';
            renderMessage(snap.key, snap.val(), path);
        }
    });
    
    msgsRef.on('child_changed', (snap) => {
        updateMessageInUI(snap.key, snap.val());
    });
    
    msgsRef.on('child_removed', (snap) => {
        removeMessageFromUI(snap.key);
    });
    
    messageListeners.set(path, msgsRef);
}

function updateMessageInUI(msgId, msg) {
    const el = document.getElementById(`msg-${msgId}`);
    if(el && msg) {
        const reactionsDiv = el.querySelector('.reactions');
        if(reactionsDiv && msg.reactions) {
            let reactionsHtml = '';
            const reactionMap = new Map();
            for(let uid in msg.reactions) {
                for(let r of msg.reactions[uid]) {
                    reactionMap.set(r, (reactionMap.get(r) || 0) + 1);
                }
            }
            for(let [r, count] of reactionMap) {
                reactionsHtml += `<span class="reaction" onclick="event.stopPropagation(); addReaction('${msgId}', ${JSON.stringify(msg).replace(/"/g, '&quot;')}, '${r}')">${r} <span class="reaction-count">${count}</span></span>`;
            }
            reactionsHtml += `<span class="add-reaction" onclick="event.stopPropagation(); showReactionPickerFromEvent(event, '${msgId}', ${JSON.stringify(msg).replace(/"/g, '&quot;')})">➕</span>`;
            reactionsDiv.innerHTML = reactionsHtml;
        }
        if(msg.userId === currentUserId && !currentChat.isGroup && !currentChat.isChannel) {
            const statusSpan = el.querySelector('.read-status');
            if(statusSpan) {
                statusSpan.innerHTML = msg.read ? '✓✓' : '✓';
                statusSpan.classList.toggle('read', msg.read);
            }
        }
    }
}

function removeMessageFromUI(msgId) {
    const el = document.getElementById(`msg-${msgId}`);
    if(el) el.remove();
    selectedMessages.delete(msgId);
    updateSelectionBar();
}

async function addReaction(msgId, msg, reaction) {
    const path = getChatPath();
    const reactions = msg.reactions || {};
    const userReactions = reactions[currentUserId] || [];
    if(userReactions.includes(reaction)) {
        reactions[currentUserId] = userReactions.filter(r => r !== reaction);
        if(reactions[currentUserId].length === 0) delete reactions[currentUserId];
    } else {
        reactions[currentUserId] = [...userReactions, reaction];
    }
    await db.ref(`${path}/${msgId}`).update({ reactions });
}

function showReactionPicker(x, y, msgId, msg) {
    let picker = document.getElementById('reactionPicker');
    if(!picker) {
        picker = document.createElement('div');
        picker.id = 'reactionPicker';
        picker.className = 'reaction-picker';
        picker.innerHTML = `
            <span class="reaction-emoji">👍</span>
            <span class="reaction-emoji">❤️</span>
            <span class="reaction-emoji">😂</span>
            <span class="reaction-emoji">😮</span>
            <span class="reaction-emoji">😢</span>
            <span class="reaction-emoji">🔥</span>
        `;
        document.body.appendChild(picker);
    }
    picker.style.left = Math.min(x - 100, window.innerWidth - 220) + 'px';
    picker.style.top = y - 60 + 'px';
    picker.style.display = 'flex';
    picker.querySelectorAll('.reaction-emoji').forEach(emoji => {
        emoji.onclick = () => {
            addReaction(msgId, msg, emoji.innerText);
            picker.style.display = 'none';
        };
    });
    setTimeout(() => {
        document.addEventListener('click', function hidePicker(e) {
            if(!picker.contains(e.target)) {
                picker.style.display = 'none';
                document.removeEventListener('click', hidePicker);
            }
        });
    }, 100);
}

function showReactionPickerFromEvent(event, msgId, msg) {
    showReactionPicker(event.clientX, event.clientY, msgId, msg);
}

function renderMessage(msgId, msg, path) {
    if(document.getElementById(`msg-${msgId}`)) return;
    const isMe = msg.userId === currentUserId;
    const isSystem = msg.isSystem;
    const canWrite = !currentChat.isChannel || currentChat.creator === currentUserId;
    
    const div = document.createElement('div');
    div.className = `message ${isSystem ? 'system-message' : (isMe ? 'my-message' : 'other-message')}`;
    div.id = `msg-${msgId}`;
    div.dataset.msgId = msgId;
    
    let pressTimer;
    div.addEventListener('touchstart', (e) => {
        pressTimer = setTimeout(() => {
            toggleMessageSelection(msgId);
            showToast('Сообщение выделено');
        }, 500);
    });
    div.addEventListener('touchend', () => clearTimeout(pressTimer));
    div.addEventListener('touchmove', () => clearTimeout(pressTimer));
    
    let touchStartX = 0;
    div.addEventListener('touchstart', (e) => {
        touchStartX = e.touches[0].clientX;
    });
    div.addEventListener('touchend', (e) => {
        const diffX = e.changedTouches[0].clientX - touchStartX;
        if(diffX > 60 && !isMe && !isSystem && !selectedMessages.size && canWrite) {
            replyingTo = { messageId: msgId, userName: msg.name, text: (msg.text || 'Медиа').substring(0,50) };
            document.getElementById('replyIndicator').style.display = 'flex';
            document.getElementById('replyToName').innerText = msg.name;
            document.getElementById('replyToText').innerText = (msg.text || 'Медиа').substring(0,50);
            showToast('Сообщение отмечено для ответа');
        }
    });
    
    let replyHtml = '', mediaHtml = '', reactionsHtml = '';
    if(msg.replyTo) replyHtml = `<div class="reply-preview" onclick="scrollToMessage('${msg.replyTo.messageId}')"><i class="fas fa-reply"></i> <strong>${escapeHtml(msg.replyTo.userName)}</strong>: ${escapeHtml(msg.replyTo.text)}</div>`;
    
    if(msg.mediaType === 'image') {
        mediaHtml = `<img src="${msg.mediaUrl}" class="media-content" onclick="window.open('${msg.mediaUrl}')">`;
    } else if(msg.mediaType === 'video') {
        if(msg.isCircle) {
            mediaHtml = `<div class="circle-video"><video controls preload="metadata"><source src="${msg.mediaUrl}" type="video/webm"></video></div>`;
        } else {
            mediaHtml = `<video controls class="media-content" preload="metadata"><source src="${msg.mediaUrl}" type="video/mp4"></video>`;
        }
    } else if(msg.mediaType === 'audio') {
        mediaHtml = `<audio controls preload="metadata"><source src="${msg.mediaUrl}" type="audio/webm"></audio>`;
    } else if(msg.mediaType === 'file') {
        mediaHtml = `<div class="file-attachment" onclick="window.open('${msg.mediaUrl}')"><i class="fas fa-file"></i><div><div>${escapeHtml(msg.fileName)}</div><div>${formatFileSize(msg.fileSize)}</div></div></div>`;
    } else if(msg.mediaType === 'contact' && msg.contactData) {
        mediaHtml = `<div class="contact-card" onclick="showUserProfile('${msg.contactData.userId}')"><img src="${getAvatarUrl(msg.contactData.name, msg.contactData.avatar)}"><div><div>${escapeHtml(msg.contactData.name)}</div><div style="font-size:0.7rem;">Контакт</div></div></div>`;
    }
    
    if(msg.reactions) {
        const reactionMap = new Map();
        for(let uid in msg.reactions) {
            for(let r of msg.reactions[uid]) {
                reactionMap.set(r, (reactionMap.get(r) || 0) + 1);
            }
        }
        for(let [r, count] of reactionMap) {
            reactionsHtml += `<span class="reaction" onclick="event.stopPropagation(); addReaction('${msgId}', ${JSON.stringify(msg).replace(/"/g, '&quot;')}, '${r}')">${r} <span class="reaction-count">${count}</span></span>`;
        }
    }
    reactionsHtml += `<span class="add-reaction" onclick="event.stopPropagation(); showReactionPickerFromEvent(event, '${msgId}', ${JSON.stringify(msg).replace(/"/g, '&quot;')})">➕</span>`;
    
    const readStats = (isMe && currentChat.isGroup && msg.readBy) ? `<span class="read-stats" onclick="showReadBy('${msgId}')">👁 ${Object.keys(msg.readBy).length - 1}</span>` : '';
    const readStatus = (isMe && !currentChat.isGroup && !currentChat.isChannel) ? `<span class="read-status ${msg.read ? 'read' : ''}">${msg.read ? '✓✓' : '✓'}</span>` : '';
    
    div.innerHTML = `<div class="bubble"><div class="message-header"><img class="msg-avatar" src="${getAvatarUrl(msg.name, msg.avatar)}" onclick="showUserProfile('${msg.userId}')"><span class="message-name" onclick="showUserProfile('${msg.userId}')">${escapeHtml(msg.name)}</span></div>${replyHtml}${mediaHtml}${msg.text && !msg.mediaType ? `<div>${escapeHtml(msg.text)}</div>` : ''}<div class="message-time">${formatTime(msg.time)} ${readStatus} ${readStats}</div>${reactionsHtml ? `<div class="reactions">${reactionsHtml}</div>` : ''}</div>`;
    
    const area = document.getElementById('messagesArea');
    area.appendChild(div);
    area.scrollTop = area.scrollHeight;
    
    if(!isMe && !msg.read && !currentChat.isGroup && !currentChat.isChannel && !isSystem) {
        db.ref(`${path}/${msgId}`).update({ read: true, [`readBy/${currentUserId}`]: true });
    } else if(!isMe && currentChat.isGroup && !isSystem && !msg.readBy?.[currentUserId]) {
        db.ref(`${path}/${msgId}/readBy/${currentUserId}`).set(true);
    }
    
    if(!isMe && !isSystem && currentChat.id !== msg.userId) {
        unreadCounts.set(currentChat.id, (unreadCounts.get(currentChat.id) || 0) + 1);
        renderChats();
        if(notificationsEnabled && !mutedChats.has(currentChat.id)) {
            playSound();
            if(document.hidden && Notification.permission === 'granted') {
                new Notification(msg.name, { body: msg.text || 'Новое сообщение', icon: getAvatarUrl(msg.name, msg.avatar) });
            }
        }
    }
}

function formatFileSize(bytes) { 
    if(bytes<1024) return bytes+' B'; 
    if(bytes<1048576) return (bytes/1024).toFixed(1)+' KB'; 
    return (bytes/1048576).toFixed(1)+' MB'; 
}

async function sendMessage(text, mediaType, mediaUrl, fileData, contactData, isCircle) {
    if(isSending) return;
    if(currentChat.isChannel && currentChat.creator !== currentUserId) {
        showToast('В канале могут писать только создатели');
        return;
    }
    isSending = true;
    const sendBtn = document.getElementById('sendBtn');
    sendBtn.classList.add('disabled');
    
    try {
        const msg = { 
            userId: currentUserId, 
            name: currentUserName, 
            avatar: currentUserAvatar, 
            text: text || '', 
            mediaType, 
            mediaUrl, 
            time: Date.now(), 
            read: false, 
            delivered: false, 
            readBy: { [currentUserId]: true } 
        };
        if(isCircle) msg.isCircle = true;
        if(fileData) { msg.fileName = fileData.name; msg.fileSize = fileData.size; }
        if(contactData) { msg.contactData = contactData; msg.mediaType = 'contact'; }
        if(replyingTo) { msg.replyTo = replyingTo; replyingTo = null; document.getElementById('replyIndicator').style.display = 'none'; }
        await db.ref(getChatPath()).push(msg);
    } finally {
        setTimeout(() => {
            isSending = false;
            sendBtn.classList.remove('disabled');
        }, 500);
    }
}

// Выделение сообщений
function toggleMessageSelection(msgId) {
    const el = document.getElementById(`msg-${msgId}`);
    if(selectedMessages.has(msgId)) {
        selectedMessages.delete(msgId);
        el.classList.remove('selected');
    } else {
        selectedMessages.add(msgId);
        el.classList.add('selected');
    }
    updateSelectionBar();
}

function updateSelectionBar() {
    const bar = document.getElementById('selectionBar');
    const count = selectedMessages.size;
    if(count > 0) {
        bar.classList.add('visible');
        document.getElementById('selectedCount').innerHTML = `${count}`;
    } else {
        bar.classList.remove('visible');
    }
}

function clearSelection() {
    selectedMessages.forEach(id => {
        const el = document.getElementById(`msg-${id}`);
        if(el) el.classList.remove('selected');
    });
    selectedMessages.clear();
    updateSelectionBar();
}

async function forwardSelectedMessages() {
    if(selectedMessages.size === 0) return;
    const targets = chats.filter(c => c.id !== currentChat.id);
    const container = document.getElementById('forwardList');
    container.innerHTML = targets.map(t => `<div class="contact-list-item" onclick="forwardToChat('${t.id}', ${t.isGroup}, ${t.isChannel})"><img src="${getAvatarUrl(t.name, t.avatar)}"><div class="contact-list-item-name">${escapeHtml(t.name)}</div></div>`).join('');
    if(targets.length === 0) container.innerHTML = '<div style="padding:16px;text-align:center;">Нет контактов</div>';
    window.pendingForwardMessages = Array.from(selectedMessages);
    document.getElementById('forwardModal').classList.add('visible');
}

window.forwardToChat = async (targetId, isGroup, isChannel) => {
    for(let msgId of window.pendingForwardMessages) {
        const msgSnap = await db.ref(getChatPath() + '/' + msgId).once('value');
        const msgData = msgSnap.val();
        if(msgData) {
            const forwardData = { 
                ...msgData, 
                userId: currentUserId, 
                name: currentUserName, 
                avatar: currentUserAvatar, 
                time: Date.now(), 
                read: false, 
                readBy: { [currentUserId]: true } 
            };
            let targetPath;
            if(isGroup) targetPath = `group_messages/${targetId}`;
            else if(isChannel) targetPath = `channel_messages/${targetId}`;
            else targetPath = `private_messages/${[currentUserId, targetId].sort().join('_')}`;
            await db.ref(targetPath).push(forwardData);
        }
    }
    showToast('Переслано');
    closeModal('forwardModal');
    clearSelection();
};

document.getElementById('replySelectedBtn').onclick = () => {
    if(selectedMessages.size === 1) {
        const msgId = Array.from(selectedMessages)[0];
        const msgEl = document.getElementById(`msg-${msgId}`);
        const name = msgEl.querySelector('.message-name')?.innerText;
        const text = msgEl.querySelector('.bubble > div:last-child')?.innerText || 'Медиа';
        replyingTo = { messageId: msgId, userName: name, text: text.substring(0,50) };
        document.getElementById('replyIndicator').style.display = 'flex';
        document.getElementById('replyToName').innerText = name;
        document.getElementById('replyToText').innerText = text.substring(0,50);
        clearSelection();
    } else showToast('Выберите одно сообщение');
};

document.getElementById('forwardSelectedBtn').onclick = forwardSelectedMessages;
document.getElementById('deleteSelectedBtn').onclick = async () => {
    if(confirm(`Удалить ${selectedMessages.size} сообщений?`)) {
        for(let msgId of selectedMessages) {
            const msgEl = document.getElementById(`msg-${msgId}`);
            const isMe = msgEl?.classList.contains('my-message');
            const canDelete = isMe || (isAdmin && currentChat.isGroup);
            if(canDelete) await db.ref(`${getChatPath()}/${msgId}`).remove();
        }
        clearSelection();
    }
};
document.getElementById('cancelSelectionBtn').onclick = clearSelection;

// Запись голосовых и кружков
async function startRecording(type) {
    recordType = type;
    recordSeconds = 0;
    isPaused = false;
    document.getElementById('recTimer').innerText = '00:00';
    document.getElementById('recProgress').style.width = '0%';
    const overlay = document.getElementById('recordingOverlay');
    overlay.classList.add('visible');
    
    if(type === 'circle') {
        document.getElementById('recPreview').style.display = 'block';
        document.getElementById('recWave').style.display = 'none';
        document.getElementById('flipCameraBtn').style.display = 'block';
        try {
            const stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: currentFacing }, audio: true });
            recordStream = stream;
            document.getElementById('recVideo').srcObject = stream;
            mediaRecorder = new MediaRecorder(stream, { mimeType: 'video/webm' });
            videoChunks = [];
            mediaRecorder.ondataavailable = e => { if(e.data.size > 0) videoChunks.push(e.data); };
            mediaRecorder.start();
            isRecording = true;
        } catch(e) { showToast('Ошибка доступа к камере'); overlay.classList.remove('visible'); return; }
    } else {
        document.getElementById('recPreview').style.display = 'none';
        document.getElementById('recWave').style.display = 'flex';
        document.getElementById('flipCameraBtn').style.display = 'none';
        const waveContainer = document.getElementById('recWave');
        waveContainer.innerHTML = '';
        for(let i = 0; i < 30; i++) {
            const bar = document.createElement('div');
            bar.className = 'wave-bar';
            bar.style.height = '20px';
            waveContainer.appendChild(bar);
        }
        try {
            const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
            recordStream = stream;
            audioContext = new (window.AudioContext || window.webkitAudioContext)();
            analyser = audioContext.createAnalyser();
            const source = audioContext.createMediaStreamSource(stream);
            source.connect(analyser);
            analyser.fftSize = 256;
            mediaRecorder = new MediaRecorder(stream);
            audioChunks = [];
            mediaRecorder.ondataavailable = e => { if(e.data.size > 0) audioChunks.push(e.data); };
            mediaRecorder.start();
            isRecording = true;
            drawWaveform();
        } catch(e) { showToast('Ошибка доступа к микрофону'); overlay.classList.remove('visible'); return; }
    }
    
    recordTimer = setInterval(() => {
        if(!isPaused && isRecording) {
            recordSeconds++;
            document.getElementById('recTimer').innerText = Math.floor(recordSeconds/60).toString().padStart(2,'0')+':'+(recordSeconds%60).toString().padStart(2,'0');
            document.getElementById('recProgress').style.width = (recordSeconds/60*100)+'%';
            if(recordSeconds >= 60) stopRecordingAndSend();
        }
    }, 1000);
}

function drawWaveform() {
    if(!isRecording || isPaused || !analyser) return;
    const buffer = new Uint8Array(analyser.frequencyBinCount);
    analyser.getByteFrequencyData(buffer);
    const bars = document.querySelectorAll('.wave-bar');
    for(let i = 0; i < bars.length && i < buffer.length; i++) {
        const value = buffer[i] || 0;
        bars[i].style.height = Math.max(5, Math.min(80, value / 3)) + 'px';
    }
    animationId = requestAnimationFrame(drawWaveform);
}

function stopRecordingAndSend() {
    if(mediaRecorder && mediaRecorder.state === 'recording') mediaRecorder.stop();
    if(recordStream) recordStream.getTracks().forEach(t => t.stop());
    clearInterval(recordTimer);
    if(animationId) cancelAnimationFrame(animationId);
    if(audioContext) audioContext.close();
    
    setTimeout(() => {
        const blob = recordType === 'circle' ? new Blob(videoChunks, {type:'video/webm'}) : new Blob(audioChunks, {type:'audio/webm'});
        const url = URL.createObjectURL(blob);
        sendMessage('', recordType === 'circle' ? 'video' : 'audio', url, null, null, recordType === 'circle');
        document.getElementById('recordingOverlay').classList.remove('visible');
        isRecording = false;
    }, 100);
}

document.getElementById('pauseRecBtn').onclick = () => {
    if(mediaRecorder && mediaRecorder.state === 'recording') { mediaRecorder.pause(); isPaused = true; document.getElementById('pauseRecBtn').innerHTML = '<i class="fas fa-play"></i>'; }
    else if(mediaRecorder && mediaRecorder.state === 'paused') { mediaRecorder.resume(); isPaused = false; document.getElementById('pauseRecBtn').innerHTML = '<i class="fas fa-pause"></i>'; if(recordType !== 'circle') drawWaveform(); }
};
document.getElementById('stopRecBtn').onclick = () => { stopRecordingAndSend(); };
document.getElementById('deleteRecBtn').onclick = () => { stopRecordingAndSend(); };
document.getElementById('sendRecBtn').onclick = () => stopRecordingAndSend();
document.getElementById('flipCameraBtn').onclick = () => { currentFacing = currentFacing === 'user' ? 'environment' : 'user'; if(recordStream) { recordStream.getTracks().forEach(t => t.stop()); startRecording('circle'); } };

// Отправка сообщений
document.getElementById('sendBtn').onclick = async () => {
    const input = document.getElementById('messageInput');
    const text = input.value.trim();
    if(text && !isSending) await sendMessage(text, null, null, null, null, false);
    input.value = ''; input.style.height = 'auto';
};
document.getElementById('voiceBtn').onclick = () => startRecording('voice');
document.getElementById('circleBtn').onclick = () => startRecording('circle');

// Меню вложения
document.getElementById('attachBtn').onclick = () => {
    const menu = document.getElementById('attachMenu');
    if(menu.classList.contains('visible')) menu.classList.remove('visible');
    else menu.classList.add('visible');
};

document.getElementById('attachPhotoBtn').onclick = () => { document.getElementById('attachMenu').classList.remove('visible'); document.getElementById('photoInput').click(); };
document.getElementById('attachCameraBtn').onclick = () => { document.getElementById('attachMenu').classList.remove('visible'); document.getElementById('cameraInput').click(); };
document.getElementById('attachVideoBtn').onclick = () => { document.getElementById('attachMenu').classList.remove('visible'); document.getElementById('videoInput').click(); };
document.getElementById('attachFileBtn').onclick = () => { document.getElementById('attachMenu').classList.remove('visible'); document.getElementById('fileInput').click(); };
document.getElementById('attachContactBtn').onclick = async () => {
    document.getElementById('attachMenu').classList.remove('visible');
    await loadContacts();
    const container = document.getElementById('forwardList');
    container.innerHTML = contactsList.map(c => `<div class="contact-list-item" onclick="sendContact('${c.id}','${escapeHtml(c.name)}','${c.avatar}'); closeModal('forwardModal');"><img src="${getAvatarUrl(c.name, c.avatar)}"><div class="contact-list-item-name">${escapeHtml(c.name)}</div></div>`).join('');
    if(contactsList.length === 0) container.innerHTML = '<div style="padding:16px;text-align:center;">Нет контактов для отправки</div>';
    window.sendContact = (id, name, avatar) => sendMessage('', 'contact', '', null, {userId: id, name, avatar}, false);
    document.getElementById('forwardModal').classList.add('visible');
};

document.getElementById('photoInput').onchange = e => handleFile(e.target.files[0], 'image');
document.getElementById('cameraInput').onchange = e => handleFile(e.target.files[0], 'image');
document.getElementById('videoInput').onchange = e => handleFile(e.target.files[0], 'video');
document.getElementById('fileInput').onchange = e => handleFile(e.target.files[0], 'file');

function handleFile(file, type) {
    if(!file) return;
    const reader = new FileReader();
    reader.onload = e => sendMessage('', type, e.target.result, {name: file.name, size: file.size}, null, false);
    reader.readAsDataURL(file);
}

document.getElementById('cancelReplyBtn').onclick = () => { replyingTo = null; document.getElementById('replyIndicator').style.display = 'none'; };
document.getElementById('backBtn').onclick = () => { clearSelection(); document.getElementById('chatScreen').classList.remove('open'); };
document.getElementById('messageInput').addEventListener('input', function(){ this.style.height = 'auto'; this.style.height = Math.min(this.scrollHeight, 120) + 'px'; });

// Контакты и группы
async function showAddContactModal() {
    await loadAllUsers();
    const existingContactIds = new Set(contactsList.map(c => c.id));
    const availableUsers = allUsers.filter(u => !existingContactIds.has(u.id));
    const container = document.getElementById('allUsersList');
    container.innerHTML = availableUsers.map(u => `<div class="contact-list-item" onclick="addContact('${u.id}', '${escapeHtml(u.name)}')"><img src="${getAvatarUrl(u.name, u.avatar)}"><div class="contact-list-item-name">${escapeHtml(u.name)}</div></div>`).join('');
    if(availableUsers.length === 0) container.innerHTML = '<div style="padding:16px;text-align:center;">Нет доступных пользователей для добавления</div>';
    document.getElementById('addContactModal').classList.add('visible');
}

window.addContact = async (id, name) => {
    const exists = await db.ref('users/' + currentUserId + '/contacts/' + id).once('value');
    if(exists.exists()) {
        showToast('Этот контакт уже добавлен');
        return;
    }
    await db.ref('users/' + currentUserId + '/contacts/' + id).set({ userId: id, name, addedAt: Date.now() });
    await loadChats();
    closeModal('addContactModal');
    showToast(`✅ ${name} добавлен`);
};

async function showCreateGroupModal() {
    await loadAllUsers();
    const container = document.getElementById('membersList');
    container.innerHTML = '<div style="margin-bottom:8px;">Выберите участников:</div>';
    for(let u of allUsers) {
        if(u.id !== currentUserId) {
            container.innerHTML += `<div class="member-item" onclick="toggleMember('${u.id}')"><input type="checkbox" id="member_${u.id}" style="width:20px;margin:0;"><img src="${getAvatarUrl(u.name, u.avatar)}"><span>${escapeHtml(u.name)}</span></div>`;
        }
    }
    document.getElementById('createGroupModal').classList.add('visible');
}

window.toggleMember = (id) => { const cb = document.getElementById(`member_${id}`); if(cb) cb.checked = !cb.checked; };

document.getElementById('createGroupBtn').onclick = async () => {
    const name = document.getElementById('groupName').value.trim();
    if(!name) { showToast('Введите название'); return; }
    const check = await db.ref('groups').orderByChild('name').equalTo(name).once('value');
    if(check.exists()) { showToast('Группа с таким названием уже существует'); return; }
    const members = [currentUserId];
    document.querySelectorAll('#membersList input:checked').forEach(cb => members.push(cb.id));
    const membersObj = {};
    members.forEach(m => membersObj[m] = true);
    const groupId = Date.now().toString();
    let avatar = '';
    const avatarFile = document.getElementById('groupAvatarInput').files[0];
    if(avatarFile) {
        const reader = new FileReader();
        avatar = await new Promise((resolve) => {
            reader.onload = e => resolve(e.target.result);
            reader.readAsDataURL(avatarFile);
        });
    }
    await db.ref('groups/' + groupId).set({ id: groupId, name, description: document.getElementById('groupDesc').value, creator: currentUserId, members: membersObj, avatar, createdAt: Date.now() });
    await db.ref(`group_messages/${groupId}`).push({ userId: 'system', name: 'Система', text: `${currentUserName} создал группу`, time: Date.now(), isSystem: true });
    await loadChats();
    closeModal('createGroupModal');
    document.getElementById('groupName').value = '';
    document.getElementById('groupDesc').value = '';
    document.getElementById('groupAvatarInput').value = '';
    showToast('Группа создана');
};

async function showCreateChannelModal() {
    document.getElementById('createChannelModal').classList.add('visible');
}

document.getElementById('createChannelBtn').onclick = async () => {
    const name = document.getElementById('channelName').value.trim();
    if(!name) { showToast('Введите название'); return; }
    const check = await db.ref('channels').orderByChild('name').equalTo(name).once('value');
    if(check.exists()) { showToast('Канал с таким названием уже существует'); return; }
    const channelId = Date.now().toString();
    let avatar = '';
    const avatarFile = document.getElementById('channelAvatarInput').files[0];
    if(avatarFile) {
        const reader = new FileReader();
        avatar = await new Promise((resolve) => {
            reader.onload = e => resolve(e.target.result);
            reader.readAsDataURL(avatarFile);
        });
    }
    await db.ref('channels/' + channelId).set({ id: channelId, name, description: document.getElementById('channelDesc').value, creator: currentUserId, subscribers: { [currentUserId]: true }, avatar, createdAt: Date.now() });
    await loadChats();
    closeModal('createChannelModal');
    document.getElementById('channelName').value = '';
    document.getElementById('channelDesc').value = '';
    document.getElementById('channelAvatarInput').value = '';
    showToast('Канал создан');
};

window.showGroupInfo = async (groupId) => {
    const group = groups.find(g => g.id === groupId);
    if(!group) return;
    document.getElementById('groupInfoAvatar').src = getAvatarUrl(group.name, group.avatar);
    document.getElementById('groupInfoTitle').innerHTML = escapeHtml(group.name) + (group.creator === currentUserId ? ' 👑' : '');
    document.getElementById('groupInfoDesc').innerHTML = group.description || 'Нет описания';
    let html = '';
    for(let mid in group.members) {
        const u = await db.ref('users/' + mid).once('value');
        if(u.exists()) {
            html += `<div class="group-member-item"><img src="${getAvatarUrl(u.val().name, u.val().avatarUrl)}"><div class="member-name">${escapeHtml(u.val().name)}${mid === group.creator ? ' 👑' : ''}</div>${group.creator === currentUserId && mid !== currentUserId ? `<button class="kick-btn" onclick="kickFromGroup('${groupId}','${mid}')">Исключить</button>` : ''}</div>`;
        }
    }
    document.getElementById('groupMembersList').innerHTML = html;
    
    const addMemberDiv = document.getElementById('addMemberToGroup');
    const addSelect = document.getElementById('addMemberSelect');
    if(group.creator === currentUserId) {
        addMemberDiv.style.display = 'block';
        const allUsersSnap = await db.ref('users').once('value');
        addSelect.innerHTML = '<option value="">-- Выберите пользователя --</option>';
        for(let id in allUsersSnap.val()) {
            const u = allUsersSnap.val()[id];
            if(!group.members[id] && id !== currentUserId && !u.blocked) {
                addSelect.innerHTML += `<option value="${id}">${escapeHtml(u.name)}</option>`;
            }
        }
        document.getElementById('addMemberBtn').onclick = async () => {
            const userId = addSelect.value;
            if(!userId) { showToast('Выберите пользователя'); return; }
            const userSnap = await db.ref('users/'+userId).once('value');
            await db.ref('groups/'+groupId+'/members/'+userId).set(true);
            await db.ref(`group_messages/${groupId}`).push({ userId: 'system', name: 'Система', text: `${userSnap.val().name} присоединился к группе`, time: Date.now(), isSystem: true });
            showGroupInfo(groupId);
            await loadChats();
        };
    } else addMemberDiv.style.display = 'none';
    
    const leaveBtn = document.getElementById('leaveGroupBtn');
    const deleteBtn = document.getElementById('deleteGroupBtn');
    leaveBtn.onclick = async () => { 
        if(confirm('Покинуть группу?')) { 
            await db.ref('groups/'+groupId+'/members/'+currentUserId).remove(); 
            await db.ref(`group_messages/${groupId}`).push({ userId: 'system', name: 'Система', text: `${currentUserName} покинул группу`, time: Date.now(), isSystem: true });
            await loadChats(); 
            closeModal('groupInfoModal'); 
            if(currentChat.id === groupId) document.getElementById('chatScreen').classList.remove('open'); 
        } 
    };
    if(group.creator === currentUserId) { 
        deleteBtn.style.display = 'block'; 
        deleteBtn.onclick = async () => { 
            if(confirm('Удалить группу?')) { 
                await db.ref('groups/'+groupId).remove(); 
                await db.ref('group_messages/'+groupId).remove(); 
                await loadChats(); 
                closeModal('groupInfoModal'); 
                if(currentChat.id === groupId) document.getElementById('chatScreen').classList.remove('open'); 
            } 
        }; 
    } else deleteBtn.style.display = 'none';
    document.getElementById('groupInfoModal').classList.add('visible');
};

window.kickFromGroup = async (groupId, userId) => { 
    const userSnap = await db.ref('users/'+userId).once('value');
    await db.ref('groups/'+groupId+'/members/'+userId).remove(); 
    await db.ref(`group_messages/${groupId}`).push({ userId: 'system', name: 'Система', text: `${userSnap.val().name} был исключён из группы`, time: Date.now(), isSystem: true });
    showGroupInfo(groupId); 
    await loadChats(); 
};

// Профиль
window.showUserProfile = async (userId) => {
    const u = await db.ref('users/'+userId).once('value');
    if(!u.exists()) return;
    const user = u.val();
    if(userId === currentUserId) {
        document.getElementById('profileAvatar').src = getAvatarUrl(user.name, user.avatarUrl);
        document.getElementById('profileName').value = user.name;
        document.getElementById('profilePhone').value = user.phone || '';
        document.getElementById('profileBirthday').value = user.birthday || '';
        document.getElementById('profileBio').value = user.bio || '';
        document.getElementById('profileModal').classList.add('visible');
    } else {
        document.getElementById('viewProfileAvatar').src = getAvatarUrl(user.name, user.avatarUrl);
        document.getElementById('viewProfileName').innerText = user.name;
        document.getElementById('viewProfileBio').innerText = user.bio || 'Нет описания';
        document.getElementById('viewProfilePhone').innerHTML = user.phone ? `📞 ${user.phone}` : '';
        document.getElementById('viewProfileBirthday').innerHTML = user.birthday ? `🎂 ${user.birthday}` : '';
        document.getElementById('viewProfileModal').dataset.userId = userId;
        document.getElementById('viewProfileModal').dataset.userName = user.name;
        document.getElementById('viewProfileModal').classList.add('visible');
    }
};

document.getElementById('viewProfileSendMsg').onclick = () => {
    const userId = document.getElementById('viewProfileModal').dataset.userId;
    const userName = document.getElementById('viewProfileModal').dataset.userName;
    closeModal('viewProfileModal');
    openChat(userId, userName, false, false);
};

window.showMyProfile = () => showUserProfile(currentUserId);
window.showChatProfile = () => { if(!currentChat.isGroup && !currentChat.isChannel) showUserProfile(currentChat.id); else showGroupInfo(currentChat.id); };

document.getElementById('saveProfileBtn').onclick = async () => {
    const newName = document.getElementById('profileName').value.trim();
    const newPhone = document.getElementById('profilePhone').value.trim();
    const newBirthday = document.getElementById('profileBirthday').value;
    const newBio = document.getElementById('profileBio').value;
    const file = document.getElementById('avatarInput').files[0];
    
    const updates = {};
    if(newName && newName !== currentUserName) {
        const check = await db.ref('users').orderByChild('name').equalTo(newName).once('value');
        if(check.exists()) { showToast('Ник занят'); return; }
        updates.name = newName;
    }
    if(newPhone !== currentUserPhone) updates.phone = newPhone;
    if(newBirthday !== currentUserBirthday) updates.birthday = newBirthday;
    if(newBio !== currentUserBio) updates.bio = newBio;
    
    if(file) {
        const reader = new FileReader();
        const avatarBase64 = await new Promise((resolve) => {
            reader.onload = e => resolve(e.target.result);
            reader.readAsDataURL(file);
        });
        updates.avatarUrl = avatarBase64;
        currentUserAvatar = avatarBase64;
    }
    
    if(Object.keys(updates).length) {
        await db.ref('users/'+currentUserId).update(updates);
        if(updates.name) {
            currentUserName = updates.name;
            document.getElementById('menuName').innerText = currentUserName;
        }
        if(updates.phone) currentUserPhone = updates.phone;
        if(updates.birthday) currentUserBirthday = updates.birthday;
        if(updates.bio) currentUserBio = updates.bio;
        showToast('Профиль обновлён');
        closeModal('profileModal');
        await loadChats();
    }
};

document.getElementById('avatarInput').onchange = async (e) => {
    if(e.target.files[0]) {
        const reader = new FileReader();
        reader.onload = async (ev) => {
            await db.ref('users/'+currentUserId).update({ avatarUrl: ev.target.result });
            currentUserAvatar = ev.target.result;
            document.getElementById('menuAvatar').src = currentUserAvatar;
            showToast('Аватар обновлён');
        };
        reader.readAsDataURL(e.target.files[0]);
    }
};

// Настройки
function initToggles() {
    const showSecondsToggle = document.getElementById('showSecondsToggle');
    const disableAnimationsToggle = document.getElementById('disableAnimationsToggle');
    const notificationsToggle = document.getElementById('notificationsToggle');
    const soundToggle = document.getElementById('soundToggle');
    const darkThemeToggle = document.getElementById('darkThemeToggle');
    
    const updateToggle = (el, active) => {
        if(active) el.classList.add('active');
        else el.classList.remove('active');
    };
    
    showSecondsToggle.onclick = () => {
        showSeconds = !showSeconds;
        updateToggle(showSecondsToggle, showSeconds);
        saveSettings();
        loadChats();
    };
    disableAnimationsToggle.onclick = () => {
        animationsEnabled = !animationsEnabled;
        updateToggle(disableAnimationsToggle, !animationsEnabled);
        if(!animationsEnabled) document.body.classList.add('no-animations');
        else document.body.classList.remove('no-animations');
        saveSettings();
    };
    notificationsToggle.onclick = () => {
        notificationsEnabled = !notificationsEnabled;
        updateToggle(notificationsToggle, notificationsEnabled);
        saveSettings();
    };
    soundToggle.onclick = () => {
        soundEnabled = !soundEnabled;
        updateToggle(soundToggle, soundEnabled);
        saveSettings();
    };
    darkThemeToggle.onclick = () => {
        document.body.classList.toggle('dark');
        updateToggle(darkThemeToggle, document.body.classList.contains('dark'));
        saveSettings();
    };
    
    updateToggle(showSecondsToggle, showSeconds);
    updateToggle(disableAnimationsToggle, !animationsEnabled);
    updateToggle(notificationsToggle, notificationsEnabled);
    updateToggle(soundToggle, soundEnabled);
    updateToggle(darkThemeToggle, document.body.classList.contains('dark'));
}

document.getElementById('settingsMenuItem').onclick = () => {
    document.getElementById('settingsModal').classList.add('visible');
};
document.getElementById('themeMenuItem').onclick = () => {
    document.getElementById('themeModal').classList.add('visible');
};
document.getElementById('favoritesMenuItem').onclick = () => {
    openChat('favorites', '⭐ Избранное', false, false);
};
document.getElementById('searchChannelsMenuItem').onclick = async () => {
    const snap = await db.ref('channels').once('value');
    const allChannels = snap.val() || {};
    const container = document.getElementById('channelsSearchList');
    container.innerHTML = '';
    for(let id in allChannels) {
        const c = allChannels[id];
        if(!c.subscribers || !c.subscribers[currentUserId]) {
            container.innerHTML += `<div class="contact-list-item" onclick="subscribeToChannel('${id}', '${escapeHtml(c.name)}')"><img src="${getAvatarUrl(c.name, c.avatar)}"><div class="contact-list-item-name">${escapeHtml(c.name)}</div><div style="font-size:0.7rem; opacity:0.7;">${c.subscribers ? Object.keys(c.subscribers).length : 0} подписчиков</div></div>`;
        }
    }
    if(container.innerHTML === '') container.innerHTML = '<div style="padding:16px;text-align:center;">Нет доступных каналов</div>';
    document.getElementById('searchChannelsModal').classList.add('visible');
};

window.subscribeToChannel = async (channelId, channelName) => {
    await db.ref('channels/' + channelId + '/subscribers/' + currentUserId).set(true);
    await loadChats();
    closeModal('searchChannelsModal');
    showToast(`Вы подписались на канал ${channelName}`);
};

document.getElementById('uploadBgBtn').onclick = () => {
    document.getElementById('bgImageInput').click();
};

document.getElementById('resetBgBtn').onclick = () => {
    document.documentElement.style.setProperty('--chat-bg-image', 'none');
    saveSettings();
    showToast('Фон сброшен');
};

document.getElementById('bgImageInput').onchange = (e) => {
    if(e.target.files[0]) {
        const reader = new FileReader();
        reader.onload = (ev) => {
            document.documentElement.style.setProperty('--chat-bg-image', `url(${ev.target.result})`);
            saveSettings();
            showToast('Фон обновлён');
        };
        reader.readAsDataURL(e.target.files[0]);
    }
};

document.querySelectorAll('.color-option').forEach(opt => {
    opt.onclick = () => {
        const color = opt.dataset.color;
        currentAccentColor = color;
        document.documentElement.style.setProperty('--icon-color', color);
        document.documentElement.style.setProperty('--my-bubble', color);
        document.querySelectorAll('.color-option').forEach(o => o.classList.remove('selected'));
        opt.classList.add('selected');
        saveSettings();
        initToggles();
        loadChats();
    };
});

// Админ панель
async function showAdminPanel() {
    await loadAllUsers();
    let html = '';
    for(let u of allUsers) {
        html += `
            <div class="admin-user-card">
                <div class="admin-user-name">${escapeHtml(u.name)}</div>
                <div class="admin-user-detail">🔑 Пароль: ${escapeHtml(u.password)}</div>
                <div class="admin-user-detail">📝 О себе: ${escapeHtml(u.bio || 'Нет')}</div>
                <div class="admin-user-detail">${u.isAdmin ? '👑 Администратор' : '👤 Пользователь'}</div>
                <div class="admin-user-actions">
                    <button onclick="adminMakeAdmin('${u.id}')" style="background:#8b5cf6;">${u.isAdmin ? '⬇️ Снять админа' : '⬆️ Назначить админом'}</button>
                    <button onclick="adminEditUser('${u.id}')" style="background:#3b82f6;">✏️ Редактировать</button>
                    <button onclick="adminClearUserMessages('${u.id}')" style="background:#f59e0b;">🗑️ Очистить чаты</button>
                    <button onclick="adminDeleteUser('${u.id}')" style="background:#dc2626;">💀 Удалить</button>
                </div>
            </div>
        `;
    }
    document.getElementById('usersAdminList').innerHTML = html;
    document.getElementById('adminModal').classList.add('visible');
}

window.adminMakeAdmin = async (userId) => {
    const user = allUsers.find(u => u.id === userId);
    if(!user) return;
    const newAdminStatus = !user.isAdmin;
    await db.ref('users/'+userId).update({ isAdmin: newAdminStatus });
    showToast(newAdminStatus ? 'Пользователь назначен администратором' : 'Пользователь лишён прав администратора');
    showAdminPanel();
};

window.adminEditUser = async (userId) => {
    const user = allUsers.find(u => u.id === userId);
    if(!user) return;
    const newName = prompt('Новый никнейм:', user.name);
    if(newName && newName !== user.name) {
        const check = await db.ref('users').orderByChild('name').equalTo(newName).once('value');
        if(check.exists()) { showToast('Ник занят'); return; }
        await db.ref('users/'+userId).update({ name: newName });
    }
    const newPass = prompt('Новый пароль:', user.password);
    if(newPass && newPass !== user.password) await db.ref('users/'+userId).update({ password: newPass });
    const newBio = prompt('Новое описание:', user.bio || '');
    if(newBio !== undefined) await db.ref('users/'+userId).update({ bio: newBio });
    showToast('Данные пользователя обновлены');
    showAdminPanel();
};

window.adminClearUserMessages = async (userId) => {
    if(confirm('Очистить все сообщения этого пользователя во всех чатах?')) {
        const privatePath = `private_messages`;
        const privateSnap = await db.ref(privatePath).once('value');
        for(let chatId in privateSnap.val()) {
            if(chatId.includes(userId)) await db.ref(`${privatePath}/${chatId}`).remove();
        }
        const groupSnap = await db.ref('group_messages').once('value');
        for(let groupId in groupSnap.val()) {
            const msgs = groupSnap.val()[groupId];
            for(let msgId in msgs) if(msgs[msgId].userId === userId) await db.ref(`group_messages/${groupId}/${msgId}`).remove();
        }
        showToast('Сообщения пользователя удалены');
    }
};

window.adminDeleteUser = async (userId) => {
    if(confirm('Удалить пользователя навсегда?')) {
        await db.ref('users/'+userId).remove();
        await db.ref('users/'+currentUserId+'/contacts/'+userId).remove();
        const privatePath = `private_messages`;
        const privateSnap = await db.ref(privatePath).once('value');
        for(let chatId in privateSnap.val()) if(chatId.includes(userId)) await db.ref(`${privatePath}/${chatId}`).remove();
        const groupSnap = await db.ref('group_messages').once('value');
        for(let groupId in groupSnap.val()) {
            const msgs = groupSnap.val()[groupId];
            for(let msgId in msgs) if(msgs[msgId].userId === userId) await db.ref(`group_messages/${groupId}/${msgId}`).remove();
        }
        showToast('Пользователь удалён');
        showAdminPanel();
        await loadChats();
    }
};

// Регистрация
document.getElementById('registerBtn').onclick = () => {
    document.getElementById('registerModal').classList.add('visible');
};

document.getElementById('registerSubmitBtn').onclick = async () => {
    const name = document.getElementById('regName').value.trim();
    const pass = document.getElementById('regPassword').value.trim();
    const phone = document.getElementById('regPhone').value.trim();
    const birthday = document.getElementById('regBirthday').value;
    const bio = document.getElementById('regBio').value.trim();
    const file = document.getElementById('regAvatar').files[0];
    
    if(!name || !pass) {
        showToast('Заполните имя и пароль');
        return;
    }
    
    const check = await db.ref('users').orderByChild('name').equalTo(name).once('value');
    if(check.exists()) {
        showToast('Никнейм уже занят');
        return;
    }
    
    let avatar = '';
    if(file) {
        const reader = new FileReader();
        avatar = await new Promise((resolve) => {
            reader.onload = e => resolve(e.target.result);
            reader.readAsDataURL(file);
        });
    }
    
    const newUser = db.ref('users').push();
    await newUser.set({
        name, password: pass, avatarUrl: avatar, bio, phone, birthday,
        blocked: false, createdAt: Date.now(), online: true, isAdmin: false
    });
    
    showToast('Аккаунт создан! Теперь войдите');
    closeModal('registerModal');
    document.getElementById('loginName').value = name;
    document.getElementById('loginPassword').value = pass;
};

// Меню
document.getElementById('menuBtn').onclick = () => { document.getElementById('sideMenu').classList.add('open'); document.getElementById('menuOverlay').classList.add('visible'); };
document.getElementById('menuOverlay').onclick = () => { document.getElementById('sideMenu').classList.remove('open'); document.getElementById('menuOverlay').classList.remove('visible'); };
document.getElementById('profileMenuBtn').onclick = () => { showMyProfile(); document.getElementById('sideMenu').classList.remove('open'); document.getElementById('menuOverlay').classList.remove('visible'); };
document.getElementById('adminMenuItem').onclick = () => { if(isSuperAdmin) { showAdminPanel(); } else { showToast('Доступ только у суперадмина'); } document.getElementById('sideMenu').classList.remove('open'); document.getElementById('menuOverlay').classList.remove('visible'); };
document.getElementById('logoutMenuItem').onclick = () => { db.ref('users/'+currentUserId).update({ online: false }); localStorage.clear(); location.reload(); };
document.getElementById('fabBtn').onclick = () => {
    const modal = document.createElement('div');
    modal.className = 'modal-overlay visible';
    modal.innerHTML = `<div class="modal-content"><h3>Действия</h3><button id="createGroupOpt">👥 Создать группу</button><button id="createChannelOpt">📢 Создать канал</button><button id="addContactOpt">➕ Добавить контакт</button><button id="deleteChatsOpt">🗑️ Удалить выбранные чаты</button><button class="close-modal">Отмена</button></div>`;
    document.body.appendChild(modal);
    modal.querySelector('#createGroupOpt').onclick = () => { showCreateGroupModal(); modal.remove(); };
    modal.querySelector('#createChannelOpt').onclick = () => { showCreateChannelModal(); modal.remove(); };
    modal.querySelector('#addContactOpt').onclick = () => { showAddContactModal(); modal.remove(); };
    modal.querySelector('#deleteChatsOpt').onclick = () => { deleteSelectedChats(); modal.remove(); };
    modal.querySelector('.close-modal').onclick = () => modal.remove();
};
document.getElementById('chatInfoBtn').onclick = () => { if(currentChat.isGroup) showGroupInfo(currentChat.id); else if(currentChat.isChannel) showToast('Информация о канале'); else showUserProfile(currentChat.id); };

window.scrollToMessage = (id) => { 
    const el = document.getElementById(`msg-${id}`); 
    if(el) { el.scrollIntoView({behavior:'smooth', block:'center'}); el.classList.add('highlight-message'); setTimeout(()=>el.classList.remove('highlight-message'),1000); } 
};

window.showReadBy = async (id) => { 
    const msg = await db.ref(`${getChatPath()}/${id}`).once('value'); 
    const data = msg.val(); 
    if(!data?.readBy) return; 
    const users = Object.keys(data.readBy).filter(uid => uid !== currentUserId); 
    let html = ''; 
    for(let uid of users) { 
        const u = await db.ref('users/'+uid).once('value'); 
        if(u.exists()) html += `<div class="read-by-user"><img src="${getAvatarUrl(u.val().name, u.val().avatarUrl)}"><span>${escapeHtml(u.val().name)}</span></div>`; 
    } 
    document.getElementById('readByList').innerHTML = html || '<div style="padding:16px;text-align:center;">Никто не прочитал</div>'; 
    document.getElementById('readByModal').classList.add('visible'); 
};

// Авторизация
async function auth() {
    const name = document.getElementById('loginName').value.trim();
    const pass = document.getElementById('loginPassword').value.trim();
    if(!name || !pass) { document.getElementById('authError').innerText = 'Заполните поля'; return; }
    const btn = document.getElementById('authBtn');
    btn.disabled = true; btn.innerText = '⏳...';
    try {
        const snap = await db.ref('users').orderByChild('name').equalTo(name).once('value');
        if(snap.exists()) {
            let found = false;
            snap.forEach(c => { 
                if(c.val().password === pass) { 
                    currentUserId = c.key; 
                    currentUserName = c.val().name; 
                    currentUserAvatar = c.val().avatarUrl; 
                    currentUserBio = c.val().bio; 
                    currentUserPhone = c.val().phone; 
                    currentUserBirthday = c.val().birthday;
                    isSuperAdmin = currentUserName === 'DaniksGames';
                    isAdmin = c.val().isAdmin || isSuperAdmin;
                    found = true; 
                } 
            });
            if(!found) { document.getElementById('authError').innerText = 'Неверный пароль'; btn.disabled = false; btn.innerText = 'Войти'; return; }
        } else {
            showToast('Сначала зарегистрируйтесь');
            btn.disabled = false;
            btn.innerText = 'Войти';
            return;
        }
        if(isAdmin) document.getElementById('adminMenuItem').style.display = 'flex';
        await db.ref('users/'+currentUserId).update({ online: true, lastSeen: Date.now() });
        localStorage.setItem('userId', currentUserId);
        document.getElementById('authOverlay').style.display = 'none';
        document.getElementById('appContainer').style.display = 'flex';
        document.getElementById('menuName').innerText = currentUserName;
        document.getElementById('menuAvatar').src = getAvatarUrl(currentUserName, currentUserAvatar);
        loadSavedSettings();
        initToggles();
        await loadChats();
        setInterval(() => db.ref('users/'+currentUserId).update({ online: true, lastSeen: Date.now() }), 30000);
        setInterval(updateOnlineStatuses, 10000);
        if(notificationsEnabled && Notification.permission === 'default') Notification.requestPermission();
        
        // Скрываем экран загрузки
        const loadingScreen = document.getElementById('loadingScreen');
        loadingScreen.classList.add('hide');
        setTimeout(() => loadingScreen.style.display = 'none', 800);
    } catch(e) { document.getElementById('authError').innerText = 'Ошибка: ' + e.message; btn.disabled = false; btn.innerText = 'Войти'; }
}

document.getElementById('authBtn').onclick = auth;

// Инициализация
(async () => {
    // Показываем экран загрузки минимум 2 секунды
    await new Promise(resolve => setTimeout(resolve, 2000));
    
    const uid = localStorage.getItem('userId');
    if(uid) {
        const snap = await db.ref('users/'+uid).once('value');
        if(snap.exists() && !snap.val().blocked) {
            currentUserId = uid; currentUserName = snap.val().name; currentUserAvatar = snap.val().avatarUrl; currentUserBio = snap.val().bio; currentUserPhone = snap.val().phone; currentUserBirthday = snap.val().birthday;
            isSuperAdmin = currentUserName === 'DaniksGames';
            isAdmin = snap.val().isAdmin || isSuperAdmin;
            if(isAdmin) document.getElementById('adminMenuItem').style.display = 'flex';
            await db.ref('users/'+uid).update({ online: true });
            document.getElementById('authOverlay').style.display = 'none';
            document.getElementById('appContainer').style.display = 'flex';
            document.getElementById('menuName').innerText = currentUserName;
            document.getElementById('menuAvatar').src = getAvatarUrl(currentUserName, currentUserAvatar);
            loadSavedSettings();
            initToggles();
            await loadChats();
            setInterval(() => db.ref('users/'+uid).update({ online: true, lastSeen: Date.now() }), 30000);
            setInterval(updateOnlineStatuses, 10000);
            if(notificationsEnabled && Notification.permission === 'default') Notification.requestPermission();
            
            // Скрываем экран загрузки
            const loadingScreen = document.getElementById('loadingScreen');
            loadingScreen.classList.add('hide');
            setTimeout(() => loadingScreen.style.display = 'none', 800);
            return;
        }
    }
    // Если нет сохранённой сессии, показываем экран авторизации
    const loadingScreen = document.getElementById('loadingScreen');
    loadingScreen.classList.add('hide');
    setTimeout(() => {
        loadingScreen.style.display = 'none';
        document.getElementById('authOverlay').style.display = 'flex';
    }, 800);
})();
</script>
</body>
</html>
