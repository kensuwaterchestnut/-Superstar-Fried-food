<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="UTF-8">
  <title>叫貨明細（預覽）</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
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
  <!-- 引入 EmailJS -->
  <script src="https://cdn.jsdelivr.net/npm/emailjs-com@2.6.4/dist/email.min.js"></script>
</head>
<body>

  <h1>叫貨明細（預覽）</h1>
  <div class="param-box" id="paramsBox">
    讀取中...
  </div>

  <script>
    // 1. 解析網址參數
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
    box.innerHTML = '';

    let content = '';
    for (const key in params) {
      const line = `<strong>【${key}】</strong>：${params[key] || '（空）'}`;
      const p = document.createElement('p');
      p.innerHTML = line;
      box.appendChild(p);
      content += line + '\n';
    }

    // 2. 初始化 EmailJS
    emailjs.init("nMamM2Ecz2ztnkCOV"); // API 金鑰

    // 3. 寄送 Email
    emailjs.send("service_ov4783q", "template_ceydmzp", {
      to_email: "kensu.water.chestnut@gmail.com", // 總部信箱
      message: content
    }).then(function(response) {
      console.log("✅ 寄出成功", response);
    }, function(error) {
      console.error("❌ 寄信失敗", error);
    });
  </script>

</body>
</html>
