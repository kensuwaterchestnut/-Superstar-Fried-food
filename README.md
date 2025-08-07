<!DOCTYPE html>
<html lang="zh-TW">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>訂單資訊確認</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background-color: #f3f3f3;
      color: #333;
      padding: 20px;
    }
    .container {
      max-width: 600px;
      margin: 40px auto;
      background: white;
      padding: 30px;
      border-radius: 10px;
      box-shadow: 0 0 10px rgba(0,0,0,0.1);
    }
    h2 {
      margin-top: 0;
      color: #444;
    }
    .info {
      margin: 10px 0;
    }
    .label {
      font-weight: bold;
    }
    #send-button {
      margin-top: 20px;
      padding: 10px 20px;
      background-color: black;
      color: white;
      border: none;
      border-radius: 5px;
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
    // 讀取 URL 中的參數
    const params = new URLSearchParams(window.location.search);

    // 映射參數對應到的 DOM id
    const fields = [
      "order_time", "order_id", "email", "store_name",
      "delivery_method", "pickup_note", "order_details", "shipping", "total"
    ];

    fields.forEach(field => {
      const value = decodeURIComponent(params.get(field) || "");
      document.getElementById(field).innerText = value;
    });

    // 後續你可在這裡加上 EmailJS 發送邏輯
    document.getElementById("send-button").addEventListener("click", function () {
      alert("✅ 訂單資訊確認，EmailJS 還沒串接，請確認內容是否正確。");
    });
  </script>
</body>
</html>
