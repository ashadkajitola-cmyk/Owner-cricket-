<!DOCTYPE html>
<html lang="hi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Cricket Central Pro</title>
    
    <!-- Firebase App & Realtime Database (Compat SDK) -->
    <script src="https://www.gstatic.com/firebasejs/9.22.2/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.2/firebase-database-compat.js"></script>

    <style>
        :root {
            --primary: #0f766e;
            --primary-dark: #115e59;
            --bg-light: #f8fafc;
            --card-bg: #ffffff;
            --text-main: #1e293b;
            --text-muted: #64748b;
            --accent: #f59e0b;
            --danger: #ef4444;
            --success: #10b981;
        }
        * { box-sizing: border-box; }
        body { 
            font-family: 'Inter', system-ui, -apple-system, sans-serif; 
            background: var(--bg-light); 
            margin: 0; 
            padding: 0; 
            color: var(--text-main); 
        }
        .app-header {
            background: var(--primary);
            color: white;
            padding: 16px 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            box-shadow: 0 4px 6px -1px rgba(0,0,0,0.1);
        }
        .app-header h2 { margin: 0; font-size: 20px; font-weight: 700; letter-spacing: 0.5px; }
        .admin-badge-btn {
            background: white;
            color: var(--primary);
            border: none;
            padding: 6px 14px;
            border-radius: 6px;
            font-weight: 600;
            cursor: pointer;
            box-shadow: 0 2px 4px rgba(0,0,0,0.05);
            transition: all 0.2s;
        }
        .admin-badge-btn:hover { background: #f1f5f9; }
        
        .tabs-container {
            background: var(--primary-dark);
            display: flex;
            justify-content: space-around;
            padding: 0 10px;
            overflow-x: auto;
            box-shadow: inset 0 2px 4px rgba(0,0,0,0.1);
        }
        .tab-btn {
            background: none;
            border: none;
            color: rgba(255,255,255,0.7);
            padding: 14px 16px;
            font-weight: 600;
            cursor: pointer;
            font-size: 13px;
            text-transform: uppercase;
            white-space: nowrap;
            transition: color 0.2s;
        }
        .tab-btn.active {
            color: white;
            border-bottom: 3px solid white;
        }

        .container { max-width: 520px; margin: auto; padding: 16px; }
        .card { 
            background: var(--card-bg); 
            padding: 18px; 
            border-radius: 12px; 
            margin-bottom: 16px; 
            box-shadow: 0 4px 6px -1px rgba(0,0,0,0.05), 0 2px 4px -1px rgba(0,0,0,0.03);
            border: 1px solid #e2e8f0;
        }
        input, select, button { 
            width: 100%; 
            padding: 11px 14px; 
            margin-top: 8px; 
            border: 1px solid #cbd5e1; 
            border-radius: 8px; 
            font-size: 14px;
            outline: none;
            transition: border-color 0.2s;
        }
        input:focus, select:focus { border-color: var(--primary); }
        
        button.action-btn { 
            background: var(--primary); 
            color: white; 
            border: none; 
            font-weight: 600; 
            cursor: pointer; 
            box-shadow: 0 2px 4px rgba(15, 118, 110, 0.2);
            transition: background 0.2s;
        }
        button.action-btn:hover { background: var(--primary-dark); }
        
        .logout-btn { 
            background: var(--danger); 
            color: white; 
            margin-top: 12px; 
            border: none; 
            font-weight: 600; 
            padding: 11px; 
            border-radius: 8px; 
            cursor: pointer;
            transition: opacity 0.2s;
        }
        .logout-btn:hover { opacity: 0.9; }
        .hidden { display: none !important; }

        /* Login Overlay */
        #loginOverlay {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(15, 23, 42, 0.85); z-index: 9999; display: flex;
            justify-content: center; align-items: center; backdrop-filter: blur(4px);
        }
        .login-box {
            background: #fff; width: 90%; max-width: 400px; padding: 28px;
            border-radius: 16px; text-align: center; box-shadow: 0 20px 25px -5px rgba(0,0,0,0.1);
        }
        .login-box h3 { margin-top: 0; color: var(--text-main); font-size: 22px; }

        /* Saved Accounts Selector */
        .saved-accounts { margin-bottom: 14px; text-align: left; }
        .saved-account-chip {
            display: inline-block; background: #f1f5f9; border: 1px solid #cbd5e1;
            padding: 6px 12px; border-radius: 20px; font-size: 13px; font-weight: 600;
            cursor: pointer; margin: 4px 4px 0 0; color: var(--text-main);
        }
        .saved-account-chip:hover { background: #e2e8f0; }

        .match-box {
            background: #fff;
            border: 1px solid #e2e8f0;
            border-radius: 10px;
            padding: 14px;
            margin-bottom: 12px;
            box-shadow: 0 1px 3px rgba(0,0,0,0.02);
        }
        .match-info-top {
            display: flex;
            justify-content: space-between;
            font-size: 12px;
            color: var(--text-muted);
            margin-bottom: 8px;
            font-weight: 500;
        }
        .match-teams {
            display: flex;
            justify-content: space-between;
            align-items: center;
            font-weight: 700;
            font-size: 16px;
            margin: 10px 0;
        }
        .match-toss-banner {
            background: #ccfbf1;
            color: #115e59;
            padding: 6px 10px;
            font-size: 12px;
            border-radius: 6px;
            margin-top: 8px;
            font-weight: 600;
        }
        .match-result-banner {
            background: #fee2e2;
            color: #991b1b;
            padding: 6px 10px;
            font-size: 12px;
            border-radius: 6px;
            margin-top: 8px;
            font-weight: 600;
        }

        .pro-table-container { background: #fff; border-radius: 10px; overflow: hidden; border: 1px solid #e2e8f0; }
        .pro-table-header { background: #ccfbf1; color: var(--primary-dark); padding: 12px 14px; font-weight: 700; font-size: 15px; display: flex; align-items: center; gap: 8px; }
        .ptable { width: 100%; border-collapse: collapse; font-size: 13px; }
        .ptable th { background: #f8fafc; color: var(--text-muted); font-weight: 600; padding: 10px 6px; border-bottom: 1px solid #e2e8f0; border-top: 1px solid #e2e8f0; text-align: center; }
        .ptable th:nth-child(2) { text-align: left; padding-left: 12px; }
        .ptable td { padding: 10px 6px; text-align: center; border-bottom: 1px solid #f1f5f9; color: var(--text-main); }
        .ptable td:nth-child(2) { text-align: left; padding-left: 12px; font-weight: 600; }
        .team-badge-row { display: flex; align-items: center; gap: 8px; }
        .team-mini-logo { width: 24px; height: 24px; background: #e2e8f0; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-size: 10px; font-weight: 700; color: #475569; }
        
        .plan-box { border: 1px solid #ccfbf1; padding: 12px; border-radius: 8px; margin-top: 10px; background: #f0fdfa; }
        .admin-section-box { background: #fffbeb; border: 1px solid #fde68a; padding: 14px; border-radius: 10px; margin-bottom: 16px; }
        .admin-section-box h4 { margin-top: 0; color: #d97706; font-size: 15px; border-bottom: 1px dashed #fde68a; padding-bottom: 6px; }
    </style>
</head>
<body>

    <!-- Phone Login Overlay -->
    <div id="loginOverlay">
        <div class="login-box">
            <h3>📱 Phone Login</h3>
            <p style="font-size: 13px; color: #64748b; margin-bottom: 16px;">App mein enter karne ke liye apna mobile number dalein (Max 2 numbers per device).</p>
            
            <div id="savedAccountsContainer" class="saved-accounts hidden">
                <div style="font-size: 11px; font-weight: 600; color: #64748b; margin-bottom: 4px;">Pehle se saved numbers:</div>
                <div id="savedChipsList"></div>
            </div>

            <input type="tel" id="loginPhoneInput" placeholder="Mobile Number (e.g. 9876543210)" maxlength="10">
            <button type="button" class="action-btn" id="loginSubmitBtn" style="margin-top: 14px; padding: 12px;">Login / Continue</button>
            <p id="loginErrorMsg" style="color: var(--danger); font-size: 12px; margin-top: 10px; font-weight: 500;"></p>
        </div>
    </div>

    <div class="app-header">
        <h2>Cricket Central</h2>
        <button type="button" class="admin-badge-btn" id="adminBtn">Admin Panel</button>
    </div>

    <div id="appTabs" class="tabs-container">
        <button type="button" class="tab-btn active" data-tab="matches">Matches</button>
        <button type="button" class="tab-btn" data-tab="points">Points Table</button>
        <button type="button" class="tab-btn" data-tab="groups">Groups</button>
        <button type="button" class="tab-btn" data-tab="wallet">Wallet</button>
    </div>

<div class="container">
    <div id="dashboardSection">
        
        <!-- TAB 1: MATCHES -->
        <div id="tabMatches" class="tab-content">
            <div class="card">
                <h3 style="margin-top:0;">📅 Live & Upcoming Schedule</h3>
                <div id="scheduleList"></div>
            </div>
        </div>

        <!-- TAB 2: POINTS TABLE -->
        <div id="tabPoints" class="tab-content hidden">
            <div id="pointsTablesDisplayContainer"></div>
        </div>

        <!-- TAB 3: GROUPS -->
        <div id="tabGroups" class="tab-content hidden">
            <div class="card">
                <h3 style="margin-top:0;">👥 Group Management</h3>
                <p style="font-size: 12px; color: var(--text-muted);">Aap unlimited teams ke sath kitne bhi groups bana sakte hain.</p>
                <div id="groupsContainer" style="margin-top: 12px;"></div>
            </div>
        </div>

        <!-- TAB 4: WALLET & SUBSCRIPTION -->
        <div id="tabWallet" class="tab-content hidden">
            <div class="card" style="background: #f0fdfa; border-color: #ccfbf1;">
                <h3 style="color: var(--primary-dark); margin-top:0;">💰 Aapka Wallet</h3>
                <p style="font-size: 16px; text-align: center; margin-bottom: 6px;"><strong>Available Points:</strong> <span id="userPoints" style="color: var(--primary); font-weight: 700; font-size: 22px;">0</span></p>
                <p style="font-size: 12px; text-align: center; color: #64748b;">Phone ID: <span id="myUserIdDisplay" style="font-family: monospace; background: #fff; padding: 2px 6px; border-radius: 4px; border: 1px solid #cbd5e1;">Loading...</span></p>
                <p id="subStatus" style="text-align: center; font-weight: 600; color: #065f46; font-size: 13px; margin-top: 10px; background: #ffffff; padding: 8px; border-radius: 6px; border: 1px solid #a7f3d0;">Status: No Active Subscription</p>
                <button type="button" id="logoutPhoneBtn" class="logout-btn">Change Number / Switch Account</button>
            </div>

            <!-- PRICE LIST INFO -->
            <div class="card" style="background: #fffbeb; border-color: #fde68a;">
                <h4 style="margin-top:0; color:#d97706;">🏷️ Special Point Packages</h4>
                <p style="margin: 4px 0; font-size: 13px;"><strong>80₹</strong> = 400 Points</p>
                <p style="margin: 4px 0; font-size: 13px;"><strong>230₹</strong> = 8000 Points</p>
                <p style="margin: 10px 0 0 0; font-size: 13px; color: var(--danger); font-weight: 700; text-align: center;">Point purchase karne ke liye 9569981484 per sampark karein.</p>
            </div>

            <div class="card">
                <h3 style="margin-top:0;">⭐ Subscription Plans</h3>
                <div class="plan-box">
                    <p style="margin: 0 0 6px 0; font-weight: 600;">30 Minutes Plan: <span style="color: var(--primary);">149 Points</span></p>
                    <button type="button" class="action-btn buy-sub-btn" data-cost="149" data-plan="30 Minutes Plan" data-time="30">Buy 30 Mins Plan</button>
                </div>
                <div class="plan-box">
                    <p style="margin: 0 0 6px 0; font-weight: 600;">Half Monthly Plan: <span style="color: var(--primary);">400 Points</span></p>
                    <button type="button" class="action-btn buy-sub-btn" data-cost="400" data-plan="Half Monthly Plan" data-days="15">Buy Half Monthly</button>
                </div>
                <div class="plan-box">
                    <p style="margin: 0 0 6px 0; font-weight: 600;">Monthly Plan: <span style="color: var(--primary);">600 Points</span></p>
                    <button type="button" class="action-btn buy-sub-btn" data-cost="600" data-plan="Monthly Plan" data-days="30">Buy Monthly</button>
                </div>
                <div class="plan-box">
                    <p style="margin: 0 0 6px 0; font-weight: 600;">Half Yearly Plan: <span style="color: var(--primary);">6000 Points</span></p>
                    <button type="button" class="action-btn buy-sub-btn" data-cost="6000" data-plan="Half Yearly Plan" data-days="180">Buy Half Yearly</button>
                </div>
                <div class="plan-box">
                    <p style="margin: 0 0 6px 0; font-weight: 600;">Yearly Plan: <span style="color: var(--primary);">8000 Points</span></p>
                    <button type="button" class="action-btn buy-sub-btn" data-cost="8000" data-plan="Yearly Plan" data-days="365">Buy Yearly</button>
                </div>
            </div>
        </div>

        <!-- ADMIN PANEL SECTION -->
        <div id="adminPanelSection" class="card hidden" style="border: 2px solid var(--accent); background: #fff;">
            <h3 style="color: #d97706; margin-top:0;">👑 Admin Control Panel</h3>
            
            <div class="admin-section-box">
                <h4>1. Kisi bhi User ko Points Bhejein</h4>
                <input type="text" id="adminTargetUserId" placeholder="User ka phone number dalein">
                <input type="number" id="adminSendPoints" placeholder="Kitne points bhejne hain? (e.g. 500)">
                <button type="button" class="action-btn" id="sendPointsToUserBtn" style="background: #d97706; margin-top:10px;">User ko Points Bhejein</button>
            </div>

            <div class="admin-section-box">
                <h4>2. Match Schedule & Result Update</h4>
                <input type="hidden" id="editMatchId" value="">
                <input type="text" id="seriesName" placeholder="Series Name (jaise: IPL 2026)">
                
                <label style="font-size: 12px; font-weight: 600; color: #475569; display: block; margin-top: 8px;">Series ka Type Chunein:</label>
                <select id="seriesTypeOption">
                    <option value="same">1. Usi series ka match hai</option>
                    <option value="new">2. New series ka match hai</option>
                </select>

                <input type="text" id="matchFormat" placeholder="Format (jaise: 1st Match / T20)">
                <input type="text" id="team1" placeholder="Team 1 (jaise: MI)">
                <input type="text" id="team2" placeholder="Team 2 (jaise: CSK)">
                <input type="text" id="venue" placeholder="Venue / Stadium">
                <input type="text" id="matchTossUpdate" placeholder="Toss Update">
                <button type="button" class="action-btn" id="saveMatchBtn" style="margin-top:10px;">Match Save Karein</button>

                <h5 style="margin: 14px 0 6px 0; color: var(--text-main);">Existing Matches (Edit / Delete)</h5>
                <div id="adminMatchesList" style="max-height: 160px; overflow-y: auto; border: 1px solid #cbd5e1; padding: 6px; border-radius: 6px; background: #fff;"></div>

                <h5 style="margin: 14px 0 6px 0; color: var(--text-main);">Match Result & Scores Update</h5>
                <select id="matchSelectForUpdate"></select>
                <input type="text" id="matchWinner" placeholder="Match Winner">
                <input type="text" id="team1ScoreDetails" placeholder="Team 1 Score">
                <input type="text" id="team2ScoreDetails" placeholder="Team 2 Score">
                <button type="button" class="action-btn" id="updateResultBtn" style="background: #0284c7; margin-top:10px;">Result Update Karein</button>
            </div>

            <div class="admin-section-box">
                <h4>3. Group Creation (Unlimited Teams)</h4>
                <input type="text" id="groupNameInput" placeholder="Group Name">
                <input type="text" id="groupTeamsInput" placeholder="Teams comma se alag karein (jaise: MI, CSK, RCB)">
                <button type="button" class="action-btn" id="createGroupBtn" style="margin-top:10px;">Group Banayein</button>
                <div id="adminGroupsList" style="margin-top: 10px;"></div>
            </div>

            <div class="admin-section-box">
                <h4>4. Points Table Management</h4>
                <label style="font-size: 12px; font-weight: 600; color: #475569; display: block; margin-top: 6px;">Action Chunein:</label>
                <select id="tableActionMode" style="margin-bottom: 10px;">
                    <option value="existing">Existing Table mein Team/Match Update Karein</option>
                    <option value="new">Nayi Points Table Banayein</option>
                </select>

                <div id="existingTableSection">
                    <label style="font-size: 12px; font-weight: 600; color: #475569; display: block; margin-top: 6px;">Points Table Chunein:</label>
                    <select id="ptGroupSelect">
                        <option value="">-- Table Chunein --</option>
                    </select>

                    <div style="margin-top: 8px;">
                        <input type="text" id="extraTeamInput" placeholder="Nayi Team ka naam jodein">
                        <button type="button" class="action-btn" id="addTeamToTableBtn" style="background: #64748b; margin-top:6px;">Table mein Team Jodein</button>
                    </div>

                    <label style="font-size: 12px; font-weight: 600; color: #475569; display: block; margin-top: 10px;">Team 1 Chunein:</label>
                    <select id="ptTeam1Select"><option value="">Pehle Table Chunein</option></select>
                    <label style="font-size: 12px; font-weight: 600; color: #475569; display: block; margin-top: 10px;">Team 2 Chunein:</label>
                    <select id="ptTeam2Select"><option value="">Pehle Table Chunein</option></select>
                    <input type="text" id="ptCustomNrrInput" placeholder="Optional: NRR adjust (e.g. +1.245)" style="margin-top:10px;">
                    <button type="button" class="action-btn" id="savePointTableMatchBtn" style="margin-top: 12px; background: var(--primary-dark);">Points Table Match Update Karein</button>
                </div>

                <div id="newTableSection" class="hidden">
                    <input type="text" id="newPtTableName" placeholder="Nayi Table/Group Name" style="margin-top:4px;">
                    <input type="text" id="newPtTableTeams" placeholder="Teams comma se dalein (MI, CSK, RCB)" style="margin-top:8px;">
                    <button type="button" class="action-btn" id="createPtTableBtn" style="background: var(--primary); margin-top:8px;">Nayi Points Table Create Karein</button>
                </div>

                <div id="adminPtTablesList" style="margin-top: 12px;"></div>
            </div>
            
            <button type="button" id="closeAdminBtn" class="logout-btn" style="background: #64748b; width:100%;">Admin Panel Band Karein</button>
        </div>
    </div>
</div>

<script>
    const firebaseConfig = {
        apiKey: "YOUR_API_KEY",
        authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
        databaseURL: "https://YOUR_PROJECT_ID-default-rtdb.firebaseio.com",
        projectId: "YOUR_PROJECT_ID",
        storageBucket: "YOUR_PROJECT_ID.appspot.com",
        messagingSenderId: "YOUR_SENDER_ID",
        appId: "YOUR_APP_ID"
    };

    if (!firebase.apps.length) {
        firebase.initializeApp(firebaseConfig);
    }
    const db = firebase.database();

    let currentUserSessionData = null;

    document.addEventListener('DOMContentLoaded', function() {
        checkPhoneLoginState();

        document.getElementById('loginSubmitBtn').addEventListener('click', handlePhoneLogin);
        document.getElementById('logoutPhoneBtn').addEventListener('click', handleLogout);

        document.getElementById('adminBtn').addEventListener('click', checkAdminAccess);
        document.getElementById('saveMatchBtn').addEventListener('click', saveMatch);
        document.getElementById('updateResultBtn').addEventListener('click', updateMatchResultAction);
        
        document.getElementById('sendPointsToUserBtn').addEventListener('click', sendPointsToUserAction);
        document.getElementById('createGroupBtn').addEventListener('click', createGroupAction);

        document.getElementById('createPtTableBtn').addEventListener('click', createNewPointsTableDirectly);
        document.getElementById('addTeamToTableBtn').addEventListener('click', addTeamToExistingTable);
        document.getElementById('savePointTableMatchBtn').addEventListener('click', savePointTableMatch);
        
        document.getElementById('tableActionMode').addEventListener('change', handleTableActionMode);
        document.getElementById('closeAdminBtn').addEventListener('click', closeAdminPanel);
        
        document.getElementById('ptGroupSelect').addEventListener('change', onAdminGroupSelected);

        document.querySelectorAll('.tab-btn').forEach(btn => {
            btn.addEventListener('click', function() { switchTab(this.getAttribute('data-tab'), this); });
        });

        document.querySelectorAll('.buy-sub-btn').forEach(btn => {
            btn.addEventListener('click', function() {
                let cost = parseInt(this.getAttribute('data-cost')) || 0;
                let plan = this.getAttribute('data-plan');
                let days = parseInt(this.getAttribute('data-days')) || 0;
                let mins = parseInt(this.getAttribute('data-time')) || 0;
                buySubscription(cost, plan, days, mins);
            });
        });
    });

    function getSavedDevices() {
        let saved = localStorage.getItem('cricket_saved_devices');
        return saved ? JSON.parse(saved) : [];
    }

    function checkPhoneLoginState() {
        let activePhone = localStorage.getItem('cricket_phone_number');
        renderSavedAccountsChips();
        if(activePhone) {
            document.getElementById('loginOverlay').classList.add('hidden');
            initUserSession(activePhone);
        } else {
            document.getElementById('loginOverlay').classList.remove('hidden');
        }
    }

    function renderSavedAccountsChips() {
        let list = getSavedDevices();
        let container = document.getElementById('savedAccountsContainer');
        let chipsList = document.getElementById('savedChipsList');
        
        if(list.length > 0) {
            container.classList.remove('hidden');
            let html = "";
            list.forEach(num => {
                html += `<div class="saved-account-chip" onclick="selectSavedAccount('${num}')">📱 ${num}</div>`;
            });
            chipsList.innerHTML = html;
        } else {
            container.classList.add('hidden');
        }
    }

    window.selectSavedAccount = function(num) {
        document.getElementById('loginPhoneInput').value = num;
        handlePhoneLogin();
    };

    function handlePhoneLogin() {
        let phoneInput = document.getElementById('loginPhoneInput').value.trim();
        let errorMsg = document.getElementById('loginErrorMsg');

        if(!phoneInput || phoneInput.length < 10) {
            errorMsg.innerText = "Kripya valid 10-digit mobile number dalein!";
            return;
        }

        let savedList = getSavedDevices();
        if(!savedList.includes(phoneInput)) {
            if(savedList.length >= 2) {
                errorMsg.innerText = "Is device par maximum 2 numbers hi save kiye ja sakte hain!";
                return;
            }
            savedList.push(phoneInput);
            localStorage.setItem('cricket_saved_devices', JSON.stringify(savedList));
        }

        localStorage.setItem('cricket_phone_number', phoneInput);
        document.getElementById('loginOverlay').classList.add('hidden');
        initUserSession(phoneInput);
    }

    function handleLogout() {
        localStorage.removeItem('cricket_phone_number');
        location.reload();
    }

    function initUserSession(phoneNum) {
        document.getElementById('myUserIdDisplay').innerText = phoneNum;

        let userRef = db.ref('users/' + phoneNum);
        userRef.once('value', (snapshot) => {
            let bonusToAdd = (phoneNum === "9569981484") ? 5000 : 50;

            if(!snapshot.exists()) {
                let initialData = {
                    points: bonusToAdd,
                    subscription: null,
                    subExpiry: 0
                };
                userRef.set(initialData, () => {
                    loadDashboard(phoneNum);
                });
            } else {
                let userData = snapshot.val();
                if (phoneNum === "9569981484" && (!userData.points || userData.points < 5000)) {
                    userRef.update({ points: 5000 }, () => {
                        loadDashboard(phoneNum);
                    });
                } else {
                    loadDashboard(phoneNum);
                }
            }
        }).catch((err) => {
            console.error("Init Error:", err);
            loadDashboard(phoneNum);
        });
    }

    function loadDashboard(phoneNum) {
        db.ref('users/' + phoneNum).on('value', (snapshot) => {
            let userData = snapshot.val();
            if(userData) {
                currentUserSessionData = userData;
                document.getElementById('userPoints').innerText = userData.points || 0;
                updateSubscriptionStatusUI(userData);
            } else {
                let defaultPoints = (phoneNum === "9569981484") ? 5000 : 50;
                currentUserSessionData = { points: defaultPoints, subscription: null, subExpiry: 0 };
                document.getElementById('userPoints').innerText = defaultPoints;
                updateSubscriptionStatusUI(currentUserSessionData);
            }
        });

        setupRealtimeListeners();
    }

    function setupRealtimeListeners() {
        db.ref('matches').on('value', (snapshot) => { renderSchedule(snapshot.val() || {}); renderAdminMatchesList(snapshot.val() || {}); updateMatchDropdown(snapshot.val() || {}); });
        db.ref('points_tables').on('value', (snapshot) => { renderAllPointsTables(snapshot.val() || {}); populateAdminGroupDropdown(snapshot.val() || {}); renderAdminPointsTablesList(snapshot.val() || {}); });
        db.ref('groups').on('value', (snapshot) => { renderGroups(snapshot.val() || {}); renderAdminGroupsList(snapshot.val() || {}); });
    }

    function switchTab(tabName, btnElement) {
        document.querySelectorAll('.tab-content').forEach(el => el.classList.add('hidden'));
        document.querySelectorAll('.tab-btn').forEach(el => el.classList.remove('active'));
        document.getElementById('adminPanelSection').classList.add('hidden');

        if(tabName === 'matches') {
            document.getElementById('tabMatches').classList.remove('hidden');
        } else if(tabName === 'points') {
            document.getElementById('tabPoints').classList.remove('hidden');
        } else if(tabName === 'groups') {
            document.getElementById('tabGroups').classList.remove('hidden');
        } else if(tabName === 'wallet') {
            document.getElementById('tabWallet').classList.remove('hidden');
        }
        if(btnElement) btnElement.classList.add('active');
    }

    function checkAdminAccess() {
        let currentTime = Date.now();
        
        if(currentUserSessionData && currentUserSessionData.subExpiry && currentUserSessionData.subExpiry > currentTime) {
            document.getElementById('adminPanelSection').classList.remove('hidden');
            document.getElementById('adminPanelSection').scrollIntoView({ behavior: 'smooth' });
            return;
        }

        alert("Admin panel kholne ke liye active subscription hona zaroori hai! Kripya pehle Wallet se koi plan buy karein.");
        switchTab('wallet', document.querySelector('.tab-btn[data-tab="wallet"]'));
    }

    function closeAdminPanel() {
        document.getElementById('adminPanelSection').classList.add('hidden');
    }

    function sendPointsToUserAction() {
        let targetUserId = document.getElementById('adminTargetUserId').value.trim();
        let pointsToAdd = parseInt(document.getElementById('adminSendPoints').value);

        if(!targetUserId) {
            alert("Kripya User ka phone number dalein!");
            return;
        }
        if(isNaN(pointsToAdd) || pointsToAdd <= 0) {
            alert("Kripya points ki sahi matra dalein!");
            return;
        }

        let userRef = db.ref('users/' + targetUserId);
        userRef.once('value', (snapshot) => {
            let currentPoints = 0;
            let subData = null;
            let subExp = 0;

            if(snapshot.exists()) {
                let val = snapshot.val();
                currentPoints = parseInt(val.points) || 0;
                subData = val.subscription || null;
                subExp = val.subExpiry || 0;
            }

            userRef.set({
                points: currentPoints + pointsToAdd,
                subscription: subData,
                subExpiry: subExp
            }, (error) => {
                if(!error) {
                    document.getElementById('adminTargetUserId').value = "";
                    document.getElementById('adminSendPoints').value = "";
                    alert(`Successfully ${pointsToAdd} points user (${targetUserId}) ke wallet mein add kar diye gaye hain!`);
                } else {
                    alert("Points bhejne mein error aayi hai.");
                }
            });
        });
    }

    function createGroupAction() {
        let groupName = document.getElementById('groupNameInput').value.trim();
        let teamsInput = document.getElementById('groupTeamsInput').value.trim();

        if(!groupName || !teamsInput) {
            alert("Kripya Group Name aur Teams ke naam dalein!");
            return;
        }

        let teamsArr = teamsInput.split(',').map(t => t.trim()).filter(t => t.length > 0);
        db.ref('groups/' + groupName).set(teamsArr);

        document.getElementById('groupNameInput').value = "";
        document.getElementById('groupTeamsInput').value = "";
        alert(`Group '${groupName}' successfully ban gaya hai!`);
    }

    function buySubscription(cost, planName, extraDays, extraMins) {
        let myPhone = localStorage.getItem('cricket_phone_number');
        if(!myPhone) {
            alert("Kripya pehle login karein!");
            return;
        }

        let userRef = db.ref('users/' + myPhone);

        userRef.once('value', (snapshot) => {
            let userData = snapshot.val() || {};
            let currentPoints = parseInt(userData.points) || 0;

            if(currentPoints < cost) {
                alert("Aapke paas sufficient points nahi hain!");
                return;
            }

            let newPoints = currentPoints - cost;
            let currentTime = Date.now();
            let baseTime = (userData.subExpiry && userData.subExpiry > currentTime) ? userData.subExpiry : currentTime;
            let subExpiry = baseTime;

            if(extraDays && extraDays > 0) {
                subExpiry = baseTime + (extraDays * 24 * 60 * 60 * 1000);
            } else if(extraMins && extraMins > 0) {
                subExpiry = baseTime + (extraMins * 60 * 1000);
            }

            let updatedData = {
                points: newPoints,
                subscription: planName,
                subExpiry: subExpiry
            };

            userRef.update(updatedData, (error) => {
                if(!error) {
                    currentUserSessionData = Object.assign({}, userData, updatedData);
                    document.getElementById('userPoints').innerText = newPoints;
                    updateSubscriptionStatusUI(currentUserSessionData);
                    alert(`Aapne '${planName}' successfully buy kar liya hai! Ab aap Admin Panel access kar sakte hain.`);
                } else {
                    alert("Purchase karne mein error aayi.");
                }
            });
        });
    }

    function updateSubscriptionStatusUI(userData) {
        let subStatusElem = document.getElementById('subStatus');

        if(!userData || !userData.subscription) {
            subStatusElem.innerText = "Status: No Active Subscription";
            subStatusElem.style.color = "#dc2626";
            return;
        }
        let currentTime = Date.now();
        if(userData.subExpiry && userData.subExpiry > currentTime) {
            subStatusElem.innerText = `Active Plan: ${userData.subscription}`;
            subStatusElem.style.color = "#059669";
        } else {
            subStatusElem.innerText = "Status: Subscription Expired";
            subStatusElem.style.color = "#dc2626";
        }
    }

    function saveMatch() {
        let editId = document.getElementById('editMatchId').value;
        let series = document.getElementById('seriesName').value.trim();
        let seriesType = document.getElementById('seriesTypeOption').value;
        let format = document.getElementById('matchFormat').value.trim();
        let t1 = document.getElementById('team1').value.trim();
        let t2 = document.getElementById('team2').value.trim();
        let venue = document.getElementById('venue').value.trim();
        let toss = document.getElementById('matchTossUpdate').value.trim();

        if(!series || !format || !t1 || !t2) {
            alert("Kripya Series, Format, aur Dono Teams ke naam bharein!");
            return;
        }

        let matchesRef = db.ref('matches');
        if(editId !== "") {
            matchesRef.child(editId).update({ series, seriesType, format, t1, t2, venue, toss });
            document.getElementById('editMatchId').value = "";
            document.getElementById('saveMatchBtn').innerText = "Match Save Karein";
        } else {
            let newMatchRef = matchesRef.push();
            newMatchRef.set({ series, seriesType, format, t1, t2, venue, toss, winner: "", t1Score: "", t2Score: "" });
        }

        document.getElementById('seriesName').value = "";
        document.getElementById('matchFormat').value = "";
        document.getElementById('team1').value = "";
        document.getElementById('team2').value = "";
        document.getElementById('venue').value = "";
        document.getElementById('matchTossUpdate').value = "";
        alert("Match successfully save ho gaya hai!");
    }

    window.editMatch = function(id, m) {
        document.getElementById('editMatchId').value = id;
        document.getElementById('seriesName').value = m.series || "";
        document.getElementById('seriesTypeOption').value = m.seriesType || "same";
        document.getElementById('matchFormat').value = m.format || "";
        document.getElementById('team1').value = m.t1 || "";
        document.getElementById('team2').value = m.t2 || "";
        document.getElementById('venue').value = m.venue || "";
        document.getElementById('matchTossUpdate').value = m.toss || "";
        document.getElementById('saveMatchBtn').innerText = "Match Update Karein";
    };

    window.deleteMatch = function(id) {
        if(confirm("Kya aap is match ko delete karna chahte hain?")) {
            db.ref('matches/' + id).remove();
        }
    };

    function updateMatchResultAction() {
        let matchId = document.getElementById('matchSelectForUpdate').value;
        let winner = document.getElementById('matchWinner').value.trim();
        let t1Score = document.getElementById('team1ScoreDetails').value.trim();
        let t2Score = document.getElementById('team2ScoreDetails').value.trim();

        if(!matchId) {
            alert("Kripya result ke liye match chunein!");
            return;
        }

        db.ref('matches/' + matchId).update({
            winner: winner,
            t1Score: t1Score,
            t2Score: t2Score
        }, (error) => {
            if(!error) {
                document.getElementById('matchWinner').value = "";
                document.getElementById('team1ScoreDetails').value = "";
                document.getElementById('team2ScoreDetails').value = "";
                alert("Match ka result aur scores successfully update ho gaye hain!");
            }
        });
    }

    function renderAdminMatchesList(matchesObj) {
        let container = document.getElementById('adminMatchesList');
        let keys = Object.keys(matchesObj);
        if(keys.length === 0) {
            container.innerHTML = "<p style='font-size:11px; color:#64748b; margin:0;'>Koi match available nahi hai.</p>";
            return;
        }
        let html = "";
        keys.forEach((id) => {
            let m = matchesObj[id];
            let mJson = JSON.stringify(m).replace(/"/g, '&quot;');
            html += `<div style="display:flex; justify-content:space-between; align-items:center; border-bottom:1px solid #f1f5f9; padding:6px 0; font-size:12px;">
                <span><b>${m.t1} vs ${m.t2}</b> (${m.format})</span>
                <div>
                    <button type="button" onclick='editMatch("${id}", ${mJson})' style="background:#f59e0b; color:white; border:none; padding:3px 8px; border-radius:4px; cursor:pointer; font-size:10px; width:auto; margin-right:4px;">Edit</button>
                    <button type="button" onclick="deleteMatch('${id}')" style="background:#ef4444; color:white; border:none; padding:3px 8px; border-radius:4px; cursor:pointer; font-size:10px; width:auto;">Delete</button>
                </div>
            </div>`;
        });
        container.innerHTML = html;
    }

    function updateMatchDropdown(matchesObj) {
        let select = document.getElementById('matchSelectForUpdate');
        if(!select) return;
        select.innerHTML = "<option value=''>-- Match Chunein Result ke liye --</option>";
        Object.keys(matchesObj).forEach((id) => {
            let m = matchesObj[id];
            select.innerHTML += `<option value="${id}">${m.t1} vs ${m.t2} (${m.format})</option>`;
        });
    }

    function handleTableActionMode() {
        let mode = document.getElementById('tableActionMode').value;
        if(mode === 'existing') {
            document.getElementById('existingTableSection').classList.remove('hidden');
            document.getElementById('newTableSection').classList.add('hidden');
        } else {
            document.getElementById('existingTableSection').classList.add('hidden');
            document.getElementById('newTableSection').classList.remove('hidden');
        }
    }

    function createNewPointsTableDirectly() {
        let tableName = document.getElementById('newPtTableName').value.trim();
        let teamsInput = document.getElementById('newPtTableTeams').value.trim();

        if(!tableName || !teamsInput) {
            alert("Table Name aur Teams dono dalein!");
            return;
        }

        let teamsArr = teamsInput.split(',').map(t => t.trim()).filter(t => t.length > 0);
        let tableData = {};
        teamsArr.forEach(t => {
            tableData[t] = { played: 0, won: 0, lost: 0, nr: 0, nrr: '0.000', points: 0 };
        });

        db.ref('points_tables/' + tableName).set(tableData);
        db.ref('groups/' + tableName).set(teamsArr);

        document.getElementById('newPtTableName').value = "";
        document.getElementById('newPtTableTeams').value = "";
        alert("Nayi Points Table successfully create ho gayi!");
    }

    function addTeamToExistingTable() {
        let tableName = document.getElementById('ptGroupSelect').value.trim();
        let teamName = document.getElementById('extraTeamInput').value.trim();

        if(!tableName || !teamName) {
            alert("Table aur team ka naam dalein!");
            return;
        }

        db.ref(`points_tables/${tableName}/${teamName}`).set({ played: 0, won: 0, lost: 0, nr: 0, nrr: '0.000', points: 0 }, (error) => {
            if(!error) {
                document.getElementById('extraTeamInput').value = "";
                alert(`Team '${teamName}' ko table '${tableName}' mein jodh diya gaya hai!`);
            }
        });
    }

    function populateAdminGroupDropdown(tablesObj) {
        let groupSelect = document.getElementById('ptGroupSelect');
        let currentSelected = groupSelect.value;
        groupSelect.innerHTML = "<option value=''>-- Table Chunein --</option>";
        
        Object.keys(tablesObj).forEach(gName => {
            groupSelect.innerHTML += `<option value="${gName}">${gName}</option>`;
        });
        if(currentSelected) groupSelect.value = currentSelected;
    }

    function onAdminGroupSelected() {
        let groupName = document.getElementById('ptGroupSelect').value;
        let t1Select = document.getElementById('ptTeam1Select');
        let t2Select = document.getElementById('ptTeam2Select');
        
        if(!groupName) {
            t1Select.innerHTML = "<option value=''>Pehle Table Chunein</option>";
            t2Select.innerHTML = "<option value=''>Pehle Table Chunein</option>";
            return;
        }

        db.ref('points_tables/' + groupName).once('value', (snapshot) => {
            let teamsObj = snapshot.val() || {};
            let teams = Object.keys(teamsObj);
            let optionsHtml = "<option value=''>-- Team Chunein --</option>";
            teams.forEach(team => {
                optionsHtml += `<option value="${team}">${team}</option>`;
            });
            t1Select.innerHTML = optionsHtml;
            t2Select.innerHTML = optionsHtml;
        });
    }

    function savePointTableMatch() {
        let tableName = document.getElementById('ptGroupSelect').value.trim();
        let team1 = document.getElementById('ptTeam1Select').value.trim();
        let team2 = document.getElementById('ptTeam2Select').value.trim();
        let customNrr = document.getElementById('ptCustomNrrInput').value.trim();

        if(!tableName || !team1 || !team2 || team1 === team2) {
            alert("Sahi Table aur alag-alag Teams chunein!");
            return;
        }

        db.ref(`points_tables/${tableName}`).once('value', (snapshot) => {
            let tables = snapshot.val() || {};
            if(!tables[team1]) tables[team1] = { played: 0, won: 0, lost: 0, nr: 0, nrr: '0.000', points: 0 };
            if(!tables[team2]) tables[team2] = { played: 0, won: 0, lost: 0, nr: 0, nrr: '0.000', points: 0 };

            tables[team1].played = (parseInt(tables[team1].played) || 0) + 1;
            tables[team2].played = (parseInt(tables[team2].played) || 0) + 1;
            tables[team1].won = (parseInt(tables[team1].won) || 0) + 1;
            tables[team1].points = (parseInt(tables[team1].points) || 0) + 2;
            tables[team2].lost = (parseInt(tables[team2].lost) || 0) + 1;

            if(customNrr) tables[team1].nrr = customNrr;

            db.ref('points_tables/' + tableName).set(tables, () => {
                document.getElementById('ptCustomNrrInput').value = "";
                alert("Points Table match result ke mutabiq successfully update ho gayi hai!");
            });
        });
    }

    window.deleteEntireTable = function(tableName) {
        if(confirm(`Kya aap poori table '${tableName}' ko delete karna chahte hain?`)) {
            db.ref('points_tables/' + tableName).remove();
            db.ref('groups/' + tableName).remove();
            alert("Points table successfully delete kar di gayi hai!");
        }
    };

    function renderAdminPointsTablesList(tablesObj) {
        let container = document.getElementById('adminPtTablesList');
        let keys = Object.keys(tablesObj);
        if(keys.length === 0) {
            container.innerHTML = "<p style='font-size:12px; color:#64748b;'>Abhi koi points table nahi hai.</p>";
            return;
        }

        let html = "<div style='font-size:12px; font-weight:600; margin-bottom:6px;'>Bani hui Tables:</div>";
        keys.forEach(tName => {
            let teamsCount = Object.keys(tablesObj[tName] || {}).length;
            html += `<div style="background:#fff; padding:6px 10px; border:1px solid #cbd5e1; border-radius:6px; margin-bottom:6px; display:flex; justify-content:space-between; align-items:center;">
                <span><b>${tName}</b> (${teamsCount} Teams)</span>
                <button type="button" onclick="deleteEntireTable('${tName}')" style="background:#ef4444; color:white; border:none; padding:4px 8px; border-radius:4px; font-size:11px; cursor:pointer; width:auto;">Delete Table</button>
            </div>`;
        });
        container.innerHTML = html;
    }

    function renderAllPointsTables(tablesObj) {
        let container = document.getElementById('pointsTablesDisplayContainer');
        let keys = Object.keys(tablesObj);

        if(keys.length === 0) {
            container.innerHTML = `<div class="card"><h3 style="margin-top:0;">🏆 Points Table</h3><p style='font-size:13px; color:var(--text-muted); text-align:center;'>Abhi koi points table uplabdh nahi hai.</p></div>`;
            return;
        }

        let html = "";
        keys.forEach(tName => {
            let teamsObj = tablesObj[tName];
            let teamKeys = Object.keys(teamsObj);

            teamKeys.sort((a, b) => {
                let ptsDiff = (teamsObj[b].points || 0) - (teamsObj[a].points || 0);
                if(ptsDiff !== 0) return ptsDiff;
                return parseFloat(teamsObj[b].nrr || 0) - parseFloat(teamsObj[a].nrr || 0);
            });

            html += `<div class="pro-table-container" style="margin-bottom: 16px;">
                <div class="pro-table-header">🏆 <span>${tName}</span></div>
                <table class="ptable">
                    <tr>
                        <th style="width: 35px;">#</th>
                        <th style="text-align:left;">Team</th>
                        <th>P</th><th>W</th><th>L</th><th>NR</th><th>Pts</th><th>NRR</th>
                    </tr>`;
            
            let rank = 1;
            teamKeys.forEach(t => {
                let d = teamsObj[t];
                let shortLogo = t.length >= 2 ? t.substring(0, 3).toUpperCase() : t.toUpperCase();
                html += `<tr>
                    <td>${rank++}</td>
                    <td>
                        <div class="team-badge-row">
                            <div class="team-mini-logo">${shortLogo}</div>
                            <span>${t}</span>
                        </div>
                    </td>
                    <td>${d.played || 0}</td><td>${d.won || 0}</td><td>${d.lost || 0}</td><td>${d.nr || 0}</td><td><b>${d.points || 0}</b></td><td>${d.nrr || '0.000'}</td>
                </tr>`;
            });
            html += `</table></div>`;
        });
        container.innerHTML = html;
    }

    function renderSchedule(matchesObj) {
        let container = document.getElementById('scheduleList');
        let keys = Object.keys(matchesObj);
        if(keys.length === 0) {
            container.innerHTML = "<p style='font-size:13px; color:var(--text-muted); text-align:center;'>Abhi koi match schedule nahi hai.</p>";
            return;
        }
        let html = "";
        keys.forEach((id) => {
            let m = matchesObj[id];
            let bannerHtml = "";
            if(m.winner) bannerHtml = `<div class="match-result-banner">🏆 Winner: ${m.winner}</div>`;
            else if(m.toss) bannerHtml = `<div class="match-toss-banner">📢 Toss: ${m.toss}</div>`;

            let scoresHtml = "";
            if(m.t1Score || m.t2Score) {
                scoresHtml = `<div style="font-size:12px; color:#475569; margin-top:6px; background:#f8fafc; padding:6px; border-radius:6px;">${m.t1}: <b>${m.t1Score || 'Yet to bat'}</b><br>${m.t2}: <b>${m.t2Score || 'Yet to bat'}</b></div>`;
            }

            html += `<div class="match-box">
                <div class="match-info-top"><span>${m.format}</span><span>📍 ${m.venue || 'N/A'}</span></div>
                <div class="match-teams"><span>🏏 ${m.t1}</span> vs <span>${m.t2} 🏏</span></div>
                ${scoresHtml} ${bannerHtml}
            </div>`;
        });
        container.innerHTML = html;
    }

    function renderGroups(groupsObj) {
        let container = document.getElementById('groupsContainer');
        let keys = Object.keys(groupsObj);
        if(keys.length === 0) {
            container.innerHTML = "<p style='font-size:13px; color:var(--text-muted);'>Abhi koi group nahi hai.</p>";
            return;
        }
        let html = "";
        keys.forEach(g => {
            html += `<div style="background:#f8fafc; border:1px solid #cbd5e1; padding:10px; border-radius:8px; margin-bottom:10px;"><strong style="color:var(--primary-dark);">📌 ${g}</strong><br><span style="font-size:13px; color:#475569;">Teams: ${groupsObj[g].join(', ')}</span></div>`;
        });
        container.innerHTML = html;
    }

    function renderAdminGroupsList(groupsObj) {
        let container = document.getElementById('adminGroupsList');
        let keys = Object.keys(groupsObj);
        if(keys.length === 0) {
            container.innerHTML = "<p style='font-size:12px; color:#64748b;'>Abhi koi group nahi hai.</p>";
            return;
        }
        let html = "<div style='font-size:12px; font-weight:600; margin-bottom:4px;'>Groups List:</div>";
        keys.forEach(g => {
            html += `<div style="background:#fff; padding:6px 10px; border:1px solid #cbd5e1; margin-top:4px; border-radius:6px; font-size:13px;"><b>${g}</b></div>`;
        });
        container.innerHTML = html;
    }
</script>
</body>
</html>
