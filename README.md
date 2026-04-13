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
            --bg-body: #f5f5f5;
            --chat-bg: #ffffff;
            --sidebar-bg: #ffffff;
            --header-bg: #ffffff;
            --header-text: #1a1a2e;
            --message-area-bg: #f8f9fc;
            --my-bubble: #4a6cf7;
            --my-text: #ffffff;
            --other-bubble: #e9ecef;
            --other-text: #1a1a2e;
            --system-bubble: #e9ecef;
            --system-text: #555555;
            --input-bg: #ffffff;
            --input-border: #e2e8f0;
            --icon-color: #4a6cf7;
            --read-color: #10b981;
            --online-color: #10b981;
            --offline-color: #94a3b8;
            --contact-hover: #f1f5f9;
            --active-chat: #eef2ff;
            --selected-message: rgba(74, 108, 247, 0.25);
            --unread-badge: #ef4444;
            --shadow: 0 2px 8px rgba(0,0,0,0.05);
            --safe-area-bottom: env(safe-area-inset-bottom, 0px);
            --transition-smooth: cubic-bezier(0.34, 1.2, 0.64, 1);
        }
        
        body.dark {
            --bg-body: #0f0f1a;
            --chat-bg: #1a1a2e;
            --sidebar-bg: #1a1a2e;
            --header-bg: #1a1a2e;
            --header-text: #e2e8f0;
            --message-area-bg: #16162a;
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
            --selected-message: rgba(96, 165, 250, 0.3);
            --shadow: 0 2px 8px rgba(0,0,0,0.2);
        }
        
        body { background: var(--bg-body); min-height: 100vh; transition: background 0.3s var(--transition-smooth); }
        
        /* Анимации загрузки */
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
        @keyframes dots { 0%, 20% { content: "."; } 40% { content: ".."; } 60%, 100% { content: "..."; } }
        @keyframes floatIn { 0% { opacity: 0; transform: translateY(30px) scale(0.95); filter: blur(8px); } 100% { opacity: 1; transform: translateY(0) scale(1); filter: blur(0); } }
        @keyframes floatOut { 0% { opacity: 1; transform: translateY(0) scale(1); filter: blur(0); } 100% { opacity: 0; transform: translateY(-30px) scale(0.95); filter: blur(8px); } }
        @keyframes slideIn3D { 0% { opacity: 0; transform: translateX(100%) rotateY(-30deg); } 100% { opacity: 1; transform: translateX(0) rotateY(0); } }
        @keyframes slideOut3D { 0% { opacity: 1; transform: translateX(0) rotateY(0); } 100% { opacity: 0; transform: translateX(100%) rotateY(-30deg); } }
        @keyframes morphIn { 0% { opacity: 0; border-radius: 60px; transform: scale(0.8); background: var(--icon-color); } 100% { opacity: 1; border-radius: 28px; transform: scale(1); background: var(--chat-bg); } }
        @keyframes morphOut { 0% { opacity: 1; border-radius: 28px; transform: scale(1); } 100% { opacity: 0; border-radius: 60px; transform: scale(0.8); } }
        @keyframes messageAppear { 0% { opacity: 0; transform: translateX(-20px) scale(0.9); filter: blur(4px); } 100% { opacity: 1; transform: translateX(0) scale(1); filter: blur(0); } }
        @keyframes messageAppearMy { 0% { opacity: 0; transform: translateX(20px) scale(0.9); filter: blur(4px); } 100% { opacity: 1; transform: translateX(0) scale(1); filter: blur(0); } }
        @keyframes glowPulse { 0% { box-shadow: 0 0 0 0 rgba(74, 108, 247, 0.4); } 70% { box-shadow: 0 0 0 10px rgba(74, 108, 247, 0); } 100% { box-shadow: 0 0 0 0 rgba(74, 108, 247, 0); } }
        @keyframes wave { 0%, 100% { transform: scaleY(1); } 50% { transform: scaleY(1.5); } }
        @keyframes shimmer { 0% { background-position: -1000px 0; } 100% { background-position: 1000px 0; } }
        
        .loading-spinner { width: 40px; height: 40px; border: 3px solid var(--input-border); border-top-color: var(--icon-color); border-radius: 50%; animation: spin 0.8s linear infinite; margin: 20px auto; }
        .loading-dots { display: inline-flex; gap: 4px; }
        .loading-dots span { width: 8px; height: 8px; background: var(--system-text); border-radius: 50%; animation: wave 0.8s ease-in-out infinite; }
        .loading-dots span:nth-child(2) { animation-delay: 0.1s; }
        .loading-dots span:nth-child(3) { animation-delay: 0.2s; }
        .skeleton { background: linear-gradient(90deg, var(--input-border) 25%, var(--contact-hover) 50%, var(--input-border) 75%); background-size: 1000px 100%; animation: shimmer 1.5s infinite; border-radius: 12px; }
        
        .app-container { display: flex; flex-direction: column; height: 100vh; max-width: 600px; margin: 0 auto; background: var(--chat-bg); position: relative; overflow: hidden; transition: background 0.3s var(--transition-smooth); }
        
        .app-header { background: var(--header-bg); padding: 12px 16px; display: flex; align-items: center; justify-content: space-between; border-bottom: 1px solid var(--input-border); transition: all 0.3s var(--transition-smooth); }
        .app-header h1 { font-size: 1.4rem; color: var(--header-text); transition: color 0.3s; }
        .menu-btn { background: none; border: none; font-size: 1.5rem; color: var(--icon-color); cursor: pointer; padding: 8px; border-radius: 50%; width: 44px; height: 44px; transition: all 0.2s var(--transition-smooth); }
        .menu-btn:active { transform: scale(0.9) rotate(90deg); background: var(--contact-hover); }
        
        .side-menu { position: fixed; top: 0; left: -280px; width: 280px; height: 100vh; background: var(--sidebar-bg); z-index: 200; transition: left 0.4s cubic-bezier(0.34, 1.2, 0.64, 1); box-shadow: 2px 0 20px rgba(0,0,0,0.15); backdrop-filter: blur(10px); }
        .side-menu.open { left: 0; }
        .side-menu-header { padding: 20px; border-bottom: 1px solid var(--input-border); display: flex; align-items: center; gap: 12px; cursor: pointer; transition: all 0.2s; }
        .side-menu-header:active { background: var(--contact-hover); transform: scale(0.98); }
        .side-menu-header img { width: 50px; height: 50px; border-radius: 50%; object-fit: cover; transition: transform 0.3s; }
        .side-menu-header:active img { transform: scale(0.95); }
        .side-menu-header-info h3 { color: var(--other-text); font-size: 1rem; }
        .side-menu-header-info p { color: var(--system-text); font-size: 0.8rem; }
        .side-menu-item { display: flex; align-items: center; gap: 16px; padding: 14px 20px; cursor: pointer; color: var(--other-text); border-bottom: 1px solid var(--input-border); transition: all 0.2s; }
        .side-menu-item:active { background: var(--contact-hover); transform: translateX(10px); }
        .side-menu-item i { width: 24px; font-size: 1.2rem; color: var(--icon-color); transition: transform 0.2s; }
        .side-menu-item:active i { transform: scale(1.2); }
        .menu-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.5); z-index: 199; display: none; backdrop-filter: blur(4px); transition: all 0.3s; }
        .menu-overlay.visible { display: block; animation: fadeIn 0.3s; }
        
        .chats-list { flex: 1; overflow-y: auto; background: var(--chat-bg); }
        .chat-item { display: flex; align-items: center; gap: 12px; padding: 12px 16px; cursor: pointer; border-bottom: 1px solid var(--input-border); transition: all 0.2s var(--transition-smooth); animation: floatIn 0.3s ease-out; }
        .chat-item:active { background: var(--contact-hover); transform: scale(0.98); }
        .chat-avatar { width: 52px; height: 52px; border-radius: 50%; object-fit: cover; position: relative; transition: transform 0.2s; }
        .chat-item:active .chat-avatar { transform: scale(0.95); }
        .online-dot { position: absolute; bottom: 2px; right: 2px; width: 12px; height: 12px; border-radius: 50%; border: 2px solid var(--chat-bg); transition: all 0.3s; }
        .online-dot.online { background: var(--online-color); animation: glowPulse 2s infinite; }
        .chat-info { flex: 1; min-width: 0; }
        .chat-name { font-weight: 600; color: var(--other-text); display: flex; align-items: center; gap: 6px; flex-wrap: wrap; }
        .chat-preview { font-size: 0.8rem; color: var(--system-text); white-space: nowrap; overflow: hidden; text-overflow: ellipsis; margin-top: 4px; }
        .chat-meta { text-align: right; }
        .chat-time { font-size: 0.7rem; color: var(--system-text); }
        .unread-count { background: var(--unread-badge); color: white; border-radius: 50%; min-width: 20px; height: 20px; padding: 0 6px; font-size: 0.7rem; font-weight: bold; display: inline-flex; align-items: center; justify-content: center; margin-top: 4px; animation: glowPulse 1s infinite; }
        
        .chat-screen { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: var(--message-area-bg); z-index: 100; display: flex; flex-direction: column; transition: transform 0.5s cubic-bezier(0.34, 1.2, 0.64, 1); }
        .chat-screen.open { animation: slideIn3D 0.5s forwards; }
        .chat-screen:not(.open) { animation: slideOut3D 0.5s forwards; }
        .chat-header { background: var(--header-bg); padding: 12px 16px; display: flex; align-items: center; gap: 12px; border-bottom: 1px solid var(--input-border); }
        .back-btn { background: none; border: none; font-size: 1.3rem; color: var(--icon-color); cursor: pointer; padding: 8px; border-radius: 50%; width: 40px; height: 40px; transition: all 0.2s; }
        .back-btn:active { transform: translateX(-5px) scale(0.9); }
        .chat-header-avatar { width: 44px; height: 44px; border-radius: 50%; object-fit: cover; cursor: pointer; transition: all 0.2s; }
        .chat-header-avatar:active { transform: scale(0.95); }
        .chat-header-info { flex: 1; cursor: pointer; }
        .chat-header-name { font-weight: 600; color: var(--other-text); }
        .chat-header-status { font-size: 0.7rem; color: var(--system-text); }
        .chat-action-btn { background: none; border: none; font-size: 1.2rem; color: var(--icon-color); cursor: pointer; padding: 8px; border-radius: 50%; width: 40px; height: 40px; transition: all 0.2s; }
        .chat-action-btn:active { transform: rotate(90deg) scale(0.9); }
        
        .messages-area { flex: 1; overflow-y: auto; padding: 16px; display: flex; flex-direction: column; gap: 8px; }
        .message { display: flex; max-width: 85%; padding: 4px; border-radius: 12px; position: relative; transition: all 0.2s; cursor: pointer; user-select: none; }
        .message.selected { background: var(--selected-message); outline: 2px solid var(--icon-color); outline-offset: 2px; animation: shake 0.3s; }
        .my-message { align-self: flex-end; justify-content: flex-end; animation: messageAppearMy 0.3s ease-out; }
        .other-message { align-self: flex-start; animation: messageAppear 0.3s ease-out; }
        .system-message { align-self: center; max-width: 90%; animation: fadeIn 0.3s; }
        .bubble { padding: 10px 14px; border-radius: 20px; background: var(--other-bubble); color: var(--other-text); word-break: break-word; position: relative; }
        .my-message .bubble { background: var(--my-bubble); color: var(--my-text); border-bottom-right-radius: 4px; }
        .other-message .bubble { border-bottom-left-radius: 4px; }
        .system-message .bubble { background: var(--system-bubble); color: var(--system-text); text-align: center; font-size: 0.8rem; }
        .message-header { display: flex; align-items: center; gap: 8px; margin-bottom: 5px; font-size: 0.7rem; }
        .msg-avatar { width: 20px; height: 20px; border-radius: 50%; object-fit: cover; cursor: pointer; transition: all 0.2s; }
        .msg-avatar:active { transform: scale(1.2); }
        .message-name { font-weight: 600; cursor: pointer; color: inherit; }
        .reply-preview { font-size: 0.7rem; background: rgba(0,0,0,0.08); padding: 6px 10px; border-radius: 12px; margin-bottom: 6px; border-left: 3px solid var(--icon-color); cursor: pointer; transition: all 0.2s; }
        .reply-preview:active { transform: scale(0.98); background: rgba(0,0,0,0.12); }
        .media-content { max-width: 200px; max-height: 200px; border-radius: 12px; margin-top: 6px; cursor: pointer; transition: transform 0.2s; }
        .media-content:active { transform: scale(1.02); }
        .circle-video { width: 150px; height: 150px; border-radius: 50%; overflow: hidden; margin-top: 6px; background: #000; }
        .circle-video video { width: 100%; height: 100%; object-fit: cover; }
        audio { max-width: 180px; height: 36px; margin-top: 6px; border-radius: 20px; }
        .file-attachment { display: flex; align-items: center; gap: 10px; padding: 8px 12px; background: rgba(0,0,0,0.05); border-radius: 12px; margin-top: 6px; cursor: pointer; transition: all 0.2s; }
        .file-attachment:active { transform: scale(0.98); background: rgba(0,0,0,0.1); }
        .contact-card { display: flex; align-items: center; gap: 12px; padding: 8px 12px; background: var(--input-bg); border-radius: 16px; margin-top: 8px; cursor: pointer; border: 1px solid var(--input-border); transition: all 0.2s; }
        .contact-card:active { transform: scale(0.98); }
        .message-time { font-size: 0.55rem; opacity: 0.7; margin-top: 4px; display: flex; align-items: center; gap: 4px; }
        .read-status { font-size: 0.55rem; }
        .read-status.read { color: var(--read-color); }
        .read-stats { font-size: 0.6rem; cursor: pointer; margin-left: 8px; color: var(--read-color); transition: all 0.2s; }
        .read-stats:active { transform: scale(1.1); }
        
        .selection-bar { position: fixed; bottom: 80px; left: 16px; right: 16px; background: var(--header-bg); color: white; padding: 12px 20px; border-radius: 40px; display: none; align-items: center; justify-content: space-between; z-index: 150; box-shadow: 0 4px 20px rgba(0,0,0,0.3); animation: morphIn 0.3s forwards; }
        .selection-bar.visible { display: flex; }
        .selection-bar.closing { animation: morphOut 0.3s forwards; }
        .selection-actions { display: flex; gap: 16px; }
        .selection-actions button { background: none; border: none; color: white; font-size: 1.2rem; cursor: pointer; padding: 8px; border-radius: 50%; transition: all 0.2s; }
        .selection-actions button:active { transform: scale(0.85) rotate(360deg); background: rgba(255,255,255,0.2); }
        
        .input-area { padding: 12px 16px; background: var(--input-bg); border-top: 1px solid var(--input-border); padding-bottom: calc(12px + var(--safe-area-bottom)); position: relative; transition: all 0.3s; }
        .reply-indicator { background: var(--input-bg); padding: 8px 12px; margin-bottom: 8px; border-radius: 12px; display: flex; justify-content: space-between; align-items: center; font-size: 0.8rem; border-left: 3px solid var(--icon-color); animation: slideIn3D 0.2s; }
        .input-row { display: flex; gap: 8px; align-items: flex-end; }
        .message-input { flex: 1; padding: 12px 16px; border-radius: 25px; border: 1px solid var(--input-border); background: var(--input-bg); color: var(--other-text); outline: none; resize: none; font-size: 16px; max-height: 120px; min-height: 44px; transition: all 0.2s; }
        .message-input:focus { border-color: var(--icon-color); box-shadow: 0 0 0 2px rgba(74,108,247,0.2); }
        .attach-btn, .voice-btn, .circle-btn { background: none; border: none; font-size: 1.3rem; color: var(--icon-color); cursor: pointer; width: 44px; height: 44px; border-radius: 50%; transition: all 0.2s; }
        .attach-btn:active, .voice-btn:active, .circle-btn:active { transform: scale(0.85) rotate(15deg); background: var(--contact-hover); }
        .send-btn { background: var(--icon-color); color: white; border: none; width: 44px; height: 44px; border-radius: 50%; cursor: pointer; font-size: 1.2rem; transition: all 0.2s; }
        .send-btn:active { transform: scale(0.85); animation: glowPulse 0.5s; }
        
        .attach-menu { position: absolute; bottom: 70px; left: 16px; background: var(--input-bg); border-radius: 20px; padding: 8px; display: none; grid-template-columns: repeat(3, 1fr); gap: 8px; box-shadow: 0 4px 20px rgba(0,0,0,0.2); z-index: 160; animation: morphIn 0.2s forwards; transform-origin: bottom left; }
        .attach-menu.visible { display: grid; }
        .attach-menu.closing { animation: morphOut 0.2s forwards; }
        .attach-menu-btn { background: none; border: none; padding: 12px; border-radius: 12px; display: flex; flex-direction: column; align-items: center; gap: 4px; color: var(--other-text); font-size: 0.7rem; cursor: pointer; transition: all 0.2s; }
        .attach-menu-btn i { font-size: 1.3rem; color: var(--icon-color); transition: all 0.2s; }
        .attach-menu-btn:active { transform: scale(0.9); background: var(--contact-hover); }
        .attach-menu-btn:active i { transform: rotate(360deg); }
        
        .fab { position: fixed; bottom: 80px; right: 16px; width: 56px; height: 56px; background: var(--icon-color); border-radius: 50%; display: flex; align-items: center; justify-content: center; color: white; font-size: 1.5rem; cursor: pointer; box-shadow: 0 4px 12px rgba(0,0,0,0.2); z-index: 90; border: none; transition: all 0.3s cubic-bezier(0.34, 1.2, 0.64, 1); animation: glowPulse 2s infinite; }
        .fab:active { transform: scale(0.85) rotate(90deg); }
        
        .modal-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.8); backdrop-filter: blur(8px); z-index: 300; display: none; align-items: center; justify-content: center; }
        .modal-overlay.visible { display: flex; animation: fadeIn 0.3s; }
        .modal-overlay.closing { animation: fadeOut 0.3s forwards; }
        .modal-content { background: var(--chat-bg); border-radius: 28px; padding: 24px; width: 90%; max-width: 350px; max-height: 80vh; overflow-y: auto; animation: morphIn 0.3s; }
        .modal-content.closing { animation: morphOut 0.3s forwards; }
        .modal-content h3 { color: var(--other-text); margin-bottom: 16px; text-align: center; }
        .modal-content input, .modal-content textarea { width: 100%; padding: 12px; margin: 8px 0; border-radius: 20px; border: 1px solid var(--input-border); background: var(--input-bg); color: var(--other-text); font-size: 1rem; transition: all 0.2s; }
        .modal-content input:focus, .modal-content textarea:focus { border-color: var(--icon-color); box-shadow: 0 0 0 2px rgba(74,108,247,0.2); outline: none; }
        .modal-content button { background: var(--icon-color); color: white; border: none; padding: 12px; border-radius: 30px; width: 100%; margin-top: 12px; cursor: pointer; transition: all 0.2s; }
        .modal-content button:active { transform: scale(0.98); }
        .close-modal { background: #94a3b8 !important; margin-top: 8px !important; }
        
        .recording-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.95); backdrop-filter: blur(20px); z-index: 400; display: none; flex-direction: column; align-items: center; justify-content: center; }
        .recording-overlay.visible { display: flex; animation: fadeIn 0.3s; }
        .recording-preview { width: 250px; height: 250px; border-radius: 50%; overflow: hidden; background: #000; margin-bottom: 30px; box-shadow: 0 0 30px rgba(74,108,247,0.5); animation: glowPulse 2s infinite; }
        .recording-preview video { width: 100%; height: 100%; object-fit: cover; }
        .recording-wave { width: 80%; height: 100px; display: flex; align-items: center; justify-content: center; gap: 4px; margin-bottom: 30px; }
        .wave-bar { width: 4px; background: var(--icon-color); border-radius: 2px; transition: height 0.05s; animation: wave 0.5s ease-in-out infinite; animation-delay: calc(var(--i) * 0.05s); }
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
        
        .avatar-preview { width: 100px; height: 100px; border-radius: 50%; object-fit: cover; margin: 0 auto 16px; display: block; transition: transform 0.2s; }
        .avatar-preview:active { transform: scale(0.95); }
        .contact-list-item { display: flex; align-items: center; gap: 12px; padding: 12px; cursor: pointer; border-radius: 12px; transition: all 0.2s; }
        .contact-list-item:active { background: var(--contact-hover); transform: translateX(5px); }
        .contact-list-item img { width: 44px; height: 44px; border-radius: 50%; transition: transform 0.2s; }
        .contact-list-item:active img { transform: scale(0.95); }
        .contact-list-item-name { font-weight: 500; color: var(--other-text); }
        
        .member-item { display: flex; align-items: center; gap: 12px; padding: 10px; cursor: pointer; border-radius: 12px; transition: all 0.2s; }
        .member-item:active { background: var(--contact-hover); transform: scale(0.98); }
        .member-item img { width: 40px; height: 40px; border-radius: 50%; }
        .member-item span { color: var(--other-text); }
        .group-member-item { display: flex; align-items: center; gap: 12px; padding: 10px; border-bottom: 1px solid var(--input-border); transition: all 0.2s; }
        .kick-btn { background: #ef4444; border: none; color: white; padding: 6px 12px; border-radius: 20px; cursor: pointer; transition: all 0.2s; }
        .kick-btn:active { transform: scale(0.95); }
        .read-by-user { display: flex; align-items: center; gap: 12px; padding: 10px; border-bottom: 1px solid var(--input-border); }
        .read-by-user img { width: 36px; height: 36px; border-radius: 50%; }
        .read-by-user span { color: var(--other-text); }
        .admin-user-card { background: var(--input-bg); border-radius: 16px; padding: 12px; margin-bottom: 12px; transition: all 0.2s; animation: floatIn 0.3s; }
        .admin-user-card:active { transform: scale(0.98); }
        .admin-user-name { font-weight: bold; margin-bottom: 8px; color: var(--other-text); }
        .admin-user-detail { font-size: 0.8rem; opacity: 0.8; margin-bottom: 4px; color: var(--system-text); }
        .admin-user-actions { display: flex; flex-wrap: wrap; gap: 8px; margin-top: 8px; }
        .admin-user-actions button { padding: 6px 12px; border-radius: 20px; border: none; cursor: pointer; font-size: 0.7rem; transition: all 0.2s; color: white; }
        .admin-user-actions button:active { transform: scale(0.95); }
        
        .toast { position: fixed; bottom: 100px; left: 50%; transform: translateX(-50%); background: rgba(0,0,0,0.9); color: white; padding: 12px 24px; border-radius: 40px; font-size: 0.9rem; z-index: 500; display: none; backdrop-filter: blur(10px); animation: floatIn 0.2s; }
        
        .swipe-hint { position: absolute; left: -30px; top: 50%; transform: translateY(-50%); font-size: 1rem; opacity: 0; transition: opacity 0.2s; color: var(--icon-color); }
        .other-message:hover .swipe-hint { opacity: 0.5; }
        
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        @keyframes fadeOut { from { opacity: 1; } to { opacity: 0; } }
        @keyframes shake { 0%, 100% { transform: translateX(0); } 25% { transform: translateX(-3px); } 75% { transform: translateX(3px); } }
        
        .highlight-message { animation: shake 0.3s; background: var(--selected-message); }
    </style>
</head>
<body>

<div class="app-container" id="appContainer">
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
        <div class="side-menu-item" id="themeMenuItem"><i class="fas fa-moon"></i><span>Тема</span></div>
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
    
    <div id="profileModal" class="modal-overlay">
        <div class="modal-content">
            <h3>Настройки профиля</h3>
            <img id="profileAvatar" class="avatar-preview">
            <input type="file" id="avatarInput" accept="image/*">
            <input type="text" id="profileName" placeholder="Никнейм">
            <textarea id="profileBio" placeholder="О себе" rows="3"></textarea>
            <button id="saveProfileBtn">Сохранить</button>
            <button class="close-modal" onclick="closeModal('profileModal')">Отмена</button>
        </div>
    </div>
    
    <div id="viewProfileModal" class="modal-overlay">
        <div class="modal-content">
            <img id="viewProfileAvatar" class="avatar-preview">
            <h3 id="viewProfileName"></h3>
            <p id="viewProfileBio" style="color: var(--system-text); margin-bottom: 20px;"></p>
            <button id="viewProfileSendMsg" class="close-modal" style="background: var(--icon-color);">💬 Написать</button>
            <button class="close-modal" onclick="closeModal('viewProfileModal')">Закрыть</button>
        </div>
    </div>
    
    <div id="createGroupModal" class="modal-overlay">
        <div class="modal-content">
            <h3>Создать группу</h3>
            <input type="text" id="groupName" placeholder="Название группы">
            <textarea id="groupDesc" placeholder="Описание"></textarea>
            <div id="membersList"></div>
            <button id="createGroupBtn">Создать</button>
            <button class="close-modal" onclick="closeModal('createGroupModal')">Отмена</button>
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
            <h3 id="groupInfoTitle"></h3>
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
let currentUserId = null, currentUserName = null, currentUserAvatar = null, currentUserBio = null;
let currentChat = { id: null, name: null, isGroup: false };
let chats = [];
let groups = [];
let unreadCounts = new Map();
let mutedChats = new Set();
let replyingTo = null;
let selectedMessages = new Set();
let allUsers = [];
let contactsList = [];
let isAdmin = false;
let processedMsgIds = new Map();
let selectionMode = false;

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

// Вспомогательные функции
function showToast(text) { 
    const t = document.getElementById('toast'); 
    t.innerText = text; 
    t.style.display = 'block'; 
    setTimeout(() => {
        t.style.animation = 'fadeOut 0.2s';
        setTimeout(() => {
            t.style.display = 'none';
            t.style.animation = '';
        }, 200);
    }, 2000); 
}

function escapeHtml(s) { 
    if(!s) return ''; 
    return String(s).replace(/[&<>]/g, m => ({'&':'&amp;','<':'&lt;','>':'&gt;'}[m])); 
}

function getAvatarUrl(name, avatar) { 
    return avatar || `https://ui-avatars.com/api/?background=4a6cf7&color=fff&name=${encodeURIComponent(name)}`; 
}

function closeModal(id) { 
    const modal = document.getElementById(id);
    modal.classList.add('closing');
    setTimeout(() => {
        modal.classList.remove('visible', 'closing');
    }, 300);
}

function playSound() { 
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

function showLoading(element) {
    element.innerHTML = '<div class="loading-spinner"></div>';
}

// Загрузка данных
async function loadAllUsers() {
    const snap = await db.ref('users').once('value');
    allUsers = [];
    for(let id in snap.val()) {
        const u = snap.val()[id];
        if(id !== currentUserId && !u.blocked) {
            allUsers.push({id, name: u.name, avatar: u.avatarUrl || '', password: u.password, bio: u.bio, blocked: u.blocked});
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
            groups.push({id, name: g.name, description: g.description, creator: g.creator, members: g.members});
        }
    }
}

async function loadChats() {
    await loadGroups();
    const contactsSnap = await db.ref('users/' + currentUserId + '/contacts').once('value');
    const contacts = contactsSnap.val() || {};
    chats = [];
    for(let id in contacts) {
        const u = await db.ref('users/' + id).once('value');
        if(u.exists() && !u.val().blocked) {
            chats.push({id, name: u.val().name, avatar: u.val().avatarUrl, isGroup: false, lastMessage: '', lastTime: 0});
        }
    }
    for(let g of groups) {
        chats.push({id: g.id, name: g.name, avatar: '', isGroup: true, lastMessage: '', lastTime: 0, creator: g.creator});
    }
    renderChats();
}

function renderChats() {
    const container = document.getElementById('chatsList');
    if(!container) return;
    container.innerHTML = chats.map(c => `
        <div class="chat-item" onclick="openChat('${c.id}', '${escapeHtml(c.name).replace(/'/g,"\\'")}', ${c.isGroup})">
            <div style="position:relative;">
                <img class="chat-avatar" src="${getAvatarUrl(c.name, c.avatar)}">
                ${!c.isGroup ? `<div class="online-dot offline" id="online-${c.id}"></div>` : ''}
            </div>
            <div class="chat-info">
                <div class="chat-name">${escapeHtml(c.name)}${mutedChats.has(c.id) ? ' <i class="fas fa-volume-mute" style="font-size:0.7rem;"></i>' : ''}</div>
                <div class="chat-preview">${c.lastMessage || 'Нет сообщений'}</div>
            </div>
            <div class="chat-meta">
                <div class="chat-time">${c.lastTime ? new Date(c.lastTime).toLocaleTimeString([],{hour:'2-digit',minute:'2-digit'}) : ''}</div>
                ${unreadCounts.get(c.id) > 0 ? `<div class="unread-count">${unreadCounts.get(c.id)}</div>` : ''}
            </div>
        </div>
    `).join('');
    updateOnlineStatuses();
}

async function updateOnlineStatuses() {
    for(let c of chats) {
        if(!c.isGroup) {
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
    const ids = [currentUserId, currentChat.id].sort();
    return `private_messages/${ids[0]}_${ids[1]}`;
}

window.openChat = async function(id, name, isGroup) {
    currentChat = { id, name, isGroup };
    document.getElementById('chatName').innerText = name;
    document.getElementById('chatAvatar').src = getAvatarUrl(name, chats.find(c=>c.id===id)?.avatar);
    document.getElementById('chatStatus').innerHTML = isGroup ? '👥 Группа' : 'Загрузка...';
    if(!isGroup) {
        const u = await db.ref('users/' + id).once('value');
        if(u.exists()) document.getElementById('chatStatus').innerHTML = u.val().online ? '🟢 В сети' : '⚫ Не в сети';
    }
    unreadCounts.set(id, 0);
    renderChats();
    replyingTo = null;
    document.getElementById('replyIndicator').style.display = 'none';
    clearSelection();
    selectionMode = false;
    document.getElementById('selectionBar').classList.remove('visible');
    loadMessages();
    document.getElementById('chatScreen').classList.add('open');
};

function loadMessages() {
    const area = document.getElementById('messagesArea');
    area.innerHTML = '<div class="loading-spinner"></div>';
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
            if(area.innerHTML === '<div class="loading-spinner"></div>') area.innerHTML = '';
            renderMessage(snap.key, snap.val(), path);
        }
    });
    setTimeout(() => {
        if(!hasMessages && area.innerHTML === '<div class="loading-spinner"></div>') {
            area.innerHTML = '<div class="system-message"><div class="bubble">💬 Нет сообщений. Напишите первое!</div></div>';
        }
    }, 1500);
}

function renderMessage(msgId, msg, path) {
    if(document.getElementById(`msg-${msgId}`)) return;
    const isMe = msg.userId === currentUserId;
    const isSystem = msg.isSystem;
    
    const div = document.createElement('div');
    div.className = `message ${isSystem ? 'system-message' : (isMe ? 'my-message' : 'other-message')}`;
    div.id = `msg-${msgId}`;
    div.dataset.msgId = msgId;
    div.dataset.userId = msg.userId;
    div.dataset.isMe = isMe;
    
    // Обработка свайпа для ответа
    let touchStartX = 0, touchStartY = 0;
    div.addEventListener('touchstart', (e) => {
        touchStartX = e.touches[0].clientX;
        touchStartY = e.touches[0].clientY;
    });
    div.addEventListener('touchend', (e) => {
        const touchEndX = e.changedTouches[0].clientX;
        const touchEndY = e.changedTouches[0].clientY;
        const diffX = touchEndX - touchStartX;
        const diffY = Math.abs(touchEndY - touchStartY);
        if(diffX > 60 && diffY < 30 && !isMe && !isSystem && !selectionMode) {
            replyingTo = { messageId: msgId, userName: msg.name, text: (msg.text || 'Медиа').substring(0,50) };
            document.getElementById('replyIndicator').style.display = 'flex';
            document.getElementById('replyToName').innerText = msg.name;
            document.getElementById('replyToText').innerText = (msg.text || 'Медиа').substring(0,50);
            showToast('👆 Сообщение отмечено для ответа');
        }
    });
    
    // Длинное нажатие для выделения
    let pressTimer;
    div.addEventListener('touchstart', (e) => {
        if(selectionMode) {
            toggleMessageSelection(msgId);
            e.preventDefault();
        } else {
            pressTimer = setTimeout(() => {
                selectionMode = true;
                toggleMessageSelection(msgId);
                showToast('Режим выделения активирован');
            }, 500);
        }
    });
    div.addEventListener('touchend', () => clearTimeout(pressTimer));
    div.addEventListener('touchmove', () => clearTimeout(pressTimer));
    
    let replyHtml = '', mediaHtml = '';
    if(msg.replyTo) replyHtml = `<div class="reply-preview" onclick="scrollToMessage('${msg.replyTo.messageId}')"><i class="fas fa-reply"></i> <strong>${escapeHtml(msg.replyTo.userName)}</strong>: ${escapeHtml(msg.replyTo.text)}</div>`;
    
    if(msg.mediaType === 'image') mediaHtml = `<img src="${msg.mediaUrl}" class="media-content" onclick="window.open('${msg.mediaUrl}')">`;
    else if(msg.mediaType === 'video') {
        if(msg.isCircle) mediaHtml = `<div class="circle-video"><video controls><source src="${msg.mediaUrl}"></video></div>`;
        else mediaHtml = `<video controls class="media-content"><source src="${msg.mediaUrl}"></video>`;
    }
    else if(msg.mediaType === 'audio') mediaHtml = `<audio controls src="${msg.mediaUrl}"></audio>`;
    else if(msg.mediaType === 'file') mediaHtml = `<div class="file-attachment" onclick="window.open('${msg.mediaUrl}')"><i class="fas fa-file"></i><div><div>${escapeHtml(msg.fileName)}</div><div>${formatFileSize(msg.fileSize)}</div></div></div>`;
    else if(msg.mediaType === 'contact' && msg.contactData) mediaHtml = `<div class="contact-card" onclick="showUserProfile('${msg.contactData.userId}')"><img src="${getAvatarUrl(msg.contactData.name, msg.contactData.avatar)}"><div><div>${escapeHtml(msg.contactData.name)}</div><div style="font-size:0.7rem;">Контакт</div></div></div>`;
    
    const readStats = (isMe && currentChat.isGroup && msg.readBy) ? `<span class="read-stats" onclick="showReadBy('${msgId}')">👁 ${Object.keys(msg.readBy).length - 1}</span>` : '';
    const readStatus = (isMe && !currentChat.isGroup) ? `<span class="read-status ${msg.read ? 'read' : ''}">${msg.read ? '✓✓' : '✓'}</span>` : '';
    const swipeHint = (!isMe && !isSystem) ? `<div class="swipe-hint"><i class="fas fa-arrow-right"></i></div>` : '';
    
    div.innerHTML = `<div class="bubble">${swipeHint}<div class="message-header"><img class="msg-avatar" src="${getAvatarUrl(msg.name, msg.avatar)}" onclick="showUserProfile('${msg.userId}')"><span class="message-name" onclick="showUserProfile('${msg.userId}')">${escapeHtml(msg.name)}</span></div>${replyHtml}${mediaHtml}${msg.text && !msg.mediaType ? `<div>${escapeHtml(msg.text)}</div>` : ''}<div class="message-time">${new Date(msg.time).toLocaleTimeString([],{hour:'2-digit',minute:'2-digit'})} ${readStatus} ${readStats}</div></div>`;
    
    const area = document.getElementById('messagesArea');
    area.appendChild(div);
    area.scrollTop = area.scrollHeight;
    
    if(!isMe && !msg.read && !currentChat.isGroup && !isSystem) db.ref(`${path}/${msgId}`).update({ read: true, [`readBy/${currentUserId}`]: true });
    else if(!isMe && currentChat.isGroup && !isSystem && !msg.readBy?.[currentUserId]) db.ref(`${path}/${msgId}/readBy/${currentUserId}`).set(true);
    if(!isMe && !mutedChats.has(currentChat.id)) playSound();
    if(!isMe && currentChat.id !== msg.userId && !isSystem) {
        unreadCounts.set(currentChat.id, (unreadCounts.get(currentChat.id)||0)+1);
        renderChats();
    }
}

function formatFileSize(bytes) { 
    if(bytes<1024) return bytes+' B'; 
    if(bytes<1048576) return (bytes/1024).toFixed(1)+' KB'; 
    return (bytes/1048576).toFixed(1)+' MB'; 
}

async function sendMessage(text, mediaType, mediaUrl, fileData, contactData, isCircle) {
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
    if(selectedMessages.size === 0) selectionMode = false;
}

function updateSelectionBar() {
    const bar = document.getElementById('selectionBar');
    const count = selectedMessages.size;
    if(count > 0) {
        bar.classList.add('visible');
        document.getElementById('selectedCount').innerHTML = `${count} <i class="fas fa-check-circle"></i>`;
    } else {
        bar.classList.add('closing');
        setTimeout(() => bar.classList.remove('visible', 'closing'), 300);
        selectionMode = false;
    }
}

function clearSelection() {
    selectedMessages.forEach(id => {
        const el = document.getElementById(`msg-${id}`);
        if(el) el.classList.remove('selected');
    });
    selectedMessages.clear();
    updateSelectionBar();
    selectionMode = false;
}

async function forwardSelectedMessages() {
    if(selectedMessages.size === 0) return;
    const targets = chats.filter(c => c.id !== currentChat.id);
    const container = document.getElementById('forwardList');
    container.innerHTML = targets.map(t => `<div class="contact-list-item" onclick="forwardToChat('${t.id}', ${t.isGroup})"><img src="${getAvatarUrl(t.name, t.avatar)}"><div class="contact-list-item-name">${escapeHtml(t.name)}</div></div>`).join('');
    if(targets.length === 0) container.innerHTML = '<div style="padding:16px;text-align:center;">Нет контактов</div>';
    window.pendingForwardMessages = Array.from(selectedMessages);
    document.getElementById('forwardModal').classList.add('visible');
}

window.forwardToChat = async (targetId, isGroup) => {
    for(let msgId of window.pendingForwardMessages) {
        const msg = await db.ref(getChatPath() + '/' + msgId).once('value');
        const msgData = msg.val();
        if(msgData) {
            const forwardData = { ...msgData, userId: currentUserId, name: currentUserName, avatar: currentUserAvatar, time: Date.now(), read: false, readBy: { [currentUserId]: true } };
            delete forwardData.readBy;
            forwardData.readBy = { [currentUserId]: true };
            const targetPath = isGroup ? `group_messages/${targetId}` : `private_messages/${[currentUserId, targetId].sort().join('_')}`;
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
            const isMe = msgEl?.dataset.isMe === 'true';
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
            bar.style.setProperty('--i', i);
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
    const blob = recordType === 'circle' ? new Blob(videoChunks, {type:'video/webm'}) : new Blob(audioChunks, {type:'audio/webm'});
    const reader = new FileReader();
    reader.onload = e => sendMessage('', recordType === 'circle' ? 'video' : 'audio', e.target.result, null, null, recordType === 'circle');
    reader.readAsDataURL(blob);
    document.getElementById('recordingOverlay').classList.remove('visible');
    isRecording = false;
}

document.getElementById('pauseRecBtn').onclick = () => {
    if(mediaRecorder && mediaRecorder.state === 'recording') { mediaRecorder.pause(); isPaused = true; document.getElementById('pauseRecBtn').innerHTML = '<i class="fas fa-play"></i>'; }
    else if(mediaRecorder && mediaRecorder.state === 'paused') { mediaRecorder.resume(); isPaused = false; document.getElementById('pauseRecBtn').innerHTML = '<i class="fas fa-pause"></i>'; if(recordType !== 'circle') drawWaveform(); }
};
document.getElementById('stopRecBtn').onclick = () => { stopRecordingAndSend(); document.getElementById('recordingOverlay').classList.remove('visible'); };
document.getElementById('deleteRecBtn').onclick = () => { stopRecordingAndSend(); document.getElementById('recordingOverlay').classList.remove('visible'); };
document.getElementById('sendRecBtn').onclick = () => stopRecordingAndSend();
document.getElementById('flipCameraBtn').onclick = () => { currentFacing = currentFacing === 'user' ? 'environment' : 'user'; if(recordStream) { recordStream.getTracks().forEach(t => t.stop()); startRecording('circle'); } };

// Отправка сообщений
document.getElementById('sendBtn').onclick = async () => {
    const input = document.getElementById('messageInput');
    const text = input.value.trim();
    if(text) await sendMessage(text, null, null, null, null, false);
    input.value = ''; input.style.height = 'auto';
};
document.getElementById('voiceBtn').onclick = () => startRecording('voice');
document.getElementById('circleBtn').onclick = () => startRecording('circle');

// Меню вложения
document.getElementById('attachBtn').onclick = () => {
    const menu = document.getElementById('attachMenu');
    if(menu.classList.contains('visible')) {
        menu.classList.add('closing');
        setTimeout(() => menu.classList.remove('visible', 'closing'), 200);
    } else { menu.classList.remove('closing'); menu.classList.add('visible'); }
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
document.getElementById('backBtn').onclick = () => { clearSelection(); selectionMode = false; document.getElementById('chatScreen').classList.remove('open'); };
document.getElementById('messageInput').addEventListener('input', function(){ this.style.height = 'auto'; this.style.height = Math.min(this.scrollHeight, 120) + 'px'; });

// Контакты и группы
async function showAddContactModal() {
    await loadAllUsers();
    const container = document.getElementById('allUsersList');
    container.innerHTML = allUsers.map(u => `<div class="contact-list-item" onclick="addContact('${u.id}', '${escapeHtml(u.name)}')"><img src="${getAvatarUrl(u.name, u.avatar)}"><div class="contact-list-item-name">${escapeHtml(u.name)}</div></div>`).join('');
    if(allUsers.length === 0) container.innerHTML = '<div style="padding:16px;text-align:center;">Нет пользователей для добавления</div>';
    document.getElementById('addContactModal').classList.add('visible');
}

window.addContact = async (id, name) => {
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
    await db.ref('groups/' + groupId).set({ id: groupId, name, description: document.getElementById('groupDesc').value, creator: currentUserId, members: membersObj, createdAt: Date.now() });
    await db.ref(`group_messages/${groupId}`).push({ userId: 'system', name: 'Система', text: `${currentUserName} создал группу`, time: Date.now(), isSystem: true });
    await loadChats();
    closeModal('createGroupModal');
    document.getElementById('groupName').value = '';
    document.getElementById('groupDesc').value = '';
    showToast('Группа создана');
};

window.showGroupInfo = async (groupId) => {
    const group = groups.find(g => g.id === groupId);
    if(!group) return;
    document.getElementById('groupInfoTitle').innerHTML = escapeHtml(group.name) + (group.creator === currentUserId ? ' 👑' : '');
    let html = '';
    for(let mid in group.members) {
        const u = await db.ref('users/' + mid).once('value');
        if(u.exists()) {
            html += `<div class="group-member-item"><img src="${getAvatarUrl(u.val().name, u.val().avatarUrl)}"><div style="flex:1;"><div style="color:var(--other-text);">${escapeHtml(u.val().name)}</div><div style="font-size:0.7rem;opacity:0.7;">${mid === group.creator ? 'Создатель' : 'Участник'}</div></div>${group.creator === currentUserId && mid !== currentUserId ? `<button class="kick-btn" onclick="kickFromGroup('${groupId}','${mid}')">Исключить</button>` : ''}</div>`;
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
        document.getElementById('profileBio').value = user.bio || '';
        document.getElementById('profileModal').dataset.userId = userId;
        document.getElementById('profileModal').classList.add('visible');
    } else {
        document.getElementById('viewProfileAvatar').src = getAvatarUrl(user.name, user.avatarUrl);
        document.getElementById('viewProfileName').innerText = user.name;
        document.getElementById('viewProfileBio').innerText = user.bio || 'Нет описания';
        document.getElementById('viewProfileModal').dataset.userId = userId;
        document.getElementById('viewProfileModal').dataset.userName = user.name;
        document.getElementById('viewProfileModal').classList.add('visible');
    }
};

document.getElementById('viewProfileSendMsg').onclick = () => {
    const userId = document.getElementById('viewProfileModal').dataset.userId;
    const userName = document.getElementById('viewProfileModal').dataset.userName;
    closeModal('viewProfileModal');
    openChat(userId, userName, false);
};

window.showMyProfile = () => showUserProfile(currentUserId);
window.showChatProfile = () => { if(!currentChat.isGroup) showUserProfile(currentChat.id); else showGroupInfo(currentChat.id); };

document.getElementById('saveProfileBtn').onclick = async () => {
    const userId = document.getElementById('profileModal').dataset.userId;
    if(userId === currentUserId) {
        const newName = document.getElementById('profileName').value.trim();
        const newBio = document.getElementById('profileBio').value;
        if(newName && newName !== currentUserName) {
            const check = await db.ref('users').orderByChild('name').equalTo(newName).once('value');
            if(check.exists()) { showToast('Ник занят'); return; }
            await db.ref('users/'+currentUserId).update({ name: newName, bio: newBio });
            currentUserName = newName;
        } else await db.ref('users/'+currentUserId).update({ bio: newBio });
        document.getElementById('menuName').innerText = currentUserName;
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
                <div class="admin-user-detail">${u.blocked ? '🔒 Заблокирован' : '✅ Активен'}</div>
                <div class="admin-user-actions">
                    <button onclick="adminEditUser('${u.id}')" style="background:#8b5cf6;">✏️ Редактировать</button>
                    <button onclick="adminClearUserMessages('${u.id}')" style="background:#f59e0b;">🗑️ Очистить чаты</button>
                    <button onclick="adminToggleBlock('${u.id}', ${u.blocked})" style="background:${u.blocked ? '#10b981' : '#ef4444'};">${u.blocked ? '🔓 Разблокировать' : '🔒 Заблокировать'}</button>
                    <button onclick="adminDeleteUser('${u.id}')" style="background:#dc2626;">💀 Удалить</button>
                </div>
            </div>
        `;
    }
    document.getElementById('usersAdminList').innerHTML = html;
    document.getElementById('adminModal').classList.add('visible');
}

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

window.adminToggleBlock = async (userId, blocked) => {
    await db.ref('users/'+userId).update({ blocked: !blocked });
    showToast(blocked ? 'Пользователь разблокирован' : 'Пользователь заблокирован');
    showAdminPanel();
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

// Меню
document.getElementById('menuBtn').onclick = () => { document.getElementById('sideMenu').classList.add('open'); document.getElementById('menuOverlay').classList.add('visible'); };
document.getElementById('menuOverlay').onclick = () => { document.getElementById('sideMenu').classList.remove('open'); document.getElementById('menuOverlay').classList.remove('visible'); };
document.getElementById('profileMenuBtn').onclick = () => { showMyProfile(); document.getElementById('sideMenu').classList.remove('open'); document.getElementById('menuOverlay').classList.remove('visible'); };
document.getElementById('themeMenuItem').onclick = () => { document.body.classList.toggle('dark'); localStorage.setItem('theme', document.body.classList.contains('dark') ? 'dark' : 'light'); };
document.getElementById('adminMenuItem').onclick = () => { showAdminPanel(); document.getElementById('sideMenu').classList.remove('open'); document.getElementById('menuOverlay').classList.remove('visible'); };
document.getElementById('logoutMenuItem').onclick = () => { db.ref('users/'+currentUserId).update({ online: false }); localStorage.clear(); location.reload(); };
document.getElementById('fabBtn').onclick = () => {
    const modal = document.createElement('div');
    modal.className = 'modal-overlay visible';
    modal.innerHTML = `<div class="modal-content"><h3>Действия</h3><button id="createGroupOpt">👥 Создать группу</button><button id="addContactOpt">➕ Добавить контакт</button><button class="close-modal">Отмена</button></div>`;
    document.body.appendChild(modal);
    modal.querySelector('#createGroupOpt').onclick = () => { showCreateGroupModal(); modal.remove(); };
    modal.querySelector('#addContactOpt').onclick = () => { showAddContactModal(); modal.remove(); };
    modal.querySelector('.close-modal').onclick = () => modal.remove();
};
document.getElementById('chatInfoBtn').onclick = () => { if(currentChat.isGroup) showGroupInfo(currentChat.id); else showUserProfile(currentChat.id); };

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
            snap.forEach(c => { if(c.val().password === pass) { currentUserId = c.key; currentUserName = c.val().name; currentUserAvatar = c.val().avatarUrl; currentUserBio = c.val().bio; found = true; } });
            if(!found) { document.getElementById('authError').innerText = 'Неверный пароль'; btn.disabled = false; btn.innerText = 'Войти'; return; }
        } else {
            const newUser = db.ref('users').push();
            currentUserId = newUser.key;
            await newUser.set({ name, password: pass, avatarUrl: '', bio: '', blocked: false, createdAt: Date.now(), online: true });
            currentUserName = name; currentUserAvatar = null; currentUserBio = '';
        }
        isAdmin = currentUserName === 'DaniksGames';
        if(isAdmin) document.getElementById('adminMenuItem').style.display = 'flex';
        await db.ref('users/'+currentUserId).update({ online: true, lastSeen: Date.now() });
        localStorage.setItem('userId', currentUserId);
        document.getElementById('authOverlay').style.display = 'none';
        document.getElementById('menuName').innerText = currentUserName;
        document.getElementById('menuAvatar').src = getAvatarUrl(currentUserName, currentUserAvatar);
        const savedMuted = localStorage.getItem('mutedChats');
        if(savedMuted) mutedChats = new Set(JSON.parse(savedMuted));
        await loadChats();
        setInterval(() => db.ref('users/'+currentUserId).update({ online: true, lastSeen: Date.now() }), 30000);
        setInterval(updateOnlineStatuses, 10000);
        if(Notification.permission === 'default') Notification.requestPermission();
    } catch(e) { document.getElementById('authError').innerText = 'Ошибка: ' + e.message; btn.disabled = false; btn.innerText = 'Войти'; }
}

document.getElementById('authBtn').onclick = auth;
if(localStorage.getItem('theme') === 'dark') document.body.classList.add('dark');

(async () => {
    const uid = localStorage.getItem('userId');
    if(uid) {
        const snap = await db.ref('users/'+uid).once('value');
        if(snap.exists() && !snap.val().blocked) {
            currentUserId = uid; currentUserName = snap.val().name; currentUserAvatar = snap.val().avatarUrl; currentUserBio = snap.val().bio;
            isAdmin = currentUserName === 'DaniksGames';
            if(isAdmin) document.getElementById('adminMenuItem').style.display = 'flex';
            await db.ref('users/'+uid).update({ online: true });
            document.getElementById('authOverlay').style.display = 'none';
            document.getElementById('menuName').innerText = currentUserName;
            document.getElementById('menuAvatar').src = getAvatarUrl(currentUserName, currentUserAvatar);
            const savedMuted = localStorage.getItem('mutedChats');
            if(savedMuted) mutedChats = new Set(JSON.parse(savedMuted));
            await loadChats();
            setInterval(() => db.ref('users/'+uid).update({ online: true, lastSeen: Date.now() }), 30000);
            setInterval(updateOnlineStatuses, 10000);
            if(Notification.permission === 'default') Notification.requestPermission();
            return;
        }
    }
    document.getElementById('authOverlay').style.display = 'flex';
})();
</script>
</body>
</html>
