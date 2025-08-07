<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="UTF-8">
  <title>訂單確認頁面</title>
  <script src="https://cdn.jsdelivr.net/npm/emailjs-com@3/dist/email.min.js"></script>
  <style>
    body { font-family: sans-serif; padding: 20px; background: #f2f2f2; }
    h2 { color: #333; }
    .box { background: white; padding: 20px; border-radius: 8px; max-width: 600px; margin: auto; }
    .row { margin-bottom: 12px; }
    label { font-weight: bold; }
  </style>
</head>
<body>
  <div class="box">
    <h2>訂單資訊確認</h2>
    <div class="row"><label>訂單時間：</label><span id="order_time"></span></div>
    <div class="row"><label>訂單編號：</label><span id="order_id"></span></div>
    <div class="row"><label>Email：</label><span id="email"></span></div>
    <div class="row"><label>分店名稱：</label><span id="branch"></span></div>
    <div class="row"><label>寄貨方式：</label><span id="delivery"></span></div>
    <div class="row"><label>取貨備註：</label><span id="pickup_note"></span></div>
    <div class="row"><label>商品明細：</label><pre id="items_json"></pre></div>
    <div class="row"><label>運費：</label><span id="shipping"></span></div>
    <div class="row"><label>總金額：</label><span id="total"></span></div>

    <button onclick="sendEmail()">✅ 確認送出通知</button>
  </div>

  <script>
    // 初始化 EmailJS
    emailjs.init("nMamM2Ecz2ztnkCOV");

    // 從網址取得參數
    const query = new URLSearchParams(window.location.search);
    const order_time = query.get('order_time') || '';
    const order_id = query.get('order_id') || '';
    const email = query.get('email') || '';
    const branch = query.get('branch') || '';
    const delivery = query.get('delivery') || '';
    const pickup_note = query.get('pickup_note') || '';
    const items_json = query.get('items_json') || '';
    const shipping = query.get('shipping') || '';
    const total = query.get('total') || '';

    // 顯示在畫面上
    document.getElementById('order_time').textContent = order_time;
    document.getElementById('order_id').textContent = order_id;
    document.getElementById('email').textContent = email;
    document.getElementById('branch').textContent = branch;
    document.getElementById('delivery').textContent = delivery;
    document.getElementById('pickup_note').textContent = pickup_note;
    document.getElementById('items_json').textContent = items_json;
    document.getElementById('shipping').textContent = shipping;
    document.getElementById('total').textContent = total;

    // 送出 EmailJS
    function sendEmail() {
      emailjs.send("service_ov4783q", "template_ceydmzp", {
        order_time: order_time,
        order_id: order_id,
        email: email,
        branch: branch,
        delivery: delivery,
        pickup_note: pickup_note,
        items_json: items_json,
        shipping: shipping,
        total: total
      }).then(function(response) {
        alert("✅ 通知已送出成功！");
      }, function(error) {
        alert("❌ 發送失敗：" + JSON.stringify(error));
      });
    }
  </script>
</body>
</html>
