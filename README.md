<!DOCTYPE html>
<html lang="zh-TW">
<head>
  <meta charset="UTF-8">
  <title>訂單資料確認</title>
  <script src="https://cdn.jsdelivr.net/npm/emailjs-com@3/dist/email.min.js"></script>
  <style>
    body {
      font-family: sans-serif;
      max-width: 600px;
      margin: 40px auto;
      padding: 20px;
      border: 1px solid #ccc;
      border-radius: 10px;
    }
    h2 {
      color: #333;
    }
    .field {
      margin: 10px 0;
    }
    .label {
      font-weight: bold;
    }
    #sendButton {
      margin-top: 20px;
      padding: 10px 20px;
      font-size: 16px;
      background-color: #007BFF;
      color: white;
      border: none;
      border-radius: 5px;
      cursor: pointer;
    }
    #sendButton:disabled {
      background-color: #ccc;
    }
  </style>
</head>
<body>
  <h2>訂單資訊確認</h2>

  <div class="field"><span class="label">訂單時間：</span><span id="order_time"></span></div>
  <div class="field"><span class="label">訂單編號：</span><span id="order_id"></span></div>
  <div class="field"><span class="label">Email：</span><span id="email"></span></div>
  <div class="field"><span class="label">分店名稱：</span><span id="branch_name"></span></div>
  <div class="field"><span class="label">寄貨方式：</span><span id="delivery_type"></span></div>
  <div class="field"><span class="label">取貨備註：</span><span id="pickup_note"></span></div>
  <div class="field"><span class="label">商品明細：</span><span id="product_detail"></span></div>
  <div class="field"><span class="label">運費：</span><span id="shipping_fee"></span></div>
  <div class="field"><span class="label">總金額：</span><span id="total"></span></div>

  <button id="sendButton">✅ 確認送出通知</button>

  <script>
    // 載入參數
    const urlParams = new URLSearchParams(window.location.search);

    const fields = {
      order_time: urlParams.get("訂單時間"),
      order_id: urlParams.get("訂單編號"),
      email: urlParams.get("Email"),
      branch_name: urlParams.get("分店名稱"),
      delivery_type: urlParams.get("寄貨方式"),
      pickup_note: urlParams.get("取貨備註"),
      product_detail: urlParams.get("商品明細"),
      shipping_fee: urlParams.get("運費"),
      total: urlParams.get("總金額")
    };

    // 填入畫面
    for (const key in fields) {
      const el = document.getElementById(key);
      if (el) {
        el.innerText = fields[key] || '';
      }
    }

    // 初始化 EmailJS
    emailjs.init("nMamM2Ecz2ztnkCOV"); // 🔑 你的 public key

    // 發送按鈕事件
    document.getElementById("sendButton").addEventListener("click", () => {
      document.getElementById("sendButton").disabled = true;

      emailjs.send("kensu_email_service", "kensu_order_template", {
        order_time: fields.order_time,
        order_id: fields.order_id,
        email: fields.email,
        branch_name: fields.branch_name,
        delivery_type: fields.delivery_type,
        pickup_note: fields.pickup_note,
        product_detail: fields.product_detail,
        shipping_fee: fields.shipping_fee,
        total: fields.total
      }).then(response => {
        alert("✅ 通知發送成功！");
      }, error => {
        alert("❌ 發送失敗：" + JSON.stringify(error));
        document.getElementById("sendButton").disabled = false;
      });
    });
  </script>
</body>
</html>
