<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="UTF-8" />
  <title>訂單發送中...</title>
  <script src="https://cdn.jsdelivr.net/npm/emailjs-com@3/dist/email.min.js"></script>
  <script>
    (function () {
      emailjs.init("nMamM2Ecz2ztnkCOV"); // 這是你的 EmailJS 公鑰
    })();

    window.onload = function () {
      const params = new URLSearchParams(window.location.search);

      const templateParams = {
        order_time: params.get("order_time"),
        order_id: params.get("order_id"),
        email: params.get("email"),
        store_name: params.get("store_name"),
        order_details: params.get("order_details"),
        delivery_method: params.get("delivery_method"),
        pickup_note: params.get("pickup_note"),
        shipping: params.get("shipping"),
        total: params.get("total")
      };

      // 🔍 除錯用：你可以打開 F12 console 看參數有沒有抓到
      console.log("🚀 傳送的內容：", templateParams);

      emailjs.send("service_ov4783q", "template_ceydmzp", templateParams)
        .then(function (response) {
          document.getElementById("status").innerHTML =
            "✅ 訂單已成功通知總部！";
        })
        .catch(function (error) {
          document.getElementById("status").innerHTML =
            "❌ 發送失敗，請稍後重試";
          console.error("EmailJS 發送錯誤：", error);
        });
    };
  </script>
  <style>
    body {
      font-family: "Arial", sans-serif;
      background-color: #111;
      color: #fff;
      text-align: center;
      padding-top: 100px;
    }
    .error {
      color: #ff4e4e;
    }
  </style>
</head>
<body>
  <h1>訂單正在通知總部中...</h1>
  <p id="status" class="error">請稍候，我們正在處理您的訂單資料。</p>
</body>
</html>
