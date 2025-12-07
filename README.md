<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Ruhi Textile App</title>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        /* --- 1. CORE VARIABLES --- */
        :root { 
            --primary: #00f2ff; 
            --secondary: #bc13fe; 
            --gold: #FFD700;
            --bg: #000000; 
            --card: #1a1a20;
            --text: #ffffff;
            --border: #333;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: sans-serif; -webkit-tap-highlight-color: transparent; }
        body { background: var(--bg); width: 100%; height: 100vh; overflow: hidden; }

        /* --- 2. SCREENS --- */
        .screen {
            position: absolute; top: 0; left: 0; width: 100%; height: 100%;
            background: var(--bg);
            display: none; flex-direction: column;
            z-index: 10; overflow-y: auto;
        }
        #loginScreen { display: flex; align-items: center; justify-content: center; }

        /* --- 3. NEW STYLISH ALERT POPUP --- */
        .alert-overlay {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0,0,0,0.9); z-index: 100000; /* Highest Layer */
            display: none; align-items: center; justify-content: center;
            backdrop-filter: blur(5px);
        }

        .alert-box {
            width: 85%; max-width: 300px;
            background: #14141e; border: 2px solid var(--primary);
            border-radius: 20px; padding: 25px; text-align: center;
            box-shadow: 0 0 50px rgba(0, 242, 255, 0.3);
            animation: popIn 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
        }
        @keyframes popIn { from { transform: scale(0); opacity: 0; } to { transform: scale(1); opacity: 1; } }

        .alert-icon { font-size: 40px; color: var(--gold); margin-bottom: 15px; }
        .alert-title { color: var(--primary); font-size: 20px; font-weight: 900; margin-bottom: 10px; text-transform: uppercase; }
        .alert-msg { color: #fff; font-size: 14px; margin-bottom: 20px; line-height: 1.5; }
        
        .alert-btn {
            width: 100%; padding: 12px; border: none; border-radius: 50px;
            background: linear-gradient(90deg, var(--primary), var(--secondary));
            color: white; font-weight: bold; font-size: 14px; cursor: pointer;
            box-shadow: 0 5px 15px rgba(0,0,0,0.3);
        }
        .alert-btn:active { transform: scale(0.95); }

        /* --- 4. LOGIN UI --- */
        .login-container {
            width: 90%; max-width: 400px; text-align: center; padding: 20px;
            position: relative; z-index: 100;
        }

        .ruhi-title {
            font-size: 36px; font-weight: 900; color: var(--primary); 
            margin-bottom: 10px; letter-spacing: 2px;
            text-shadow: 0 0 10px rgba(0, 242, 255, 0.5);
        }

        .promo { color: var(--gold); border: 1px dashed var(--gold); padding: 5px 10px; border-radius: 20px; display: inline-block; margin-bottom: 30px; font-size: 12px; }

        input {
            width: 100%; padding: 15px; margin-bottom: 15px;
            background: #222; border: 1px solid #444; color: white;
            border-radius: 10px; outline: none; font-size: 16px;
            pointer-events: auto;
        }

        .btn {
            width: 100%; padding: 15px; border: none; border-radius: 50px;
            font-weight: bold; font-size: 14px; cursor: pointer;
            margin-top: 10px; pointer-events: auto; position: relative; z-index: 200;
        }
        .btn-main { background: linear-gradient(90deg, var(--primary), var(--secondary)); color: white; }
        .btn-sec { background: transparent; border: 1px solid var(--secondary); color: var(--secondary); }
        .btn-help { background: #333; color: #ccc; font-size: 12px; padding: 10px; }

        /* --- 5. DASHBOARD UI --- */
        .header {
            height: 60px; padding: 0 15px; display: flex; justify-content: space-between; align-items: center;
            background: #111; border-bottom: 1px solid #333; flex-shrink: 0;
        }
        .header i { font-size: 22px; color: white; cursor: pointer; }

        .content-body { flex: 1; overflow-y: auto; padding: 15px; padding-bottom: 80px; }

        .banner {
            width: 100%; height: 160px; background: linear-gradient(45deg, #ff0055, #bc13fe);
            border-radius: 15px; display: flex; flex-direction: column; justify-content: center; padding: 20px;
            color: white; margin-bottom: 20px; position: relative; overflow: hidden;
        }
        .banner h2 { font-size: 24px; font-weight: 900; margin-bottom: 5px; }
        .banner i { position: absolute; right: -10px; bottom: -20px; font-size: 90px; opacity: 0.3; }

        .cat-scroll { display: flex; gap: 15px; overflow-x: auto; padding-bottom: 10px; margin-bottom: 15px; }
        .cat-icon { width: 60px; height: 60px; border-radius: 50%; background: #222; border: 2px solid var(--primary); display: flex; align-items: center; justify-content: center; color: white; font-size: 24px; flex-shrink: 0; }

        .grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
        .card { background: var(--card); padding: 8px; border-radius: 10px; border: 1px solid #333; }
        .p-img { width: 100%; height: 160px; background: #333; border-radius: 8px; object-fit: cover; }
        .p-name { color: white; font-size: 13px; font-weight: bold; margin-top: 5px; }
        .p-price { color: var(--primary); font-size: 14px; font-weight: bold; }
        .p-btn { width: 100%; padding: 8px; background: white; border: none; border-radius: 5px; font-weight: bold; margin-top: 5px; cursor: pointer; }

        /* Loader */
        .loader { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.9); z-index: 5000; display: none; align-items: center; justify-content: center; flex-direction: column; }
        .spinner { width: 50px; height: 50px; border: 5px solid var(--primary); border-top-color: transparent; border-radius: 50%; animation: spin 1s linear infinite; }
        @keyframes spin { to { transform: rotate(360deg); } }

        /* Sidebar & Top Bar */
        .sidebar { position: fixed; top: 0; left: -280px; width: 280px; height: 100%; background: #111; z-index: 2000; transition: 0.3s; padding: 20px; border-right: 1px solid var(--primary); }
        .sidebar.active { left: 0; }
        .menu-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.8); z-index: 1500; display: none; }
        .side-item { padding: 15px 0; color: white; border-bottom: 1px solid #333; display: flex; gap: 15px; align-items: center; }

        .login-top { position: absolute; top: 0; left: 0; width: 100%; padding: 15px; display: flex; justify-content: space-between; z-index: 200; pointer-events: none; }
        .login-top i { color: #888; font-size: 22px; cursor: pointer; pointer-events: auto; }

        .nav { height: 60px; background: #111; position: fixed; bottom: 0; width: 100%; display: flex; justify-content: space-around; align-items: center; border-top: 1px solid #333; z-index: 100; }
        .nav i { font-size: 22px; color: #666; }
        .nav i.active { color: var(--primary); }
    </style>
</head>
<body>

    <div id="customAlert" class="alert-overlay">
        <div class="alert-box">
            <div class="alert-icon"><i class="fas fa-bell"></i></div>
            <div class="alert-title">RUHI ALERT</div>
            <p class="alert-msg" id="alertTxt">Welcome to Ruhi Fashion!</p>
            <button class="alert-btn" onclick="closeAlert()">OK, GOT IT</button>
        </div>
    </div>

    <div id="loader" class="loader">
        <div class="spinner"></div>
        <p style="color:var(--primary); margin-top:15px;">Processing...</p>
    </div>

    <div id="menuOverlay" class="menu-overlay" onclick="toggleMenu()"></div>
    <div id="sidebar" class="sidebar">
        <h2 style="color:var(--primary); margin-bottom:20px;">MENU</h2>
        <div class="side-item" onclick="openMap()"><i class="fas fa-map-marker-alt" style="color:gold;"></i> GUJARAT SURAT</div>
        <div class="side-item" onclick="doCall()"><i class="fas fa-phone"></i> Call Support</div>
        <div class="side-item" onclick="doChat()"><i class="fab fa-whatsapp"></i> WhatsApp</div>
        <div class="side-item" style="color:red;" onclick="location.reload()"><i class="fas fa-sign-out-alt"></i> Logout</div>
    </div>

    <div id="loginScreen" class="screen">
        <div class="login-top">
            <i class="fas fa-bars" onclick="showAlert('Please Login First')"></i>
            <div style="display:flex; gap:15px;">
                <i class="fas fa-bell" onclick="showAlert('No Notifications')"></i>
                <i class="fas fa-cog" onclick="showAlert('Settings Locked')"></i>
            </div>
        </div>

        <div class="login-container">
            <div class="ruhi-title">RUHI FASHION</div>
            <div class="promo">50% OFF SALE LIVE</div>

            <input type="text" id="uid" placeholder="Email ID">
            <input type="password" id="pass" placeholder="Password">

            <button class="btn btn-main" onclick="handleLogin()">LOGIN</button>
            <button class="btn btn-sec" onclick="openScreen('detailScreen')">CREATE ACCOUNT</button>
            <button class="btn btn-help" onclick="showAlert('Support is coming soon!')">NEED HELP?</button>
        </div>
    </div>

    <div id="detailScreen" class="screen" style="justify-content:center; align-items:center;">
        <div class="login-container">
            <h2 style="color:white; margin-bottom:20px;">PROFILE</h2>
            <div style="width:80px; height:80px; background:var(--primary); border-radius:50%; margin:0 auto 20px; display:flex; align-items:center; justify-content:center; font-size:40px; font-weight:bold;">U</div>
            
            <input type="text" id="uname" placeholder="Your Name">
            <input type="number" id="uage" placeholder="Age">
            <input type="text" id="ucity" placeholder="City">

            <button class="btn btn-main" onclick="saveData()">ENTER SHOP</button>
        </div>
    </div>

    <div id="appScreen" class="screen">
        <div class="header">
            <i class="fas fa-bars" onclick="toggleMenu()"></i>
            <span style="color:var(--primary); font-weight:bold; font-size:18px;">RUHI FASHION</span>
            <i class="fas fa-shopping-cart" onclick="showAlert('Cart is Empty')"></i>
        </div>

        <div class="content-body">
            <div class="banner">
                <div style="z-index:2;">
                    <h2>RUHI FASHION</h2>
                    <p style="font-size:14px;">Best Sale Offer</p>
                </div>
                <i class="fas fa-shopping-bag"></i>
            </div>

            <div class="cat-scroll">
                <div style="text-align:center;"><div class="cat-icon"><i class="fas fa-female"></i></div><small style="color:white;">Saree</small></div>
                <div style="text-align:center;"><div class="cat-icon"><i class="fas fa-tshirt"></i></div><small style="color:white;">Kurti</small></div>
                <div style="text-align:center;"><div class="cat-icon"><i class="fas fa-crown"></i></div><small style="color:white;">Lehenga</small></div>
                <div style="text-align:center;"><div class="cat-icon"><i class="fas fa-gem"></i></div><small style="color:white;">Jewelry</small></div>
            </div>

            <div style="background:#222; padding:10px; border-radius:10px; margin-bottom:15px; display:flex; align-items:center; gap:10px; border:1px solid #333;">
                <div style="width:40px; height:40px; background:var(--primary); border-radius:50%; display:flex; align-items:center; justify-content:center; font-weight:bold; font-size:20px;" id="userLet">U</div>
                <div style="color:white; font-weight:bold;" id="dname">GUEST</div>
            </div>

            <div style="color:white; font-weight:bold; margin-bottom:10px;">Trending Now</div>
            <div class="grid" id="plist"></div>
        </div>

        <div class="nav">
            <i class="fas fa-home active"></i>
            <i class="fas fa-search" onclick="showAlert('Search Coming Soon')"></i>
            <i class="fas fa-heart" onclick="showAlert('Wishlist Empty')"></i>
            <i class="fas fa-user" onclick="showAlert('Profile Settings')"></i>
        </div>
    </div>

    <script>
        const PHONE = "919608720622";
        const LOCATION = "https://share.google/KtTDecNh54sPJIE82";
        
        const ITEMS = [
            {n:'Bollywood Saree', p:1200, i:'IMG_20251206_210754.png'},
            {n:'Khadi Kurti', p:550, i:'IMG_20251206_210813.png'},
            {n:'Bridal Lehenga', p:5000, i:'IMG_20251206_210835.png'},
            {n:'Azouk Dori', p:620, i:'IMG_20251206_210852.png'}
        ];

        // --- NAVIGATION ---
        function openScreen(id) {
            document.querySelectorAll('.screen').forEach(s => s.style.display = 'none');
            document.getElementById(id).style.display = 'flex';
        }

        // --- ALERT SYSTEM ---
        function showAlert(msg) {
            document.getElementById('alertTxt').innerText = msg;
            document.getElementById('customAlert').style.display = 'flex';
        }
        function closeAlert() {
            document.getElementById('customAlert').style.display = 'none';
        }

        // --- LOGIC ---
        function handleLogin() {
            let u = document.getElementById('uid').value;
            let p = document.getElementById('pass').value;
            if(!u || !p) { showAlert("Enter Email & Password"); return; }
            
            document.getElementById('loader').style.display = 'flex';
            setTimeout(() => {
                document.getElementById('loader').style.display = 'none';
                if(localStorage.getItem('ruhi_user')) openScreen('appScreen');
                else openScreen('detailScreen');
            }, 1000);
        }

        function saveData() {
            let n = document.getElementById('uname').value;
            if(!n) { showAlert("Enter Name"); return; }
            localStorage.setItem('ruhi_user', n);
            localStorage.setItem('ruhi_age', document.getElementById('uage').value);
            localStorage.setItem('ruhi_city', document.getElementById('ucity').value);
            
            setProfile(n);
            document.getElementById('loader').style.display = 'flex';
            setTimeout(() => {
                document.getElementById('loader').style.display = 'none';
                openScreen('appScreen');
            }, 1000);
        }

        function setProfile(name) {
            document.getElementById('dname').innerText = name.toUpperCase();
            document.getElementById('userLet').innerText = name.charAt(0).toUpperCase();
        }

        // RENDER
        window.onload = function() {
            let h = '';
            ITEMS.forEach(x => {
                let img = x.i; 
                h += `<div class="card">
                    <img src="${img}" class="p-img" onerror="this.src='https://via.placeholder.com/150'">
                    <div class="p-name">${x.n}</div>
                    <div class="p-price">₹${x.p}</div>
                    <button class="p-btn" onclick="buy('${x.n}',${x.p})">BUY NOW</button>
                </div>`;
            });
            document.getElementById('plist').innerHTML = h;

            if(localStorage.getItem('ruhi_user')) {
                setProfile(localStorage.getItem('ruhi_user'));
            }
        };

        function buy(n, p) {
            document.getElementById('loader').style.display = 'flex';
            setTimeout(() => {
                document.getElementById('loader').style.display = 'none';
                let u = localStorage.getItem('ruhi_user') || 'Guest';
                let a = localStorage.getItem('ruhi_age') || 'N/A';
                let c = localStorage.getItem('ruhi_city') || 'N/A';
                let msg = `*ORDER*%0AItem: ${n}%0APrice: ${p}%0A%0A*Customer:*%0AName: ${u}%0AAge: ${a}%0ACity: ${c}`;
                window.open(`https://wa.me/${PHONE}?text=${msg}`, '_blank');
            }, 2000);
        }

        function toggleMenu() {
            let s = document.getElementById('sidebar');
            let o = document.getElementById('menuOverlay');
            if(s.classList.contains('active')) {
                s.classList.remove('active'); o.style.display='none';
            } else {
                s.classList.add('active'); o.style.display='block';
            }
        }

        function openMap() { window.open(LOCATION, '_blank'); }
        function doCall() { window.location.href = `tel:${PHONE}`; }
        function doChat() { window.open(`https://wa.me/${PHONE}`, '_blank'); }
    </script>
</body>
</html>
