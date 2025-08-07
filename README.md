<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="UTF-8">
  <title>正在通知總部...</title>
  <script src="https://cdn.jsdelivr.net/npm/emailjs-com@2.4.1/dist/email.min.js"></script>
  <style>
    body {
      background-color: #111;
      color: #fff;
      font-family: "Segoe UI", "Noto Sans TC", sans-serif;
      text-align: center;
      padding: 80px 20px;
    }
    h1 {
      font-size: 28px;
      margin-bottom: 10px;
    }
    p {
      font-size: 16px;
      color: #aaa;
    }
    .success {
      color: #4caf50;
      margin-top: 30px;
    }
    .error {
      color: #ff5252;
      margin-top: 30px;
    }
  </style>
</head>
<body>
  <h1>訂單正在通知總部中…</h1>
  <p>請稍候，我們正在處理您的訂單資訊。</p>
  <p id="status" class="success"></p>

  <script>
    (function() {
      emailjs.init("nMamM2Ecz2ztnkCOV"); // 這是你的 Public Key
    })();

    // 抓取網址參數
    function getParam(key) {
      return new URLSearchParams(window.location.search).get(key) || '';
    }

    // 傳送參數
    const templateParams = {
      branch: getParam("branch"),
      detail: getParam("detail"),
      email: getParam("email"),
      shipping: getParam("shipping"),
      note: getParam("note"),
      freight: getParam("freight"),
      total: getParam("total")
    };

    // 使用 emailjs 寄送 email
    emailjs.send("service_ov4783q", "template_ceydmzp", templateParams)
      .then(function(response) {
        document.getElementById("status").innerText = "✅ 訂單通知已寄出！";
      }, function(error) {
        document.getElementById("status").innerText = "❌ 發送失敗，請稍後重試";
        document.getElementById("status").className = "error";
      });
  </script>
</body>
</html>
