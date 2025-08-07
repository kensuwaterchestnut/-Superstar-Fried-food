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
  </style>
</head>
<body>
  <h1>訂單正在通知總部中…</h1>
  <p>請稍候，我們正在處理您的訂單資訊。</p>

  <p id="status" class="success"></p>

  <script>
    // 初始化 emailjs
    (function(){
      emailjs.init("nMamM2Ecz2ztnkCOV");
    })();

    // 從網址抓參數
    function getQueryParam(name) {
      const urlParams = new URLSearchParams(window.location.search);
      return urlParams.get(name) || '';
    }

    // 將網址參數組合成變數
    const templateParams = {
      branch: getQueryParam("branch"),
      detail: getQueryParam("detail"),
      email: getQueryParam("email"),
      shipping: getQueryParam("shipping"),
      note: getQueryParam("note"),
      freight: getQueryParam("freight"),
      total: getQueryParam("total"),
    };

    // 寄送 Email
    emailjs.send("service_ov4783q", "template_ceydmzp", templateParams)
      .then(function(response) {
          document.getElementById("status").innerText = "✅ 訂單通知已寄出！";
      }, function(error) {
          document.getElementById("status").innerText = "❌ 發送失敗，請稍後重試";
      });
  </script>
</body>
</html>
