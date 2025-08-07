<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>送出中...</title>
  <style>
    body {
      background-color: #111;
      color: white;
      font-family: sans-serif;
      text-align: center;
      padding-top: 100px;
    }
  </style>
</head>
<body>
  <h1>正在送出您的訂單資料...</h1>
  <p>請稍候，處理中</p>

  <script>
    const params = new URLSearchParams(window.location.search);
    const data = {};

    for (const [key, value] of params.entries()) {
      data[key] = value;
    }

    // 呼叫 Google Apps Script Web App
    fetch("https://script.google.com/macros/s/AKfycbyQNTF0rHwaTR-rBaTnnOvi-yipqqCaPH2w9NvHFofZn0b9vCgdkS7tpGUo38uiysQS/exec", {
      method: "POST",
      headers: {
        "Content-Type": "application/json"
      },
      body: JSON.stringify(data)
    })
    .then(response => {
      if (response.ok) {
        console.log("✅ 成功送出");
        document.body.innerHTML = "<h1>✅ 訂單送出成功！</h1><p>我們已收到您的資料</p>";
      } else {
        console.error("❌ 傳送失敗");
        document.body.innerHTML = "<h1>❌ 傳送失敗</h1><p>請稍後再試，或聯絡客服</p>";
      }
    })
    .catch(error => {
      console.error("❌ 發生錯誤", error);
      document.body.innerHTML = "<h1>❌ 系統錯誤</h1><p>請稍後再試</p>";
    });
  </script>
</body>
</html>
