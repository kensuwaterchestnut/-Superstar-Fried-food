<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="UTF-8">
  <title>叫貨明細通知中繼頁</title>
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
</head>
<body>
  <h1>叫貨明細（預覽與寄送）</h1>
  <div class="param-box" id="paramsBox">讀取中...</div>

  <script>
    const params = new URLSearchParams(window.location.search);
    const box = document.getElementById('paramsBox');
    box.innerHTML = '';

    let content = '';
    params.forEach((value, key) => {
      const line = `【${decodeURIComponent(key)}】：${decodeURIComponent(value)}\n`;
      const html = `<p><strong>【${key}】</strong>：${value || '（空）'}</p>`;
      content += line;
      box.innerHTML += html;
    });

    // 寄送信件給總部
    fetch("https://api.mailersend.com/v1/email", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
        "Authorization": "Bearer mlsn.92978e95cacbd35be8150e5a571ea2c1b3fcd93b56693a49e04c70be7efdd849"
      },
      body: JSON.stringify({
        from: { email: "notify@kensufried.com", name: "啃酥叫貨通知" },
        to: [{ email: "kensu.water.chestnut@gmail.com" }],
        subject: "新叫貨通知信",
        text: content,
        html: content.replace(/\n/g, '<br>'),
        domain_id: "x2p0347yde34zdrn"
      })
    }).then(res => {
      if (res.ok) {
        console.log("Email sent successfully.");
      } else {
        console.error("Failed to send email.", res);
      }
    });
  </script>
</body>
</html>
