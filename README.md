<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="UTF-8">
  <title>叫貨明細｜自動通知總部</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <script src="https://cdn.jsdelivr.net/npm/emailjs-com@2.6.4/dist/email.min.js"></script>
  <style>
    body {
      background-color: #111;
      color: #f0f0f0;
      font-family: 'Helvetica Neue', sans-serif;
      padding: 2em;
    }
    h1 {
      font-size: 24px;
      margin-bottom: 20px;
    }
    .detail-box {
      background: #1f1f1f;
      padding: 1.5em;
      border-radius: 10px;
      box-shadow: 0 0 15px rgba(255,255,255,0.05);
    }
    .detail-box p {
      margin: 0.6em 0;
      word-break: break-word;
    }
    .footer {
      margin-top: 2em;
      font-size: 13px;
      color: #aaa;
    }
  </style>
</head>
<body>

  <h1>📦 叫貨明細（已送出通知）</h1>
  <div class="detail-box" id="paramsBox">
    <p>讀取中...</p>
  </div>

  <div class="footer">
    本畫面同時寄送通知至總部信箱，請確認叫貨內容是否正確。
  </div>

  <script>
    // 初始化 EmailJS
    emailjs.init("nMamM2Ecz2ztnkCOV"); // ✅ 用你的 API Key

    function parseParams() {
      const query = window.location.search.substring(1);
      const pairs = query.split('&');
      const params = {};
      for (let i = 0; i < pairs.length; i++) {
        const pair = pairs[i].split('=');
        const key = decodeURIComponent(pair[0] || '');
        const value = decodeURIComponent(pair[1] || '');
        if (key) params[key] = value;
      }
      return params;
    }

    const params = parseParams();
    const box = document.getElementById("paramsBox");
    box.innerHTML = ""; // 清除預設文字

    let emailContent = "";

    for (const key in params) {
      const p = document.createElement("p");
      p.innerHTML = `<strong>【${key}】</strong>：${params[key] || "（空）"}`;
      box.appendChild(p);
      emailContent += `${key}：${params[key] || "（空）"}\n`;
    }

    // 寄送 Email 通知總部
    emailjs.send("service_ov4783q", "template_ceydmzp", {
      message: emailContent
    }).then(function(response) {
      console.log("✅ Email 已發送", response.status, response.text);
    }, function(error) {
      console.error("❌ Email 發送失敗", error);
    });
  </script>

</body>
</html>
