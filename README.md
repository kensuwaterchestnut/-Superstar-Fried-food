<!DOCTYPE html>
<html lang="zh-TW">
<head>
  <meta charset="UTF-8" />
  <title>啃酥叫貨通知發送</title>
  <script type="text/javascript" src="https://cdn.jsdelivr.net/npm/emailjs-com@3/dist/email.min.js"></script>
</head>
<body>
  <h2>發送叫貨通知</h2>
  <form id="form">
    <label>訂單時間：<input name="order_time" required></label><br>
    <label>訂單編號：<input name="order_id" required></label><br>
    <label>Email：<input name="email" required></label><br>
    <label>分店名稱：<input name="store_name" required></label><br>
    <label>寄貨方式：<input name="delivery_method" required></label><br>
    <label>取貨備註：<input name="pickup_note"></label><br>
    <label>商品明細（JSON）：<textarea name="orders" required></textarea></label><br>
    <label>運費：<input name="cost_shipping" required></label><br>
    <label>總金額：<input name="cost_total" required></label><br>

    <button type="submit">送出通知</button>
  </form>

  <script>
    (function () {
      emailjs.init("YOUR_PUBLIC_KEY"); // ← 換成你的公開金鑰
    })();

    document.getElementById('form').addEventListener('submit', function (e) {
      e.preventDefault();

      const formData = new FormData(this);
      const data = Object.fromEntries(formData.entries());

      // 將 cost 與 orders 轉為嵌套物件格式
      data.cost = {
        shipping: data.cost_shipping,
        total: data.cost_total
      };

      try {
        data.orders = JSON.parse(data.orders); // ← 使用者需輸入 JSON 格式，如：[{"name":"菱角酥","units":3,"price":90}]
      } catch (e) {
        alert("商品明細格式錯誤，請使用 JSON 格式！");
        return;
      }

      // 移除 cost_shipping / cost_total 多餘欄位
      delete data.cost_shipping;
      delete data.cost_total;

      emailjs.send("default_service", "template_ceydmzp", data)
        .then(function () {
          alert("叫貨通知信已發送！");
        }, function (err) {
          alert("發送失敗：" + JSON.stringify(err));
        });
    });
  </script>
</body>
</html>
