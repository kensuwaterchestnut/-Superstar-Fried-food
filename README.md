<!DOCTYPE html>
<html lang="zh-TW">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>訂單資訊確認</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background-color: #f5f5f5;
      padding: 40px;
      color: #333;
    }
    .container {
      max-width: 600px;
      margin: auto;
      background: #fff;
      padding: 30px;
      border-radius: 12px;
      box-shadow: 0 0 15px rgba(0, 0, 0, 0.1);
    }
    h2 {
      text-align: center;
      color: #222;
    }
    .info {
      margin: 10px 0;
      font-size: 16px;
    }
    .label {
      font-weight: bold;
      display: inline-block;
      width: 120px;
    }
    #send-button {
      margin-top: 30px;
      width: 100%;
      padding: 12px;
      font-size: 16px;
      background-color: #000;
      color: #fff;
      border: none;
      border-radius: 8px;
      cursor: pointer;
    }
    #send-button:hover {
      background-color: #444;
    }
  </style>
</head>
<body>
  <div class="container">
    <h2>訂單資訊確認</h2>
    <div class="info"><span class="label">訂單時間：</span><span id="order_time"></span></div>
    <div class="info"><span class="label">訂單編號：</span><span id="order_id"></span></div>
    <div class="info"><span class="label">Email：</span><span id="email"></span></div>
    <div class="info"><span class="label">分店名稱：</span><span id="store_name"></span></div>
    <div class="info"><span class="label">寄貨方式：</span><span id="delivery_method"></span></div>
    <div class="info"><span class="label">取貨備註：</span><span id="pickup_note"></span></div>
    <div class="info"><span class="label">商品明細：</span><span id="order_details"></span></div>
    <div class="info"><span class="label">運費：</span><span id="shipping"></span></div>
    <div class="info"><span class="label">總金額：</span><span id="total"></span></div>
    
    <button id="send-button">✅ 確認送出通知</button>
  </div>

  <script>
    const params = new URLSearchParams(window.location.search);
    const decode = (key) => decodeURIComponent(params.get(key) || '').replace(/\+/g, ' ');

    const fields = [
      "order_time", "order_id", "email", "store_name",
      "delivery_method", "pickup_note", "order_details",
      "shipping", "total"
    ];

    fields.forEach(field => {
      document.getElementById(field).innerText = decode(field);
    });

    document.getElementById("send-button").addEventListener("click", () => {
      alert("✅ 訂單資料已確認，如需通知總部請啟用 EmailJS 功能");
    });
  </script>
</body>
</html>
