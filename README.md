<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=yes">
    <title>🔐 A-Z 專案 · 超級用戶0＋原始碼導向</title>
    <!-- 字體 Inter -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:ital,opsz,wght@0,14..32,100..900;1,14..32,100..900&display=swap" rel="stylesheet">
    <!-- Font Awesome 6 -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <style>
        /* 樣式與前完全相同，為節省篇幅此處省略，但完整程式碼需包含所有樣式 */
        * { margin: 0; padding: 0; box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        html, body { height: 100%; overflow: hidden; font-family: "Inter", sans-serif; }
        body {
            background: #0a0f1e;
            display: flex;
            align-items: center;
            justify-content: center;
            color: #e2e8f0;
        }
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
            background: rgba(15, 25, 45, 0.6);
            backdrop-filter: blur(20px);
            -webkit-backdrop-filter: blur(20px);
            padding: 15px;
            box-shadow: 0 30px 60px rgba(0, 0, 0, 0.8);
            border: 1px solid rgba(255,255,255,0.1);
            z-index: 2;
            display: flex;
            flex-direction: column;
            overflow: hidden;
        }

        .auth-card {
            max-width: 450px;
            width: 100%;
            margin: auto;
            background: rgba(20, 30, 50, 0.9);
            backdrop-filter: blur(15px);
            -webkit-backdrop-filter: blur(15px);
            border-radius: 40px;
            padding: 30px 20px;
            border: 1px solid rgba(255,255,255,0.15);
            box-shadow: 0 30px 50px -20px black;
        }
        .auth-tabs {
            display: flex;
            gap: 10px;
            margin-bottom: 25px;
            background: rgba(0,0,0,0.3);
            padding: 6px;
            border-radius: 50px;
        }
        .auth-tab {
            flex: 1;
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
        .form-group { text-align: left; }
        .form-group label {
            font-weight: 500; font-size: 0.85rem; color: #a5b4cb;
            display: block; margin-bottom: 6px;
        }
        .form-control {
            width: 100%;
            padding: 14px 18px;
            border: 1px solid #2d3f5e;
            border-radius: 40px;
            background: rgba(10, 20, 35, 0.8);
            color: white;
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
        .auth-btn {
            background: linear-gradient(135deg, #2563eb, #1e40af);
            color: white;
            border: none;
            padding: 14px;
            border-radius: 50px;
            font-weight: 600;
            font-size: 1rem;
            cursor: pointer;
            transition: 0.2s;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
        }
        .auth-btn:active { transform: scale(0.98); }
        .auth-btn.loading { background: #475569; pointer-events: none; }
        .auth-btn.loading i { animation: spin 1s linear infinite; }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
        .error-message {
            color: #f87171; font-size: 0.85rem;
            background: rgba(220,38,38,0.2); padding: 10px 16px; border-radius: 30px;
            border: 1px solid #ef4444; display: none;
        }
        .student-fields { display: none; flex-direction: column; gap: 15px; }

        .main-panel {
            display: none;
            flex: 1;
            gap: 15px;
            height: 100%;
            overflow: hidden;
        }
        .sidebar {
            width: 95px;
            background: rgba(20, 30, 50, 0.7);
            backdrop-filter: blur(10px);
            -webkit-backdrop-filter: blur(10px);
            border-radius: 35px;
            padding: 15px 0;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 10px;
            border: 1px solid rgba(255,255,255,0.15);
            height: 100%;
            overflow-y: auto;
            scrollbar-width: thin;
        }
        .sidebar .alpha-tab {
            width: 55px;
            height: 55px;
            display: flex;
            align-items: center;
            justify-content: center;
            background: rgba(255,255,255,0.08);
            border-radius: 25px;
            color: white;
            font-weight: 700;
            font-size: 1.5rem;
            cursor: pointer;
            transition: 0.2s;
            border: 1px solid rgba(255,255,255,0.1);
            flex-shrink: 0;
            position: relative;
        }
        .sidebar .alpha-tab:active { transform: scale(0.95); }
        .sidebar .alpha-tab.active {
            background: #2563eb;
            border-color: white;
            box-shadow: 0 0 20px #2563eb;
        }
        .sidebar .alpha-tab.locked::after {
            content: '\f023';
            font-family: 'Font Awesome 6 Free';
            font-weight: 900;
            position: absolute;
            top: 2px;
            right: 2px;
            font-size: 0.7rem;
            color: #f87171;
        }
        .sidebar .alpha-tab.unlocked::after {
            content: '\f3c1';
            font-family: 'Font Awesome 6 Free';
            font-weight: 900;
            position: absolute;
            top: 2px;
            right: 2px;
            font-size: 0.7rem;
            color: #4ade80;
        }

        .content-area {
            flex: 1;
            min-width: 0;
            height: 100%;
            overflow-y: auto;
            padding-right: 5px;
            scrollbar-width: thin;
        }

        .project-card {
            background: rgba(25, 35, 55, 0.85);
            backdrop-filter: blur(10px);
            -webkit-backdrop-filter: blur(10px);
            border-radius: 40px;
            padding: 20px;
            margin-bottom: 15px;
            border: 1px solid rgba(255,255,255,0.15);
        }
        .project-header {
            display: flex;
            align-items: center;
            gap: 15px;
        }
        .project-avatar {
            width: 60px;
            height: 60px;
            background: #2563eb;
            border-radius: 25px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 2rem;
            color: white;
        }
        .project-info h2 { font-size: 1.6rem; color: white; }
        .project-info .project-desc { color: #94a3b8; font-size: 0.9rem; }

        .source-section {
            background: rgba(0,0,0,0.2);
            border-radius: 30px;
            padding: 15px;
            margin-bottom: 15px;
        }
        .source-type-toggle {
            display: flex;
            gap: 8px;
            margin-bottom: 12px;
        }
        .source-type-btn {
            flex: 1;
            padding: 8px;
            border-radius: 30px;
            background: #1e293b;
            color: #94a3b8;
            text-align: center;
            font-size: 0.85rem;
            cursor: pointer;
        }
        .source-type-btn.active {
            background: #2563eb;
            color: white;
        }
        .source-input-group {
            display: flex;
            gap: 8px;
        }
        .source-input-group input {
            flex: 1;
            padding: 12px 15px;
            border-radius: 40px;
            border: 1px solid #3b82f6;
            background: #0f172a;
            color: white;
            font-size: 0.9rem;
        }
        .source-input-group button {
            padding: 12px 18px;
            border-radius: 40px;
            background: #2563eb;
            color: white;
            border: none;
            cursor: pointer;
            font-weight: 600;
        }
        .source-message { margin-top: 8px; font-size: 0.8rem; color: #94a3b8; }

        .person-selector {
            margin-bottom: 15px;
        }
        .person-selector select {
            width: 100%;
            padding: 14px 18px;
            border-radius: 40px;
            background: #1e293b;
            color: white;
            border: 1px solid #3b82f6;
            font-size: 0.95rem;
            -webkit-appearance: none;
            appearance: none;
        }
        .person-details {
            background: rgba(0,0,0,0.2);
            border-radius: 30px;
            padding: 20px;
        }
        .person-detail-item {
            display: flex;
            padding: 10px 0;
            border-bottom: 1px solid rgba(255,255,255,0.1);
        }
        .person-detail-item:last-child { border-bottom: none; }
        .person-detail-label {
            width: 70px;
            color: #94a3b8;
            font-size: 0.9rem;
        }
        .person-detail-value {
            flex: 1;
            color: white;
            font-weight: 500;
            font-size: 0.95rem;
        }

        .password-modal {
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0,0,0,0.7);
            display: flex;
            align-items: center;
            justify-content: center;
            z-index: 10000;
            visibility: hidden;
            opacity: 0;
            transition: 0.2s;
        }
        .password-modal.show {
            visibility: visible;
            opacity: 1;
        }
        .modal-content {
            background: #1e293b;
            padding: 30px;
            border-radius: 40px;
            width: 90%;
            max-width: 350px;
            border: 1px solid #3b82f6;
        }
        .modal-content h3 {
            color: white;
            margin-bottom: 20px;
            font-size: 1.3rem;
        }
        .modal-content input {
            width: 100%;
            padding: 15px;
            border-radius: 40px;
            border: 1px solid #3b82f6;
            background: #0f172a;
            color: white;
            margin-bottom: 20px;
        }
        .modal-buttons {
            display: flex;
            gap: 10px;
        }
        .modal-buttons button {
            flex: 1;
            padding: 12px;
            border-radius: 40px;
            border: none;
            font-weight: 600;
            cursor: pointer;
        }
        .modal-buttons .confirm {
            background: #2563eb;
            color: white;
        }
        .modal-buttons .cancel {
            background: #475569;
            color: white;
        }

        .settings-card {
            background: rgba(25, 35, 55, 0.85);
            backdrop-filter: blur(10px);
            -webkit-backdrop-filter: blur(10px);
            border-radius: 40px;
            padding: 20px;
            margin-top: 15px;
            text-align: center;
        }
        .logout-btn {
            background: rgba(239,68,68,0.2);
            color: #f87171;
            border: 1px solid #ef4444;
            padding: 12px 30px;
            border-radius: 40px;
            cursor: pointer;
            display: inline-flex;
            align-items: center;
            gap: 8px;
            font-weight: 600;
        }
        .toast {
            position: fixed; bottom: 20px; right: 20px;
            background: #1e293b; color: white; padding: 12px 24px; border-radius: 50px;
            z-index: 9999; opacity: 0; transition: opacity 0.3s;
            border: 1px solid #3b82f6;
            font-size: 0.9rem;
            pointer-events: none;
        }
        .toast.show { opacity: 1; }

        .shake {
            animation: shake 0.4s ease-in-out;
        }
        @keyframes shake {
            0%,100% { transform: translateX(0); }
            20%,60% { transform: translateX(-6px); }
            40%,80% { transform: translateX(6px); }
        }

        @media (max-width: 600px) {
            .sidebar { width: 70px; }
            .sidebar .alpha-tab { width: 45px; height: 45px; font-size: 1.2rem; }
            .project-avatar { width: 50px; height: 50px; font-size: 1.6rem; }
            .project-info h2 { font-size: 1.3rem; }
        }
    </style>
</head>
<body>
    <canvas id="particleCanvas"></canvas>

    <!-- 密碼輸入 Modal -->
    <div class="password-modal" id="passwordModal">
        <div class="modal-content">
            <h3 id="modalTitle">輸入專案密碼</h3>
            <input type="password" id="modalPassword" placeholder="請輸入密碼">
            <div class="modal-buttons">
                <button class="confirm" id="modalConfirm">確認</button>
                <button class="cancel" id="modalCancel">取消</button>
            </div>
        </div>
    </div>

    <div class="app-container">
        <!-- 登入/註冊卡片 -->
        <div id="authCard" class="auth-card">
            <div class="auth-tabs">
                <div class="auth-tab active" id="loginTab">登入</div>
                <div class="auth-tab" id="registerTab">註冊</div>
            </div>

            <!-- 登入表單 -->
            <div id="loginForm" class="auth-form">
                <div class="form-group">
                    <label>👤 帳號</label>
                    <input type="text" class="form-control" id="loginUsername" placeholder="請輸入帳號" value="user1">
                </div>
                <div class="form-group">
                    <label>🔑 密碼</label>
                    <input type="password" class="form-control" id="loginPassword" placeholder="••••••••" value="pass">
                </div>
                <div id="loginError" class="error-message"><i class="fas fa-exclamation-triangle"></i> 帳號或密碼錯誤</div>
                <button class="auth-btn" id="loginBtn"><i class="fas fa-sign-in-alt"></i> 登入</button>
            </div>

            <!-- 註冊表單 -->
            <div id="registerForm" class="auth-form" style="display: none;">
                <div class="form-group">
                    <label>👤 帳號</label>
                    <input type="text" class="form-control" id="regUsername" placeholder="自訂帳號">
                </div>
                <div class="form-group">
                    <label>🔑 密碼</label>
                    <input type="password" class="form-control" id="regPassword" placeholder="至少6位">
                </div>
                <div class="form-group">
                    <label>💼 職業</label>
                    <select class="form-control" id="regOccupation">
                        <option value="student">學生</option>
                        <option value="teacher">老師</option>
                        <option value="engineer">工程師</option>
                        <option value="other">其他</option>
                    </select>
                </div>
                <div class="student-fields" id="studentFields">
                    <div class="form-group">
                        <label>📚 班級</label>
                        <input type="text" class="form-control" id="regClass" placeholder="例如 三年一班">
                    </div>
                    <div class="form-group">
                        <label>🔢 座號</label>
                        <input type="text" class="form-control" id="regNumber" placeholder="例如 12">
                    </div>
                </div>
                <div id="registerError" class="error-message"></div>
                <button class="auth-btn" id="registerBtn"><i class="fas fa-user-plus"></i> 註冊</button>
            </div>
        </div>

        <!-- 登入後主面板 -->
        <div id="mainPanel" class="main-panel">
            <div class="sidebar" id="sidebar"></div>
            <div class="content-area" id="contentArea">
                <div class="project-card" id="projectCard">
                    <div class="project-header">
                        <div class="project-avatar" id="projectAvatar">A</div>
                        <div class="project-info">
                            <h2 id="projectName">A 專案</h2>
                            <div class="project-desc" id="projectDesc">請選擇一個專案</div>
                        </div>
                    </div>
                </div>

                <div class="source-section">
                    <div class="source-type-toggle">
                        <div class="source-type-btn active" id="permBtn">永久原始碼</div>
                        <div class="source-type-btn" id="onceBtn">一次性原始碼</div>
                    </div>
                    <div class="source-input-group">
                        <input type="text" id="sourceCodeInput" placeholder="請輸入原始碼" value="ROOTA">
                        <button id="verifySourceBtn">驗證</button>
                    </div>
                    <div id="sourceMessage" class="source-message"></div>
                </div>

                <div class="person-selector">
                    <select id="personSelect"></select>
                </div>

                <div class="person-details" id="personDetails">
                    <div class="person-detail-item">
                        <span class="person-detail-label">姓名</span>
                        <span class="person-detail-value" id="personName">-</span>
                    </div>
                    <div class="person-detail-item">
                        <span class="person-detail-label">職業</span>
                        <span class="person-detail-value" id="personOccupation">-</span>
                    </div>
                    <div class="person-detail-item">
                        <span class="person-detail-label">班級</span>
                        <span class="person-detail-value" id="personClass">-</span>
                    </div>
                    <div class="person-detail-item">
                        <span class="person-detail-label">座號</span>
                        <span class="person-detail-value" id="personNumber">-</span>
                    </div>
                    <div class="person-detail-item">
                        <span class="person-detail-label">電子郵件</span>
                        <span class="person-detail-value" id="personEmail">-</span>
                    </div>
                </div>

                <div class="settings-card">
                    <button class="logout-btn" id="logoutBtn"><i class="fas fa-sign-out-alt"></i> 登出</button>
                </div>
            </div>
        </div>
    </div>

    <div id="toast" class="toast"></div>

    <script>
        (function() {
            // 粒子背景
            const canvas = document.getElementById('particleCanvas');
            const ctx = canvas.getContext('2d');
            let width, height, particles = [];
            function initParticles() {
                width = window.innerWidth; height = window.innerHeight;
                canvas.width = width; canvas.height = height;
                const isMobile = width < 768;
                const particleCount = isMobile ? 40 : 80;
                particles = [];
                for (let i = 0; i < particleCount; i++) {
                    particles.push({
                        x: Math.random() * width,
                        y: Math.random() * height,
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
            const loginForm = document.getElementById('loginForm');
            const registerForm = document.getElementById('registerForm');
            const loginBtn = document.getElementById('loginBtn');
            const registerBtn = document.getElementById('registerBtn');
            const loginError = document.getElementById('loginError');
            const registerError = document.getElementById('registerError');
            const regOccupation = document.getElementById('regOccupation');
            const studentFields = document.getElementById('studentFields');
            const logoutBtn = document.getElementById('logoutBtn');
            const toast = document.getElementById('toast');
            const sidebar = document.getElementById('sidebar');
            const projectAvatar = document.getElementById('projectAvatar');
            const projectName = document.getElementById('projectName');
            const projectDesc = document.getElementById('projectDesc');
            const sourceCodeInput = document.getElementById('sourceCodeInput');
            const verifySourceBtn = document.getElementById('verifySourceBtn');
            const sourceMessage = document.getElementById('sourceMessage');
            const permBtn = document.getElementById('permBtn');
            const onceBtn = document.getElementById('onceBtn');
            const personSelect = document.getElementById('personSelect');
            const personName = document.getElementById('personName');
            const personOccupation = document.getElementById('personOccupation');
            const personClass = document.getElementById('personClass');
            const personNumber = document.getElementById('personNumber');
            const personEmail = document.getElementById('personEmail');

            // 密碼 Modal
            const passwordModal = document.getElementById('passwordModal');
            const modalPassword = document.getElementById('modalPassword');
            const modalConfirm = document.getElementById('modalConfirm');
            const modalCancel = document.getElementById('modalCancel');
            const modalTitle = document.getElementById('modalTitle');

            // 原始碼類型
            let sourceType = 'permanent';
            permBtn.addEventListener('click', () => {
                permBtn.classList.add('active');
                onceBtn.classList.remove('active');
                sourceType = 'permanent';
            });
            onceBtn.addEventListener('click', () => {
                onceBtn.classList.add('active');
                permBtn.classList.remove('active');
                sourceType = 'once';
            });

            function showToast(msg, duration = 2000) {
                toast.textContent = msg; toast.classList.add('show');
                setTimeout(() => toast.classList.remove('show'), duration);
            }

            // 切換登入/註冊
            loginTab.addEventListener('click', () => {
                loginTab.classList.add('active');
                registerTab.classList.remove('active');
                loginForm.style.display = 'flex';
                registerForm.style.display = 'none';
            });
            registerTab.addEventListener('click', () => {
                registerTab.classList.add('active');
                loginTab.classList.remove('active');
                registerForm.style.display = 'flex';
                loginForm.style.display = 'none';
            });

            regOccupation.addEventListener('change', () => {
                studentFields.style.display = regOccupation.value === 'student' ? 'flex' : 'none';
            });

            // ========== 【使用者帳號密碼】 ==========
            const users = {
                user1: { password: 'pass' },
                admin: { password: 'admin' },
                // 超級用戶 0 不需要密碼，由程式碼特別處理
            };

            // ========== 【原始碼清單】 ==========
            const sourceCodes = {
                "ROOTA": { type: "permanent", letter: "A", isAdmin: false },
                "ROOTB": { type: "permanent", letter: "B", isAdmin: false },
                "ROOTC": { type: "permanent", letter: "C", isAdmin: false },
                "ROOTD": { type: "permanent", letter: "D", isAdmin: false },
                "ROOTE": { type: "permanent", letter: "E", isAdmin: false },
                "ROOTF": { type: "permanent", letter: "F", isAdmin: false },
                "ROOTG": { type: "permanent", letter: "G", isAdmin: false },
                "ROOTH": { type: "permanent", letter: "H", isAdmin: false },
                "ROOTI": { type: "permanent", letter: "I", isAdmin: false },
                "ROOTJ": { type: "permanent", letter: "J", isAdmin: false },
                "ROOTK": { type: "permanent", letter: "K", isAdmin: false },
                "ROOTL": { type: "permanent", letter: "L", isAdmin: false },
                "ROOTM": { type: "permanent", letter: "M", isAdmin: false },
                "ROOTN": { type: "permanent", letter: "N", isAdmin: false },
                "ROOTO": { type: "permanent", letter: "O", isAdmin: false },
                "ROOTP": { type: "permanent", letter: "P", isAdmin: false },
                "ROOTQ": { type: "permanent", letter: "Q", isAdmin: false },
                "ROOTR": { type: "permanent", letter: "R", isAdmin: false },
                "ROOTS": { type: "permanent", letter: "S", isAdmin: false },
                "ROOTT": { type: "permanent", letter: "T", isAdmin: false },
                "ROOTU": { type: "permanent", letter: "U", isAdmin: false },
                "ROOTV": { type: "permanent", letter: "V", isAdmin: false },
                "ROOTW": { type: "permanent", letter: "W", isAdmin: false },
                "ROOTX": { type: "permanent", letter: "X", isAdmin: false },
                "ROOTY": { type: "permanent", letter: "Y", isAdmin: false },
                "ROOTZ": { type: "permanent", letter: "Z", isAdmin: false },
                "ONCEA": { type: "once", letter: "A", isAdmin: false, used: false },
                "ONCEB": { type: "once", letter: "B", isAdmin: false, used: false },
                "ONCEC": { type: "once", letter: "C", isAdmin: false, used: false },
                "ADMIN": { type: "permanent", letter: "A", isAdmin: true },
            };

            // ========== 【專案密碼】 ==========
            const projectPasswords = {
                A: '1234', B: '5678', C: 'abcd', D: 'passD', E: 'passE',
                F: 'passF', G: 'passG', H: 'passH', I: 'passI', J: 'passJ',
                K: 'passK', L: 'passL', M: 'passM', N: 'passN', O: 'passO',
                P: 'passP', Q: 'passQ', R: 'passR', S: 'passS', T: 'passT',
                U: 'passU', V: 'passV', W: 'passW', X: 'passX', Y: 'passY',
                Z: 'passZ',
            };

            // ========== 【專案資訊】 ==========
            const projectInfo = {
                A: { name: 'A 專案', desc: '人工智慧研發中心' },
                B: { name: 'B 專案', desc: '區塊鏈金融平台' },
                C: { name: 'C 專案', desc: '物聯網智慧工廠' },
                D: { name: 'D 專案', desc: '雲端數據分析' },
                E: { name: 'E 專案', desc: '行動應用開發' },
                F: { name: 'F 專案', desc: '電子商務網站' },
                G: { name: 'G 專案', desc: '遊戲引擎研發' },
                H: { name: 'H 專案', desc: '資安防護系統' },
                I: { name: 'I 專案', desc: '大數據平台' },
                J: { name: 'J 專案', desc: '機器人自動化' },
                K: { name: 'K 專案', desc: '智慧醫療' },
                L: { name: 'L 專案', desc: '教育科技' },
                M: { name: 'M 專案', desc: '金融科技' },
                N: { name: 'N 專案', desc: '社群媒體分析' },
                O: { name: 'O 專案', desc: 'AR/VR 應用' },
                P: { name: 'P 專案', desc: '智慧城市' },
                Q: { name: 'Q 專案', desc: '量子運算研究' },
                R: { name: 'R 專案', desc: '機器學習框架' },
                S: { name: 'S 專案', desc: '供應鏈管理' },
                T: { name: 'T 專案', desc: '數位轉型顧問' },
                U: { name: 'U 專案', desc: '使用者體驗設計' },
                V: { name: 'V 專案', desc: '影片串流技術' },
                W: { name: 'W 專案', desc: '智慧穿戴裝置' },
                X: { name: 'X 專案', desc: '無人機系統' },
                Y: { name: 'Y 專案', desc: '語音識別' },
                Z: { name: 'Z 專案', desc: '自動駕駛' },
            };

            // ========== 【100個人物資料】 ==========
            const peopleData = {};
            for (let i = 65; i <= 90; i++) {
                let letter = String.fromCharCode(i);
                peopleData[letter] = [];
                for (let j = 1; j <= 100; j++) {
                    peopleData[letter].push({
                        name: '',
                        occupation: '',
                        class: '',
                        number: '',
                        email: ''
                    });
                }
            }
            // 範例填寫
            peopleData['A'][0] = { name: '張三', occupation: '學生', class: '三年一班', number: '12', email: 'zhangsan@school.com' };
            peopleData['A'][1] = { name: '李四', occupation: '老師', class: '', number: '', email: 'lisi@school.com' };
            peopleData['A'][2] = { name: '王五', occupation: '工程師', class: '', number: '', email: 'wangwu@company.com' };

            // 全域狀態
            let currentUser = null;
            let unlockedLetters = new Set();
            let currentLetter = null;
            let isSuperUser = false; // 是否為超級用戶 0

            // 渲染側邊欄 (永遠顯示全部)
            function renderSidebar() {
                sidebar.innerHTML = '';
                for (let i = 0; i < 26; i++) {
                    let letter = String.fromCharCode(65 + i);
                    const tab = document.createElement('div');
                    tab.className = 'alpha-tab';
                    if (letter === currentLetter) tab.classList.add('active');
                    // 超級用戶直接解鎖全部，否則根據 unlockedLetters
                    if (isSuperUser || unlockedLetters.has(letter)) {
                        tab.classList.add('unlocked');
                    } else {
                        tab.classList.add('locked');
                    }
                    tab.textContent = letter;
                    tab.dataset.letter = letter;
                    tab.addEventListener('click', () => handleLetterClick(letter));
                    sidebar.appendChild(tab);
                }
            }

            // 處理字母點擊
            function handleLetterClick(letter) {
                if (isSuperUser || unlockedLetters.has(letter)) {
                    // 已解鎖，直接切換
                    switchToLetter(letter);
                } else {
                    // 未解鎖，彈出密碼視窗
                    modalTitle.textContent = `輸入 ${letter} 專案密碼`;
                    modalPassword.value = '';
                    passwordModal.classList.add('show');
                    passwordModal.dataset.targetLetter = letter;
                }
            }

            // 切換到指定字母
            function switchToLetter(letter) {
                document.querySelectorAll('.alpha-tab').forEach(tab => {
                    if (tab.dataset.letter === letter) {
                        tab.classList.add('active');
                    } else {
                        tab.classList.remove('active');
                    }
                });
                currentLetter = letter;
                const info = projectInfo[letter] || { name: `${letter} 專案`, desc: '暫無描述' };
                projectAvatar.textContent = letter;
                projectName.textContent = info.name;
                projectDesc.textContent = info.desc;
                populatePersonSelect(letter);
                loadPerson(letter, 1);
            }

            function populatePersonSelect(letter) {
                let options = '';
                for (let i = 1; i <= 100; i++) {
                    options += `<option value="${i}">人物 ${i}</option>`;
                }
                personSelect.innerHTML = options;
                personSelect.value = 1;
            }

            function loadPerson(letter, index) {
                const person = peopleData[letter]?.[index-1] || { name: '', occupation: '', class: '', number: '', email: '' };
                personName.textContent = person.name || '未填寫';
                personOccupation.textContent = person.occupation || '未填寫';
                personClass.textContent = person.class || '未填寫';
                personNumber.textContent = person.number || '未填寫';
                personEmail.textContent = person.email || '未填寫';
            }

            personSelect.addEventListener('change', (e) => {
                if (currentLetter) {
                    loadPerson(currentLetter, parseInt(e.target.value));
                }
            });

            // 密碼確認
            modalConfirm.addEventListener('click', () => {
                const letter = passwordModal.dataset.targetLetter;
                const enteredPwd = modalPassword.value.trim();
                const correctPwd = projectPasswords[letter];
                if (enteredPwd === correctPwd) {
                    unlockedLetters.add(letter);
                    passwordModal.classList.remove('show');
                    renderSidebar();
                    switchToLetter(letter);
                    showToast(`🔓 ${letter} 專案已解鎖`);
                } else {
                    showToast('❌ 密碼錯誤');
                }
            });

            modalCancel.addEventListener('click', () => {
                passwordModal.classList.remove('show');
            });
            passwordModal.addEventListener('click', (e) => {
                if (e.target === passwordModal) {
                    passwordModal.classList.remove('show');
                }
            });

            // ========== 登入 ==========
            loginBtn.addEventListener('click', (e) => {
                e.preventDefault();
                const username = document.getElementById('loginUsername').value.trim();
                const password = document.getElementById('loginPassword').value.trim();

                // 超級用戶 0：不需密碼
                if (username === '0') {
                    // 直接登入，不檢查密碼
                    loginSuccess(username, true);
                    return;
                }

                // 一般用戶檢查密碼
                if (!users[username] || users[username].password !== password) {
                    loginError.style.display = 'flex';
                    authCard.classList.add('shake');
                    setTimeout(() => authCard.classList.remove('shake'), 500);
                    return;
                }

                loginSuccess(username, false);
            });

            function loginSuccess(username, isSuper) {
                loginBtn.classList.add('loading');
                loginBtn.innerHTML = '<i class="fas fa-spinner"></i> 登入中...';

                setTimeout(() => {
                    loginBtn.classList.remove('loading');
                    loginBtn.innerHTML = '<i class="fas fa-sign-in-alt"></i> 登入';
                    loginError.style.display = 'none';

                    currentUser = username;
                    isSuperUser = isSuper;
                    unlockedLetters.clear(); // 清空先前解鎖狀態
                    currentLetter = null;

                    authCard.style.display = 'none';
                    mainPanel.style.display = 'flex';

                    // 如果是超級用戶，直接解鎖全部字母
                    if (isSuperUser) {
                        for (let i = 65; i <= 90; i++) {
                            unlockedLetters.add(String.fromCharCode(i));
                        }
                    }

                    renderSidebar();

                    // 清空右側內容
                    projectAvatar.textContent = '?';
                    projectName.textContent = '請選擇一個專案';
                    projectDesc.textContent = '';
                    personSelect.innerHTML = '<option>請先選擇專案</option>';
                    personName.textContent = personOccupation.textContent = personClass.textContent = personNumber.textContent = personEmail.textContent = '-';

                    showToast(isSuper ? '👑 歡迎超級用戶 0' : `👋 歡迎 ${username}`);
                }, 500);
            }

            // ========== 註冊 ==========
            registerBtn.addEventListener('click', (e) => {
                e.preventDefault();
                const username = document.getElementById('regUsername').value.trim();
                const password = document.getElementById('regPassword').value.trim();
                const occupation = regOccupation.value;
                const regClass = document.getElementById('regClass').value.trim();
                const regNumber = document.getElementById('regNumber').value.trim();

                if (!username || !password) {
                    registerError.innerHTML = '❌ 帳號密碼必填';
                    registerError.style.display = 'flex';
                    return;
                }
                if (password.length < 1) {
                    registerError.innerHTML = '❌ 密碼至少1位';
                    registerError.style.display = 'flex';
                    return;
                }
                // 禁止註冊用戶名 0
                if (username === '0') {
                    registerError.innerHTML = '❌ 此帳號不可註冊';
                    registerError.style.display = 'flex';
                    return;
                }
                if (users[username]) {
                    registerError.innerHTML = '❌ 帳號已存在';
                    registerError.style.display = 'flex';
                    return;
                }
                if (occupation === 'student' && (!regClass || !regNumber)) {
                    registerError.innerHTML = '❌ 學生請填寫班級與座號';
                    registerError.style.display = 'flex';
                    return;
                }

                registerBtn.classList.add('loading');
                registerBtn.innerHTML = '<i class="fas fa-spinner"></i> 註冊中...';

                setTimeout(() => {
                    users[username] = { password: password };
                    registerBtn.classList.remove('loading');
                    registerBtn.innerHTML = '<i class="fas fa-user-plus"></i> 註冊';
                    showToast('✅ 註冊成功，請登入');
                    loginTab.click();
                    document.getElementById('regUsername').value = '';
                    document.getElementById('regPassword').value = '';
                    document.getElementById('regClass').value = '';
                    document.getElementById('regNumber').value = '';
                    registerError.style.display = 'none';
                }, 500);
            });

            // ========== 原始碼驗證 (超級用戶也可使用，直接導向對應專案) ==========
            verifySourceBtn.addEventListener('click', () => {
                const code = sourceCodeInput.value.trim();
                const source = sourceCodes[code];

                if (!source) {
                    sourceMessage.textContent = '❌ 原始碼無效';
                    return;
                }
                // 一次性原始碼檢查是否已使用
                if (source.type === 'once' && source.used) {
                    sourceMessage.textContent = '❌ 一次性原始碼已使用';
                    return;
                }
                // 檢查類型是否匹配 (永久/一次性)
                if (source.type !== sourceType) {
                    sourceMessage.textContent = `❌ 請選擇正確的類型 (目前選擇 ${sourceType === 'permanent' ? '永久' : '一次性'})`;
                    return;
                }

                // 標記一次性原始碼為已使用 (無論用戶類型，使用後即失效)
                if (source.type === 'once') {
                    source.used = true;
                }

                // 如果是管理員原始碼，且用戶不是超級用戶，才解鎖全部 (超級用戶已經解鎖全部，不需要)
                if (source.isAdmin && !isSuperUser) {
                    for (let i = 65; i <= 90; i++) {
                        unlockedLetters.add(String.fromCharCode(i));
                    }
                    showToast('👑 管理員權限，全部專案已解鎖');
                } else if (!source.isAdmin && !isSuperUser) {
                    // 一般原始碼，解鎖對應字母
                    unlockedLetters.add(source.letter);
                    showToast(`🔓 ${source.letter} 專案已解鎖 (原始碼)`);
                }

                // 重新渲染側邊欄 (解鎖狀態可能更新)
                renderSidebar();

                // 切換到原始碼對應的字母 (若尚未切換)
                if (currentLetter !== source.letter) {
                    switchToLetter(source.letter);
                }

                // 顯示成功訊息
                sourceMessage.textContent = '✅ 驗證成功，已切換到對應專案';
            });

            // ========== 登出 ==========
            logoutBtn.addEventListener('click', () => {
                currentUser = null;
                isSuperUser = false;
                unlockedLetters.clear();
                currentLetter = null;
                mainPanel.style.display = 'none';
                authCard.style.display = 'block';
                document.getElementById('loginUsername').value = 'user1';
                document.getElementById('loginPassword').value = 'pass';
                loginError.style.display = 'none';
                showToast('👋 已安全登出');
            });

            // 初始化
            loginError.style.display = 'none';
            registerError.style.display = 'none';
            studentFields.style.display = 'none';
            authCard.style.display = 'block';
            mainPanel.style.display = 'none';
        })();
    </script>

    <!-- ========== 🛠️ 修改教學 ========== -->
    <!--
        🔧 超級用戶「0」：使用帳號 0 登入，不需密碼，可查看所有專案及人物資料，且可使用原始碼直接導向對應專案。
        🔧 一般用戶：需註冊，登入後需使用原始碼或專案密碼解鎖字母才能查看。
        🔧 原始碼清單、專案密碼、專案資訊、人物資料皆可依需求修改（位置已在程式碼中標註）。
    -->
    <!-- ================================== -->
</body>
</html>
