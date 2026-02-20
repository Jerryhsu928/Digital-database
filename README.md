# Digital-database
數位資料庫
<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.5">
    <title>🔐 原始碼登入 · 下載與 IP 限制</title>
    <!-- 字體 Inter -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:ital,opsz,wght@0,14..32,100..900;1,14..32,100..900&display=swap" rel="stylesheet">
    <!-- Font Awesome 6 -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <!-- GSAP 動畫庫 -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/gsap.min.js"></script>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        html, body { height: 100%; overflow: hidden; }
        body {
            font-family: "Inter", sans-serif;
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
            width: 95%;
            max-width: 1400px;
            height: 90vh;
            background: rgba(15, 25, 45, 0.6);
            backdrop-filter: blur(20px);
            border-radius: 60px;
            padding: 30px;
            box-shadow: 0 30px 60px rgba(0, 0, 0, 0.8);
            border: 1px solid rgba(255,255,255,0.1);
            z-index: 2;
            display: flex;
            flex-direction: column;
            overflow: hidden;
        }

        .auth-row {
            display: flex;
            gap: 30px;
            justify-content: center;
            align-items: flex-start;
            margin: auto;
            width: 100%;
            max-width: 1000px;
        }
        .auth-card {
            flex: 1;
            max-width: 400px;
            backdrop-filter: blur(15px);
            border-radius: 50px;
            padding: 35px;
            border: 1px solid rgba(255,255,255,0.15);
            box-shadow: 0 30px 50px -20px black;
        }
        .auth-card.login-card {
            background: linear-gradient(145deg, #1e3a8a, #1e40af);
        }
        .auth-card.register-card {
            background: linear-gradient(145deg, #6b21a5, #7e22ce);
        }
        .auth-card h2 {
            text-align: center;
            margin-bottom: 25px;
            font-size: 1.8rem;
            color: white;
        }
        .auth-form { display: flex; flex-direction: column; gap: 20px; }
        .form-group { display: flex; flex-direction: column; gap: 8px; }
        .form-group label { font-weight: 500; font-size: 0.9rem; color: #e2e8f0; }
        .form-control {
            padding: 16px 20px; border: 1px solid rgba(255,255,255,0.2); border-radius: 40px;
            background: rgba(0,0,0,0.3); color: white; font-size: 1rem;
            backdrop-filter: blur(5px);
        }
        .form-control:focus { outline: none; border-color: #3b82f6; box-shadow: 0 0 0 3px rgba(59,130,246,0.3); }
        .auth-btn {
            background: linear-gradient(135deg, #2563eb, #1e40af); color: white;
            border: none; padding: 16px; border-radius: 50px; font-weight: 700;
            cursor: pointer; transition: 0.2s; display: flex; align-items: center; justify-content: center; gap: 10px;
        }
        .auth-btn:hover { transform: scale(1.02); }
        .auth-btn.loading { background: #475569; pointer-events: none; }
        .auth-btn.loading i { animation: spin 1s linear infinite; }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
        .error-message {
            color: #f87171; font-size: 0.9rem; display: flex; align-items: center; gap: 6px;
            background: rgba(220,38,38,0.2); padding: 10px 16px; border-radius: 30px;
            border: 1px solid #ef4444; display: none;
        }
        .small-note { color: #cbd5e1; font-size: 0.8rem; margin-top: 5px; }

        .block-message {
            background: rgba(239,68,68,0.2);
            border: 1px solid #ef4444;
            color: #f87171;
            padding: 12px;
            border-radius: 40px;
            text-align: center;
            font-size: 0.9rem;
            display: none;
            margin: 20px auto 0;
            max-width: 600px;
        }

        .main-panel {
            display: none;
            flex: 1;
            gap: 25px;
            height: 100%;
            overflow: hidden;
        }
        .sidebar {
            width: 120px;
            background: rgba(20, 30, 50, 0.7);
            backdrop-filter: blur(10px);
            border-radius: 50px;
            padding: 20px 0;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 12px;
            border: 1px solid rgba(255,255,255,0.15);
            height: 100%;
            overflow-y: auto;
            scrollbar-width: thin;
            scrollbar-color: #2563eb #1e293b;
        }
        .sidebar::-webkit-scrollbar { width: 6px; }
        .sidebar::-webkit-scrollbar-track { background: #1e293b; border-radius: 10px; }
        .sidebar::-webkit-scrollbar-thumb { background: #2563eb; border-radius: 10px; }
        .sidebar .alpha-tab {
            width: 70px;
            height: 70px;
            display: flex;
            align-items: center;
            justify-content: center;
            background: rgba(255,255,255,0.08);
            border-radius: 30px;
            color: white;
            font-weight: 700;
            font-size: 1.8rem;
            cursor: pointer;
            transition: 0.2s;
            border: 1px solid rgba(255,255,255,0.1);
            flex-shrink: 0;
        }
        .sidebar .alpha-tab:hover { background: rgba(37,99,235,0.5); transform: scale(1.05); }
        .sidebar .alpha-tab.active { background: #2563eb; border-color: white; box-shadow: 0 0 25px #2563eb; }

        .content-area {
            flex: 1;
            min-width: 0;
            height: 100%;
            overflow-y: auto;
            padding-right: 5px;
            scrollbar-width: thin;
            scrollbar-color: #2563eb #1e293b;
        }
        .content-area::-webkit-scrollbar { width: 6px; }
        .content-area::-webkit-scrollbar-track { background: #1e293b; border-radius: 10px; }
        .content-area::-webkit-scrollbar-thumb { background: #2563eb; border-radius: 10px; }

        .profile-card {
            background: rgba(25, 35, 55, 0.85);
            backdrop-filter: blur(10px);
            border-radius: 50px;
            padding: 35px;
            margin-bottom: 25px;
            border: 1px solid rgba(255,255,255,0.15);
        }
        .profile-header { display: flex; align-items: center; gap: 30px; flex-wrap: wrap; }
        .avatar-large {
            width: 120px; height: 120px;
            background: linear-gradient(145deg, #2563eb, #7c3aed);
            border-radius: 40px;
            display: flex; align-items: center; justify-content: center;
            color: white; font-size: 3.5rem; font-weight: 700;
        }
        .profile-info { flex: 1; }
        .profile-info h2 { font-size: 2.5rem; font-weight: 700; color: white; }
        .profile-info .role { color: #94a3b8; font-size: 1.2rem; margin-bottom: 20px; }
        .info-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
            gap: 15px;
        }
        .info-item {
            display: flex; align-items: center; gap: 15px;
            background: rgba(0,0,0,0.3); padding: 15px 20px; border-radius: 40px;
        }
        .info-item i { width: 40px; height: 40px; background: #2563eb; border-radius: 50%; display: flex; align-items: center; justify-content: center; }
        .info-item .label { font-size: 0.8rem; color: #94a3b8; }
        .info-item .value { font-weight: 600; color: white; }

        .download-btn {
            display: inline-flex;
            align-items: center;
            gap: 10px;
            background: #10b981;
            color: white;
            padding: 12px 25px;
            border-radius: 40px;
            margin-top: 20px;
            border: none;
            cursor: pointer;
            transition: 0.2s;
            font-weight: 600;
        }
        .download-btn:hover { background: #059669; }

        .project-link {
            display: inline-flex;
            align-items: center;
            gap: 10px;
            background: #1e293b;
            color: #60a5fa;
            padding: 12px 25px;
            border-radius: 40px;
            margin-top: 20px;
            border: 1px solid #3b82f6;
            text-decoration: none;
            transition: 0.2s;
        }
        .project-link:hover { background: #2d3b52; color: white; }

        .settings-card {
            background: rgba(25, 35, 55, 0.85);
            backdrop-filter: blur(10px);
            border-radius: 50px;
            padding: 35px;
            border: 1px solid rgba(255,255,255,0.15);
        }
        .settings-card h3 { font-size: 1.8rem; color: white; margin-bottom: 25px; }
        .settings-item {
            display: flex; justify-content: space-between; align-items: center;
            padding: 18px 0; border-bottom: 1px solid rgba(255,255,255,0.1);
        }
        .settings-item .left { display: flex; align-items: center; gap: 15px; color: #cbd5e1; }
        .toggle-switch {
            width: 60px; height: 30px; background: #334155; border-radius: 30px;
            position: relative; cursor: pointer;
        }
        .toggle-switch.active { background: #2563eb; }
        .toggle-switch::after {
            content: ''; width: 26px; height: 26px; background: white; border-radius: 50%;
            position: absolute; top: 2px; left: 2px; transition: 0.2s;
        }
        .toggle-switch.active::after { left: 32px; }
        .logout-btn {
            background: rgba(239,68,68,0.2); color: #f87171;
            border: 1px solid #ef4444; padding: 14px 30px; border-radius: 50px;
            cursor: pointer; margin-top: 20px; display: inline-flex; align-items: center; gap: 10px;
        }
        .toast {
            position: fixed; bottom: 30px; right: 30px;
            background: #1e293b; color: white; padding: 16px 28px; border-radius: 60px;
            z-index: 9999; opacity: 0; transition: opacity 0.3s;
            border: 1px solid #3b82f6;
        }
        .toast.show { opacity: 1; }

        @media (max-width: 800px) {
            .auth-row { flex-direction: column; align-items: center; }
            .main-panel { flex-direction: column; }
            .sidebar { width: 100%; flex-direction: row; justify-content: center; padding: 15px; }
        }
    </style>
</head>
<body>
    <canvas id="particleCanvas"></canvas>

    <div class="app-container">
        <!-- 登入/註冊區塊 (左右獨立，不同色調) -->
        <div id="authBlock" class="auth-row">
            <!-- 左邊：登入 (原始碼) - 藍色調 -->
            <div class="auth-card login-card">
                <h2><i class="fas fa-key"></i> 原始碼登入</h2>
                <div class="auth-form">
                    <div class="form-group">
                        <label>🔢 原始碼</label>
                        <input type="text" class="form-control" id="sourceCodeInput" placeholder="請輸入原始碼" value="ROOTA">
                        <div class="small-note">原始碼最後一個字母決定專案 (例如 ROOTA 進入 A 專案)</div>
                    </div>
                    <div id="loginError" class="error-message"><i class="fas fa-exclamation-triangle"></i> 原始碼錯誤</div>
                    <button class="auth-btn" id="loginBtn"><i class="fas fa-sign-in-alt"></i> 登入</button>
                </div>
            </div>

            <!-- 右邊：註冊 (一次性驗證碼) - 紫色調 -->
            <div class="auth-card register-card">
                <h2><i class="fas fa-user-plus"></i> 註冊帳號</h2>
                <div class="auth-form">
                    <div class="form-group">
                        <label>👤 使用者名稱</label>
                        <input type="text" class="form-control" id="regUsername" placeholder="自訂帳號">
                    </div>
                    <div class="form-group">
                        <label>🔑 密碼</label>
                        <input type="password" class="form-control" id="regPassword" placeholder="至少6位">
                    </div>
                    <div class="form-group">
                        <label>📨 一次性驗證碼</label>
                        <input type="text" class="form-control" id="regInvite" placeholder="請輸入驗證碼">
                    </div>
                    <div id="registerError" class="error-message"></div>
                    <button class="auth-btn" id="registerBtn"><i class="fas fa-check-circle"></i> 註冊</button>
                    <div class="small-note">註冊成功後請等待管理員分配原始碼，才能登入</div>
                </div>
            </div>
        </div>

        <!-- IP 封鎖提示 -->
        <div id="blockMessage" class="block-message">
            ⚠️ 嘗試次數過多，請等待 1 分鐘後再試
        </div>

        <!-- 登入後主面板 -->
        <div id="mainPanel" class="main-panel">
            <!-- 左側 A-Z 側邊欄 -->
            <div class="sidebar" id="sidebar"></div>

            <!-- 右側內容區 -->
            <div class="content-area" id="contentArea">
                <!-- 個人資訊卡片 -->
                <div class="profile-card" id="profileCard">
                    <div class="profile-header">
                        <div class="avatar-large" id="profileAvatar">A</div>
                        <div class="profile-info">
                            <h2 id="profileName">王小明</h2>
                            <div class="role" id="profileRole">專案經理</div>
                            <div class="info-grid">
                                <div class="info-item"><i class="fas fa-envelope"></i> <span><span class="label">電子郵件</span><br><span class="value" id="profileEmail">a@project.com</span></span></div>
                                <div class="info-item"><i class="fas fa-phone-alt"></i> <span><span class="label">電話</span><br><span class="value" id="profilePhone">0912-345-678</span></span></div>
                                <div class="info-item"><i class="fas fa-map-marker-alt"></i> <span><span class="label">地區</span><br><span class="value" id="profileLocation">台北</span></span></div>
                                <div class="info-item"><i class="fas fa-calendar-alt"></i> <span><span class="label">加入日期</span><br><span class="value" id="profileJoin">2025-01-01</span></span></div>
                            </div>
                            <!-- 自訂連結按鈕 -->
                            <a href="#" target="_blank" class="project-link" id="projectLink"><i class="fas fa-external-link-alt"></i> <span id="linkText">前往專案網站</span></a>
                            <!-- 下載檔案按鈕 -->
                            <button class="download-btn" id="downloadBtn"><i class="fas fa-download"></i> 下載專案文件</button>
                        </div>
                    </div>
                </div>

                <!-- 設定卡片 (裝飾用) -->
                <div class="settings-card">
                    <h3><i class="fas fa-cog"></i> 個人設定</h3>
                    <div class="settings-item">
                        <div class="left"><i class="fas fa-bell"></i> 通知提醒</div>
                        <div class="toggle-switch" id="notifyToggle"></div>
                    </div>
                    <div class="settings-item">
                        <div class="left"><i class="fas fa-lock"></i> 雙重驗證</div>
                        <div class="toggle-switch" id="mfaToggle"></div>
                    </div>
                    <div class="settings-item">
                        <div class="left"><i class="fas fa-globe"></i> 語言</div>
                        <select><option>繁體中文</option><option>English</option></select>
                    </div>
                    <div class="settings-item">
                        <div class="left"><i class="fas fa-moon"></i> 深色模式</div>
                        <div class="toggle-switch active" id="darkToggle"></div>
                    </div>
                    <div style="margin-top:25px;">
                        <button class="auth-btn" style="background:#4b5563; width:auto; display:inline-block; margin-right:15px;" id="saveSettingsBtn"><i class="fas fa-save"></i> 儲存設定</button>
                        <button class="logout-btn" id="logoutBtn"><i class="fas fa-sign-out-alt"></i> 登出</button>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <div id="toast" class="toast"></div>

    <script>
        (function() {
            // 粒子背景 (裝飾)
            const canvas = document.getElementById('particleCanvas');
            const ctx = canvas.getContext('2d');
            let width, height, particles = [];
            function initParticles() {
                width = window.innerWidth; height = window.innerHeight;
                canvas.width = width; canvas.height = height;
                particles = [];
                for (let i = 0; i < 100; i++) {
                    particles.push({
                        x: Math.random() * width, y: Math.random() * height,
                        radius: Math.random() * 4 + 1,
                        vx: (Math.random() - 0.5) * 0.4,
                        vy: (Math.random() - 0.5) * 0.4,
                        color: `rgba(59,130,246,${Math.random() * 0.4 + 0.2})`
                    });
                }
            }
            function drawParticles() {
                ctx.clearRect(0, 0, width, height);
                particles.forEach(p => {
                    ctx.beginPath(); ctx.arc(p.x, p.y, p.radius, 0, Math.PI*2);
                    ctx.fillStyle = p.color; ctx.fill();
                    p.x += p.vx; p.y += p.vy;
                    if (p.x<0||p.x>width) p.vx=-p.vx;
                    if (p.y<0||p.y>height) p.vy=-p.vy;
                });
                requestAnimationFrame(drawParticles);
            }
            window.addEventListener('resize', initParticles);
            initParticles(); drawParticles();

            // ---------- DOM ----------
            const authBlock = document.getElementById('authBlock');
            const mainPanel = document.getElementById('mainPanel');
            const loginBtn = document.getElementById('loginBtn');
            const registerBtn = document.getElementById('registerBtn');
            const loginError = document.getElementById('loginError');
            const registerError = document.getElementById('registerError');
            const logoutBtn = document.getElementById('logoutBtn');
            const toast = document.getElementById('toast');
            const sidebar = document.getElementById('sidebar');
            const blockMessage = document.getElementById('blockMessage');
            // 個人資訊元素
            const profileAvatar = document.getElementById('profileAvatar');
            const profileName = document.getElementById('profileName');
            const profileRole = document.getElementById('profileRole');
            const profileEmail = document.getElementById('profileEmail');
            const profilePhone = document.getElementById('profilePhone');
            const profileLocation = document.getElementById('profileLocation');
            const profileJoin = document.getElementById('profileJoin');
            const projectLink = document.getElementById('projectLink');
            const linkText = document.getElementById('linkText');
            const downloadBtn = document.getElementById('downloadBtn');
            // 開關
            const notifyToggle = document.getElementById('notifyToggle');
            const mfaToggle = document.getElementById('mfaToggle');
            const darkToggle = document.getElementById('darkToggle');
            document.getElementById('saveSettingsBtn').addEventListener('click', ()=> showToast('✅ 設定已儲存 (示範)'));

            function showToast(msg, duration=2500) {
                toast.textContent = msg; toast.classList.add('show');
                setTimeout(()=>toast.classList.remove('show'), duration);
            }

            // ========== IP 失敗次數限制 (模擬) ==========
            const FAIL_LIMIT = 3;
            const BLOCK_TIME = 60 * 1000; // 1分鐘
            let failCount = 0;
            let firstFailTime = null;
            let blocked = false;

            function checkBlock() {
                if (blocked) return true;
                if (firstFailTime && (Date.now() - firstFailTime > BLOCK_TIME)) {
                    // 超過一分鐘，重置計數
                    failCount = 0;
                    firstFailTime = null;
                    blockMessage.style.display = 'none';
                }
                return false;
            }

            function recordFail() {
                const now = Date.now();
                if (!firstFailTime) {
                    firstFailTime = now;
                    failCount = 1;
                } else {
                    if (now - firstFailTime <= BLOCK_TIME) {
                        failCount++;
                        if (failCount >= FAIL_LIMIT) {
                            blocked = true;
                            blockMessage.style.display = 'block';
                            // 設定一分鐘後解除封鎖
                            setTimeout(() => {
                                blocked = false;
                                failCount = 0;
                                firstFailTime = null;
                                blockMessage.style.display = 'none';
                            }, BLOCK_TIME);
                        }
                    } else {
                        // 超過一分鐘，重置
                        firstFailTime = now;
                        failCount = 1;
                    }
                }
            }

            function resetFail() {
                failCount = 0;
                firstFailTime = null;
                blocked = false;
                blockMessage.style.display = 'none';
            }

            // ========== 【請在這裡設定原始碼清單】 ==========
            const sourceCodes = {
                "ADMIN": { letter: "A", isAdmin: true },
                "ROOTA": { letter: "A", isAdmin: false },
                "ROOTB": { letter: "B", isAdmin: false },
                "ROOTC": { letter: "C", isAdmin: false },
                "ROOTD": { letter: "D", isAdmin: false },
                "ROOTE": { letter: "E", isAdmin: false },
                "ROOTF": { letter: "F", isAdmin: false },
                "ROOTG": { letter: "G", isAdmin: false },
                "ROOTH": { letter: "H", isAdmin: false },
                "ROOTI": { letter: "I", isAdmin: false },
                "ROOTJ": { letter: "J", isAdmin: false },
                "ROOTK": { letter: "K", isAdmin: false },
                "ROOTL": { letter: "L", isAdmin: false },
                "ROOTM": { letter: "M", isAdmin: false },
                "ROOTN": { letter: "N", isAdmin: false },
                "ROOTO": { letter: "O", isAdmin: false },
                "ROOTP": { letter: "P", isAdmin: false },
                "ROOTQ": { letter: "Q", isAdmin: false },
                "ROOTR": { letter: "R", isAdmin: false },
                "ROOTS": { letter: "S", isAdmin: false },
                "ROOTT": { letter: "T", isAdmin: false },
                "ROOTU": { letter: "U", isAdmin: false },
                "ROOTV": { letter: "V", isAdmin: false },
                "ROOTW": { letter: "W", isAdmin: false },
                "ROOTX": { letter: "X", isAdmin: false },
                "ROOTY": { letter: "Y", isAdmin: false },
                "ROOTZ": { letter: "Z", isAdmin: false },
            };

            // ========== 【請在這裡設定註冊一次性驗證碼】 ==========
            const validInviteCodes = {
                "WELCOME": true,
                "TEST123": true,
                "DEMO2025": true,
                "HELLO": true,
            };

            // ========== 【請在這裡設定每個字母的個人資料與下載內容】 ==========
            const profiles = {
                A: {
                    name: '王小明',
                    role: '專案經理',
                    email: 'a@project.com',
                    phone: '0912-111-111',
                    location: '台北',
                    joinDate: '2025-01-01',
                    linkUrl: 'https://www.google.com',
                    linkText: '前往 A 專案官網',
                    downloadContent: '這是 A 專案的機密文件。\n版本: 1.0\n發行日期: 2025-01-01\n備註: 僅供內部使用。'
                },
                B: {
                    name: '李小華',
                    role: '開發工程師',
                    email: 'b@project.com',
                    phone: '0922-222-222',
                    location: '新竹',
                    joinDate: '2025-02-01',
                    linkUrl: 'https://www.github.com',
                    linkText: 'B 專案原始碼',
                    downloadContent: 'B 專案原始碼壓縮檔 (模擬)\n包含 main.c, config.h 等'
                },
                C: {
                    name: '張小美',
                    role: 'UI設計師',
                    email: 'c@project.com',
                    phone: '0933-333-333',
                    location: '台中',
                    joinDate: '2025-03-01',
                    linkUrl: 'https://www.figma.com',
                    linkText: '設計稿',
                    downloadContent: 'C 專案設計資源\n首頁設計稿.psd\n元件庫.fig'
                },
                // 為了節省篇幅，D~Z 請自行依此格式添加，或沿用預設
                D: { name: '陳大文', role: '行銷專員', email: 'd@project.com', phone: '0944-444-444', location: '高雄', joinDate: '2025-04-01', linkUrl: '#', linkText: '行銷頁面', downloadContent: 'D 專案行銷資料\n季報表.pdf' },
                E: { name: '林怡君', role: '財務分析', email: 'e@project.com', phone: '0955-555-555', location: '台南', joinDate: '2025-05-01', linkUrl: '#', linkText: '財務儀表板', downloadContent: 'E 專案財務模型.xlsx' },
                F: { name: '黃志強', role: '業務代表', email: 'f@project.com', phone: '0966-666-666', location: '桃園', joinDate: '2025-06-01', linkUrl: '#', linkText: '客戶管理', downloadContent: 'F 專案客戶清單.csv' },
                G: { name: '劉淑芬', role: '人資專員', email: 'g@project.com', phone: '0977-777-777', location: '基隆', joinDate: '2025-07-01', linkUrl: '#', linkText: '人資系統', downloadContent: 'G 專案員工手冊.pdf' },
                H: { name: '吳建宏', role: '系統管理員', email: 'h@project.com', phone: '0988-888-888', location: '宜蘭', joinDate: '2025-08-01', linkUrl: '#', linkText: '後台', downloadContent: 'H 專案伺服器配置.zip' },
                I: { name: '鄭雅文', role: '客服主任', email: 'i@project.com', phone: '0911-999-999', location: '花蓮', joinDate: '2025-09-01', linkUrl: '#', linkText: '客服系統', downloadContent: 'I 專案常見問題.docx' },
                J: { name: '蔡明翰', role: '產品設計', email: 'j@project.com', phone: '0922-111-222', location: '台東', joinDate: '2025-10-01', linkUrl: '#', linkText: '設計資源', downloadContent: 'J 專案線框圖.bmpr' },
                K: { name: '許婷婷', role: '品牌經理', email: 'k@project.com', phone: '0933-222-333', location: '澎湖', joinDate: '2025-11-01', linkUrl: '#', linkText: '品牌網站', downloadContent: 'K 專案品牌指南.pdf' },
                L: { name: '何冠宇', role: '數據分析', email: 'l@project.com', phone: '0944-333-444', location: '金門', joinDate: '2025-12-01', linkUrl: '#', linkText: '數據平台', downloadContent: 'L 專案分析報告.ipynb' },
                M: { name: '曾怡婷', role: '法務顧問', email: 'm@project.com', phone: '0955-444-555', location: '馬祖', joinDate: '2025-01-15', linkUrl: '#', linkText: '合約管理', downloadContent: 'M 專案合約範本.docx' },
                N: { name: '蘇怡安', role: '採購專員', email: 'n@project.com', phone: '0966-555-666', location: '東京', joinDate: '2025-02-14', linkUrl: '#', linkText: '採購系統', downloadContent: 'N 專案供應商名單.xlsx' },
                O: { name: '潘志偉', role: '品管工程', email: 'o@project.com', phone: '0977-666-777', location: '大阪', joinDate: '2025-03-20', linkUrl: '#', linkText: '品管平台', downloadContent: 'O 專案檢驗標準.pdf' },
                P: { name: '姜佩珊', role: '公關專員', email: 'p@project.com', phone: '0988-777-888', location: '紐約', joinDate: '2025-04-10', linkUrl: '#', linkText: '新聞稿', downloadContent: 'P 專案新聞稿彙整.docx' },
                Q: { name: '方啟文', role: '技術總監', email: 'q@project.com', phone: '0912-888-999', location: '倫敦', joinDate: '2025-05-05', linkUrl: '#', linkText: '技術文件', downloadContent: 'Q 專案架構圖.drawio' },
                R: { name: '宋慧君', role: '創意總監', email: 'r@project.com', phone: '0923-999-000', location: '巴黎', joinDate: '2025-06-18', linkUrl: '#', linkText: '創意庫', downloadContent: 'R 專案靈感板.pdf' },
                S: { name: '沈大鈞', role: '運營總監', email: 's@project.com', phone: '0934-111-222', location: '柏林', joinDate: '2025-07-22', linkUrl: '#', linkText: '運營儀表板', downloadContent: 'S 專案運營數據.xlsx' },
                T: { name: '賀雲飛', role: '策略顧問', email: 't@project.com', phone: '0945-222-333', location: '雪梨', joinDate: '2025-08-30', linkUrl: '#', linkText: '策略地圖', downloadContent: 'T 專案策略規劃.pptx' },
                U: { name: '白若蘭', role: '投資關係', email: 'u@project.com', phone: '0956-333-444', location: '上海', joinDate: '2025-09-12', linkUrl: '#', linkText: '投資人專區', downloadContent: 'U 專案財報.pdf' },
                V: { name: '文信宏', role: '供應鏈經理', email: 'v@project.com', phone: '0967-444-555', location: '香港', joinDate: '2025-10-25', linkUrl: '#', linkText: '供應鏈追蹤', downloadContent: 'V 專案物流追蹤系統規格.docx' },
                W: { name: '莊心怡', role: '內容編輯', email: 'w@project.com', phone: '0978-555-666', location: '新加坡', joinDate: '2025-11-07', linkUrl: '#', linkText: '內容管理', downloadContent: 'W 專案文章庫.csv' },
                X: { name: '夏于喬', role: '執行助理', email: 'x@project.com', phone: '0989-666-777', location: '首爾', joinDate: '2025-12-19', linkUrl: '#', linkText: '行程表', downloadContent: 'X 專案行事曆.ics' },
                Y: { name: '顧正浩', role: '研發主管', email: 'y@project.com', phone: '0913-777-888', location: '曼谷', joinDate: '2025-01-28', linkUrl: '#', linkText: '研發日誌', downloadContent: 'Y 專案實驗數據.csv' },
                Z: { name: '游舒涵', role: '客戶成功', email: 'z@project.com', phone: '0924-888-999', location: '吉隆坡', joinDate: '2025-02-09', linkUrl: '#', linkText: '客戶支援', downloadContent: 'Z 專案客戶手冊.pdf' }
            };

            let currentUser = null; // { sourceCode, letter, isAdmin }

            function renderSidebar(activeLetter = 'A') {
                sidebar.innerHTML = '';
                let letters = [];
                if (currentUser && currentUser.isAdmin) {
                    for (let i = 0; i < 26; i++) letters.push(String.fromCharCode(65 + i));
                } else if (currentUser) {
                    letters = [currentUser.letter];
                } else return;

                letters.forEach(letter => {
                    const tab = document.createElement('div');
                    tab.className = `alpha-tab ${letter === activeLetter ? 'active' : ''}`;
                    tab.textContent = letter;
                    tab.dataset.letter = letter;
                    tab.addEventListener('click', () => {
                        if (!currentUser.isAdmin && letter !== currentUser.letter) {
                            showToast('⛔ 權限不足，無法查看其他專案');
                            return;
                        }
                        document.querySelectorAll('.alpha-tab').forEach(t => t.classList.remove('active'));
                        tab.classList.add('active');
                        updateProfile(letter);
                        document.getElementById('contentArea').scrollTop = 0;
                    });
                    sidebar.appendChild(tab);
                });
            }

            function updateProfile(letter) {
                const p = profiles[letter] || { name: '未知', role: '', email: '', phone: '', location: '', joinDate: '', linkUrl: '#', linkText: '連結', downloadContent: '無內容' };
                profileAvatar.textContent = letter;
                profileName.textContent = p.name;
                profileRole.textContent = p.role;
                profileEmail.textContent = p.email;
                profilePhone.textContent = p.phone;
                profileLocation.textContent = p.location;
                profileJoin.textContent = p.joinDate;
                if (p.linkUrl) {
                    projectLink.href = p.linkUrl;
                    linkText.textContent = p.linkText || '前往專案網站';
                }
                downloadBtn.dataset.content = p.downloadContent || '無內容';
                downloadBtn.dataset.filename = `${letter}_專案文件.txt`;
            }

            // 下載功能
            downloadBtn.addEventListener('click', function() {
                const content = this.dataset.content || '預設內容';
                const filename = this.dataset.filename || 'download.txt';
                const blob = new Blob([content], { type: 'text/plain;charset=utf-8' });
                const url = URL.createObjectURL(blob);
                const a = document.createElement('a');
                a.href = url;
                a.download = filename;
                document.body.appendChild(a);
                a.click();
                document.body.removeChild(a);
                URL.revokeObjectURL(url);
                showToast(`📥 開始下載 ${filename}`);
            });

            // ========== 登入動畫：使用 GSAP ==========
            function loginSuccessAnimation() {
                return new Promise(resolve => {
                    // 淡出縮小登入區塊
                    gsap.to(authBlock, {
                        opacity: 0,
                        scale: 0.9,
                        duration: 0.3,
                        ease: "power2.in",
                        onComplete: () => {
                            authBlock.style.display = 'none';
                            mainPanel.style.display = 'flex';
                            // 主面板淡入放大
                            gsap.fromTo(mainPanel,
                                { opacity: 0, scale: 0.95 },
                                { opacity: 1, scale: 1, duration: 0.4, ease: "power2.out", onComplete: resolve }
                            );
                        }
                    });
                });
            }

            // 登入：只驗證原始碼
            loginBtn.addEventListener('click', (e) => {
                e.preventDefault();
                if (checkBlock() || blocked) {
                    showToast('⛔ 目前已被封鎖，請稍後再試');
                    return;
                }

                const sourceCode = document.getElementById('sourceCodeInput').value.trim();
                const sourceData = sourceCodes[sourceCode];

                if (!sourceData) {
                    recordFail();
                    loginError.style.display = 'flex';
                    return;
                }

                // 成功登入，重置失敗計數
                resetFail();
                loginError.style.display = 'none';

                loginBtn.classList.add('loading');
                loginBtn.innerHTML = '<i class="fas fa-spinner"></i> 驗證中...';

                setTimeout(() => {
                    loginBtn.classList.remove('loading');
                    loginBtn.innerHTML = '<i class="fas fa-sign-in-alt"></i> 登入';
                    currentUser = { sourceCode, letter: sourceData.letter, isAdmin: sourceData.isAdmin || false };
                    
                    // 更新個人資料 (先準備好)
                    updateProfile(sourceData.letter);
                    
                    // 執行登入動畫
                    loginSuccessAnimation().then(() => {
                        renderSidebar(sourceData.letter);
                        showToast(`✨ 歡迎，原始碼 ${sourceCode} 登入成功！`);
                    });
                }, 500);
            });

            // 註冊：驗證一次性驗證碼
            registerBtn.addEventListener('click', (e) => {
                e.preventDefault();
                if (checkBlock() || blocked) {
                    showToast('⛔ 目前已被封鎖，請稍後再試');
                    return;
                }

                const username = document.getElementById('regUsername').value.trim();
                const password = document.getElementById('regPassword').value.trim();
                const invite = document.getElementById('regInvite').value.trim();

                if (!username || !password || !invite) {
                    registerError.innerHTML = '❌ 所有欄位必填';
                    registerError.style.display = 'flex';
                    recordFail();
                    return;
                }
                if (password.length < 1) { // 可改為 6
                    registerError.innerHTML = '❌ 密碼至少1位 (示範)';
                    registerError.style.display = 'flex';
                    recordFail();
                    return;
                }
                if (!validInviteCodes[invite]) {
                    registerError.innerHTML = '❌ 驗證碼無效';
                    registerError.style.display = 'flex';
                    recordFail();
                    return;
                }

                // 註冊成功，重置失敗計數
                resetFail();
                registerError.style.display = 'none';

                registerBtn.classList.add('loading');
                registerBtn.innerHTML = '<i class="fas fa-spinner"></i> 註冊中...';

                setTimeout(() => {
                    registerBtn.classList.remove('loading');
                    registerBtn.innerHTML = '<i class="fas fa-check-circle"></i> 註冊';
                    showToast('✅ 註冊成功，請等候管理員分配原始碼');
                    document.getElementById('regUsername').value = '';
                    document.getElementById('regPassword').value = '';
                    document.getElementById('regInvite').value = '';
                }, 600);
            });

            logoutBtn.addEventListener('click', () => {
                currentUser = null;
                // 登出動畫：主面板淡出，登入區塊淡入
                gsap.to(mainPanel, {
                    opacity: 0,
                    scale: 0.9,
                    duration: 0.3,
                    ease: "power2.in",
                    onComplete: () => {
                        mainPanel.style.display = 'none';
                        authBlock.style.display = 'flex';
                        gsap.fromTo(authBlock,
                            { opacity: 0, scale: 0.95 },
                            { opacity: 1, scale: 1, duration: 0.4, ease: "power2.out" }
                        );
                        showToast('👋 已安全登出');
                    }
                });
            });

            [notifyToggle, mfaToggle, darkToggle].forEach(t => {
                t.addEventListener('click', function() { this.classList.toggle('active'); });
            });

            loginError.style.display = 'none';
            registerError.style.display = 'none';
            authBlock.style.display = 'flex';
            mainPanel.style.display = 'none';
            blockMessage.style.display = 'none';
        })();
    </script>

    <!-- ========== 🛠️ 完整修改教學 ========== -->
    <!--
        🔧 1. 設定原始碼清單：在 sourceCodes 物件中 (約 250 行)，格式 "原始碼": { letter: "字母", isAdmin: true/false }
        🔧 2. 設定註冊一次性驗證碼：在 validInviteCodes 物件中 (約 280 行)，加入存在的驗證碼即可。
        🔧 3. 設定每個專案的個人資料與下載內容：在 profiles 物件中 (約 290 行)，每個字母可設定：
               name, role, email, phone, location, joinDate, linkUrl, linkText, downloadContent (下載的檔案文字內容)
        🔧 4. 下載按鈕會自動產生包含 downloadContent 的 .txt 檔案，檔名為 {字母}_專案文件.txt。
        🔧 5. IP 失敗限制已實作：同一瀏覽器 1 分鐘內累積 3 次失敗（登入+註冊合併計算）即封鎖 1 分鐘。
        🔧 6. 登入時有 GSAP 動畫：登入區塊淡出縮小，主面板淡入放大。
        🔧 7. 兩個卡片不同色調，易於區分。
    -->
    <!-- ==================================== -->
</body>
</html>
