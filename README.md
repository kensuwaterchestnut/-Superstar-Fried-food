<!DOCTYPE html>
<html lang="zh-TW">
<head>
  <meta charset="UTF-8" />
  <title>啃酥叫貨通知</title>
  <script src="https://cdn.jsdelivr.net/npm/emailjs-com@3/dist/email.min.js"></script>
  <style>
    body {
      font-family: system-ui, sans-serif;
      background-color: #f5f5f5;
      padding: 30px;
    }
    label {
      display: block;
      margin-top: 10px;
      font-weight: bold;
    }
    input, textarea {
      width: 100%;
      padding: 6px;
      font-size: 14px;
      margin-top: 4px;
    }
    button {
      margin-top: 20px;
      padding: 10px 20px;
      background-color: #458500;
      color: white;
      border: none;
      cursor: pointer;
    }
  </style>
</head>
<body>
  <h2>啃酥叫貨通知</h2>
  <form id="orderForm">
    <label for="order_time">訂單時間：</label>
    <input type="text" name="order_time" id="order_time" required>

    <label for="order_id">訂單編號：</label>
    <input type="text" name="order_id" id="order_id" required>

    <label for="email">Email：</label>
    <input type="email" name="email" id="email" required>

    <label for="store_name">分店名稱：</label>
    <input type="text" name="store_name" id="store_name" required>

    <label for="delivery_method">寄貨方式：</label>
    <input type="text" name="delivery_method" id="delivery_method">

    <label for="pickup_note">取貨備註：</label>
    <input type="text" name="pickup_note" id="pickup_note">

    <label for="orders">商品明細（JSON）：</label>
    <textarea name="orders" id="orders" rows="6" required></textarea>

    <label for="cost_shipping">運費：</label>
    <input type="text" name="cost_shipping" id="cost_shipping">

    <label for="cost_total">總金額：</label>
    <input type="text" name="cost_total" id="cost_total">

    <button type="submit">送出通知</button>
  </form>

  <script>
    // 初始化 EmailJS
    emailjs.init("nMamM2Ecz2ztnkCOV"); // ✅ 這裡是你提供的 API 金鑰

    document.getElementById("orderForm").addEventListener("submit", function (e) {
      e.preventDefault();

      // 送出郵件
      emailjs.sendForm("service_ov4783q", "template_ceydmzp", this)
        .then(function () {
          alert("✅ 通知信已成功發送！");
        }, function (error) {
          alert("❌ 發送失敗：" + JSON.stringify(error));
        });
    });
  </script>
</body>
</html>
