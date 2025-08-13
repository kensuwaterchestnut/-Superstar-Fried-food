<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>叫貨明細</title>
  <style>
    :root {
      --border:#e5e7eb;
      --muted:#6b7280;
      --text:#111827;
    }
    body{
      margin:0;
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Noto Sans TC", "Helvetica Neue", Arial, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol", sans-serif;
      color:var(--text);
      background:#f8fafc;
    }
    .wrap{
      max-width: 840px;
      margin: 48px auto;
      padding: 0 16px;
    }
    .card{
      background:#fff;
      border:1px solid var(--border);
      border-radius:12px;
      box-shadow: 0 6px 16px rgba(0,0,0,.06);
      padding: 28px 28px 20px;
    }
    h1{
      margin: 0 0 20px;
      text-align:center;
      letter-spacing:.12em;
      font-weight:800;
      font-size:28px;
    }
    .hint{
      color:var(--muted);
      margin: 0 0 14px;
    }
    .table{
      width:100%;
      border-collapse: collapse;
      table-layout: fixed;
      border:1px solid var(--border);
      border-radius:8px;
      overflow:hidden;
      background:#fff;
    }
    .table th,.table td{
      border-bottom:1px solid var(--border);
      padding:14px 16px;
      vertical-align: top;
      word-break: break-word;
    }
    .table th{
      width: 28%;
      background:#f9fafb;
      text-align:left;
      color:#111827;
      font-weight:700;
    }
    .muted{ color: var(--muted); }
    .prewrap{ white-space: pre-wrap; } /* 商品明細換行 */
    .footline{
      margin-top:14px;
      border-left: 4px solid #3b82f6;
      padding-left:10px;
      color:var(--muted);
    }
    .ok{ color:#059669; }
    .bad{ color:#b91c1c; }
  </style>
</head>
<body>
  <div class="wrap">
    <div class="card">
      <h1>叫貨明細</h1>

      <p class="hint">以下為本次訂單資訊：</p>

      <table class="table">
        <tbody>
          <tr><th>訂單時間</th><td id="order_time" class="muted">（空）</td></tr>
          <tr><th>訂單編號</th><td id="order_id"   class="muted">（空）</td></tr>
          <tr><th>信箱</th><td id="email"          class="muted">（空）</td></tr>
          <tr><th>分店名稱</th><td id="store_name"  class="muted">（空）</td></tr>
          <tr><th>寄貨方式</th><td id="delivery_method" class="muted">（空）</td></tr>
          <tr><th>取貨備註</th><td id="pickup_note" class="muted">（空）</td></tr>
          <tr><th>商品明細</th><td id="orders"      class="muted prewrap">（空）</td></tr>
          <tr><th>實際運費</th><td id="shipping_fee" class="muted">（空）</td></tr>
          <tr><th>總計金額</th><td id="total_amount" class="muted">（空）</td></tr>
        </tbody>
      </table>

      <p id="resultLine" class="footline">正在將通知送出總部…</p>
    </div>
  </div>

  <script>
    // ① 這裡改成 Make Webhook 的正確 URL（在 Webhooks 模組按 Copy address）
    const MAKE_WEBHOOK_URL = "https://hook.eu2.make.com/XXXXXXXXXXXXXXX";

    // ② 解析 Glide 產的網址欄位
    function getParams() {
      const p = new URLSearchParams(location.search);
      // 你的欄位鍵：time, order_id, email, store_name, delivery_method, pickup_note, order_details, shipping, total
      return {
        order_time:      p.get('time')            || '',
        order_id:        p.get('order_id')        || '',
        email:           p.get('email')           || '',
        store_name:      p.get('store_name')      || '',
        delivery_method: p.get('delivery_method') || '',
        pickup_note:     p.get('pickup_note')     || '',
        orders:          p.get('order_details')   || '',
        shipping_fee:    p.get('shipping')        || '',
        total_amount:    p.get('total')           || ''
      };
    }

    // ③ 將值塞到畫面
    function fillView(m){
      const set = (id,val)=> {
        const el = document.getElementById(id);
        el.textContent = val || '（空）';
        el.classList.toggle('muted', !val);
      };
      set('order_time',   m.order_time);
      set('order_id',     m.order_id);
      set('email',        m.email);
      set('store_name',   m.store_name);
      set('delivery_method', m.delivery_method);
      set('pickup_note',  m.pickup_note);
      set('orders',       m.orders);
      set('shipping_fee', m.shipping_fee);
      set('total_amount', m.total_amount);
    }

    // ④ 送到 Make（不解析 JSON，避免 Unexpected token）
    async function notifyMake(payload){
      try{
        const res = await fetch(MAKE_WEBHOOK_URL, {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({ __emailjs__: payload }) // 給 Make → HTTP 模組使用
        });
        // 不做 res.json()，有些 Webhook 回空字串
        if (!res.ok) throw new Error('HTTP ' + res.status);
        return true;
      }catch(err){
        console.error('Send to Make failed:', err);
        return false;
      }
    }

    (async ()=>{
      const data = getParams();
      fillView(data);

      const ok = await notifyMake(data);
      const line = document.getElementById('resultLine');
      if (ok){
        line.innerHTML = '✅ <span class="ok">成功</span>寄通知給總部，明細表請自行留存。';
      }else{
        line.innerHTML = '❌ <span class="bad">失敗</span>寄通知給總部，請稍後重試或聯絡管理員。';
      }
    })();
  </script>
</body>
</html>
