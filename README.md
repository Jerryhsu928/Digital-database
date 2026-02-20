<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.5">
    <title>✨ 原始碼登入 · A-Z 專案＋100人物資料</title>
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
        /* 粒子背景 */
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

        /* 登入卡片 (獨立置中) */
        .login-container {
            max-width: 450px;
            margin: auto;
            background: rgba(20, 30, 50, 0.9);
            backdrop-filter: blur(15px);
            border-radius: 60px;
            padding: 40px;
            border: 1px solid rgba(255,255,255,0.15);
            box-shadow: 0 30px 50px -20px black;
            text-align: center;
        }
        .login-container h2 {
            font-size: 2rem;
            margin-bottom: 30px;
            color: white;
        }
        .login-form { display: flex; flex-direction: column; gap: 25px; }
        .form-group { text-align: left; }
        .form-group label {
            font-weight: 500; font-size: 0.9rem; color: #a5b4cb;
            display: block; margin-bottom: 8px;
        }
        .form-control {
            width: 100%;
            padding: 18px 25px;
            border: 1px solid #2d3f5e;
            border-radius: 60px;
            background: rgba(10, 20, 35, 0.8);
            color: white;
            font-size: 1.1rem;
            transition: 0.2s;
        }
        .form-control:focus {
            outline: none;
            border-color: #3b82f6;
            box-shadow: 0 0 0 3px rgba(59,130,246,0.3);
        }
        .login-btn {
            background: linear-gradient(135deg, #2563eb, #1e40af);
            color: white;
            border: none;
            padding: 18px;
            border-radius: 60px;
            font-weight: 700;
            font-size: 1.2rem;
            cursor: pointer;
            transition: 0.2s;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            box-shadow: 0 8px 20px #1e3a8a;
        }
        .login-btn:hover { transform: scale(1.02); }
        .login-btn.loading { background: #475569; pointer-events: none; }
        .login-btn.loading i { animation: spin 1s linear infinite; }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
        .error-message {
            color: #f87171; font-size: 0.9rem;
            background: rgba(220,38,38,0.2); padding: 12px 20px; border-radius: 40px;
            border: 1px solid #ef4444; display: none;
            margin-top: 10px;
        }
        .small-note { color: #94a3b8; font-size: 0.85rem; margin-top: 10px; }

        /* 登入後主面板 (左右佈局) */
        .main-panel {
            display: none;
            flex: 1;
            gap: 25px;
            height: 100%;
            overflow: hidden;
        }
        /* 左側 A-Z 側邊欄 (可獨立滑動) */
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

        /* 右側內容區 (可獨立滑動) */
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

        /* 專案個人資訊卡片 */
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

        /* 100個人員卡片區域 */
        .people-section {
            margin-top: 30px;
        }
        .people-section h3 {
            font-size: 1.8rem;
            margin-bottom: 20px;
            color: white;
        }
        .people-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(180px, 1fr));
            gap: 15px;
        }
        .person-card {
            background: rgba(30, 40, 60, 0.6);
            backdrop-filter: blur(5px);
            border-radius: 25px;
            padding: 20px 15px;
            border: 1px solid rgba(255,255,255,0.1);
            text-align: center;
            transition: 0.2s;
        }
        .person-card:hover {
            background: rgba(40, 55, 80, 0.8);
            transform: translateY(-3px);
        }
        .person-avatar {
            width: 60px;
            height: 60px;
            background: #3b82f6;
            border-radius: 30px;
            display: flex;
            align-items: center;
            justify-content: center;
            margin: 0 auto 12px;
            font-size: 1.8rem;
            color: white;
        }
        .person-name {
            font-weight: 600;
            font-size: 1.1rem;
            margin-bottom: 5px;
            color: white;
        }
        .person-role {
            font-size: 0.8rem;
            color: #94a3b8;
        }

        /* 設定卡片 (裝飾) */
        .settings-card {
            background: rgba(25, 35, 55, 0.85);
            backdrop-filter: blur(10px);
            border-radius: 50px;
            padding: 35px;
            margin-top: 25px;
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

        /* 搖晃動畫 */
        .shake {
            animation: shake 0.4s ease-in-out;
        }
        @keyframes shake {
            0%, 100% { transform: translateX(0); }
            20%, 60% { transform: translateX(-8px); }
            40%, 80% { transform: translateX(8px); }
        }
    </style>
</head>
<body>
    <canvas id="particleCanvas"></canvas>

    <div class="app-container">
        <!-- 登入卡片 (只輸入原始碼) -->
        <div id="loginBlock" class="login-container">
            <h2><i class="fas fa-key"></i> 原始碼登入</h2>
            <div class="login-form">
                <div class="form-group">
                    <label>🔢 請輸入原始碼</label>
                    <input type="text" class="form-control" id="sourceCodeInput" placeholder="例如 ROOTA 或 ONCEA" value="ROOTA">
                    <div class="small-note">一次性原始碼只能使用一次，永久原始碼可重複使用。<br>原始碼最後一個字母決定進入哪個專案 (A~Z)。</div>
                </div>
                <div id="loginError" class="error-message"><i class="fas fa-exclamation-triangle"></i> 原始碼錯誤或已失效</div>
                <button class="login-btn" id="loginBtn"><i class="fas fa-sign-in-alt"></i> 登入</button>
            </div>
        </div>

        <!-- 登入後主面板 -->
        <div id="mainPanel" class="main-panel">
            <!-- 左側 A-Z 側邊欄 -->
            <div class="sidebar" id="sidebar"></div>

            <!-- 右側內容區 -->
            <div class="content-area" id="contentArea">
                <!-- 專案個人資訊卡片 (可自訂) -->
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
                            <!-- 可自訂連結按鈕 (例如專案外部連結) -->
                            <a href="#" target="_blank" class="project-link" id="projectLink" style="display:inline-flex; align-items:center; gap:10px; background:#1e293b; color:#60a5fa; padding:12px 25px; border-radius:40px; margin-top:20px; border:1px solid #3b82f6; text-decoration:none;"><i class="fas fa-external-link-alt"></i> <span id="linkText">前往專案網站</span></a>
                        </div>
                    </div>
                </div>

                <!-- 100個人物資料卡片區 (大部分空白，可自行填入) -->
                <div class="people-section">
                    <h3><i class="fas fa-users"></i> 人物詳細資料 (100個)</h3>
                    <div class="people-grid" id="peopleGrid">
                        <!-- 這裡會由 JavaScript 動態生成 100 個卡片，內容可從 profiles.people 陣列讀取 -->
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
                        <button class="login-btn" style="background:#4b5563; width:auto; display:inline-block; margin-right:15px; padding:12px 25px;" id="saveSettingsBtn"><i class="fas fa-save"></i> 儲存設定</button>
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
            const loginBlock = document.getElementById('loginBlock');
            const mainPanel = document.getElementById('mainPanel');
            const loginBtn = document.getElementById('loginBtn');
            const loginError = document.getElementById('loginError');
            const logoutBtn = document.getElementById('logoutBtn');
            const toast = document.getElementById('toast');
            const sidebar = document.getElementById('sidebar');
            const peopleGrid = document.getElementById('peopleGrid');

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

            // 開關
            const notifyToggle = document.getElementById('notifyToggle');
            const mfaToggle = document.getElementById('mfaToggle');
            const darkToggle = document.getElementById('darkToggle');
            document.getElementById('saveSettingsBtn').addEventListener('click', ()=> showToast('✅ 設定已儲存 (示範)'));

            function showToast(msg, duration=2500) {
                toast.textContent = msg; toast.classList.add('show');
                setTimeout(()=>toast.classList.remove('show'), duration);
            }

            // ========== 【請在這裡設定原始碼清單】 ==========
            // 格式: "原始碼字串": { type: "permanent" 或 "once", letter: 對應字母, isAdmin: true/false, used: false (一次性專用) }
            // 一次性原始碼使用後會自動標記 used，下次登入失效
            const sourceCodes = {
                // 管理員永久原始碼
                "ADMIN": { type: "permanent", letter: "A", isAdmin: true },
                // 一般永久原始碼 (最後字母決定專案)
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

                // 一次性原始碼範例 (最後字母決定專案，使用一次後失效)
                "ONCEA": { type: "once", letter: "A", isAdmin: false, used: false },
                "ONCEB": { type: "once", letter: "B", isAdmin: false, used: false },
                "ONCEC": { type: "once", letter: "C", isAdmin: false, used: false },
                // 請自行增加更多一次性原始碼
            };

            // ========== 【請在這裡設定每個專案的個人資料 (顯示在頂部卡片)】 ==========
            const profiles = {
                A: {
                    name: '王小明',
                    role: '專案經理',
                    email: 'a@project.com',
                    phone: '0912-111-111',
                    location: '台北',
                    joinDate: '2025-01-01',
                    linkUrl: 'https://www.google.com',
                    linkText: '前往 A 專案官網'
                },
                B: { name: '李小華', role: '開發工程師', email: 'b@project.com', phone: '0922-222-222', location: '新竹', joinDate: '2025-02-01', linkUrl: '#', linkText: 'B 專案' },
                C: { name: '張小美', role: 'UI設計師', email: 'c@project.com', phone: '0933-333-333', location: '台中', joinDate: '2025-03-01', linkUrl: '#', linkText: 'C 專案' },
                D: { name: '陳大文', role: '行銷專員', email: 'd@project.com', phone: '0944-444-444', location: '高雄', joinDate: '2025-04-01', linkUrl: '#', linkText: 'D 專案' },
                // ... 其他字母請自行補齊 (E~Z 可複製上方格式)
            };
            // 為了避免遺漏，補上 E~Z 預設資料 (您可自行修改)
            for (let i = 69; i <= 90; i++) { // E~Z ASCII 69-90
                let letter = String.fromCharCode(i);
                if (!profiles[letter]) {
                    profiles[letter] = {
                        name: `${letter} 專案負責人`,
                        role: '預設角色',
                        email: `${letter}@project.com`,
                        phone: '0912-000-000',
                        location: '預設地區',
                        joinDate: '2025-01-01',
                        linkUrl: '#',
                        linkText: `${letter} 專案連結`
                    };
                }
            }

            // ========== 【請在這裡設定 100 個人物資料 (可留空白)】 ==========
            // 每個字母專案可獨立設定 100 個人物的資料。這裡先建立一個通用陣列，您可以在下方填入每個人的姓名、角色。
            // 若留空則卡片會顯示「未填寫」。
            const peopleData = {};
            // 初始化 A~Z 的 100 個人資料 (預設全部空白)
            for (let i = 65; i <= 90; i++) {
                let letter = String.fromCharCode(i);
                peopleData[letter] = [];
                for (let j = 1; j <= 100; j++) {
                    peopleData[letter].push({
                        name: '',
                        role: ''
                    });
                }
            }
            // 範例：填入 A 專案的前幾個人物 (您可以自由修改)
            peopleData['A'][0] = { name: '張三', role: '工程師' };
            peopleData['A'][1] = { name: '李四', role: '設計師' };
            peopleData['A'][2] = { name: '王五', role: '行銷' };
            // 其餘 97 個維持空白，卡片會顯示「未填寫」

            // 您也可以在其他字母下填入人物資料，格式同上：
            // peopleData['B'][0] = { name: '趙六', role: '分析師' };

            // 目前登入的使用者
            let currentUser = null; // { sourceCode, letter, isAdmin }

            // 渲染側邊欄
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
                        renderPeopleGrid(letter);
                        document.getElementById('contentArea').scrollTop = 0;
                    });
                    sidebar.appendChild(tab);
                });
            }

            // 更新頂部個人資訊
            function updateProfile(letter) {
                const p = profiles[letter] || { name: '未知', role: '', email: '', phone: '', location: '', joinDate: '', linkUrl: '#', linkText: '連結' };
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
            }

            // 渲染 100 個人物卡片
            function renderPeopleGrid(letter) {
                const data = peopleData[letter] || [];
                let html = '';
                for (let i = 0; i < 100; i++) {
                    const person = data[i] || { name: '', role: '' };
                    html += `
                        <div class="person-card">
                            <div class="person-avatar"><i class="fas fa-user"></i></div>
                            <div class="person-name">${person.name || '未填寫'}</div>
                            <div class="person-role">${person.role || '未填寫'}</div>
                        </div>
                    `;
                }
                peopleGrid.innerHTML = html;
            }

            // 登入按鈕事件 (含動畫)
            loginBtn.addEventListener('click', (e) => {
                e.preventDefault();
                const sourceCode = document.getElementById('sourceCodeInput').value.trim();

                // 檢查原始碼是否存在
                const sourceData = sourceCodes[sourceCode];
                if (!sourceData) {
                    // 錯誤動畫
                    loginError.style.display = 'flex';
                    document.querySelector('.login-container').classList.add('shake');
                    setTimeout(() => document.querySelector('.login-container').classList.remove('shake'), 500);
                    return;
                }

                // 如果是一次性原始碼且已被使用
                if (sourceData.type === 'once' && sourceData.used) {
                    loginError.style.display = 'flex';
                    document.querySelector('.login-container').classList.add('shake');
                    setTimeout(() => document.querySelector('.login-container').classList.remove('shake'), 500);
                    return;
                }

                // 按鈕載入動畫
                loginBtn.classList.add('loading');
                loginBtn.innerHTML = '<i class="fas fa-spinner"></i> 驗證中...';

                setTimeout(() => {
                    // 標記一次性原始碼為已使用 (模擬)
                    if (sourceData.type === 'once') {
                        sourceData.used = true;
                    }

                    loginBtn.classList.remove('loading');
                    loginBtn.innerHTML = '<i class="fas fa-sign-in-alt"></i> 登入';
                    loginError.style.display = 'none';

                    // 設定當前使用者
                    currentUser = {
                        sourceCode,
                        letter: sourceData.letter,
                        isAdmin: sourceData.isAdmin || false
                    };

                    // 切換到主面板
                    loginBlock.style.display = 'none';
                    mainPanel.style.display = 'flex';

                    // 渲染側邊欄與內容
                    renderSidebar(sourceData.letter);
                    updateProfile(sourceData.letter);
                    renderPeopleGrid(sourceData.letter);

                    showToast(`✨ 登入成功！歡迎進入 ${sourceData.letter} 專案`);
                }, 800);
            });

            // 登出
            logoutBtn.addEventListener('click', () => {
                currentUser = null;
                mainPanel.style.display = 'none';
                loginBlock.style.display = 'block';
                showToast('👋 已安全登出');
            });

            // 開關點擊
            [notifyToggle, mfaToggle, darkToggle].forEach(t => {
                t.addEventListener('click', function() { this.classList.toggle('active'); });
            });

            // 初始化
            loginError.style.display = 'none';
            loginBlock.style.display = 'block';
            mainPanel.style.display = 'none';
        })();
    </script>

    <!-- ========== 🛠️ 完整修改教學 ========== -->
    <!--
        🔧 1. 設定原始碼清單 (永久/一次性)
           在 sourceCodes 物件中 (約第 250 行)：
           - 永久原始碼格式 "代碼": { type: "permanent", letter: "字母", isAdmin: true/false }
           - 一次性原始碼格式 "代碼": { type: "once", letter: "字母", isAdmin: false, used: false }
           一次性原始碼使用後會自動標記 used，下次失效。

        🔧 2. 設定每個專案的個人資訊 (頂部卡片)
           在 profiles 物件中 (約第 300 行)，每個字母可設定 name, role, email, phone, location, joinDate, linkUrl, linkText。

        🔧 3. 設定 100 個人物資料 (大部分空白)
           在 peopleData 物件中 (約第 340 行)，peopleData[字母] 是一個長度 100 的陣列，每個元素為 { name, role }。
           您可以直接修改 peopleData['A'][0] 等來填入人物，其餘維持空白，卡片會顯示「未填寫」。

        🔧 4. 側邊欄獨立滾動，權限管理 (管理員可看全部)。

        🔧 5. 登入動畫：按鈕載入旋轉、錯誤時搖晃卡片、成功後 toast 提示。
    -->
    <!-- ========================================= -->
</body>
</html>
