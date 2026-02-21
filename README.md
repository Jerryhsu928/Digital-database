<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=yes">
    <title>🔐 企業專案管理系統 - 手機優化版</title>
    <!-- 字體 Inter -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:ital,opsz,wght@0,14..32,100..900;1,14..32,100..900&display=swap" rel="stylesheet">
    <!-- Font Awesome 6 -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        html, body { height: 100%; overflow: hidden; font-family: "Inter", sans-serif; }
        body.light-mode {
            background: #f8fafc;
            color: #0f172a;
        }
        body.light-mode .app-container { background: rgba(255,255,255,0.7); }
        body.light-mode .auth-card { background: rgba(255,255,255,0.9); color: #0f172a; }
        body.light-mode .sidebar { background: rgba(255,255,255,0.7); }
        body.light-mode .page-card { background: rgba(255,255,255,0.9); }
        body.light-mode .form-control { background: white; color: #0f172a; border-color: #cbd5e1; }
        body.light-mode .source-type-btn { background: #e2e8f0; color: #334155; }
        body.light-mode .source-type-btn.active { background: #2563eb; color: white; }
        body.light-mode .toast { background: #1e293b; color: white; }
        body.light-mode table td, body.light-mode table th { color: #0f172a; }
        /* 淺色模式所有按鈕統一 */
        body.light-mode .auth-btn,
        body.light-mode .download-btn,
        body.light-mode .source-input-group button,
        body.light-mode .save-btn,
        body.light-mode .delete-btn,
        body.light-mode .logout-btn,
        body.light-mode .theme-option,
        body.light-mode .language-select,
        body.light-mode .project-card .download-btn {
            background: #2563eb !important;
            color: white !important;
            border: none;
        }
        body.light-mode .delete-btn { background: #ef4444 !important; }
        body.light-mode .logout-btn { background: rgba(239,68,68,0.8) !important; }
        body.light-mode .theme-option { background: #e2e8f0 !important; color: #334155 !important; }
        body.light-mode .theme-option.active { background: #2563eb !important; color: white !important; }

        body.dark-mode {
            background: #0a0f1e;
            color: #e2e8f0;
        }
        body.dark-mode .app-container { background: rgba(15, 25, 45, 0.6); }
        body.dark-mode .auth-card { background: rgba(20, 30, 50, 0.9); }
        body.dark-mode .sidebar { background: rgba(20, 30, 50, 0.7); }
        body.dark-mode .page-card { background: rgba(25, 35, 55, 0.85); }
        body.dark-mode .form-control { background: rgba(10, 20, 35, 0.8); color: white; border-color: #2d3f5e; }
        body.dark-mode .source-type-btn { background: #1e293b; color: #94a3b8; }
        body.dark-mode .source-type-btn.active { background: #2563eb; color: white; }
        body.dark-mode .toast { background: #1e293b; color: white; }
        body.dark-mode table td, body.dark-mode table th { color: #e2e8f0; }

        #particleCanvas {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 0;
            pointer-events: none;
            background: radial-gradient(circle at 20% 30%, #1e2a4a, #0b1423);
        }
        .app-container {
            width: 100%;
            max-width: 1400px;
            height: 100vh;
            backdrop-filter: blur(20px);
            -webkit-backdrop-filter: blur(20px);
            padding: 15px;
            box-shadow: 0 30px 60px rgba(0, 0, 0, 0.8);
            border: 1px solid rgba(255,255,255,0.1);
            z-index: 2;
            display: flex;
            flex-direction: column;
            overflow: hidden;
            transition: background 0.3s;
        }

        /* 登入/註冊卡片 */
        .auth-card {
            max-width: 500px;
            width: 100%;
            margin: auto;
            border-radius: 40px;
            padding: 30px 25px;
            border: 1px solid rgba(255,255,255,0.15);
            box-shadow: 0 30px 50px -20px black;
            max-height: 90vh;
            overflow-y: auto;
        }
        .auth-tabs {
            display: flex;
            gap: 10px;
            margin-bottom: 25px;
            background: rgba(0,0,0,0.3);
            padding: 6px;
            border-radius: 50px;
            flex-wrap: wrap;
        }
        .auth-tab {
            flex: 1 1 auto;
            text-align: center;
            padding: 12px;
            font-weight: 600;
            font-size: 1rem;
            color: #94a3b8;
            cursor: pointer;
            border-radius: 40px;
            transition: 0.3s;
        }
        .auth-tab.active {
            background: #2563eb;
            color: white;
            box-shadow: 0 5px 15px #2563eb;
        }
        .auth-form { display: flex; flex-direction: column; gap: 18px; }
        .auth-form h3 {
            margin-bottom: 5px;
            font-size: 1.2rem;
        }
        .form-group { text-align: left; }
        .form-group label {
            font-weight: 500; font-size: 0.85rem; color: #a5b4cb;
            display: block; margin-bottom: 6px;
        }
        .form-control {
            width: 100%;
            padding: 14px 18px;
            border-radius: 40px;
            font-size: 0.95rem;
            transition: 0.2s;
            -webkit-appearance: none;
            appearance: none;
        }
        .form-control:focus {
            outline: none;
            border-color: #3b82f6;
            box-shadow: 0 0 0 3px rgba(59,130,246,0.3);
        }
        /* 統一按鈕樣式與動畫 */
        .auth-btn, .download-btn, .source-input-group button, .save-btn, .delete-btn, .logout-btn, .theme-option, .language-select, .project-card .download-btn {
            transition: transform 0.2s ease, background 0.2s, opacity 0.2s;
            cursor: pointer;
            user-select: none;
            border: none;
            outline: none;
        }
        .auth-btn:active, .download-btn:active, .source-input-group button:active, .save-btn:active, .delete-btn:active, .logout-btn:active, .theme-option:active, .language-select:active, .project-card .download-btn:active {
            transform: scale(0.98);
        }
        .auth-btn.loading, .download-btn.loading, .source-input-group button.loading, .save-btn.loading, .delete-btn.loading, .logout-btn.loading, .project-card .download-btn.loading {
            opacity: 0.7;
            pointer-events: none;
        }
        .auth-btn.loading i, .download-btn.loading i, .source-input-group button.loading i, .save-btn.loading i, .delete-btn.loading i, .logout-btn.loading i, .project-card .download-btn.loading i {
            animation: spin 1s linear infinite;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        .auth-btn {
            background: linear-gradient(135deg, #2563eb, #1e40af);
            color: white;
            padding: 14px;
            border-radius: 50px;
            font-weight: 600;
            font-size: 1rem;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
        }
        .error-message {
            color: #f87171; font-size: 0.85rem;
            background: rgba(220,38,38,0.2); padding: 10px 16px; border-radius: 30px;
            border: 1px solid #ef4444; display: none;
        }
        .student-fields { display: flex; flex-direction: column; gap: 15px; }

        /* 主面板 (左右) */
        .main-panel {
            display: none;
            flex: 1;
            gap: 15px;
            height: 100%;
            overflow: hidden;
        }
        /* 左側導航 (可折疊) */
        .sidebar {
            background: rgba(20, 30, 50, 0.7);
            backdrop-filter: blur(10px);
            -webkit-backdrop-filter: blur(10px);
            border-radius: 35px;
            padding: 20px 0;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 15px;
            border: 1px solid rgba(255,255,255,0.15);
            height: 100%;
            overflow-y: auto;
            transition: width 0.3s;
            width: 100px;
        }
        .sidebar.collapsed {
            width: 70px;
        }
        .sidebar.collapsed .nav-item span {
            display: none;
        }
        .sidebar .nav-item {
            width: 70px;
            height: 70px;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            background: rgba(255,255,255,0.08);
            border-radius: 25px;
            color: white;
            cursor: pointer;
            transition: 0.2s;
            border: 1px solid rgba(255,255,255,0.1);
            gap: 5px;
            font-size: 0.75rem;
        }
        .sidebar.collapsed .nav-item {
            width: 50px;
            height: 50px;
        }
        .nav-item i { font-size: 1.5rem; }
        .nav-item.active {
            background: #2563eb;
            border-color: white;
            box-shadow: 0 0 20px #2563eb;
        }
        .sidebar-toggle {
            width: 100%;
            display: flex;
            justify-content: center;
            margin-bottom: 10px;
            cursor: pointer;
            color: white;
            font-size: 1.2rem;
        }

        /* 右側內容區 (可滑動) */
        .content-area {
            flex: 1;
            min-width: 0;
            height: 100%;
            overflow-y: auto;
            padding-right: 5px;
            scrollbar-width: thin;
        }

        /* 頁面卡片 */
        .page-card {
            background: rgba(25, 35, 55, 0.85);
            backdrop-filter: blur(10px);
            -webkit-backdrop-filter: blur(10px);
            border-radius: 40px;
            padding: 25px;
            margin-bottom: 15px;
            border: 1px solid rgba(255,255,255,0.15);
        }
        .page-title {
            font-size: 2rem;
            margin-bottom: 20px;
            display: flex;
            align-items: center;
            gap: 10px;
            flex-wrap: wrap;
        }

        /* 專案網格 */
        .project-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(120px, 1fr));
            gap: 15px;
        }
        .project-card {
            background: rgba(0,0,0,0.2);
            border-radius: 25px;
            padding: 15px;
            text-align: center;
            cursor: pointer;
            transition: 0.2s;
            border: 1px solid rgba(255,255,255,0.1);
            position: relative;
        }
        .project-card.locked { opacity: 0.6; }
        .project-card.unlocked { border-color: #4ade80; }
        .project-card .letter {
            font-size: 2rem;
            font-weight: 700;
            margin-bottom: 5px;
        }
        .project-card .name {
            font-size: 0.9rem;
            color: #94a3b8;
            display: none;
        }
        .project-card.unlocked .name {
            display: block;
        }
        .project-card .lock-icon {
            position: absolute;
            top: 5px;
            right: 5px;
            font-size: 0.8rem;
        }

        /* 專案詳情 */
        .project-detail {
            margin-top: 20px;
        }
        .project-detail img {
            max-width: 100%;
            border-radius: 20px;
            margin: 15px 0;
        }
        .download-btn {
            display: inline-block;
            background: #2563eb;
            color: white;
            padding: 12px 25px;
            border-radius: 40px;
            text-decoration: none;
            margin: 10px 0;
        }
        .timer {
            font-size: 1.2rem;
            color: #fbbf24;
            margin: 10px 0;
        }
        .unlock-section {
            background: rgba(0,0,0,0.2);
            border-radius: 30px;
            padding: 20px;
            margin: 20px 0;
        }
        .source-type {
            display: flex;
            gap: 10px;
            margin-bottom: 15px;
            flex-wrap: wrap;
        }
        .source-type-btn {
            flex: 1 1 auto;
            padding: 10px;
            border-radius: 30px;
            background: #1e293b;
            color: #94a3b8;
            text-align: center;
            cursor: pointer;
        }
        .source-type-btn.active {
            background: #2563eb;
            color: white;
        }
        .source-input-group {
            display: flex;
            gap: 10px;
            margin-top: 10px;
            flex-wrap: wrap;
        }
        .source-input-group input {
            flex: 1 1 200px;
            padding: 12px;
            border-radius: 40px;
            border: 1px solid #3b82f6;
            background: #0f172a;
            color: white;
        }
        .source-input-group button {
            padding: 12px 20px;
            border-radius: 40px;
            background: #2563eb;
            color: white;
            border: none;
            cursor: pointer;
        }

        /* 人物區塊 (僅超級用戶) */
        .person-selector {
            display: flex;
            gap: 10px;
            margin-bottom: 20px;
            flex-wrap: wrap;
        }
        .person-selector select {
            flex: 1 1 200px;
            padding: 14px;
            border-radius: 40px;
            background: #1e293b;
            color: white;
            border: 1px solid #3b82f6;
        }
        .person-detail-item {
            display: flex;
            padding: 10px 0;
            border-bottom: 1px solid rgba(255,255,255,0.1);
            flex-wrap: wrap;
        }
        .person-detail-label {
            width: 80px;
            color: #94a3b8;
        }
        .person-detail-value {
            flex: 1;
            color: white;
        }

        /* 密碼庫表格 */
        .password-table {
            width: 100%;
            border-collapse: collapse;
        }
        .password-table th, .password-table td {
            padding: 12px;
            text-align: left;
            border-bottom: 1px solid rgba(255,255,255,0.1);
        }
        .password-table input {
            width: 100%;
            padding: 8px;
            border-radius: 20px;
            border: 1px solid #3b82f6;
            background: #1e293b;
            color: white;
        }
        .save-btn {
            margin-top: 20px;
            padding: 12px 25px;
            background: #2563eb;
            color: white;
            border: none;
            border-radius: 40px;
            cursor: pointer;
        }

        /* 用戶管理表格 */
        .user-table {
            width: 100%;
            border-collapse: collapse;
        }
        .user-table th, .user-table td {
            padding: 12px;
            text-align: left;
            border-bottom: 1px solid rgba(255,255,255,0.1);
        }
        .user-table input, .user-table select {
            padding: 5px;
            border-radius: 20px;
            background: #1e293b;
            color: white;
            border: 1px solid #3b82f6;
        }
        .delete-btn {
            background: #ef4444;
            color: white;
            border: none;
            padding: 5px 10px;
            border-radius: 20px;
            cursor: pointer;
        }

        /* 子專案管理 */
        .subproject-item {
            background: rgba(0,0,0,0.1);
            border-radius: 20px;
            padding: 15px;
            margin-bottom: 10px;
        }

        /* 設定頁面 */
        .settings-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 15px 0;
            border-bottom: 1px solid rgba(255,255,255,0.1);
            flex-wrap: wrap;
            gap: 10px;
        }
        .settings-item .value {
            background: #1e293b;
            padding: 5px 15px;
            border-radius: 30px;
            color: white;
        }
        .settings-item .dots {
            letter-spacing: 3px;
            font-size: 1.2rem;
        }
        .theme-toggle {
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
        }
        .theme-option {
            padding: 8px 20px;
            border-radius: 30px;
            background: #1e293b;
            color: #94a3b8;
            cursor: pointer;
        }
        .theme-option.active {
            background: #2563eb;
            color: white;
        }
        .language-select {
            background: #1e293b;
            color: white;
            padding: 8px 15px;
            border-radius: 30px;
            border: 1px solid #3b82f6;
        }
        .version-info {
            display: flex;
            gap: 10px;
            align-items: center;
            flex-wrap: wrap;
        }

        .logout-btn {
            background: rgba(239,68,68,0.2);
            color: #f87171;
            border: 1px solid #ef4444;
            padding: 12px 30px;
            border-radius: 40px;
            cursor: pointer;
            display: inline-block;
            margin-top: 20px;
        }

        .toast {
            position: fixed; bottom: 20px; right: 20px;
            background: #1e293b; color: white; padding: 12px 24px; border-radius: 50px;
            z-index: 9999; opacity: 0; transition: opacity 0.3s;
            border: 1px solid #3b82f6;
            pointer-events: none;
        }
        .toast.show { opacity: 1; }

        .shake { animation: shake 0.4s ease-in-out; }
        @keyframes shake {
            0%,100% { transform: translateX(0); }
            20%,60% { transform: translateX(-6px); }
            40%,80% { transform: translateX(6px); }
        }

        /* 手機優化 */
        @media (max-width: 600px) {
            .sidebar { width: 80px; }
            .sidebar.collapsed { width: 60px; }
            .sidebar .nav-item { width: 60px; height: 60px; }
            .sidebar.collapsed .nav-item { width: 40px; height: 40px; }
            .page-title { font-size: 1.5rem; }
            .auth-tab { font-size: 0.8rem; padding: 8px; }
            .auth-card { padding: 20px 15px; }
        }
    </style>
</head>
<body class="dark-mode">
    <canvas id="particleCanvas"></canvas>

    <div class="app-container">
        <!-- 登入/註冊卡片 (可滑動) -->
        <div id="authCard" class="auth-card">
            <div class="auth-tabs">
                <div class="auth-tab active" id="loginTab" data-i18n="login">登入</div>
                <div class="auth-tab" id="registerTab" data-i18n="register">註冊</div>
                <div class="auth-tab" id="permCodeTab" data-i18n="permCode">永久原始碼</div>
                <div class="auth-tab" id="onceCodeTab" data-i18n="onceCode">一次性原始碼</div>
            </div>

            <!-- 登入表單 (帳號密碼) -->
            <div id="loginForm" class="auth-form">
                <h3 data-i18n="loginTitle">帳號密碼登入</h3>
                <div class="form-group">
                    <label data-i18n="username">👤 帳號</label>
                    <input type="text" class="form-control" id="loginUsername" value="user1">
                </div>
                <div class="form-group">
                    <label data-i18n="password">🔑 密碼</label>
                    <input type="password" class="form-control" id="loginPassword" value="pass">
                </div>
                <div id="loginError" class="error-message"><i class="fas fa-exclamation-triangle"></i> <span data-i18n="loginError">帳號或密碼錯誤</span></div>
                <button class="auth-btn" id="loginBtn"><i class="fas fa-sign-in-alt"></i> <span data-i18n="loginBtn">登入</span></button>
            </div>

            <!-- 註冊表單 (職業只有學生) -->
            <div id="registerForm" class="auth-form" style="display: none;">
                <h3 data-i18n="registerTitle">註冊新帳號</h3>
                <div class="form-group">
                    <label data-i18n="username">👤 帳號</label>
                    <input type="text" class="form-control" id="regUsername">
                </div>
                <div class="form-group">
                    <label data-i18n="password">🔑 密碼</label>
                    <input type="password" class="form-control" id="regPassword">
                </div>
                <div class="form-group">
                    <label data-i18n="occupation">💼 職業</label>
                    <select class="form-control" id="regOccupation">
                        <option value="student" data-i18n="student">學生</option>
                    </select>
                </div>
                <div class="student-fields" id="studentFields">
                    <div class="form-group"><label data-i18n="class">📚 班級</label><input type="text" class="form-control" id="regClass"></div>
                    <div class="form-group"><label data-i18n="number">🔢 座號</label><input type="text" class="form-control" id="regNumber"></div>
                </div>
                <div id="registerError" class="error-message"></div>
                <button class="auth-btn" id="registerBtn"><i class="fas fa-user-plus"></i> <span data-i18n="registerBtn">註冊</span></button>
            </div>

            <!-- 永久原始碼登入 -->
            <div id="permCodeForm" class="auth-form" style="display: none;">
                <h3 data-i18n="permCodeTitle">永久原始碼登入</h3>
                <div class="form-group">
                    <label data-i18n="permCodeInput">🔢 請輸入永久原始碼 (數字)</label>
                    <input type="text" class="form-control" id="permCode" placeholder="例如 100A" value="100A">
                </div>
                <div id="permCodeError" class="error-message"><i class="fas fa-exclamation-triangle"></i> <span data-i18n="permCodeError">原始碼無效</span></div>
                <button class="auth-btn" id="permCodeBtn"><i class="fas fa-key"></i> <span data-i18n="permCodeBtn">登入</span></button>
            </div>

            <!-- 一次性原始碼登入 -->
            <div id="onceCodeForm" class="auth-form" style="display: none;">
                <h3 data-i18n="onceCodeTitle">一次性原始碼登入</h3>
                <div class="form-group">
                    <label data-i18n="onceCodeInput">🔢 請輸入一次性原始碼 (數字)</label>
                    <input type="text" class="form-control" id="onceCode" placeholder="例如 ONCEA001" value="ONCEA001">
                </div>
                <div id="onceCodeError" class="error-message"><i class="fas fa-exclamation-triangle"></i> <span data-i18n="onceCodeError">原始碼無效或已使用</span></div>
                <button class="auth-btn" id="onceCodeBtn"><i class="fas fa-key"></i> <span data-i18n="onceCodeBtn">登入</span></button>
            </div>
        </div>

        <!-- 主面板 -->
        <div id="mainPanel" class="main-panel">
            <!-- 左側導航 (可折疊) -->
            <div class="sidebar" id="sidebar">
                <div class="sidebar-toggle" id="sidebarToggle"><i class="fas fa-bars"></i></div>
                <div class="nav-item active" data-page="home"><i class="fas fa-home"></i><span data-i18n="home">首頁</span></div>
                <div class="nav-item" data-page="projects"><i class="fas fa-folder"></i><span data-i18n="projects">專案</span></div>
                <div class="nav-item" data-page="people" id="peopleNav"><i class="fas fa-users"></i><span data-i18n="people">人物</span></div>
                <!-- 超級用戶專屬導航 -->
                <div class="nav-item" data-page="passwordVault" id="passwordVaultNav" style="display: none;"><i class="fas fa-lock"></i><span data-i18n="passwordVault">密碼庫</span></div>
                <div class="nav-item" data-page="userManagement" id="userManagementNav" style="display: none;"><i class="fas fa-user-cog"></i><span data-i18n="userManagement">用戶管理</span></div>
                <div class="nav-item" data-page="subProjectAdmin" id="subProjectAdminNav" style="display: none;"><i class="fas fa-layer-group"></i><span>子專案管理</span></div>
                <div class="nav-item" data-page="sourceCodeAdmin" id="sourceCodeAdminNav" style="display: none;"><i class="fas fa-code"></i><span>原始碼管理</span></div>
                <div class="nav-item" data-page="settings"><i class="fas fa-cog"></i><span data-i18n="settings">設定</span></div>
            </div>

            <!-- 右側內容區 (可滑動) -->
            <div class="content-area" id="contentArea">
                <!-- 頁面會動態載入 -->
            </div>
        </div>
    </div>

    <div id="toast" class="toast"></div>

    <script>
        (function() {
            // ---------- 語言包 ----------
            const i18n = {
                'zh-TW': {
                    login: '登入',
                    register: '註冊',
                    permCode: '永久原始碼',
                    onceCode: '一次性原始碼',
                    loginTitle: '帳號密碼登入',
                    registerTitle: '註冊新帳號',
                    permCodeTitle: '永久原始碼登入',
                    onceCodeTitle: '一次性原始碼登入',
                    username: '👤 帳號',
                    password: '🔑 密碼',
                    occupation: '💼 職業',
                    student: '學生',
                    class: '📚 班級',
                    number: '🔢 座號',
                    loginBtn: '登入',
                    registerBtn: '註冊',
                    permCodeBtn: '登入',
                    onceCodeBtn: '登入',
                    loginError: '帳號或密碼錯誤',
                    permCodeError: '原始碼無效',
                    onceCodeError: '原始碼無效或已使用',
                    home: '首頁',
                    projects: '專案',
                    people: '人物',
                    passwordVault: '密碼庫',
                    userManagement: '用戶管理',
                    settings: '設定',
                    welcome: '歡迎',
                    role_observer: '觀察者',
                    role_user: '使用者',
                    role_manager: '管理者',
                    role_creator: '創造者',
                    accountId: '帳號ID',
                    quickCommand: '快速指令',
                    permSource: '永久原始碼',
                    onceSource: '一次性原始碼',
                    execute: '執行',
                    unlockSuccess: '解鎖成功',
                    remaining: '剩餘時間',
                    seconds: '秒',
                    download: '下載檔案',
                    unlockWithSource: '原始碼解鎖',
                    unlockWithPassword: '專案密碼解鎖',
                    enterSource: '請輸入原始碼',
                    enterPassword: '請輸入密碼',
                    confirm: '確認',
                    cancel: '取消',
                    permissionDenied: '⛔ 權限不足',
                    onlyCreator: '只有創造者可以查看此頁面',
                    personData: '人物資料',
                    project: '專案',
                    person: '人物',
                    name: '姓名',
                    occupationLabel: '職業',
                    classLabel: '班級',
                    numberLabel: '座號',
                    email: '電子郵件',
                    notFilled: '未填寫',
                    settingsTitle: '設定',
                    accountName: '帳號名稱',
                    passwordLabel: '密碼',
                    language: '語言',
                    theme: '主題',
                    light: '淺色',
                    dark: '深色',
                    version: '版本',
                    checkUpdate: '檢查更新',
                    logout: '登出',
                    back: '返回',
                    details: '詳情',
                    expired: '已過期',
                    codeFor: '此原始碼對應的是',
                    noPassword: '此專案無密碼設定',
                    wrongPassword: '密碼錯誤',
                    required: '必填',
                    cannotRegister: '不可註冊',
                    userExists: '帳號已存在',
                    classNumberRequired: '請填寫班級與座號',
                    registerSuccess: '註冊成功，請登入',
                    saveChanges: '儲存變更',
                    edit: '編輯',
                    delete: '刪除',
                    loginTime: '登入時間',
                    action: '操作',
                    checkComplete: '已是最新版本',
                },
                'en': {
                    login: 'Login',
                    register: 'Register',
                    permCode: 'Permanent Code',
                    onceCode: 'One-time Code',
                    loginTitle: 'Login with Account',
                    registerTitle: 'Register',
                    permCodeTitle: 'Permanent Code Login',
                    onceCodeTitle: 'One-time Code Login',
                    username: '👤 Username',
                    password: '🔑 Password',
                    occupation: '💼 Occupation',
                    student: 'Student',
                    class: '📚 Class',
                    number: '🔢 Number',
                    loginBtn: 'Login',
                    registerBtn: 'Register',
                    permCodeBtn: 'Login',
                    onceCodeBtn: 'Login',
                    loginError: 'Invalid username or password',
                    permCodeError: 'Invalid code',
                    onceCodeError: 'Invalid or used code',
                    home: 'Home',
                    projects: 'Projects',
                    people: 'People',
                    passwordVault: 'Password Vault',
                    userManagement: 'User Management',
                    settings: 'Settings',
                    welcome: 'Welcome',
                    role_observer: 'Observer',
                    role_user: 'User',
                    role_manager: 'Manager',
                    role_creator: 'Creator',
                    accountId: 'Account ID',
                    quickCommand: 'Quick Command',
                    permSource: 'Permanent Code',
                    onceSource: 'One-time Code',
                    execute: 'Execute',
                    unlockSuccess: 'Unlock successful',
                    remaining: 'Remaining',
                    seconds: 'sec',
                    download: 'Download',
                    unlockWithSource: 'Unlock with Code',
                    unlockWithPassword: 'Unlock with Password',
                    enterSource: 'Enter code',
                    enterPassword: 'Enter password',
                    confirm: 'Confirm',
                    cancel: 'Cancel',
                    permissionDenied: '⛔ Permission denied',
                    onlyCreator: 'Only creator can view this page',
                    personData: 'People Data',
                    project: 'Project',
                    person: 'Person',
                    name: 'Name',
                    occupationLabel: 'Occupation',
                    classLabel: 'Class',
                    numberLabel: 'Number',
                    email: 'Email',
                    notFilled: 'Not filled',
                    settingsTitle: 'Settings',
                    accountName: 'Account Name',
                    passwordLabel: 'Password',
                    language: 'Language',
                    theme: 'Theme',
                    light: 'Light',
                    dark: 'Dark',
                    version: 'Version',
                    checkUpdate: 'Check Update',
                    logout: 'Logout',
                    back: 'Back',
                    details: 'Details',
                    expired: 'Expired',
                    codeFor: 'This code is for',
                    noPassword: 'No password set',
                    wrongPassword: 'Wrong password',
                    required: 'Required',
                    cannotRegister: 'Cannot register',
                    userExists: 'Username exists',
                    classNumberRequired: 'Please fill class and number',
                    registerSuccess: 'Registration successful, please login',
                    saveChanges: 'Save Changes',
                    edit: 'Edit',
                    delete: 'Delete',
                    loginTime: 'Login Time',
                    action: 'Action',
                    checkComplete: 'Up to date',
                }
            };

            let currentLang = localStorage.getItem('lang') || 'zh-TW';

            function t(key) {
                return i18n[currentLang][key] || key;
            }

            function applyLanguage() {
                document.querySelectorAll('[data-i18n]').forEach(el => {
                    const key = el.getAttribute('data-i18n');
                    el.textContent = t(key);
                });
            }

            function setLanguage(lang) {
                currentLang = lang;
                localStorage.setItem('lang', lang);
                applyLanguage();
                if (currentUser.username) {
                    const activePage = document.querySelector('.nav-item.active')?.dataset.page || 'home';
                    renderPage(activePage);
                }
            }

            // ---------- 粒子背景 ----------
            const canvas = document.getElementById('particleCanvas');
            const ctx = canvas.getContext('2d');
            let width, height, particles = [];
            function initParticles() {
                width = window.innerWidth; height = window.innerHeight;
                canvas.width = width; canvas.height = height;
                for (let i = 0; i < 60; i++) {
                    particles.push({
                        x: Math.random() * width, y: Math.random() * height,
                        radius: Math.random() * 3 + 1,
                        vx: (Math.random() - 0.5) * 0.3,
                        vy: (Math.random() - 0.5) * 0.3,
                        color: `rgba(59,130,246,${Math.random() * 0.3 + 0.2})`
                    });
                }
            }
            function drawParticles() {
                ctx.clearRect(0, 0, width, height);
                particles.forEach(p => {
                    ctx.beginPath(); ctx.arc(p.x, p.y, p.radius, 0, Math.PI*2);
                    ctx.fillStyle = p.color; ctx.fill();
                    p.x += p.vx; p.y += p.vy;
                    if (p.x<0||p.x>width) p.vx = -p.vx;
                    if (p.y<0||p.y>height) p.vy = -p.vy;
                });
                requestAnimationFrame(drawParticles);
            }
            window.addEventListener('resize', initParticles);
            initParticles(); drawParticles();

            // ---------- DOM 元素 ----------
            const authCard = document.getElementById('authCard');
            const mainPanel = document.getElementById('mainPanel');
            const loginTab = document.getElementById('loginTab');
            const registerTab = document.getElementById('registerTab');
            const permCodeTab = document.getElementById('permCodeTab');
            const onceCodeTab = document.getElementById('onceCodeTab');
            const loginForm = document.getElementById('loginForm');
            const registerForm = document.getElementById('registerForm');
            const permCodeForm = document.getElementById('permCodeForm');
            const onceCodeForm = document.getElementById('onceCodeForm');
            const loginBtn = document.getElementById('loginBtn');
            const registerBtn = document.getElementById('registerBtn');
            const permCodeBtn = document.getElementById('permCodeBtn');
            const onceCodeBtn = document.getElementById('onceCodeBtn');
            const loginError = document.getElementById('loginError');
            const registerError = document.getElementById('registerError');
            const permCodeError = document.getElementById('permCodeError');
            const onceCodeError = document.getElementById('onceCodeError');
            const regOccupation = document.getElementById('regOccupation');
            const studentFields = document.getElementById('studentFields');
            const toast = document.getElementById('toast');
            const navItems = document.querySelectorAll('.nav-item');
            const contentArea = document.getElementById('contentArea');
            const peopleNav = document.getElementById('peopleNav');
            const passwordVaultNav = document.getElementById('passwordVaultNav');
            const userManagementNav = document.getElementById('userManagementNav');
            const subProjectAdminNav = document.getElementById('subProjectAdminNav');
            const sourceCodeAdminNav = document.getElementById('sourceCodeAdminNav');
            const sidebar = document.getElementById('sidebar');
            const sidebarToggle = document.getElementById('sidebarToggle');

            sidebarToggle.addEventListener('click', () => {
                sidebar.classList.toggle('collapsed');
            });

            // 當前登入用戶資訊
            let currentUser = {
                username: null,
                role: null,
                accountId: null,
                unlockedProjects: {} // 解鎖的字母專案
            };

            // 用戶登入記錄
            let userLogs = [];

            // ========== 使用者資料庫 (擴充) ==========
            // 格式: username: { password, role, accountId, class, number, occupation }
            const users = {
                user1: { password: 'pass', role: 'observer', accountId: 'OBS-123ABC', class: '', number: '', occupation: '' },
                admin: { password: 'admin', role: 'manager', accountId: 'MGR-456DEF', class: '', number: '', occupation: '' },
                // 超級用戶0 不在此列，由程式碼處理
            };

            // ========== 永久原始碼清單 (A-Z) ==========
            const permSourceCodes = {
                "100A": { letter: "A", expirySec: 100, role: 'manager' },
                "200B": { letter: "B", expirySec: 200, role: 'manager' },
                "300C": { letter: "C", expirySec: 300, role: 'manager' },
                "400D": { letter: "D", expirySec: 400, role: 'manager' },
                "500E": { letter: "E", expirySec: 500, role: 'manager' },
                "600F": { letter: "F", expirySec: 600, role: 'manager' },
                "700G": { letter: "G", expirySec: 700, role: 'manager' },
                "800H": { letter: "H", expirySec: 800, role: 'manager' },
                "900I": { letter: "I", expirySec: 900, role: 'manager' },
                "101J": { letter: "J", expirySec: 101, role: 'manager' },
                "202K": { letter: "K", expirySec: 202, role: 'manager' },
                "303L": { letter: "L", expirySec: 303, role: 'manager' },
                "404M": { letter: "M", expirySec: 404, role: 'manager' },
                "505N": { letter: "N", expirySec: 505, role: 'manager' },
                "606O": { letter: "O", expirySec: 606, role: 'manager' },
                "707P": { letter: "P", expirySec: 707, role: 'manager' },
                "808Q": { letter: "Q", expirySec: 808, role: 'manager' },
                "909R": { letter: "R", expirySec: 909, role: 'manager' },
                "111S": { letter: "S", expirySec: 111, role: 'manager' },
                "222T": { letter: "T", expirySec: 222, role: 'manager' },
                "333U": { letter: "U", expirySec: 333, role: 'manager' },
                "444V": { letter: "V", expirySec: 444, role: 'manager' },
                "555W": { letter: "W", expirySec: 555, role: 'manager' },
                "666X": { letter: "X", expirySec: 666, role: 'manager' },
                "777Y": { letter: "Y", expirySec: 777, role: 'manager' },
                "888Z": { letter: "Z", expirySec: 888, role: 'manager' }
            };

            // ========== 一次性原始碼清單 (A-Z) ==========
            const onceSourceCodes = {
                "ONCEA001": { letter: "A", expirySec: 1, used: false, role: 'user', allowedUsers: [], maxUses: 1, usedCount: 0, expiryDate: null },
                "ONCEB002": { letter: "B", expirySec: 2, used: false, role: 'user', allowedUsers: [], maxUses: 1, usedCount: 0, expiryDate: null },
                "ONCEC003": { letter: "C", expirySec: 3, used: false, role: 'user', allowedUsers: [], maxUses: 1, usedCount: 0, expiryDate: null },
                "ONCED004": { letter: "D", expirySec: 4, used: false, role: 'user', allowedUsers: [], maxUses: 1, usedCount: 0, expiryDate: null },
                "ONCEE005": { letter: "E", expirySec: 5, used: false, role: 'user', allowedUsers: [], maxUses: 1, usedCount: 0, expiryDate: null },
                "ONCEF006": { letter: "F", expirySec: 6, used: false, role: 'user', allowedUsers: [], maxUses: 1, usedCount: 0, expiryDate: null },
                "ONCEG007": { letter: "G", expirySec: 7, used: false, role: 'user', allowedUsers: [], maxUses: 1, usedCount: 0, expiryDate: null },
                "ONCEH008": { letter: "H", expirySec: 8, used: false, role: 'user', allowedUsers: [], maxUses: 1, usedCount: 0, expiryDate: null },
                "ONCEI009": { letter: "I", expirySec: 9, used: false, role: 'user', allowedUsers: [], maxUses: 1, usedCount: 0, expiryDate: null },
                "ONCEJ010": { letter: "J", expirySec: 10, used: false, role: 'user', allowedUsers: [], maxUses: 1, usedCount: 0, expiryDate: null },
                "ONCEK011": { letter: "K", expirySec: 11, used: false, role: 'user', allowedUsers: [], maxUses: 1, usedCount: 0, expiryDate: null },
                "ONCEL012": { letter: "L", expirySec: 12, used: false, role: 'user', allowedUsers: [], maxUses: 1, usedCount: 0, expiryDate: null },
                "ONCEM013": { letter: "M", expirySec: 13, used: false, role: 'user', allowedUsers: [], maxUses: 1, usedCount: 0, expiryDate: null },
                "ONCEN014": { letter: "N", expirySec: 14, used: false, role: 'user', allowedUsers: [], maxUses: 1, usedCount: 0, expiryDate: null },
                "ONCEO015": { letter: "O", expirySec: 15, used: false, role: 'user', allowedUsers: [], maxUses: 1, usedCount: 0, expiryDate: null },
                "ONCEP016": { letter: "P", expirySec: 16, used: false, role: 'user', allowedUsers: [], maxUses: 1, usedCount: 0, expiryDate: null },
                "ONCEQ017": { letter: "Q", expirySec: 17, used: false, role: 'user', allowedUsers: [], maxUses: 1, usedCount: 0, expiryDate: null },
                "ONCER018": { letter: "R", expirySec: 18, used: false, role: 'user', allowedUsers: [], maxUses: 1, usedCount: 0, expiryDate: null },
                "ONCES019": { letter: "S", expirySec: 19, used: false, role: 'user', allowedUsers: [], maxUses: 1, usedCount: 0, expiryDate: null },
                "ONCET020": { letter: "T", expirySec: 20, used: false, role: 'user', allowedUsers: [], maxUses: 1, usedCount: 0, expiryDate: null },
                "ONCEU021": { letter: "U", expirySec: 21, used: false, role: 'user', allowedUsers: [], maxUses: 1, usedCount: 0, expiryDate: null },
                "ONCEV022": { letter: "V", expirySec: 22, used: false, role: 'user', allowedUsers: [], maxUses: 1, usedCount: 0, expiryDate: null },
                "ONCEW023": { letter: "W", expirySec: 23, used: false, role: 'user', allowedUsers: [], maxUses: 1, usedCount: 0, expiryDate: null },
                "ONCEX024": { letter: "X", expirySec: 24, used: false, role: 'user', allowedUsers: [], maxUses: 1, usedCount: 0, expiryDate: null },
                "ONCEY025": { letter: "Y", expirySec: 25, used: false, role: 'user', allowedUsers: [], maxUses: 1, usedCount: 0, expiryDate: null },
                "ONCEZ026": { letter: "Z", expirySec: 26, used: false, role: 'user', allowedUsers: [], maxUses: 1, usedCount: 0, expiryDate: null }
            };

            // ========== 專案密碼 ==========
            const projectPasswords = {
                A: "123030", B: "456060", C: "789090", D: "111120", E: "222150",
                F: "333180", G: "444210", H: "555240", I: "666270", J: "777300",
                K: "888330", L: "999360", M: "121390", N: "232420", O: "343450",
                P: "454480", Q: "565510", R: "676540", S: "787570", T: "898600",
                U: "909630", V: "101660", W: "202690", X: "303720", Y: "404750", Z: "505780"
            };

            // ========== 專案資料 (含子專案) ==========
            const projectData = {
                A: { name: 'A 智慧城市', desc: 'AI 城市管理平台', downloadUrl: '#', imageUrl: 'https://picsum.photos/200/150?random=1', subProjects: [] },
                B: { name: 'B 區塊鏈金融', desc: '去中心化金融應用', downloadUrl: '#', imageUrl: 'https://picsum.photos/200/150?random=2', subProjects: [] },
                C: { name: 'C 物聯網', desc: '工業 4.0 解決方案', downloadUrl: '#', imageUrl: 'https://picsum.photos/200/150?random=3', subProjects: [] },
                D: { name: 'D 大數據', desc: '資料分析平台', downloadUrl: '#', imageUrl: 'https://picsum.photos/200/150?random=4', subProjects: [] },
                E: { name: 'E 雲端運算', desc: '混合雲管理', downloadUrl: '#', imageUrl: 'https://picsum.photos/200/150?random=5', subProjects: [] },
                F: { name: 'F 邊緣計算', desc: '邊緣節點管理', downloadUrl: '#', imageUrl: 'https://picsum.photos/200/150?random=6', subProjects: [] },
                G: { name: 'G 5G 通訊', desc: '5G 網路切片', downloadUrl: '#', imageUrl: 'https://picsum.photos/200/150?random=7', subProjects: [] },
                H: { name: 'H 資安防護', desc: '零信任架構', downloadUrl: '#', imageUrl: 'https://picsum.photos/200/150?random=8', subProjects: [] },
                I: { name: 'I 數位分身', desc: '虛擬工廠', downloadUrl: '#', imageUrl: 'https://picsum.photos/200/150?random=9', subProjects: [] },
                J: { name: 'J 量子運算', desc: '量子模擬', downloadUrl: '#', imageUrl: 'https://picsum.photos/200/150?random=10', subProjects: [] },
                K: { name: 'K 機器學習', desc: 'MLOps 平台', downloadUrl: '#', imageUrl: 'https://picsum.photos/200/150?random=11', subProjects: [] },
                L: { name: 'L 自然語言', desc: '對話式 AI', downloadUrl: '#', imageUrl: 'https://picsum.photos/200/150?random=12', subProjects: [] },
                M: { name: 'M 電腦視覺', desc: '影像辨識', downloadUrl: '#', imageUrl: 'https://picsum.photos/200/150?random=13', subProjects: [] },
                N: { name: 'N 推薦系統', desc: '個人化推薦', downloadUrl: '#', imageUrl: 'https://picsum.photos/200/150?random=14', subProjects: [] },
                O: { name: 'O 自動駕駛', desc: '感知融合', downloadUrl: '#', imageUrl: 'https://picsum.photos/200/150?random=15', subProjects: [] },
                P: { name: 'P 智慧醫療', desc: '遠距診療', downloadUrl: '#', imageUrl: 'https://picsum.photos/200/150?random=16', subProjects: [] },
                Q: { name: 'Q 智慧農業', desc: '精準灌溉', downloadUrl: '#', imageUrl: 'https://picsum.photos/200/150?random=17', subProjects: [] },
                R: { name: 'R 智慧零售', desc: '庫存管理', downloadUrl: '#', imageUrl: 'https://picsum.photos/200/150?random=18', subProjects: [] },
                S: { name: 'S 智慧製造', desc: '預測維護', downloadUrl: '#', imageUrl: 'https://picsum.photos/200/150?random=19', subProjects: [] },
                T: { name: 'T 智慧電網', desc: '能源調度', downloadUrl: '#', imageUrl: 'https://picsum.photos/200/150?random=20', subProjects: [] },
                U: { name: 'U 智慧家居', desc: '物聯網控制', downloadUrl: '#', imageUrl: 'https://picsum.photos/200/150?random=21', subProjects: [] },
                V: { name: 'V 智慧辦公', desc: '自動化流程', downloadUrl: '#', imageUrl: 'https://picsum.photos/200/150?random=22', subProjects: [] },
                W: { name: 'W 智慧物流', desc: '路徑優化', downloadUrl: '#', imageUrl: 'https://picsum.photos/200/150?random=23', subProjects: [] },
                X: { name: 'X 智慧交通', desc: '車流預測', downloadUrl: '#', imageUrl: 'https://picsum.photos/200/150?random=24', subProjects: [] },
                Y: { name: 'Y 智慧環保', desc: '汙染監測', downloadUrl: '#', imageUrl: 'https://picsum.photos/200/150?random=25', subProjects: [] },
                Z: { name: 'Z 智慧能源', desc: '再生能源管理', downloadUrl: '#', imageUrl: 'https://picsum.photos/200/150?random=26', subProjects: [] },
            };

            // ========== 人物資料 (全局 100 人) ==========
            const peopleData = [];
            for (let j = 1; j <= 100; j++) {
                peopleData.push({ name: '', occupation: '', class: '', number: '', email: '' });
            }
            peopleData[0] = { name: '張三', occupation: '學生', class: '三年一班', number: '12', email: 'zhang@school.com' };
            peopleData[1] = { name: '李四', occupation: '老師', class: '', number: '', email: 'li@school.com' };
            peopleData[2] = { name: '王五', occupation: '工程師', class: '', number: '', email: 'wang@company.com' };

            // ========== 輔助函數 ==========
            function showToast(msg, duration = 2000) {
                toast.textContent = msg; toast.classList.add('show');
                setTimeout(() => toast.classList.remove('show'), duration);
            }

            function setButtonLoading(btn, isLoading) {
                if (!btn) return;
                if (isLoading) {
                    btn.classList.add('loading');
                    btn.disabled = true;
                } else {
                    btn.classList.remove('loading');
                    btn.disabled = false;
                }
            }

            function isUnlocked(letter) {
                if (!currentUser.unlockedProjects) return false;
                const data = currentUser.unlockedProjects[letter];
                if (!data) return false;
                if (data.expiry && Date.now() > data.expiry) {
                    delete currentUser.unlockedProjects[letter];
                    return false;
                }
                return true;
            }

            function unlockProject(letter, expirySec) {
                if (!currentUser.unlockedProjects) currentUser.unlockedProjects = {};
                const expiry = Date.now() + expirySec * 1000;
                currentUser.unlockedProjects[letter] = { expiry };
            }

            function generateAccountId(username, role) {
                if (role === 'creator') return 'CREATOR-0';
                return role.toUpperCase() + '-' + Math.random().toString(36).substring(2,8).toUpperCase();
            }

            function logUserLogin(username, role, accountId) {
                userLogs.push({
                    username,
                    role,
                    accountId,
                    loginTime: new Date().toLocaleString()
                });
            }

            // ========== 登入成功處理 ==========
            function loginSuccess(username, role, accountId, targetLetter = null) {
                currentUser.username = username;
                currentUser.role = role;
                currentUser.accountId = accountId;

                logUserLogin(username, role, accountId);

                const isCreator = (role === 'creator');
                peopleNav.style.display = isCreator ? 'flex' : 'none';
                passwordVaultNav.style.display = isCreator ? 'flex' : 'none';
                userManagementNav.style.display = isCreator ? 'flex' : 'none';
                subProjectAdminNav.style.display = isCreator ? 'flex' : 'none';
                sourceCodeAdminNav.style.display = isCreator ? 'flex' : 'none';

                authCard.style.display = 'none';
                mainPanel.style.display = 'flex';

                navItems.forEach(item => {
                    item.classList.remove('active');
                    item.addEventListener('click', (e) => {
                        navItems.forEach(n => n.classList.remove('active'));
                        item.classList.add('active');
                        renderPage(item.dataset.page);
                    });
                });

                if (targetLetter) {
                    navItems.forEach(n => n.classList.remove('active'));
                    document.querySelector('[data-page="projects"]').classList.add('active');
                    showProjectDetail(targetLetter);
                } else {
                    navItems.forEach(n => n.classList.remove('active'));
                    document.querySelector('[data-page="home"]').classList.add('active');
                    renderPage('home');
                }

                showToast(`${t('welcome')}，${username} (${t('role_' + role)})`);
            }

            // ========== 渲染頁面 ==========
            function renderPage(page) {
                let html = '';
                if (page === 'home') {
                    html = `<div class="page-card">
                        <div class="page-title"><i class="fas fa-home"></i> ${t('home')}</div>
                        <p>${t('welcome')}，${currentUser.username} (${t('role_' + currentUser.role)})</p>
                        <p>${t('accountId')}：${currentUser.accountId}</p>
                        <hr style="margin:20px 0; border-color:#2d3f5e;">
                        <h3>${t('quickCommand')}</h3>
                        <div class="source-type" style="margin-top:15px;">
                            <div class="source-type-btn active" id="homePermBtn">${t('permSource')}</div>
                            <div class="source-type-btn" id="homeOnceBtn">${t('onceSource')}</div>
                        </div>
                        <div class="source-input-group">
                            <input type="text" id="homeSourceCode" placeholder="${t('enterSource')}">
                            <button id="homeUnlockBtn">${t('execute')}</button>
                        </div>
                        <div id="homeSourceMsg" style="color:#f87171; margin-top:10px;"></div>
                    </div>`;
                    setTimeout(() => {
                        let homeSourceType = 'permanent';
                        document.getElementById('homePermBtn').addEventListener('click', function() {
                            document.getElementById('homePermBtn').classList.add('active');
                            document.getElementById('homeOnceBtn').classList.remove('active');
                            homeSourceType = 'permanent';
                        });
                        document.getElementById('homeOnceBtn').addEventListener('click', function() {
                            document.getElementById('homeOnceBtn').classList.add('active');
                            document.getElementById('homePermBtn').classList.remove('active');
                            homeSourceType = 'once';
                        });
                        const homeUnlockBtn = document.getElementById('homeUnlockBtn');
                        homeUnlockBtn.addEventListener('click', function() {
                            setButtonLoading(this, true);
                            let code = document.getElementById('homeSourceCode').value.trim();
                            let source;
                            if (homeSourceType === 'permanent') {
                                source = permSourceCodes[code];
                            } else {
                                source = onceSourceCodes[code];
                            }
                            let msgDiv = document.getElementById('homeSourceMsg');
                            setTimeout(() => {
                                setButtonLoading(homeUnlockBtn, false);
                                if (!source) {
                                    msgDiv.textContent = '❌ ' + t('permCodeError');
                                    return;
                                }
                                if (homeSourceType === 'once' && source.used) {
                                    msgDiv.textContent = '❌ ' + t('onceCodeError');
                                    return;
                                }
                                if (homeSourceType === 'once') {
                                    source.used = true;
                                }
                                unlockProject(source.letter, source.expirySec);
                                showToast(`🔓 ${t('unlockSuccess')} (${source.expirySec}${t('seconds')})`);
                                msgDiv.textContent = '✅ ' + t('unlockSuccess');
                            }, 500);
                        });
                    }, 0);
                } else if (page === 'projects') {
                    html = `<div class="page-card">
                        <div class="page-title"><i class="fas fa-folder"></i> ${t('projects')}</div>
                        <div class="project-grid" id="projectGrid">`;
                    for (let i = 65; i <= 90; i++) {
                        let letter = String.fromCharCode(i);
                        let unlocked = isUnlocked(letter);
                        let proj = projectData[letter] || { name: letter+' '+t('project'), desc: '' };
                        html += `<div class="project-card ${unlocked ? 'unlocked' : 'locked'}" data-letter="${letter}">
                            <div class="letter">${letter}</div>
                            <div class="name">${unlocked ? proj.name : ''}</div>
                            <div class="lock-icon"><i class="fas ${unlocked ? 'fa-lock-open' : 'fa-lock'}"></i></div>
                        </div>`;
                    }
                    html += `</div></div>`;
                    setTimeout(() => {
                        document.querySelectorAll('.project-card').forEach(card => {
                            card.addEventListener('click', () => {
                                let letter = card.dataset.letter;
                                showProjectDetail(letter);
                            });
                        });
                    }, 0);
                } else if (page === 'people') {
                    if (currentUser.role !== 'creator') {
                        html = `<div class="page-card"><div class="page-title">⛔ ${t('permissionDenied')}</div><p>${t('onlyCreator')}</p></div>`;
                    } else {
                        html = `<div class="page-card">
                            <div class="page-title"><i class="fas fa-users"></i> ${t('personData')}</div>
                            <div class="person-selector">
                                <select id="peopleIndexSelect">`;
                        for (let i = 1; i <= 100; i++) html += `<option value="${i}">${t('person')} ${i}</option>`;
                        html += `</select>
                            </div>
                            <div id="peopleDetail" class="person-details">`;
                        for (let key of [t('name'), t('occupationLabel'), t('classLabel'), t('numberLabel'), t('email')]) {
                            html += `<div class="person-detail-item"><span class="person-detail-label">${key}</span><span class="person-detail-value" id="person${key}">-</span></div>`;
                        }
                        html += `</div></div>`;
                        setTimeout(() => {
                            const indexSelect = document.getElementById('peopleIndexSelect');
                            function updatePerson() {
                                let idx = parseInt(indexSelect.value) - 1;
                                let p = peopleData[idx] || {};
                                document.getElementById(`person${t('name')}`).textContent = p.name || t('notFilled');
                                document.getElementById(`person${t('occupationLabel')}`).textContent = p.occupation || t('notFilled');
                                document.getElementById(`person${t('classLabel')}`).textContent = p.class || t('notFilled');
                                document.getElementById(`person${t('numberLabel')}`).textContent = p.number || t('notFilled');
                                document.getElementById(`person${t('email')}`).textContent = p.email || t('notFilled');
                            }
                            indexSelect.addEventListener('change', updatePerson);
                            updatePerson();
                        }, 0);
                    }
                } else if (page === 'passwordVault') {
                    if (currentUser.role !== 'creator') {
                        html = `<div class="page-card"><div class="page-title">⛔ ${t('permissionDenied')}</div><p>${t('onlyCreator')}</p></div>`;
                    } else {
                        html = `<div class="page-card">
                            <div class="page-title"><i class="fas fa-lock"></i> ${t('passwordVault')}</div>
                            <table class="password-table">
                                <thead><tr><th>字母</th><th>密碼</th><th>操作</th></tr></thead>
                                <tbody id="passwordTableBody">`;
                        for (let i = 65; i <= 90; i++) {
                            let letter = String.fromCharCode(i);
                            let pwd = projectPasswords[letter] || '';
                            html += `<tr>
                                <td>${letter}</td>
                                <td><input type="text" id="pwd-${letter}" value="${pwd}" placeholder="密碼"></td>
                                <td><button class="auth-btn save-btn" style="padding:5px 10px;" onclick="updatePassword('${letter}')">${t('saveChanges')}</button></td>
                            </tr>`;
                        }
                        html += `</tbody></table></div>`;
                        setTimeout(() => {
                            window.updatePassword = function(letter) {
                                let newPwd = document.getElementById(`pwd-${letter}`).value.trim();
                                if (newPwd) {
                                    projectPasswords[letter] = newPwd;
                                    showToast(`✅ 密碼已更新`);
                                }
                            };
                        }, 0);
                    }
                } else if (page === 'userManagement') {
                    if (currentUser.role !== 'creator') {
                        html = `<div class="page-card"><div class="page-title">⛔ ${t('permissionDenied')}</div><p>${t('onlyCreator')}</p></div>`;
                    } else {
                        html = `<div class="page-card">
                            <div class="page-title"><i class="fas fa-user-cog"></i> ${t('userManagement')}</div>
                            <h3>新增用戶</h3>
                            <div class="form-group">
                                <label>帳號</label><input type="text" id="newUsername" class="form-control">
                            </div>
                            <div class="form-group">
                                <label>密碼</label><input type="password" id="newPassword" class="form-control">
                            </div>
                            <div class="form-group">
                                <label>帳號ID (可留空自動生成)</label><input type="text" id="newAccountId" class="form-control">
                            </div>
                            <div class="form-group">
                                <label>班級</label><input type="text" id="newClass" class="form-control">
                            </div>
                            <div class="form-group">
                                <label>座號</label><input type="text" id="newNumber" class="form-control">
                            </div>
                            <div class="form-group">
                                <label>職位</label><input type="text" id="newOccupation" class="form-control">
                            </div>
                            <div class="form-group">
                                <label>角色</label>
                                <select id="newRole" class="form-control">
                                    <option value="observer">觀察者</option>
                                    <option value="user">使用者</option>
                                    <option value="manager">管理者</option>
                                </select>
                            </div>
                            <button class="auth-btn" id="addUserBtn">新增用戶</button>
                            <hr>
                            <h3>用戶列表</h3>
                            <table class="user-table">
                                <thead><tr><th>帳號</th><th>密碼</th><th>ID</th><th>班級</th><th>座號</th><th>職位</th><th>角色</th><th>操作</th></tr></thead>
                                <tbody id="userTableBody">`;
                        for (let u in users) {
                            let user = users[u];
                            html += `<tr>
                                <td>${u}</td>
                                <td><input type="text" id="userPwd-${u}" value="${user.password}" style="width:80px;"></td>
                                <td><input type="text" id="userAcc-${u}" value="${user.accountId || ''}" style="width:100px;"></td>
                                <td><input type="text" id="userClass-${u}" value="${user.class || ''}" style="width:60px;"></td>
                                <td><input type="text" id="userNumber-${u}" value="${user.number || ''}" style="width:60px;"></td>
                                <td><input type="text" id="userOcc-${u}" value="${user.occupation || ''}" style="width:80px;"></td>
                                <td>
                                    <select id="userRole-${u}">
                                        <option value="observer" ${user.role === 'observer' ? 'selected' : ''}>觀察者</option>
                                        <option value="user" ${user.role === 'user' ? 'selected' : ''}>使用者</option>
                                        <option value="manager" ${user.role === 'manager' ? 'selected' : ''}>管理者</option>
                                    </select>
                                </td>
                                <td>
                                    <button class="auth-btn" style="padding:5px 10px;" onclick="updateUser('${u}')">更新</button>
                                    <button class="delete-btn" onclick="deleteUser('${u}')">刪除</button>
                                </td>
                            </tr>`;
                        }
                        html += `</tbody></table></div>`;
                        setTimeout(() => {
                            document.getElementById('addUserBtn').addEventListener('click', () => {
                                let newUsername = document.getElementById('newUsername').value.trim();
                                let newPassword = document.getElementById('newPassword').value.trim();
                                let newAccountId = document.getElementById('newAccountId').value.trim() || generateAccountId(newUsername, document.getElementById('newRole').value);
                                let newClass = document.getElementById('newClass').value.trim();
                                let newNumber = document.getElementById('newNumber').value.trim();
                                let newOccupation = document.getElementById('newOccupation').value.trim();
                                let newRole = document.getElementById('newRole').value;
                                if (!newUsername || !newPassword) {
                                    showToast('❌ 帳號密碼必填');
                                    return;
                                }
                                if (users[newUsername]) {
                                    showToast('❌ 帳號已存在');
                                    return;
                                }
                                users[newUsername] = {
                                    password: newPassword,
                                    accountId: newAccountId,
                                    class: newClass,
                                    number: newNumber,
                                    occupation: newOccupation,
                                    role: newRole
                                };
                                renderPage('userManagement');
                                showToast('✅ 用戶新增成功');
                            });
                            window.updateUser = function(username) {
                                let newPwd = document.getElementById(`userPwd-${username}`).value.trim();
                                let newAcc = document.getElementById(`userAcc-${username}`).value.trim();
                                let newClass = document.getElementById(`userClass-${username}`).value.trim();
                                let newNumber = document.getElementById(`userNumber-${username}`).value.trim();
                                let newOcc = document.getElementById(`userOcc-${username}`).value.trim();
                                let newRole = document.getElementById(`userRole-${username}`).value;
                                if (users[username]) {
                                    users[username].password = newPwd;
                                    users[username].accountId = newAcc;
                                    users[username].class = newClass;
                                    users[username].number = newNumber;
                                    users[username].occupation = newOcc;
                                    users[username].role = newRole;
                                    showToast('✅ 用戶更新成功');
                                }
                            };
                            window.deleteUser = function(username) {
                                if (confirm('確定刪除？')) {
                                    delete users[username];
                                    renderPage('userManagement');
                                    showToast('✅ 已刪除');
                                }
                            };
                        }, 0);
                    }
                } else if (page === 'subProjectAdmin') {
                    if (currentUser.role !== 'creator') {
                        html = `<div class="page-card"><div class="page-title">⛔ ${t('permissionDenied')}</div><p>${t('onlyCreator')}</p></div>`;
                    } else {
                        html = `<div class="page-card">
                            <div class="page-title"><i class="fas fa-layer-group"></i> 子專案管理</div>
                            <p>選擇母專案字母：</p>
                            <select id="subProjectLetter" class="form-control" style="margin-bottom:20px;">`;
                        for (let i = 65; i <= 90; i++) {
                            let letter = String.fromCharCode(i);
                            html += `<option value="${letter}">${letter}</option>`;
                        }
                        html += `</select>
                            <div id="subProjectList"></div>
                            <h3>新增子專案</h3>
                            <div class="form-group"><label>名稱</label><input type="text" id="newSubName" class="form-control"></div>
                            <div class="form-group"><label>描述</label><textarea id="newSubDesc" class="form-control"></textarea></div>
                            <div class="form-group"><label>下載連結</label><input type="text" id="newSubUrl" class="form-control" value="#"></div>
                            <div class="form-group"><label>圖片網址</label><input type="text" id="newSubImage" class="form-control" value="https://picsum.photos/200/150?random=999"></div>
                            <button class="auth-btn" id="addSubBtn">新增子專案</button>
                        </div>`;
                        setTimeout(() => {
                            function renderSubList() {
                                let letter = document.getElementById('subProjectLetter').value;
                                let listDiv = document.getElementById('subProjectList');
                                let proj = projectData[letter];
                                if (!proj) return;
                                let subs = proj.subProjects || [];
                                let html = '<h3>現有子專案</h3>';
                                subs.forEach((sub, idx) => {
                                    html += `<div class="subproject-item">
                                        <div><strong>${sub.name}</strong> <button class="delete-btn" onclick="deleteSub('${letter}', ${idx})">刪除</button></div>
                                        <div>描述：${sub.desc}</div>
                                        <div>下載：<a href="${sub.downloadUrl}" target="_blank">連結</a></div>
                                        <div>圖片：<img src="${sub.imageUrl}" style="max-width:100px;"></div>
                                    </div>`;
                                });
                                listDiv.innerHTML = html;
                            }
                            document.getElementById('subProjectLetter').addEventListener('change', renderSubList);
                            renderSubList();
                            document.getElementById('addSubBtn').addEventListener('click', () => {
                                let letter = document.getElementById('subProjectLetter').value;
                                let name = document.getElementById('newSubName').value.trim();
                                let desc = document.getElementById('newSubDesc').value.trim();
                                let url = document.getElementById('newSubUrl').value.trim();
                                let img = document.getElementById('newSubImage').value.trim();
                                if (!name) { showToast('❌ 名稱必填'); return; }
                                if (!projectData[letter].subProjects) projectData[letter].subProjects = [];
                                projectData[letter].subProjects.push({ name, desc, downloadUrl: url, imageUrl: img });
                                renderSubList();
                                document.getElementById('newSubName').value = '';
                                document.getElementById('newSubDesc').value = '';
                                document.getElementById('newSubUrl').value = '#';
                                document.getElementById('newSubImage').value = 'https://picsum.photos/200/150?random=999';
                                showToast('✅ 子專案新增成功');
                            });
                            window.deleteSub = function(letter, idx) {
                                if (confirm('確定刪除？')) {
                                    projectData[letter].subProjects.splice(idx, 1);
                                    renderSubList();
                                    showToast('✅ 已刪除');
                                }
                            };
                        }, 0);
                    }
                } else if (page === 'sourceCodeAdmin') {
                    if (currentUser.role !== 'creator') {
                        html = `<div class="page-card"><div class="page-title">⛔ ${t('permissionDenied')}</div><p>${t('onlyCreator')}</p></div>`;
                    } else {
                        html = `<div class="page-card">
                            <div class="page-title"><i class="fas fa-code"></i> 原始碼管理</div>
                            <h3>永久原始碼</h3>
                            <table class="user-table">
                                <thead><tr><th>代碼</th><th>字母</th><th>秒數</th><th>角色</th><th>操作</th></tr></thead>
                                <tbody>`;
                        for (let code in permSourceCodes) {
                            let sc = permSourceCodes[code];
                            html += `<tr>
                                <td><input type="text" id="perm-${code}" value="${code}" style="width:100px;"></td>
                                <td><input type="text" id="permLetter-${code}" value="${sc.letter}" style="width:50px;"></td>
                                <td><input type="number" id="permSec-${code}" value="${sc.expirySec}" style="width:70px;"></td>
                                <td><input type="text" id="permRole-${code}" value="${sc.role}" style="width:80px;"></td>
                                <td><button class="auth-btn" style="padding:5px 10px;" onclick="updatePerm('${code}')">更新</button></td>
                            </tr>`;
                        }
                        html += `</tbody></table>
                            <h3>新增永久原始碼</h3>
                            <div class="form-group"><label>代碼</label><input type="text" id="newPermCode" class="form-control"></div>
                            <div class="form-group"><label>字母</label><input type="text" id="newPermLetter" class="form-control" maxlength="1"></div>
                            <div class="form-group"><label>秒數</label><input type="number" id="newPermSec" class="form-control"></div>
                            <div class="form-group"><label>角色</label><input type="text" id="newPermRole" class="form-control" value="manager"></div>
                            <button class="auth-btn" id="addPermBtn">新增</button>
                            <hr>
                            <h3>一次性原始碼</h3>
                            <table class="user-table">
                                <thead><tr><th>代碼</th><th>字母</th><th>秒數</th><th>已使用</th><th>允許用戶</th><th>最大次數</th><th>到期日</th><th>操作</th></tr></thead>
                                <tbody>`;
                        for (let code in onceSourceCodes) {
                            let sc = onceSourceCodes[code];
                            html += `<tr>
                                <td><input type="text" id="once-${code}" value="${code}" style="width:100px;"></td>
                                <td><input type="text" id="onceLetter-${code}" value="${sc.letter}" style="width:50px;"></td>
                                <td><input type="number" id="onceSec-${code}" value="${sc.expirySec}" style="width:70px;"></td>
                                <td><input type="checkbox" id="onceUsed-${code}" ${sc.used ? 'checked' : ''} disabled></td>
                                <td><input type="text" id="onceUsers-${code}" value="${sc.allowedUsers ? sc.allowedUsers.join(',') : ''}" style="width:100px;" placeholder="用戶名,逗號"></td>
                                <td><input type="number" id="onceMax-${code}" value="${sc.maxUses || 1}" style="width:60px;"></td>
                                <td><input type="text" id="onceExpiry-${code}" value="${sc.expiryDate || ''}" style="width:100px;" placeholder="YYYY-MM-DD"></td>
                                <td><button class="auth-btn" style="padding:5px 10px;" onclick="updateOnce('${code}')">更新</button></td>
                            </tr>`;
                        }
                        html += `</tbody></table>
                            <h3>新增一次性原始碼</h3>
                            <div class="form-group"><label>代碼</label><input type="text" id="newOnceCode" class="form-control"></div>
                            <div class="form-group"><label>字母</label><input type="text" id="newOnceLetter" class="form-control" maxlength="1"></div>
                            <div class="form-group"><label>秒數</label><input type="number" id="newOnceSec" class="form-control"></div>
                            <div class="form-group"><label>允許用戶 (逗號分隔)</label><input type="text" id="newOnceUsers" class="form-control"></div>
                            <div class="form-group"><label>最大使用次數</label><input type="number" id="newOnceMax" class="form-control" value="1"></div>
                            <div class="form-group"><label>到期日 (YYYY-MM-DD)</label><input type="text" id="newOnceExpiry" class="form-control"></div>
                            <button class="auth-btn" id="addOnceBtn">新增</button>
                        </div>`;
                        setTimeout(() => {
                            window.updatePerm = function(code) {
                                let newCode = document.getElementById(`perm-${code}`).value.trim();
                                let letter = document.getElementById(`permLetter-${code}`).value.trim().toUpperCase();
                                let sec = parseInt(document.getElementById(`permSec-${code}`).value);
                                let role = document.getElementById(`permRole-${code}`).value.trim();
                                if (newCode && letter && sec && role) {
                                    delete permSourceCodes[code];
                                    permSourceCodes[newCode] = { letter, expirySec: sec, role };
                                    renderPage('sourceCodeAdmin');
                                    showToast('✅ 已更新');
                                }
                            };
                            window.updateOnce = function(code) {
                                let newCode = document.getElementById(`once-${code}`).value.trim();
                                let letter = document.getElementById(`onceLetter-${code}`).value.trim().toUpperCase();
                                let sec = parseInt(document.getElementById(`onceSec-${code}`).value);
                                let usersStr = document.getElementById(`onceUsers-${code}`).value.trim();
                                let allowedUsers = usersStr ? usersStr.split(',').map(s => s.trim()) : [];
                                let maxUses = parseInt(document.getElementById(`onceMax-${code}`).value);
                                let expiryDate = document.getElementById(`onceExpiry-${code}`).value.trim();
                                if (newCode && letter && sec) {
                                    delete onceSourceCodes[code];
                                    onceSourceCodes[newCode] = {
                                        letter,
                                        expirySec: sec,
                                        used: false,
                                        role: 'user',
                                        allowedUsers,
                                        maxUses,
                                        usedCount: 0,
                                        expiryDate: expiryDate || null
                                    };
                                    renderPage('sourceCodeAdmin');
                                    showToast('✅ 已更新');
                                }
                            };
                            document.getElementById('addPermBtn').addEventListener('click', () => {
                                let code = document.getElementById('newPermCode').value.trim();
                                let letter = document.getElementById('newPermLetter').value.trim().toUpperCase();
                                let sec = parseInt(document.getElementById('newPermSec').value);
                                let role = document.getElementById('newPermRole').value.trim();
                                if (!code || !letter || !sec || !role) { showToast('❌ 請填寫完整'); return; }
                                if (permSourceCodes[code]) { showToast('❌ 代碼已存在'); return; }
                                permSourceCodes[code] = { letter, expirySec: sec, role };
                                renderPage('sourceCodeAdmin');
                                showToast('✅ 新增成功');
                            });
                            document.getElementById('addOnceBtn').addEventListener('click', () => {
                                let code = document.getElementById('newOnceCode').value.trim();
                                let letter = document.getElementById('newOnceLetter').value.trim().toUpperCase();
                                let sec = parseInt(document.getElementById('newOnceSec').value);
                                let usersStr = document.getElementById('newOnceUsers').value.trim();
                                let allowedUsers = usersStr ? usersStr.split(',').map(s => s.trim()) : [];
                                let maxUses = parseInt(document.getElementById('newOnceMax').value);
                                let expiryDate = document.getElementById('newOnceExpiry').value.trim();
                                if (!code || !letter || !sec) { showToast('❌ 請填寫代碼、字母、秒數'); return; }
                                if (onceSourceCodes[code]) { showToast('❌ 代碼已存在'); return; }
                                onceSourceCodes[code] = {
                                    letter,
                                    expirySec: sec,
                                    used: false,
                                    role: 'user',
                                    allowedUsers,
                                    maxUses,
                                    usedCount: 0,
                                    expiryDate: expiryDate || null
                                };
                                renderPage('sourceCodeAdmin');
                                showToast('✅ 新增成功');
                            });
                        }, 0);
                    }
                } else if (page === 'settings') {
                    html = `<div class="page-card">
                        <div class="page-title"><i class="fas fa-cog"></i> ${t('settingsTitle')}</div>
                        <div class="settings-item">
                            <span>${t('accountName')}</span>
                            <span class="value">${currentUser.username}</span>
                        </div>
                        <div class="settings-item">
                            <span>${t('passwordLabel')}</span>
                            <span class="value dots">••••••••</span>
                        </div>
                        <div class="settings-item">
                            <span>${t('accountId')}</span>
                            <span class="value">${currentUser.accountId}</span>
                        </div>
                        <div class="settings-item">
                            <span>${t('language')}</span>
                            <select class="language-select" id="langSelect">
                                <option value="zh-TW" ${currentLang === 'zh-TW' ? 'selected' : ''}>繁體中文</option>
                                <option value="en" ${currentLang === 'en' ? 'selected' : ''}>English</option>
                            </select>
                        </div>
                        <div class="settings-item">
                            <span>${t('theme')}</span>
                            <div class="theme-toggle">
                                <div class="theme-option ${document.body.classList.contains('light-mode') ? 'active' : ''}" id="lightTheme">${t('light')}</div>
                                <div class="theme-option ${document.body.classList.contains('dark-mode') ? 'active' : ''}" id="darkTheme">${t('dark')}</div>
                            </div>
                        </div>
                        <div class="settings-item">
                            <span>${t('version')}</span>
                            <div class="version-info">
                                <span class="value">v2.0.0</span>
                                <button class="auth-btn" style="padding:5px 15px;" id="checkUpdate">${t('checkUpdate')}</button>
                            </div>
                        </div>
                        <button class="logout-btn" id="logoutBtn"><i class="fas fa-sign-out-alt"></i> ${t('logout')}</button>
                    </div>`;
                    setTimeout(() => {
                        document.getElementById('logoutBtn').addEventListener('click', logout);
                        const checkUpdateBtn = document.getElementById('checkUpdate');
                        checkUpdateBtn.addEventListener('click', function() {
                            setButtonLoading(this, true);
                            setTimeout(() => {
                                setButtonLoading(this, false);
                                showToast(`✅ ${t('checkComplete')}`);
                            }, 1500);
                        });
                        document.getElementById('langSelect').addEventListener('change', (e) => {
                            setLanguage(e.target.value);
                        });
                        document.getElementById('lightTheme').addEventListener('click', () => {
                            document.body.classList.remove('dark-mode');
                            document.body.classList.add('light-mode');
                            document.getElementById('lightTheme').classList.add('active');
                            document.getElementById('darkTheme').classList.remove('active');
                            localStorage.setItem('theme', 'light');
                        });
                        document.getElementById('darkTheme').addEventListener('click', () => {
                            document.body.classList.remove('light-mode');
                            document.body.classList.add('dark-mode');
                            document.getElementById('darkTheme').classList.add('active');
                            document.getElementById('lightTheme').classList.remove('active');
                            localStorage.setItem('theme', 'dark');
                        });
                    }, 0);
                }
                contentArea.innerHTML = html;
            }

            // 顯示專案詳情（需整合子專案）
            function showProjectDetail(letter) {
                let proj = projectData[letter] || { name: letter+' '+t('project'), desc: '', downloadUrl: '#', imageUrl: '', subProjects: [] };
                let unlocked = isUnlocked(letter);
                let expiryTime = currentUser.unlockedProjects[letter]?.expiry;
                let remaining = expiryTime ? Math.max(0, Math.floor((expiryTime - Date.now()) / 1000)) : 0;

                let html = `<div class="page-card">
                    <div class="page-title">${t('project')} ${letter} ${t('details')} <button class="auth-btn" style="padding:8px 15px; margin-left:auto;" onclick="renderPage('projects')">${t('back')}</button></div>
                    <div class="project-detail">`;
                if (unlocked) {
                    html += `<h2>${proj.name}</h2>
                        <p>${proj.desc}</p>
                        <img src="${proj.imageUrl}" alt="project image">
                        <div class="timer" id="timer-${letter}">${t('remaining')}：${remaining} ${t('seconds')}</div>
                        <a href="${proj.downloadUrl}" class="download-btn" download><i class="fas fa-download"></i> ${t('download')}</a>`;
                    if (proj.subProjects && proj.subProjects.length > 0) {
                        html += `<h3>子專案</h3>`;
                        proj.subProjects.forEach((sub, idx) => {
                            html += `<div class="subproject-item">
                                <h4>${sub.name}</h4>
                                <p>${sub.desc}</p>
                                <img src="${sub.imageUrl}" style="max-width:200px;"><br>
                                <a href="${sub.downloadUrl}" class="download-btn" download>下載子專案</a>
                            </div>`;
                        });
                    }
                } else {
                    html += `
                        <div class="unlock-section">
                            <h3><i class="fas fa-key"></i> ${t('unlockWithSource')}</h3>
                            <div class="source-type">
                                <div class="source-type-btn active" data-type="permanent">${t('permSource')}</div>
                                <div class="source-type-btn" data-type="once">${t('onceSource')}</div>
                            </div>
                            <div class="source-input-group">
                                <input type="text" id="sourceCode" placeholder="${t('enterSource')}">
                                <button id="unlockWithSource">${t('confirm')}</button>
                            </div>
                            <div id="sourceMsg" style="color:#f87171; margin-top:10px;"></div>
                        </div>
                        <div class="unlock-section">
                            <h3><i class="fas fa-lock"></i> ${t('unlockWithPassword')}</h3>
                            <div class="source-input-group">
                                <input type="text" id="projectPassword" placeholder="${t('enterPassword')}">
                                <button id="unlockWithPassword">${t('confirm')}</button>
                            </div>
                            <div id="passwordMsg" style="color:#f87171; margin-top:10px;"></div>
                        </div>
                    `;
                }
                html += `</div></div>`;
                contentArea.innerHTML = html;

                if (unlocked) {
                    const timerEl = document.getElementById(`timer-${letter}`);
                    const interval = setInterval(() => {
                        let expiry = currentUser.unlockedProjects[letter]?.expiry;
                        if (!expiry) {
                            clearInterval(interval);
                            timerEl.textContent = t('expired');
                            return;
                        }
                        let remaining = Math.max(0, Math.floor((expiry - Date.now()) / 1000));
                        timerEl.textContent = `${t('remaining')}：${remaining} ${t('seconds')}`;
                        if (remaining <= 0) {
                            clearInterval(interval);
                            delete currentUser.unlockedProjects[letter];
                            showProjectDetail(letter);
                        }
                    }, 1000);
                } else {
                    let sourceType = 'permanent';
                    document.querySelectorAll('.source-type-btn').forEach(btn => {
                        btn.addEventListener('click', function() {
                            document.querySelectorAll('.source-type-btn').forEach(b => b.classList.remove('active'));
                            this.classList.add('active');
                            sourceType = this.dataset.type;
                        });
                    });

                    const unlockSourceBtn = document.getElementById('unlockWithSource');
                    if (unlockSourceBtn) {
                        unlockSourceBtn.addEventListener('click', function() {
                            setButtonLoading(this, true);
                            let code = document.getElementById('sourceCode').value.trim();
                            let source;
                            if (sourceType === 'permanent') {
                                source = permSourceCodes[code];
                            } else {
                                source = onceSourceCodes[code];
                            }
                            let msgDiv = document.getElementById('sourceMsg');
                            setTimeout(() => {
                                setButtonLoading(unlockSourceBtn, false);
                                if (!source) {
                                    msgDiv.textContent = '❌ ' + t('permCodeError');
                                    return;
                                }
                                if (sourceType === 'once') {
                                    if (source.expiryDate && new Date(source.expiryDate) < new Date()) {
                                        msgDiv.textContent = '❌ 原始碼已過期';
                                        return;
                                    }
                                    if (source.maxUses && source.usedCount >= source.maxUses) {
                                        msgDiv.textContent = '❌ 原始碼已達使用次數上限';
                                        return;
                                    }
                                    if (source.allowedUsers && source.allowedUsers.length > 0 && !source.allowedUsers.includes(currentUser.username)) {
                                        msgDiv.textContent = '❌ 您無權使用此原始碼';
                                        return;
                                    }
                                    source.usedCount = (source.usedCount || 0) + 1;
                                    if (source.maxUses && source.usedCount >= source.maxUses) {
                                        source.used = true;
                                    }
                                }
                                if (source.letter !== letter) {
                                    msgDiv.textContent = `❌ ${t('codeFor')} ${source.letter}`;
                                    return;
                                }
                                unlockProject(letter, source.expirySec);
                                showToast(`🔓 ${t('unlockSuccess')} (${source.expirySec}${t('seconds')})`);
                                showProjectDetail(letter);
                            }, 500);
                        });
                    }

                    const unlockPwdBtn = document.getElementById('unlockWithPassword');
                    if (unlockPwdBtn) {
                        unlockPwdBtn.addEventListener('click', function() {
                            setButtonLoading(this, true);
                            let pwd = document.getElementById('projectPassword').value.trim();
                            let correct = projectPasswords[letter];
                            let msgDiv = document.getElementById('passwordMsg');
                            setTimeout(() => {
                                setButtonLoading(unlockPwdBtn, false);
                                if (!correct) {
                                    msgDiv.textContent = '❌ ' + t('noPassword');
                                    return;
                                }
                                if (pwd !== correct) {
                                    msgDiv.textContent = '❌ ' + t('wrongPassword');
                                    return;
                                }
                                let expirySec = parseInt(correct.slice(-3));
                                if (isNaN(expirySec) || expirySec < 1) expirySec = 30;
                                unlockProject(letter, expirySec);
                                showToast(`🔓 ${t('unlockSuccess')} (${expirySec}${t('seconds')})`);
                                showProjectDetail(letter);
                            }, 500);
                        });
                    }
                }
            }

            // ========== 登入處理 ==========
            loginBtn.addEventListener('click', (e) => {
                e.preventDefault();
                setButtonLoading(loginBtn, true);
                let username = document.getElementById('loginUsername').value.trim();
                let password = document.getElementById('loginPassword').value.trim();
                if (username === '0') {
                    let accountId = generateAccountId('0', 'creator');
                    loginSuccess('0', 'creator', accountId);
                    setButtonLoading(loginBtn, false);
                    return;
                }
                if (!users[username] || users[username].password !== password) {
                    loginError.style.display = 'flex';
                    authCard.classList.add('shake');
                    setTimeout(() => authCard.classList.remove('shake'), 500);
                    setButtonLoading(loginBtn, false);
                    return;
                }
                let role = users[username].role || 'observer';
                let accountId = users[username].accountId || generateAccountId(username, role);
                loginSuccess(username, role, accountId);
                setButtonLoading(loginBtn, false);
            });

            registerBtn.addEventListener('click', (e) => {
                e.preventDefault();
                setButtonLoading(registerBtn, true);
                let username = document.getElementById('regUsername').value.trim();
                let password = document.getElementById('regPassword').value.trim();
                let regClass = document.getElementById('regClass').value.trim();
                let regNumber = document.getElementById('regNumber').value.trim();

                if (!username || !password) {
                    registerError.innerHTML = '❌ ' + t('required'); registerError.style.display = 'flex';
                    setButtonLoading(registerBtn, false);
                    return;
                }
                if (username === '0') {
                    registerError.innerHTML = '❌ ' + t('cannotRegister'); registerError.style.display = 'flex';
                    setButtonLoading(registerBtn, false);
                    return;
                }
                if (users[username]) {
                    registerError.innerHTML = '❌ ' + t('userExists'); registerError.style.display = 'flex';
                    setButtonLoading(registerBtn, false);
                    return;
                }
                if (!regClass || !regNumber) {
                    registerError.innerHTML = '❌ ' + t('classNumberRequired'); registerError.style.display = 'flex';
                    setButtonLoading(registerBtn, false);
                    return;
                }
                let accountId = generateAccountId(username, 'observer');
                users[username] = { password, role: 'observer', accountId, class: regClass, number: regNumber, occupation: '學生' };
                showToast('✅ ' + t('registerSuccess'));
                loginTab.click();
                document.getElementById('regUsername').value = '';
                document.getElementById('regPassword').value = '';
                document.getElementById('regClass').value = '';
                document.getElementById('regNumber').value = '';
                registerError.style.display = 'none';
                setButtonLoading(registerBtn, false);
            });

            permCodeBtn.addEventListener('click', (e) => {
                e.preventDefault();
                setButtonLoading(permCodeBtn, true);
                let code = document.getElementById('permCode').value.trim();
                let source = permSourceCodes[code];
                if (!source) {
                    permCodeError.style.display = 'flex';
                    setTimeout(() => permCodeError.style.display = 'none', 2000);
                    setButtonLoading(permCodeBtn, false);
                    return;
                }
                let username = `MGR-${source.letter}`;
                let role = source.role || 'manager';
                let accountId = generateAccountId(username, role);
                unlockProject(source.letter, source.expirySec);
                loginSuccess(username, role, accountId, source.letter);
                setButtonLoading(permCodeBtn, false);
            });

            onceCodeBtn.addEventListener('click', (e) => {
                e.preventDefault();
                setButtonLoading(onceCodeBtn, true);
                let code = document.getElementById('onceCode').value.trim();
                let source = onceSourceCodes[code];
                if (!source || source.used) {
                    onceCodeError.style.display = 'flex';
                    setTimeout(() => onceCodeError.style.display = 'none', 2000);
                    setButtonLoading(onceCodeBtn, false);
                    return;
                }
                if (source.expiryDate && new Date(source.expiryDate) < new Date()) {
                    onceCodeError.style.display = 'flex';
                    setTimeout(() => onceCodeError.style.display = 'none', 2000);
                    setButtonLoading(onceCodeBtn, false);
                    return;
                }
                if (source.maxUses && source.usedCount >= source.maxUses) {
                    onceCodeError.style.display = 'flex';
                    setTimeout(() => onceCodeError.style.display = 'none', 2000);
                    setButtonLoading(onceCodeBtn, false);
                    return;
                }
                if (source.allowedUsers && source.allowedUsers.length > 0 && !source.allowedUsers.includes(currentUser?.username)) {
                    // 未登入時 currentUser 可能為空，但原始碼登入會產生新用戶
                    // 這裡簡單處理，不綁定用戶
                }
                source.usedCount = (source.usedCount || 0) + 1;
                if (source.maxUses && source.usedCount >= source.maxUses) {
                    source.used = true;
                }
                let username = `USR-${source.letter}`;
                let role = source.role || 'user';
                let accountId = generateAccountId(username, role);
                unlockProject(source.letter, source.expirySec);
                loginSuccess(username, role, accountId, source.letter);
                setButtonLoading(onceCodeBtn, false);
            });

            function logout() {
                currentUser = { username: null, role: null, accountId: null, unlockedProjects: {} };
                mainPanel.style.display = 'none';
                authCard.style.display = 'block';
                showToast('👋 ' + t('logout'));
            }

            // 頁籤切換
            loginTab.addEventListener('click', () => {
                loginTab.classList.add('active'); registerTab.classList.remove('active');
                permCodeTab.classList.remove('active'); onceCodeTab.classList.remove('active');
                loginForm.style.display = 'flex'; registerForm.style.display = 'none';
                permCodeForm.style.display = 'none'; onceCodeForm.style.display = 'none';
            });
            registerTab.addEventListener('click', () => {
                registerTab.classList.add('active'); loginTab.classList.remove('active');
                permCodeTab.classList.remove('active'); onceCodeTab.classList.remove('active');
                registerForm.style.display = 'flex'; loginForm.style.display = 'none';
                permCodeForm.style.display = 'none'; onceCodeForm.style.display = 'none';
            });
            permCodeTab.addEventListener('click', () => {
                permCodeTab.classList.add('active'); loginTab.classList.remove('active');
                registerTab.classList.remove('active'); onceCodeTab.classList.remove('active');
                permCodeForm.style.display = 'flex'; loginForm.style.display = 'none';
                registerForm.style.display = 'none'; onceCodeForm.style.display = 'none';
            });
            onceCodeTab.addEventListener('click', () => {
                onceCodeTab.classList.add('active'); loginTab.classList.remove('active');
                registerTab.classList.remove('active'); permCodeTab.classList.remove('active');
                onceCodeForm.style.display = 'flex'; loginForm.style.display = 'none';
                registerForm.style.display = 'none'; permCodeForm.style.display = 'none';
            });

            // 初始化
            loginError.style.display = 'none';
            registerError.style.display = 'none';
            permCodeError.style.display = 'none';
            onceCodeError.style.display = 'none';
            authCard.style.display = 'block';
            mainPanel.style.display = 'none';

            const savedTheme = localStorage.getItem('theme');
            if (savedTheme === 'light') {
                document.body.classList.remove('dark-mode');
                document.body.classList.add('light-mode');
            } else {
                document.body.classList.remove('light-mode');
                document.body.classList.add('dark-mode');
            }

            applyLanguage();
        })();
    </script>

    <!-- ========== 🛠️ 修改教學 ========== -->
    <!--
        🔧 一般用戶帳號：在 users 物件中修改 (約 120 行)
        🔧 永久原始碼清單：在 permSourceCodes 物件中修改 (約 140 行)
        🔧 一次性原始碼清單：在 onceSourceCodes 物件中修改 (約 170 行)
        🔧 專案密碼：在 projectPasswords 物件中修改 (約 200 行)
        🔧 專案資料 (含子專案)：在 projectData 物件中修改 (約 220 行)
        🔧 人物資料：在 peopleData 陣列中修改 (約 250 行)
        🔧 帳號ID生成規則：在 generateAccountId 函數中修改 (約 290 行)
        🔧 超級用戶帳號固定為 "0"，登入後可看到所有管理頁面
    -->
    <!-- ================================== -->
</body>
</html>
