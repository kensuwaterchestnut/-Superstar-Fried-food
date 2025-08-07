<!DOCTYPE html>
<html lang="zh-Hant">
  <head>
    <meta charset="UTF-8" />
    <title>訂單發送中...</title>
    <script src="https://cdn.jsdelivr.net/npm/emailjs-com@3/dist/email.min.js"></script>
    <script>
      (function () {
        emailjs.init("nMamM2Ecz2ztnkCOV"); // ⬅️ 你的 EmailJS 公鑰
      })();

      window.onload = function () {
        const params = new URLSearchParams(window.location.search);

        const templateParams = {
          order_time: params.get("order_time"),
          order_id: params.get("order_id"),
          email: params.get("email"),
          store_name: params.get("store_name"),
          product_list: params.get("product_list"),
          shipping_method: params.get("shipping_method"),
          pickup_note: params.get("pickup_note"),
          shipping_fee: params.get("shipping_fee"),
          total: params.get("total"),
        };

        emailjs
          .send("service_ov4783q", "template_ceydmzp", templateParams)
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
