<!DOCTYPE html>

<html lang=“fa” dir=“rtl”>

<head>

<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>App Earn Rewards</title>
<style>
    :root {
        --primary: #6200ee;
        --secondary: #03dac6;
        --background: #f5f5f5;
        --surface: #ffffff;
        --error: #cf6679;
        --text: #333333;
    }

    body {
        font-family: 'Segoeautogui', Tahoma, Geneva, Verdana, sans-serif;
        background-color: var(--background);
        margin: 0;
        display: flex;
        justify-content: center;
        align-items: center;
        min-height: 100vh;
        color: var(--text);
    }

    .app-container {
        width: 100%;
        max-width: 400px;
        height: 90vh;
        background: var(--surface);
        border-radius: 20px;
        box-shadow: 0 10px 30px rgba(0,0,0,0.1);
        position: relative;
        overflow-y: auto;
        padding: 20px;
        box-sizing: border-box;
    }

    /* Screens */
    .screen { display: none; }
    .screen.active { display: block; }

    /* Auth Styles */
    .auth-form h2 { text-align: center; color: var(--primary); }
    input {
        width: 100%;
        padding: 12px;
        margin: 10px 0;
        border: 1px solid #ddd;
        border-radius: 8px;
        box-sizing: border-box;
    }
    button {
        width: 100%;
        padding: 12px;
        background: var(--primary);
        color: white;
        border: none;
        border-radius: 8px;
        cursor: pointer;
        font-size: 16px;
        margin-top: 10px;
    }
    .btn-secondary { background: var(--secondary); color: #000; }

    /* Dashboard Styles */
    .header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; }
    .score-card {
        background: linear-gradient(135deg, var(--primary), #3700b3);
        color: white;
        padding: 20px;
        border-radius: 15px;
        text-align: center;
        margin-bottom: 20px;
    }
    .score-val { font-size: 32px; font-weight: bold; }

    /* Ad Section */
    .ad-box {
        width: 100%;
        height: 150px;
        background: #eee;
        border-radius: 12px;
        display: flex;
        justify-content: center;
        align-items: center;
        margin-bottom: 15px;
        border: 2px dashed #ccc;
        overflow: hidden;
    }
    .ad-box img { width: 100%; height: 100%; object-fit: cover; }

    .click-btn {
        height: 100px;
        width: 100px;
        border-radius: 50%;
        background: var(--primary);
        color: white;
        font-size: 20px;
        border: 5px solid #e1bee7;
        margin: 0 auto 20px;
        display: block;
        cursor: pointer;
        transition: transform 0.1s;
    }
    .click-btn:active { transform: scale(0.9); }

    /* Sections */
    .section-title { font-size: 18px; font-weight: bold; margin: 20px 0 10px; border-right: 4px solid var(--primary); padding-right: 10px; }
    .card {
        background: #fff;
        border: 1px solid #eee;
        padding: 15px;
        border-radius: 12px;
        margin-bottom: 10px;
        display: flex;
        justify-content: space-between;
        align-items: center;
    }

    /* Modal */
    .modal {
        display: none;
        position: fixed;
        top: 0; left: 0; width: 100%; height: 100%;
        background: rgba(0,0,0,0.5);
        justify-content: center;
        align-items: center;
        z-index: 100;
    }
    .modal-content {
        background: white;
        padding: 20px;
        border-radius: 15px;
        width: 80%;
        max-width: 300px;
        text-align: center;
    }

    /* Support */
    .support-box {
        background: #e8f5e9;
        padding: 15px;
        border-radius: 10px;
        text-align: center;
        cursor: pointer;
    }

    .limit-warning { color: var(--error); font-size: 12px; text-align: center; }
</style>
</head>

<body>

<div class=“app-container”>

<!-- 1. Auth Screen -->
<div id="screen-auth" class="screen active">
    <div class="auth-form">
        <h2 id="auth-title">ورود به حساب</h2>
        <input type="tel" id="auth-phone" placeholder="شماره موبایل">
        <input type="password" id="auth-pass" placeholder="رمز عبور">
        <button onclick="handleAuth()" id="auth-btn">ورود</button>
        <button class="btn-secondary" onclick="toggleAuthMode()" id="auth-toggle-btn">ثبت نام جدید</button>
    </div>
</div>

<!-- 2. Main Dashboard -->
<div id="screen-main" class="screen">
    <div class="header">
        <span>خوش آمدی!</span>
        <button onclick="logout()" style="width:auto; padding:5px 10px; font-size:12px; background:var(--error)">خروج</button>
    </div>

    <div class="score-card">
        <div>امتیاز فعلی شما</div>
        <div class="score-val" id="display-score">0</div>
    </div>

    <div class="section-title">تبلیغات</div>
    <div class="ad-box" id="ad-container">
        <span>در حال بارگذاری تبلیغ...</span>
    </div>

    <button class="click-btn" onclick="handleAdClick()">کلیک کن!</button>
    <div class="limit-warning" id="limit-text"></div>

    <div class="section-title">استخرها (افزایش قدرت)</div>
    <div id="pool-list">
        <!-- Pools rendered by JS -->
    </div>

    <div class="section-title">تبدیل امتیاز به گیگ</div>
    <div class="card">
        <span>50 امتیاز = 1 گیگ</span>
        <button onclick="redeem(50, 1)" style="width:auto">تبدیل</button>
    </div>
    <div class="card">
        <span>100 امتیاز = 2 گیگ</span>
        <button onclick="redeem(100, 2)" style="width:auto">تبدیل</button>
    </div>
    <div class="card">
        <span>400 امتیاز = 5 گیگ</span>
        <button onclick="redeem(400, 5)" style="width:auto">تبدیل</button>
    </div>

    <div class="section-title">پشتیبانی</div>
    <div class="support-box" onclick="showSupport()">
        🆔 پشتیبانی روبیکا: <b>AD1_ZERO</b>
    </div>
</div>
</div>

<!-- Modals -->

<div id=“modal-alert” class=“modal”>

<div class="modal-content">
    <p id="modal-msg"></p>
    <button onclick="closeModal()">متوجه شدم</button>
</div>
</div>

<div id=“modal-screenshot” class=“modal”>

<div class="modal-content">
    <h3>⚠️ هشدار مهم</h3>
    <p>لطفاً از صفحه تبدیل امتیاز اسکرین‌شات بگیرید و برای پشتیبانی در روبیکا ارسال کنید.</p>
    <p style="font-size:12px; color:gray;">آیدی: AD1_ZERO</p>
    <button onclick="closeModal()">بستن</button>
</div>
</div>

<script>

// --- STATE MANAGEMENT ---
let user = JSON.parse(localStorage.getItem('user')) || null;
let isLoginMode = true;

// Data Structure
let state = {
    score: 0,
    dailyClicks: 0,
    lastClickDate: new Date().toLocaleDateString(),
    activePool: 0, // 0: none, 1: pool1, etc.
    pools: [
        { id: 1, name: "استخر اول", multiplier: 0.5, price: 100, owned: false },
        { id: 2, name: "استخر دوم", multiplier: 1.5, price: 300, owned: false },
        { id: 3, name: "استخر سوم", multiplier: 3, price: 800, owned: false }
    ]
};

// Mock Ads
const ads = [
    "https://picsum.photos/400/200?random=1",
    "https://picsum.photos/400/200?random=2",
    "https://picsum.photos/400/200?random=3",
    "https://picsum.photos/400/200?random=4"
];

// --- INITIALIZATION ---
window.onload = () => {
    if(user) {
        showScreen('screen-main');
        loadUserData();
        renderPools();
        updateAd();
    } else {
        showScreen('screen-auth');
    }
};

// --- AUTH LOGIC ---
function toggleAuthMode() {
    isLoginMode = !isLoginMode;
    document.getElementById('auth-title').innerText = isLoginMode ? 'ورود به حساب' : 'ثبت نام جدید';
    document.getElementById('auth-btn').innerText = isLoginMode ? 'ورود' : 'ثبت نام';
    document.getElementById('auth-toggle-btn').innerText = isLoginMode ? 'ثبت نام جدید' : 'ورود به حساب قبلی';
}

function handleAuth() {
    const phone = document.getElementById('auth-phone').value;
    const pass = document.getElementById('auth-pass').value;

    if(!phone || !pass) return alert("لطفا همه فیلدها را پر کنید");

    // Mock Auth (Since no DB yet)
    user = { phone, pass };
    localStorage.setItem('user', JSON.stringify(user));
    
    // Load state from LocalStorage if exists
    const savedState = localStorage.getItem(`state_${phone}`);
    if(savedState) state = JSON.parse(savedState);

    showScreen('screen-main');
    renderPools();
    updateAd();
    updateUI();
}

function logout() {
    localStorage.removeItem('user');
    location.reload();
}

// --- CORE LOGIC ---
function handleAdClick() {
    // 1. Check Daily Limit
    const today = new Date().toLocaleDateString();
    if(state.lastClickDate !== today) {
        state.dailyClicks = 0;
        state.lastClickDate = today;
    }

    if(state.dailyClicks >= 10) {
        showAlert("محدودیت روزانه! شما امروز ۱۰ کلیک انجام داده‌اید. فردا دوباره بیایید.");
        return;
    }

    // 2. Calculate Score
    let baseGain = 1;
    let bonus = 0;
    state.pools.forEach(p => {
        if(p.owned) bonus += p.multiplier;
    });

    let totalGain = baseGain + bonus;
    state.score += totalGain;
    state.dailyClicks += 1;

    // 3. Update State
    saveState();
    updateUI();
    updateAd(); // Change ad after click
}

function redeem(cost, gb) {
    if(state.score < cost) {
        showAlert("امتیاز کافی ندارید!");
        return;
    }

    // In real app, we'd subtract score here after server confirmation
    // For now, we just show the warning as requested
    document.getElementById('modal-screenshot').style.display = 'flex';
}

function buyPool(poolId) {
    const pool = state.pools.find(p => p.id === poolId);
    if(state.score < pool.price) {
        showAlert("امتیاز کافی برای خرید استخر ندارید!");
        return;
    }

    state.score -= pool.price;
    pool.owned = true;
    saveState();
    renderPools();
    updateUI();
    showAlert(`تبریک! ${pool.name} فعال شد.`);
}

// --- UI HELPERS ---
function showScreen(id) {
    document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
    document.getElementById(id).classList.add('active');
}

function updateUI() {
    document.getElementById('display-score').innerText = state.score;
    document.getElementById('limit-text').innerText = `کلیک‌های امروز: ${state.dailyClicks} از 10`;
}

function updateAd() {
    const container = document.getElementById('ad-container');
    const randomAd = ads[Math.floor(Math.random() * ads.length)];
    container.innerHTML = `<img src="${randomAd}" alt="ad">`;
}

function renderPools() {
    const list = document.getElementById('pool-list');
    list.innerHTML = '';
    state.pools.forEach(p => {
        const div = document.createElement('div');
        div.className = 'card';
        div.innerHTML = `
            <div>
                <b>${p.name}</b>

                <small>امتیاز اضافه: +${p.multiplier}</small>
            </div>
            <button onclick="buyPool(${p.id})" style="width:auto; margin:0; padding:5px 10px; background:${p.owned ? '#ccc' : 'var(--primary)'}">
                ${p.owned ? 'خرید شده' : p.price + ' امتیاز'}
            </button>
        `;
        list.appendChild(div);
    });
}

function saveState() {
    localStorage.setItem(`state_${user.phone}`, JSON.stringify(state));
}

function loadUserData() {
    const savedState = localStorage.getItem(`state_${user.phone}`);
    if(savedState) state = JSON.parse(savedState);
    updateUI();
}

function showAlert(msg) {
    document.getElementById('modal-msg').innerText = msg;
    document.getElementById('modal-alert').style.display = 'flex';
}

function showSupport() {
    showAlert("لطفا برای پشتیبانی به آیدی روبیکا پیام دهید:\n\nAD1_ZERO");
}

function closeModal() {
    document.querySelectorAll('.modal').forEach(m => m.style.display = 'none');
}
</script>

</body>

</html>

