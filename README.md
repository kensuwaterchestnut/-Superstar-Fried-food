<!DOCTYPE html>
<html lang="zh-Hant">
  <head>
    <meta charset="UTF-8" />
    <title>訂單發送中...</title>
    <script src="https://cdn.jsdelivr.net/npm/emailjs-com@3/dist/email.min.js"></script>
    <script>
      (function () {
        emailjs.init("nMamM2Ecz2ztnkCOV"); // ⬅️ 替換成你的 EmailJS 公鑰
      })();

      window.onload = function () {
        const params = new URLSearchParams(window.location.search);

        const templateParams = {
  order_time: "2025-08-07 19:45",
  order_id: "TEST-00001",
  email: "kensu.water.chestnut@gmail.com",  // ✅ 合法 Email
  store_name: "台中一中店",
  order_details: "菱角酥x2、芋泥酥x1",
  delivery_method: "宅配",
  pickup_note: "晚上收貨",
  shipping: "150",
  total: "750"
};

        console.log("送出的資料內容：", templateParams); // ← 除錯用，可移除

        emailjs
          .send("service_ov4783q", "template_ceydmzp", templateParams)
          .then(function (response) {
            document.getElementById("status").innerHTML =
              "✅ 訂單已成功通知總部！";

            // ✅ 成功後幾秒自動跳轉（可選）
            // setTimeout(function () {
            //   window.location.href = "https://你的官網或感謝頁";
            // }, 3000);
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
