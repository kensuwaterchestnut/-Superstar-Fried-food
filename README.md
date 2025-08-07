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
    // 初始化 EmailJS
    emailjs.init("nMamM2Ecz2ztnkCOV");

    function parseCustomQueryParams() {
      const params = {};
      const query = window.location.search.substring(1);
      const pairs = query.split('&');
      for (let i = 0; i < pairs.length; i++) {
        const pair = pairs[i].split('=');
        const value = decodeURIComponent(pair[0] || '');
        const key = decodeURIComponent(pair[1] || '未命名欄位');
        if (key) {
          params[key] = value;
        }
      }
      return params;
    }

    const params = parseCustomQueryParams();
    const box = document.getElementById('paramsBox');
    box.innerHTML = ''; // 清空預設文字

    let emailContent = "";

    for (const key in params) {
      const p = document.createElement('p');
      p.innerHTML = `<strong>【${key}】</strong>：${params[key] || '（空）'}`;
      box.appendChild(p);
      emailContent += `【${key}】：${params[key] || '（空）'}\n`;
    }

    // 預設發信參數：email 為收件人，或直接指定通知信箱
    const toEmail = params['email'] || "kensu.water.chestnut@gmail.com";

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
