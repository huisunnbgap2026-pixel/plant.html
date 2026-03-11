<!DOCTYPE html>
<html>
<head>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>植物導覽</title>
  <style>
    body {
      margin: 0;
      padding: 24px 16px 40px;
      font-family: "Noto Sans TC", "Microsoft JhengHei", Arial, sans-serif;
      color: #2f3a2f;
      background:
        radial-gradient(circle at top left, rgba(168,196,140,0.22), transparent 28%),
        radial-gradient(circle at bottom right, rgba(122,160,110,0.16), transparent 24%),
        linear-gradient(180deg, #f8f5ec 0%, #f2ede0 100%);
    }

    .wrap {
      max-width: 640px;
      margin: 0 auto;
    }

    .card {
      background: rgba(255,255,255,0.94);
      border: 1px solid rgba(79,122,87,0.12);
      border-radius: 20px;
      padding: 24px;
      box-shadow: 0 10px 30px rgba(69,89,63,0.12);
      text-align: center;
    }

    h2 {
      margin: 0 0 12px;
      font-size: 22px;
    }

    p {
      margin: 0 0 12px;
      line-height: 1.8;
      color: #6c7a67;
    }

    input, button, a {
      width: 100%;
      box-sizing: border-box;
      display: block;
      border-radius: 14px;
      padding: 14px 16px;
      font-size: 16px;
      margin-top: 12px;
      text-decoration: none;
      border: 0;
    }

    input {
      background: #fff;
      border: 1px solid #d8e4d3;
    }

    button, a.primary {
      background: linear-gradient(135deg, #4f7a57, #3e6245);
      color: #fff;
    }

    a.secondary {
      background: #e7efe4;
      color: #2f3a2f;
    }

    #manualBox { display: none; }
    #goBtn { display: none; }
  </style>
</head>
<body>
  <div class="wrap">
    <div class="card">
      <h2>正在開啟植物介紹頁</h2>
      <p id="status">請稍候，系統正在記錄你的植物導覽點數…</p>

      <div id="manualBox">
        <input id="phoneInput" type="text" placeholder="請輸入手機號碼">
        <button id="savePhoneBtn">送出並前往植物頁</button>
      </div>

      <a id="goBtn" class="primary" href="#" target="_top">前往植物介紹頁</a>
    </div>
  </div>

  <script>
    const APP_URL = "https://script.google.com/macros/s/AKfycbyoFlYdQfjyJUu0onsq2DOI5wZJmRcNI2maSEbU7b5uKiSQmajfFRJGYL8OisrhhNUYKg/exec";

    function normalizePhone(phone) {
      let p = String(phone || "").replace(/\D/g, "").trim();
      if (p.length === 9 && p.startsWith("9")) p = "0" + p;
      return p;
    }

    function getParam(name) {
      const url = new URL(window.location.href);
      return url.searchParams.get(name) || "";
    }

    const plantId = getParam("plant");
    const pageUrl = getParam("page");
    const statusEl = document.getElementById("status");
    const manualBox = document.getElementById("manualBox");
    const goBtn = document.getElementById("goBtn");
    const phoneInput = document.getElementById("phoneInput");
    const savePhoneBtn = document.getElementById("savePhoneBtn");

    function goToPlantPage() {
      goBtn.href = pageUrl;
      goBtn.style.display = "block";
      window.top.location.href = pageUrl;
    }

    async function markPlant(phone) {
      const url = `${APP_URL}?mode=plant_mark_api&plant=${encodeURIComponent(plantId)}&phone=${encodeURIComponent(phone)}`;

      try {
        await fetch(url, { method: "GET", mode: "no-cors" });
      } catch (e) {
        // no-cors 下即使失敗也繼續導頁
      }

      goToPlantPage();
    }

    function start() {
      if (!plantId || !pageUrl) {
        statusEl.textContent = "缺少植物資料，請重新掃描 QR code。";
        return;
      }

      let savedPhone = normalizePhone(localStorage.getItem("savedPhone") || "");

      if (!savedPhone) {
        statusEl.textContent = "請先輸入手機號碼。";
        manualBox.style.display = "block";
        return;
      }

      statusEl.textContent = "已記錄導覽點數，正在前往植物介紹頁…";
      markPlant(savedPhone);
    }

    savePhoneBtn.addEventListener("click", function () {
      const phone = normalizePhone(phoneInput.value);
      if (!phone) {
        statusEl.textContent = "請先輸入正確手機號碼。";
        return;
      }
      localStorage.setItem("savedPhone", phone);
      statusEl.textContent = "已記錄導覽點數，正在前往植物介紹頁…";
      manualBox.style.display = "none";
      markPlant(phone);
    });

    start();
  </script>
</body>
</html>
