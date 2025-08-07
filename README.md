<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="UTF-8">
  <title>叫貨明細（預覽）</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <script src="https://cdn.jsdelivr.net/npm/emailjs-com@2.6.4/dist/email.min.js"></script>
  <style>
    body {
      background-color: #121212;
      color: #f0f0f0;
      font-family: 'Helvetica Neue', sans-serif;
      padding: 2em;
    }
    h1 {
      font-size: 24px;
      margin-bottom: 1em;
    }
    .param-box {
      background: #1e1e1e;
      padding: 1em 1.5em;
      border-radius: 8px;
      box-shadow: 0 0 10px rgba(255,255,255,0.05);
    }
    .param-box p {
      margin: 0.5em 0;
      word-break: break-word;
    }
  </style>
</head>
<body>

  <h1>叫貨明細（預覽）</h1>
  <div class="param-box" id="paramsBox">
    讀取中...
  </div>

  <script>
    emailjs.init("nMamM2Ecz2ztnkCOV"); // ✅ 你的 API 金鑰

    function parseQueryParams() {
      const query = window.location.search.substring(1);
      const pairs = query.split('&');
      const params = {};
      for (let i = 0; i < pairs.length; i++) {
        const pair = pairs[i].split('=');
        const value = decodeURIComponent(pair[0] || ''); // 左邊是值
        const key = decodeURIComponent(pair[1] || '未命名欄位'); // 右邊是欄位名稱
        if (key) {
          params[key] = value;
        }
      }
      return params;
    }

    const params = parseQueryParams();
    const box = document.getElementById('paramsBox');
    box.innerHTML = ''; // 清空畫面

    let emailContent = '';
    let toEmail = 'kensu.water.chestnut@gmail.com'; // 預設收件人

    for (const key in params) {
      const value = params[key] || '（空）';
      const p = document.createElement('p');
      p.innerHTML = `<strong>【${key}】</strong>：${value}`;
      box.appendChild(p);
      emailContent += `【${key}】：${value}\n`;

      if (key.includes('信箱') || key.toLowerCase().includes('email')) {
        toEmail = value; // 若網址中有 email，則當收件人
      }
    }

    // 發送 email
    emailjs.send("service_ov4783q", "template_ceydmzp", {
      to_email: toEmail,
      message: emailContent
    }).then(function(response) {
      console.log("✅ Email 發送成功", response.status, response.text);
    }, function(error) {
      console.error("❌ Email 發送失敗", error);
    });
  </script>

</body>
</html>
