
# ESP32-C3 pH Monitoring Dashboard

## ขั้นตอนสำคัญสำหรับสร้าง Dashboard

### 1. สร้าง Google Apps Script
เข้า Google Sheets > Extensions > Apps Script
<div align="center"><img src="0401_updateScript.png" alt="Update Script" width="400"/></div>

### 2. วางโค้ด Apps Script แล้ว Save
```javascript
function doGet(e) {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  if (e.parameter.action == "read") {
    var rows = sheet.getLastRow();
    var startRow = Math.max(1, rows - 49); 
    var data = sheet.getRange(startRow, 1, rows - startRow + 1, 3).getValues();
    return ContentService.createTextOutput(JSON.stringify(data))
      .setMimeType(ContentService.MimeType.JSON);
  }
  var ph = e.parameter.ph;
  var voltage = e.parameter.voltage;
  if (ph && voltage) {
    var date = new Date();
    sheet.appendRow([date, ph, voltage]);
    return ContentService.createTextOutput("Success");
  }
  return ContentService.createTextOutput("No Data");
}
```

### 3. Deploy เป็น Web App
<div align="center"><img src="0402_deploy.png" alt="Deploy Script" width="400"/></div>
<div align="center"><img src="0403_editNew_deploy.png" alt="Edit New Deploy" width="400"/></div>


### 4. สร้างไฟล์ index.html สำหรับ Dashboard
วางโค้ด HTML ด้านล่างนี้ในไฟล์ index.html
<div align="center"><img src="0404_create_html.png" alt="Create HTML Dashboard" width="400"/></div>

#### ตัวอย่างโค้ด index.html
```html
<!DOCTYPE html>
<html lang="th">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Smart pH Monitoring Dashboard</title>
  <!-- Libraries -->
  <script src="https://cdn.tailwindcss.com"></script>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
  <link href="https://fonts.googleapis.com/css2?family=Kanit:wght@300;400;600&display=swap" rel="stylesheet">
  <script src="https://cdn.jsdelivr.net/npm/sweetalert2@11"></script>
  <style>
    body { font-family: 'Kanit', sans-serif; background-color: #f3f4f6; }
    .glass-card {
      background: rgba(255, 255, 255, 0.9);
      backdrop-filter: blur(10px);
      border-radius: 1rem;
      box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
      transition: all 0.3s ease;
    }
    .glass-card:hover { transform: translateY(-2px); box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1); }
    .animate-pulse-slow { animation: pulse 3s cubic-bezier(0.4, 0, 0.6, 1) infinite; }
    @keyframes pulse { 0%, 100% { opacity: 1; } 50% { opacity: .7; } }
  </style>
</head>
<body class="text-gray-800">
  <!-- Navbar -->
  <nav class="bg-white shadow-md sticky top-0 z-50">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
      <div class="flex justify-between h-16">
        <div class="flex items-center">
          <i class="fas fa-flask text-blue-500 text-2xl mr-3"></i>
          <h1 class="text-xl font-bold text-gray-800">Smart pH <span class="text-blue-500">Monitor</span></h1>
        </div>
        <div class="flex items-center space-x-4">
          <div id="connectionStatus" class="flex items-center px-3 py-1 rounded-full bg-red-100 text-red-600 text-sm">
            <div class="w-2 h-2 rounded-full bg-red-500 mr-2"></div> Offline
          </div>
          <button onclick="openSettings()" class="text-gray-500 hover:text-blue-500 transition"><i class="fas fa-cog text-xl"></i></button>
        </div>
      </div>
    </div>
  </nav>

  <!-- Main Content -->
  <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
    <!-- Top Stats Cards -->
    <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-8">
      <!-- pH Value Card -->
      <div class="glass-card p-6 border-l-4 border-blue-500 relative overflow-hidden">
        <div class="flex justify-between items-start">
          <div>
            <p class="text-sm text-gray-500 uppercase font-semibold">Current pH</p>
            <h2 id="phValue" class="text-5xl font-bold text-gray-800 mt-2">--.--</h2>
          </div>
          <div class="p-3 bg-blue-100 rounded-full text-blue-600">
            <i class="fas fa-water text-2xl"></i>
          </div>
        </div>
        <p id="phStatus" class="mt-4 text-sm font-medium text-gray-600 flex items-center">
          <i class="fas fa-circle text-xs mr-2 text-gray-400"></i> Waiting for data...
        </p>
        <!-- Background Decoration -->
        <i class="fas fa-tint absolute -bottom-4 -right-4 text-9xl text-blue-50 opacity-20"></i>
      </div>
      <!-- Voltage Card -->
      <div class="glass-card p-6 border-l-4 border-purple-500">
        <div class="flex justify-between items-start">
          <div>
            <p class="text-sm text-gray-500 uppercase font-semibold">Sensor Voltage</p>
            <h2 id="voltageValue" class="text-4xl font-bold text-gray-800 mt-2">--.-- <span class="text-lg text-gray-400">V</span></h2>
          </div>
          <div class="p-3 bg-purple-100 rounded-full text-purple-600">
            <i class="fas fa-bolt text-2xl"></i>
          </div>
        </div>
        <p class="mt-4 text-sm text-gray-500">Raw sensor output</p>
      </div>
      <!-- Condition Card -->
      <div class="glass-card p-6 border-l-4 border-green-500" id="conditionCard">
        <div class="flex justify-between items-start">
          <div>
            <p class="text-sm text-gray-500 uppercase font-semibold">Condition</p>
            <h2 id="conditionValue" class="text-3xl font-bold text-gray-800 mt-2">Unknown</h2>
          </div>
          <div class="p-3 bg-green-100 rounded-full text-green-600" id="conditionIconBg">
            <i id="conditionIcon" class="fas fa-question text-2xl"></i>
          </div>
        </div>
        <p id="lastUpdate" class="mt-4 text-xs text-gray-400 text-right">Last update: -</p>
      </div>
    </div>
    <!-- Chart Section -->
    <div class="glass-card p-6 mb-8">
      <div class="flex justify-between items-center mb-6">
        <h3 class="text-lg font-bold text-gray-700"><i class="fas fa-chart-line mr-2"></i>pH History Trend</h3>
        <button onclick="fetchData()" class="text-sm bg-blue-50 text-blue-600 px-3 py-1 rounded hover:bg-blue-100 transition">
          <i class="fas fa-sync-alt mr-1"></i> Refresh
        </button>
      </div>
      <div class="relative h-80 w-full">
        <canvas id="phChart"></canvas>
      </div>
    </div>
  </div>
  <!-- Footer -->
  <footer class="text-center text-gray-400 text-sm py-6">
    <p>IoT Smart Farming & Water Quality Monitor System</p>
    <p class="text-xs mt-1">Powered by ESP32 & Google Sheets</p>
  </footer>
  <!-- JS Logic -->
  <script>
    // --- Configuration ---
    let SCRIPT_URL = localStorage.getItem('script_url') || ''; 
    // --- Chart Initialization ---
    const ctx = document.getElementById('phChart').getContext('2d');
    const phChart = new Chart(ctx, {
      type: 'line',
      data: {
        labels: [],
        datasets: [{
          label: 'pH Level',
          data: [],
          borderColor: '#3b82f6',
          backgroundColor: 'rgba(59, 130, 246, 0.1)',
          borderWidth: 3,
          tension: 0.4,
          pointRadius: 3,
          pointHoverRadius: 6,
          fill: true
        }]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        scales: {
          y: {
            min: 0, max: 14,
            grid: { color: '#f3f4f6' },
            title: { display: true, text: 'pH Value' }
          },
          x: {
            grid: { display: false }
          }
        },
        plugins: {
          legend: { display: false }
        },
        interaction: {
          mode: 'index',
          intersect: false,
        }
      }
    });
    // --- Main Functions ---
    async function fetchData() {
      if (!SCRIPT_URL) {
        openSettings();
        return;
      }
      updateStatus('loading');
      try {
        const response = await fetch(`${SCRIPT_URL}?action=read`);
        const data = await response.json();
        if (!Array.isArray(data) || data.length === 0) {
          console.warn("No data found");
          return;
        }
        const labels = [];
        const phValues = [];
        let lastPh = 0;
        let lastVolt = 0;
        let lastDate = "";
        data.forEach(row => {
          let dateStr = new Date(row[0]).toLocaleTimeString('th-TH', { hour: '2-digit', minute: '2-digit' });
          labels.push(dateStr);
          phValues.push(row[1]);
          lastPh = row[1];
          lastVolt = row[2];
          lastDate = new Date(row[0]).toLocaleString('th-TH');
        });
        updateDashboard(lastPh, lastVolt, lastDate);
        phChart.data.labels = labels;
        phChart.data.datasets[0].data = phValues;
        phChart.update();
        updateStatus('online');
      } catch (error) {
        console.error("Error fetching data:", error);
        updateStatus('offline');
        Swal.fire({
          icon: 'error',
          title: 'Connection Error',
          text: 'ไม่สามารถดึงข้อมูลได้ โปรดตรวจสอบ URL หรือการตั้งค่า Google Script',
          footer: '<a href="#" onclick="openSettings()">แก้ไข URL</a>'
        });
      }
    }
    function updateDashboard(ph, voltage, date) {
      document.getElementById('phValue').innerText = parseFloat(ph).toFixed(2);
      document.getElementById('voltageValue').innerHTML = `${parseFloat(voltage).toFixed(3)} <span class="text-lg text-gray-400">V</span>`;
      document.getElementById('lastUpdate').innerText = `Last update: ${date}`;
      const conditionCard = document.getElementById('conditionCard');
      const conditionVal = document.getElementById('conditionValue');
      const conditionIcon = document.getElementById('conditionIcon');
      const conditionIconBg = document.getElementById('conditionIconBg');
      const phStatus = document.getElementById('phStatus');
      const phValueEl = document.getElementById('phValue');
      let colorClass = "";
      let statusText = "";
      let iconClass = "";
      if (ph < 6.5) {
        colorClass = "text-yellow-600";
        statusText = "Acidic (เป็นกรด)";
        iconClass = "fa-lemon";
        conditionCard.className = "glass-card p-6 border-l-4 border-yellow-500";
        conditionIconBg.className = "p-3 bg-yellow-100 rounded-full text-yellow-600";
        phValueEl.className = "text-5xl font-bold text-yellow-600 mt-2";
      } else if (ph > 8.5) {
        colorClass = "text-purple-600";
        statusText = "Alkaline (เป็นด่าง)";
        iconClass = "fa-soap";
        conditionCard.className = "glass-card p-6 border-l-4 border-purple-500";
        conditionIconBg.className = "p-3 bg-purple-100 rounded-full text-purple-600";
        phValueEl.className = "text-5xl font-bold text-purple-600 mt-2";
      } else {
        colorClass = "text-green-600";
        statusText = "Neutral (ปกติ)";
        iconClass = "fa-check-circle";
        conditionCard.className = "glass-card p-6 border-l-4 border-green-500";
        conditionIconBg.className = "p-3 bg-green-100 rounded-full text-green-600";
        phValueEl.className = "text-5xl font-bold text-green-600 mt-2";
      }
      conditionVal.innerText = statusText.split(' ')[0];
      conditionIcon.className = `fas ${iconClass} text-2xl`;
      phStatus.innerHTML = `<i class="fas fa-circle text-xs mr-2 ${colorClass}"></i> ${statusText}`;
    }
    function updateStatus(status) {
      const el = document.getElementById('connectionStatus');
      if (status === 'online') {
        el.className = "flex items-center px-3 py-1 rounded-full bg-green-100 text-green-600 text-sm animate-pulse-slow";
        el.innerHTML = '<div class="w-2 h-2 rounded-full bg-green-500 mr-2"></div> Online';
      } else if (status === 'loading') {
        el.className = "flex items-center px-3 py-1 rounded-full bg-blue-100 text-blue-600 text-sm";
        el.innerHTML = '<i class="fas fa-spinner fa-spin mr-2"></i> Syncing...';
      } else {
        el.className = "flex items-center px-3 py-1 rounded-full bg-red-100 text-red-600 text-sm";
        el.innerHTML = '<div class="w-2 h-2 rounded-full bg-red-500 mr-2"></div> Offline';
      }
    }
    function openSettings() {
      Swal.fire({
        title: 'ตั้งค่าการเชื่อมต่อ',
        input: 'url',
        inputLabel: 'Google Apps Script URL (Web App URL)',
        inputValue: SCRIPT_URL,
        inputPlaceholder: 'https://script.google.com/macros/s/.../exec',
        showCancelButton: true,
        confirmButtonText: 'บันทึก',
        cancelButtonText: 'ยกเลิก',
        footer: '<span class="text-xs text-gray-500">ต้องเป็น URL ที่ได้จากการ Deploy Web App และเปิดสิทธิ์ Anyone</span>'
      }).then((result) => {
        if (result.isConfirmed && result.value) {
          SCRIPT_URL = result.value;
          localStorage.setItem('script_url', SCRIPT_URL);
          fetchData();
          Swal.fire('บันทึกสำเร็จ', 'กำลังดึงข้อมูล...', 'success');
        }
      });
    }
    setInterval(fetchData, 10000);
    if(SCRIPT_URL) {
      fetchData();
    } else {
       setTimeout(() => {
        if(!SCRIPT_URL) {
          Swal.fire({
            title: 'ยินดีต้อนรับ!',
            text: 'กรุณากดที่ไอคอนฟันเฟือง (มุมขวาบน) เพื่อใส่ Google Script URL ของคุณ',
            icon: 'info'
          });
          updateDashboard(7.05, 2.18, "Demo Mode");
        }
       }, 1000);
    }
  </script>
</body>
</html>
```

### 5. เปิด index.html ด้วย Chrome และใส่ Web App URL
<div align="center"><img src="0405_add_webScript_dash.png" alt="Add Web Script Dashboard" width="400"/></div>

### 6. ตัวอย่าง Dashboard ที่เสร็จสมบูรณ์
<div align="center"><img src="0406_full_project.png" alt="Full Project Dashboard" width="400"/></div>


