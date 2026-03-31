<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>NAC ASO System</title>

  <script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-auth-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore-compat.js"></script>

  <style>
    :root {
      --bg: #07162f;
      --bg-soft: #0b2143;
      --card: #0a1d39;
      --line: #285286;
      --text: #f7fbff;
      --muted: #a7bdd9;
      --blue: #2563eb;
      --green: #10b981;
      --red: #dc2626;
      --amber: #f59e0b;
    }

    * { box-sizing: border-box; }

    body {
      margin: 0;
      font-family: Arial, Helvetica, sans-serif;
      background: linear-gradient(135deg, var(--bg), #031125 60%, #0a2347);
      color: var(--text);
      min-height: 100vh;
    }

    .topbar {
      background: #020b1f;
      border-bottom: 1px solid var(--line);
      padding: 16px 20px;
      position: sticky;
      top: 0;
      z-index: 10;
    }

    .topbar h1 {
      margin: 0;
      font-size: 28px;
    }

    .topbar p {
      margin: 6px 0 0;
      color: var(--muted);
      font-size: 13px;
    }

    .container {
      max-width: 1400px;
      margin: 0 auto;
      padding: 20px;
    }

    .grid-2 {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 20px;
    }

    .grid-3 {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 16px;
    }

    .grid-4 {
      display: grid;
      grid-template-columns: repeat(4, 1fr);
      gap: 16px;
    }

    .card {
      background: var(--card);
      border: 1px solid var(--line);
      border-radius: 16px;
      padding: 18px;
      box-shadow: 0 10px 24px rgba(0,0,0,0.22);
    }

    h2, h3 {
      margin-top: 0;
    }

    .muted {
      color: var(--muted);
    }

    label {
      display: block;
      margin: 10px 0 6px;
      font-weight: 700;
      font-size: 14px;
    }

    input, select, textarea {
      width: 100%;
      padding: 12px;
      border-radius: 10px;
      border: 1px solid var(--line);
      background: var(--bg-soft);
      color: var(--text);
      outline: none;
    }

    textarea {
      min-height: 96px;
      resize: vertical;
    }

    button {
      border: none;
      border-radius: 10px;
      padding: 11px 15px;
      font-weight: 700;
      cursor: pointer;
      color: white;
      background: var(--blue);
    }

    .btn-green { background: var(--green); }
    .btn-red { background: var(--red); }
    .btn-amber { background: var(--amber); color: #111; }
    .btn-ghost { background: transparent; border: 1px solid var(--line); }

    .hidden { display: none !important; }

    .message {
      display: none;
      padding: 12px 14px;
      border-radius: 12px;
      margin-bottom: 16px;
      font-weight: 700;
    }

    .message.show { display: block; }
    .message.error { background: rgba(220,38,38,0.22); color: #fecaca; }
    .message.success { background: rgba(16,185,129,0.22); color: #bbf7d0; }
    .message.info { background: rgba(37,99,235,0.22); color: #dbeafe; }

    .stats .card {
      padding: 16px;
    }

    .stats .label {
      font-size: 13px;
      color: var(--muted);
      margin-bottom: 8px;
    }

    .stats .value {
      font-size: 26px;
      font-weight: 800;
    }

    .toolbar {
      display: flex;
      justify-content: space-between;
      gap: 12px;
      align-items: center;
      flex-wrap: wrap;
      margin-bottom: 16px;
    }

    .tab-row {
      display: flex;
      gap: 10px;
      flex-wrap: wrap;
      margin-bottom: 16px;
    }

    .tab-btn.active {
      outline: 2px solid #67e8f9;
    }

    table {
      width: 100%;
      border-collapse: collapse;
      font-size: 14px;
    }

    th, td {
      border-bottom: 1px solid rgba(40,82,134,0.45);
      padding: 10px 8px;
      text-align: left;
      vertical-align: top;
    }

    th {
      color: #dbeafe;
    }

    .table-wrap {
      overflow: auto;
    }

    .badge {
      display: inline-block;
      border: 1px solid var(--line);
      border-radius: 999px;
      padding: 5px 9px;
      font-size: 12px;
      font-weight: 700;
      background: var(--bg-soft);
    }

    @media (max-width: 1100px) {
      .grid-4 { grid-template-columns: repeat(2, 1fr); }
      .grid-3 { grid-template-columns: 1fr 1fr; }
    }

    @media (max-width: 800px) {
      .grid-2, .grid-3, .grid-4 { grid-template-columns: 1fr; }
    }
  </style>
</head>
<body>
  <div class="topbar">
    <h1>NAC ASO System</h1>
    <p>Airport Safety Officer operations dashboard for daily, weekly, monthly and annual management reporting.</p>
  </div>

  <div class="container">
    <div id="messageBox" class="message"></div>

    <section id="authView" class="grid-2">
      <div class="card">
        <h2>Login</h2>
        <p class="muted">Sign in with your NAC ASO Firebase account.</p>

        <label for="loginEmail">Email</label>
        <input id="loginEmail" type="email" placeholder="Email" />

        <label for="loginPassword">Password</label>
        <input id="loginPassword" type="password" placeholder="Password" />

        <button onclick="loginUser()">Login</button>
      </div>

      <div class="card">
        <h2>Create Account</h2>
        <p class="muted">Create a user profile for airport operations reporting.</p>

        <label for="registerName">Full Name</label>
        <input id="registerName" type="text" placeholder="Full name" />

        <label for="registerEmail">Email</label>
        <input id="registerEmail" type="email" placeholder="Email" />

        <label for="registerPassword">Password</label>
        <input id="registerPassword" type="password" placeholder="Password" />

        <label for="registerRole">Role</label>
        <select id="registerRole">
          <option>Airport Operations Manager (AOM)</option>
          <option>Senior Airport Safety Officer (SASO)</option>
          <option selected>Airport Safety Officer (ASO)</option>
          <option>Team Leader Grounds Staff</option>
          <option>Grounds Staff</option>
          <option>Chief Operations Officer</option>
          <option>Wild Life Manager</option>
          <option>Wildlife Officer</option>
        </select>

        <label for="registerAirport">Primary Airport</label>
        <input id="registerAirport" type="text" value="PMIA" />

        <button class="btn-green" onclick="registerUser()">Create Account</button>
      </div>
    </section>

    <section id="appView" class="hidden">
      <div class="toolbar">
        <div>
          <h2 style="margin:0 0 6px;">Operations Dashboard</h2>
          <div class="muted" id="sessionText">Signed in</div>
        </div>
        <div style="display:flex; gap:10px; flex-wrap:wrap; align-items:center;">
          <select id="summaryPeriod" onchange="refreshDashboard()">
            <option value="daily">Daily</option>
            <option value="weekly">Weekly</option>
            <option value="monthly" selected>Monthly</option>
            <option value="annual">Annual</option>
          </select>
          <input id="summaryDate" type="date" onchange="refreshDashboard()" />
          <button class="btn-ghost" onclick="printSummary()">Print Summary</button>
          <button class="btn-red" onclick="logoutUser()">Logout</button>
        </div>
      </div>

      <div class="grid-4 stats">
        <div class="card"><div class="label">User</div><div class="value" id="statUser">-</div></div>
        <div class="card"><div class="label">Role</div><div class="value" id="statRole">-</div></div>
        <div class="card"><div class="label">Airport</div><div class="value" id="statAirport">-</div></div>
        <div class="card"><div class="label">Reports This Period</div><div class="value" id="statCount">0</div></div>
      </div>

      <br />

      <div class="card">
        <div class="tab-row">
          <button class="tab-btn active" onclick="switchTab('dailylog', this)">ASO Daily Log</button>
          <button class="tab-btn" onclick="switchTab('birdstrike', this)">Birdstrike</button>
          <button class="tab-btn" onclick="switchTab('accident', this)">Aircraft Accident</button>
          <button class="tab-btn" onclick="switchTab('incident', this)">Airside Incident</button>
          <button class="tab-btn" onclick="switchTab('wildlife', this)">Wildlife Hazard</button>
          <button class="tab-btn" onclick="switchTab('fod', this)">FOD / Damage</button>
        </div>

        <div id="tab-dailylog" class="tab-panel">
          <div class="grid-3">
            <div>
              <label>Date</label><input id="dlDate" type="date" />
              <label>Shift</label>
              <select id="dlShift"><option>A Shift</option><option>B Shift</option><option>C Shift</option></select>
              <label>Weather</label><input id="dlWeather" type="text" placeholder="Weather conditions" />
              <label>Runway / Apron Status</label><input id="dlStatus" type="text" placeholder="Runway, taxiway, apron condition" />
            </div>
            <div>
              <label>Inspections Conducted</label><textarea id="dlInspections" placeholder="Patrols and inspections completed"></textarea>
              <label>Hazards Identified</label><textarea id="dlHazards" placeholder="Hazards, defects, wildlife, FOD, lighting issues"></textarea>
            </div>
            <div>
              <label>Actions Taken</label><textarea id="dlActions" placeholder="Actions taken"></textarea>
              <label>Outstanding Issues</label><textarea id="dlOutstanding" placeholder="Items requiring follow-up"></textarea>
              <button class="btn-green" onclick="submitDailyLog()">Submit Daily Log</button>
            </div>
          </div>
        </div>

        <div id="tab-birdstrike" class="tab-panel hidden">
          <div class="grid-3">
            <div>
              <label>Date / Time</label><input id="bsDateTime" type="datetime-local" />
              <label>Location</label><input id="bsLocation" type="text" placeholder="Location" />
              <label>Aircraft Registration</label><input id="bsReg" type="text" placeholder="Registration" />
              <label>Operator</label><input id="bsOperator" type="text" placeholder="Operator" />
            </div>
            <div>
              <label>Species</label><input id="bsSpecies" type="text" placeholder="Species or unknown" />
              <label>Phase of Flight</label><input id="bsPhase" type="text" placeholder="Taxi, take-off, landing, approach" />
              <label>Damage Level</label><select id="bsDamage"><option>None</option><option>Minor</option><option>Moderate</option><option>Major</option></select>
            </div>
            <div>
              <label>Details</label><textarea id="bsDetails" placeholder="What happened"></textarea>
              <label>Immediate Actions</label><textarea id="bsActions" placeholder="Actions taken"></textarea>
              <button class="btn-green" onclick="submitBirdstrike()">Submit Birdstrike Report</button>
            </div>
          </div>
        </div>

        <div id="tab-accident" class="tab-panel hidden">
          <div class="grid-3">
            <div>
              <label>Date / Time</label><input id="acDateTime" type="datetime-local" />
              <label>Location</label><input id="acLocation" type="text" placeholder="Location" />
              <label>Aircraft Registration</label><input id="acReg" type="text" placeholder="Registration" />
              <label>Operator</label><input id="acOperator" type="text" placeholder="Operator" />
            </div>
            <div>
              <label>Severity</label><select id="acSeverity"><option>Minor</option><option>Serious</option><option>Major</option><option>Catastrophic</option></select>
              <label>Injuries / Fatalities</label><input id="acInjuries" type="text" placeholder="People affected" />
              <label>Operational Impact</label><input id="acImpact" type="text" placeholder="Delays, closures, emergency response" />
            </div>
            <div>
              <label>Description</label><textarea id="acDescription" placeholder="Accident summary"></textarea>
              <label>Actions / Notifications</label><textarea id="acActions" placeholder="Who was notified and what was done"></textarea>
              <button class="btn-green" onclick="submitAccident()">Submit Aircraft Accident Report</button>
            </div>
          </div>
        </div>

        <div id="tab-incident" class="tab-panel hidden">
          <div class="grid-3">
            <div>
              <label>Date / Time</label><input id="inDateTime" type="datetime-local" />
              <label>Incident Type</label><input id="inType" type="text" placeholder="Fuel spill, vehicle breach, light failure" />
              <label>Location</label><input id="inLocation" type="text" placeholder="Location" />
            </div>
            <div>
              <label>Severity</label><select id="inSeverity"><option>Low</option><option>Medium</option><option>High</option><option>Critical</option></select>
              <label>Parties Involved</label><input id="inPeople" type="text" placeholder="People, vehicles, contractor, airline" />
            </div>
            <div>
              <label>Description</label><textarea id="inDescription" placeholder="Incident details"></textarea>
              <label>Immediate Response</label><textarea id="inActions" placeholder="Response actions"></textarea>
              <button class="btn-green" onclick="submitIncident()">Submit Airside Incident</button>
            </div>
          </div>
        </div>

        <div id="tab-wildlife" class="tab-panel hidden">
          <div class="grid-3">
            <div>
              <label>Date / Time</label><input id="whDateTime" type="datetime-local" />
              <label>Species</label><input id="whSpecies" type="text" placeholder="Species" />
              <label>Location</label><input id="whLocation" type="text" placeholder="Location" />
            </div>
            <div>
              <label>Number Observed</label><input id="whCount" type="number" min="0" placeholder="0" />
              <label>Risk Level</label><select id="whRisk"><option>Low</option><option>Medium</option><option>High</option></select>
            </div>
            <div>
              <label>Control Measures</label><textarea id="whMeasures" placeholder="Harassment, relocation, control measures"></textarea>
              <label>Notes</label><textarea id="whNotes" placeholder="Additional notes"></textarea>
              <button class="btn-green" onclick="submitWildlife()">Submit Wildlife Hazard</button>
            </div>
          </div>
        </div>

        <div id="tab-fod" class="tab-panel hidden">
          <div class="grid-3">
            <div>
              <label>Date / Time</label><input id="fdDateTime" type="datetime-local" />
              <label>Category</label><input id="fdCategory" type="text" placeholder="FOD, pavement damage, sign damage" />
              <label>Location</label><input id="fdLocation" type="text" placeholder="Location" />
            </div>
            <div>
              <label>Severity</label><select id="fdSeverity"><option>Low</option><option>Medium</option><option>High</option></select>
              <label>Impact</label><input id="fdImpact" type="text" placeholder="Operational impact" />
            </div>
            <div>
              <label>Description</label><textarea id="fdDescription" placeholder="Description"></textarea>
              <label>Corrective Action</label><textarea id="fdActions" placeholder="Corrective action"></textarea>
              <button class="btn-green" onclick="submitFod()">Submit FOD / Damage</button>
            </div>
          </div>
        </div>
      </div>

      <br />

      <div class="card">
        <div class="toolbar">
          <div>
            <h3 style="margin:0 0 6px;">Management Summary</h3>
            <div class="muted">Operational reports for daily, weekly, monthly and annual review.</div>
          </div>
        </div>

        <div class="grid-4 stats" style="margin-bottom:16px;">
          <div class="card"><div class="label">ASO Daily Logs</div><div class="value" id="sumDailyLog">0</div></div>
          <div class="card"><div class="label">Birdstrike Reports</div><div class="value" id="sumBirdstrike">0</div></div>
          <div class="card"><div class="label">Aircraft Accident Reports</div><div class="value" id="sumAccident">0</div></div>
          <div class="card"><div class="label">Other Airside Reports</div><div class="value" id="sumOther">0</div></div>
        </div>

        <div class="table-wrap">
          <table>
            <thead>
              <tr>
                <th>Date</th>
                <th>Type</th>
                <th>Airport</th>
                <th>Submitted By</th>
                <th>Summary</th>
                <th>Severity</th>
              </tr>
            </thead>
            <tbody id="reportTableBody">
              <tr><td colspan="6" class="muted">No reports yet.</td></tr>
            </tbody>
          </table>
        </div>
      </div>
    </section>
  </div>

  <script>
    const firebaseConfig = {
      apiKey: "AIzaSyA24K2cPg00R6DZRSxwJBy7TQKIoE7dGAc",
      authDomain: "nac-aso-system.firebaseapp.com",
      projectId: "nac-aso-system",
      storageBucket: "nac-aso-system.appspot.com",
      messagingSenderId: "852433876255",
      appId: "1:852433876255:web:c59a0a757ee4f7466dac2a"
    };

    if (!firebase.apps.length) {
      firebase.initializeApp(firebaseConfig);
    }

    const auth = firebase.auth();
    const db = firebase.firestore();

    const messageBox = document.getElementById('messageBox');
    const authView = document.getElementById('authView');
    const appView = document.getElementById('appView');
    const sessionText = document.getElementById('sessionText');
    const reportTableBody = document.getElementById('reportTableBody');

    let currentUserProfile = null;
    let allReports = [];

    document.getElementById('summaryDate').value = todayISO();
    document.getElementById('dlDate').value = todayISO();
    document.getElementById('bsDateTime').value = nowLocalDateTime();
    document.getElementById('acDateTime').value = nowLocalDateTime();
    document.getElementById('inDateTime').value = nowLocalDateTime();
    document.getElementById('whDateTime').value = nowLocalDateTime();
    document.getElementById('fdDateTime').value = nowLocalDateTime();

    function showMessage(type, text) {
      messageBox.className = `message show ${type}`;
      messageBox.textContent = text;
    }

    function clearMessage() {
      messageBox.className = 'message';
      messageBox.textContent = '';
    }

    function todayISO() {
      return new Date().toISOString().slice(0, 10);
    }

    function nowLocalDateTime() {
      const d = new Date();
      const tz = new Date(d.getTime() - d.getTimezoneOffset() * 60000);
      return tz.toISOString().slice(0, 16);
    }

    function startOfWeek(date) {
      const d = new Date(date);
      const day = d.getDay();
      const diff = d.getDate() - day + (day === 0 ? -6 : 1);
      d.setDate(diff);
      d.setHours(0, 0, 0, 0);
      return d;
    }

    function periodKeys(dateInput) {
      const d = new Date(dateInput);
      const year = d.getFullYear();
      const month = String(d.getMonth() + 1).padStart(2, '0');
      const date = String(d.getDate()).padStart(2, '0');
      const weekStart = startOfWeek(d);
      const ws = `${weekStart.getFullYear()}-${String(weekStart.getMonth() + 1).padStart(2, '0')}-${String(weekStart.getDate()).padStart(2, '0')}`;
      return {
        dailyKey: `${year}-${month}-${date}`,
        weeklyKey: ws,
        monthlyKey: `${year}-${month}`,
        annualKey: `${year}`
      };
    }

    async function registerUser() {
      clearMessage();
      const fullName = document.getElementById('registerName').value.trim();
      const email = document.getElementById('registerEmail').value.trim();
      const password = document.getElementById('registerPassword').value;
      const role = document.getElementById('registerRole').value;
      const airport = document.getElementById('registerAirport').value.trim();

      if (!fullName || !email || !password || !role || !airport) {
        showMessage('error', 'Complete all registration fields.');
        return;
      }

      try {
        const cred = await auth.createUserWithEmailAndPassword(email, password);
        await db.collection('users').doc(cred.user.uid).set({
          fullName,
          email,
          role,
          airport,
          createdAt: firebase.firestore.FieldValue.serverTimestamp()
        });
        showMessage('success', 'Account created successfully.');
      } catch (error) {
        showMessage('error', error.message);
      }
    }

    async function loginUser() {
      clearMessage();
      const email = document.getElementById('loginEmail').value.trim();
      const password = document.getElementById('loginPassword').value;

      if (!email || !password) {
        showMessage('error', 'Enter email and password.');
        return;
      }

      try {
        await auth.signInWithEmailAndPassword(email, password);
      } catch (error) {
        showMessage('error', error.message);
      }
    }

    async function logoutUser() {
      await auth.signOut();
      showMessage('info', 'Logged out.');
    }

    async function loadUserProfile(uid) {
      const doc = await db.collection('users').doc(uid).get();
      if (!doc.exists) {
        throw new Error('User profile not found in Firestore.');
      }

      currentUserProfile = { uid, ...doc.data() };
      document.getElementById('statUser').textContent = currentUserProfile.fullName || '-';
      document.getElementById('statRole').textContent = currentUserProfile.role || '-';
      document.getElementById('statAirport').textContent = currentUserProfile.airport || '-';
      sessionText.textContent = `${currentUserProfile.fullName} • ${currentUserProfile.role} • ${currentUserProfile.airport}`;
    }

    async function loadReports() {
      if (!currentUserProfile) return;
      const snap = await db.collection('reports')
        .where('airport', '==', currentUserProfile.airport)
        .orderBy('eventDate', 'desc')
        .limit(300)
        .get();

      allReports = snap.docs.map(doc => ({ id: doc.id, ...doc.data() }));
      refreshDashboard();
    }

    function filterReports() {
      const period = document.getElementById('summaryPeriod').value;
      const dateValue = document.getElementById('summaryDate').value || todayISO();
      const keys = periodKeys(dateValue);

      return allReports.filter(report => {
        if (period === 'daily') return report.dailyKey === keys.dailyKey;
        if (period === 'weekly') return report.weeklyKey === keys.weeklyKey;
        if (period === 'monthly') return report.monthlyKey === keys.monthlyKey;
        return report.annualKey === keys.annualKey;
      });
    }

    function refreshDashboard() {
      const filtered = filterReports();
      document.getElementById('statCount').textContent = filtered.length;

      let daily = 0, bird = 0, accident = 0, other = 0;
      filtered.forEach(r => {
        if (r.reportType === 'ASO Daily Log') daily++;
        else if (r.reportType === 'Birdstrike Report') bird++;
        else if (r.reportType === 'Aircraft Accident Report') accident++;
        else other++;
      });

      document.getElementById('sumDailyLog').textContent = daily;
      document.getElementById('sumBirdstrike').textContent = bird;
      document.getElementById('sumAccident').textContent = accident;
      document.getElementById('sumOther').textContent = other;

      if (!filtered.length) {
        reportTableBody.innerHTML = '<tr><td colspan="6" class="muted">No reports found for the selected period.</td></tr>';
        return;
      }

      reportTableBody.innerHTML = filtered.map(r => `
        <tr>
          <td>${new Date(r.eventDate).toLocaleString()}</td>
          <td><span class="badge">${escapeHtml(r.reportType || '-')}</span></td>
          <td>${escapeHtml(r.airport || '-')}</td>
          <td>${escapeHtml(r.submittedByName || '-')}</td>
          <td>${escapeHtml(r.summary || '-')}</td>
          <td>${escapeHtml(r.severity || 'N/A')}</td>
        </tr>
      `).join('');
    }

    function escapeHtml(value) {
      return String(value ?? '')
        .replaceAll('&', '&amp;')
        .replaceAll('<', '&lt;')
        .replaceAll('>', '&gt;')
        .replaceAll('"', '&quot;')
        .replaceAll("'", '&#39;');
    }

    function switchTab(name, btn) {
      document.querySelectorAll('.tab-panel').forEach(el => el.classList.add('hidden'));
      document.getElementById(`tab-${name}`).classList.remove('hidden');
      document.querySelectorAll('.tab-btn').forEach(el => el.classList.remove('active'));
      btn.classList.add('active');
    }

    async function saveReport(payload) {
      if (!currentUserProfile) {
        showMessage('error', 'You must be signed in.');
        return;
      }

      const keys = periodKeys(payload.eventDate);
      await db.collection('reports').add({
        ...payload,
        ...keys,
        airport: currentUserProfile.airport,
        submittedByUid: currentUserProfile.uid,
        submittedByName: currentUserProfile.fullName,
        submittedByRole: currentUserProfile.role,
        createdAt: firebase.firestore.FieldValue.serverTimestamp()
      });

      showMessage('success', `${payload.reportType} submitted successfully.`);
      await loadReports();
    }

    async function submitDailyLog() {
      const eventDate = document.getElementById('dlDate').value;
      if (!eventDate) return showMessage('error', 'Select a daily log date.');
      await saveReport({
        reportType: 'ASO Daily Log',
        eventDate: `${eventDate}T00:00`,
        severity: 'Routine',
        summary: `${document.getElementById('dlShift').value} • ${document.getElementById('dlStatus').value || 'Operations logged'}`,
        details: {
          shift: document.getElementById('dlShift').value,
          weather: document.getElementById('dlWeather').value,
          status: document.getElementById('dlStatus').value,
          inspections: document.getElementById('dlInspections').value,
          hazards: document.getElementById('dlHazards').value,
          actions: document.getElementById('dlActions').value,
          outstanding: document.getElementById('dlOutstanding').value
        }
      });
    }

    async function submitBirdstrike() {
      const eventDate = document.getElementById('bsDateTime').value;
      if (!eventDate) return showMessage('error', 'Select a birdstrike date and time.');
      await saveReport({
        reportType: 'Birdstrike Report',
        eventDate,
        severity: document.getElementById('bsDamage').value,
        summary: `${document.getElementById('bsSpecies').value || 'Unknown species'} at ${document.getElementById('bsLocation').value || 'unspecified location'}`,
        details: {
          location: document.getElementById('bsLocation').value,
          aircraftRegistration: document.getElementById('bsReg').value,
          operator: document.getElementById('bsOperator').value,
          species: document.getElementById('bsSpecies').value,
          phaseOfFlight: document.getElementById('bsPhase').value,
          damage: document.getElementById('bsDamage').value,
          details: document.getElementById('bsDetails').value,
          actions: document.getElementById('bsActions').value
        }
      });
    }

    async function submitAccident() {
      const eventDate = document.getElementById('acDateTime').value;
      if (!eventDate) return showMessage('error', 'Select an accident date and time.');
      await saveReport({
        reportType: 'Aircraft Accident Report',
        eventDate,
        severity: document.getElementById('acSeverity').value,
        summary: `${document.getElementById('acReg').value || 'Aircraft'} • ${document.getElementById('acLocation').value || 'location not stated'}`,
        details: {
          location: document.getElementById('acLocation').value,
          aircraftRegistration: document.getElementById('acReg').value,
          operator: document.getElementById('acOperator').value,
          severity: document.getElementById('acSeverity').value,
          injuries: document.getElementById('acInjuries').value,
          impact: document.getElementById('acImpact').value,
          description: document.getElementById('acDescription').value,
          actions: document.getElementById('acActions').value
        }
      });
    }

    async function submitIncident() {
      const eventDate = document.getElementById('inDateTime').value;
      if (!eventDate) return showMessage('error', 'Select an incident date and time.');
      await saveReport({
        reportType: 'Airside Incident Report',
        eventDate,
        severity: document.getElementById('inSeverity').value,
        summary: `${document.getElementById('inType').value || 'Incident'} • ${document.getElementById('inLocation').value || 'location not stated'}`,
        details: {
          incidentType: document.getElementById('inType').value,
          location: document.getElementById('inLocation').value,
          severity: document.getElementById('inSeverity').value,
          parties: document.getElementById('inPeople').value,
          description: document.getElementById('inDescription').value,
          actions: document.getElementById('inActions').value
        }
      });
    }

    async function submitWildlife() {
      const eventDate = document.getElementById('whDateTime').value;
      if (!eventDate) return showMessage('error', 'Select a wildlife report date and time.');
      await saveReport({
        reportType: 'Wildlife Hazard Report',
        eventDate,
        severity: document.getElementById('whRisk').value,
        summary: `${document.getElementById('whSpecies').value || 'Wildlife'} • ${document.getElementById('whLocation').value || 'location not stated'}`,
        details: {
          species: document.getElementById('whSpecies').value,
          location: document.getElementById('whLocation').value,
          count: document.getElementById('whCount').value,
          risk: document.getElementById('whRisk').value,
          measures: document.getElementById('whMeasures').value,
          notes: document.getElementById('whNotes').value
        }
      });
    }

    async function submitFod() {
      const eventDate = document.getElementById('fdDateTime').value;
      if (!eventDate) return showMessage('error', 'Select a FOD / damage report date and time.');
      await saveReport({
        reportType: 'FOD / Airside Damage Report',
        eventDate,
        severity: document.getElementById('fdSeverity').value,
        summary: `${document.getElementById('fdCategory').value || 'FOD / Damage'} • ${document.getElementById('fdLocation').value || 'location not stated'}`,
        details: {
          category: document.getElementById('fdCategory').value,
          location: document.getElementById('fdLocation').value,
          severity: document.getElementById('fdSeverity').value,
          impact: document.getElementById('fdImpact').value,
          description: document.getElementById('fdDescription').value,
          actions: document.getElementById('fdActions').value
        }
      });
    }

    function printSummary() {
      const rows = filterReports();
      const period = document.getElementById('summaryPeriod').value.toUpperCase();
      const refDate = document.getElementById('summaryDate').value || todayISO();
      const html = `
        <html>
          <head>
            <title>NAC ASO Management Summary</title>
            <style>
              body { font-family: Arial; padding: 20px; }
              table { width: 100%; border-collapse: collapse; }
              th, td { border: 1px solid #ccc; padding: 8px; text-align: left; }
              th { background: #f2f2f2; }
            </style>
          </head>
          <body>
            <h1>NAC ASO Management Summary</h1>
            <h3>${escapeHtml(currentUserProfile?.airport || '-')} • ${period} • ${escapeHtml(refDate)}</h3>
            <table>
              <thead>
                <tr><th>Date</th><th>Type</th><th>Submitted By</th><th>Summary</th><th>Severity</th></tr>
              </thead>
              <tbody>
                ${rows.length ? rows.map(r => `<tr><td>${new Date(r.eventDate).toLocaleString()}</td><td>${escapeHtml(r.reportType)}</td><td>${escapeHtml(r.submittedByName)}</td><td>${escapeHtml(r.summary)}</td><td>${escapeHtml(r.severity || 'N/A')}</td></tr>`).join('') : '<tr><td colspan="5">No reports found.</td></tr>'}
              </tbody>
            </table>
          </body>
        </html>`;
      const w = window.open('', '_blank');
      w.document.write(html);
      w.document.close();
      w.focus();
      w.print();
    }

    auth.onAuthStateChanged(async (user) => {
      clearMessage();

      if (!user) {
        currentUserProfile = null;
        authView.classList.remove('hidden');
        appView.classList.add('hidden');
        sessionText.textContent = 'Signed out';
        return;
      }

      try {
        await loadUserProfile(user.uid);
        await loadReports();
        authView.classList.add('hidden');
        appView.classList.remove('hidden');
      } catch (error) {
        showMessage('error', error.message);
      }
    });
  </script>
</body>
</html> use this, and tell me how to delete the old one and create new one with this, i want that old webpage gone. give me the git way, the easiest way. +#+#+#+#+#+assistant to=canmore.update_textdoc մեկնաբանություն 天天好彩票json 玩彩神争霸{
