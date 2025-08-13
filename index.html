<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>叫貨明細</title>
  <style>
    :root{--border:#e5e7eb;--muted:#6b7280;--text:#111827;}
    body{margin:0;font-family:-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,"Noto Sans TC","Helvetica Neue",Arial,sans-serif;color:var(--text);background:#f8fafc}
    .wrap{max-width:840px;margin:48px auto;padding:0 16px}
    .card{background:#fff;border:1px solid var(--border);border-radius:12px;box-shadow:0 6px 16px rgba(0,0,0,.06);padding:28px}
    h1{margin:0 0 20px;text-align:center;letter-spacing:.12em;font-weight:800;font-size:28px}
    .hint{color:var(--muted);margin:0 0 14px}
    .table{width:100%;border-collapse:collapse;table-layout:fixed;border:1px solid var(--border);border-radius:8px;overflow:hidden;background:#fff}
    .table th,.table td{border-bottom:1px solid var(--border);padding:14px 16px;vertical-align:top;word-break:break-word}
    .table th{width:28%;background:#f9fafb;text-align:left;color:#111827;font-weight:700}
    .muted{color:var(--muted)}
    .prewrap{white-space:pre-wrap}
    .footline{margin-top:14px;border-left:4px solid #3b82f6;padding-left:10px;color:var(--muted)}
    .ok{color:#059669}.bad{color:#b91c1c}
    .debug{margin-top:18px;padding:12px;border:1px dashed #d1d5db;border-radius:8px;background:#f9fafb;color:#374151}
    .code{font-family:ui-monospace,SFMono-Regular,Menlo,Monaco,Consolas,"Liberation Mono","Courier New",monospace;white-space:pre-wrap;word-break:break-all}
  </style>
</head>
<body>
  <div class="wrap">
    <div class="card">
      <h1>叫貨明細</h1>
      <p class="hint">以下為本次訂單資訊：</p>

      <table class="table">
        <tbody>
          <tr><th>訂單時間</th><td id="order_time"     class="muted">（空）</td></tr>
          <tr><th>訂單編號</th><td id="order_id"       class="muted">（空）</td></tr>
          <tr><th>信箱</th><td     id="email"          class="muted">（空）</td></tr>
          <tr><th>分店名稱</th><td id="store_name"     class="muted">（空）</td></tr>
          <tr><th>寄貨方式</th><td id="delivery_method"class="muted">（空）</td></tr>
          <tr><th>取貨備註</th><td id="pickup_note"    class="muted">（空）</td></tr>
          <tr><th>商品明細</th><td id="orders"         class="muted prewrap">（空）</td></tr>
          <tr><th>實際運費</th><td id="shipping_fee"   class="muted">（空）</td></tr>
          <tr><th>總計金額</th><td id="total_amount"   class="muted">（空）</td></tr>
        </tbody>
      </table>

      <p id="resultLine" class="footline">正在將通知送出總部…</p>

      <div class="debug">
        <div><strong>原始 QueryString：</strong></div>
        <div id="raw" class="code">(尚無)</div>
        <div style="margin-top:8px;"><strong>解析後的參數：</strong></div>
        <div id="kv" class="code">(尚無)</div>
        <div style="margin-top:8px;"><strong>錯誤 / 訊息：</strong></div>
        <div id="errs" class="code">(無)</div>
      </div>
    </div>
  </div>

  <script>
    // 已替換為你的 Make Webhook URL
    const MAKE_WEBHOOK_URL = "https://hook.eu2.make.com/mxd447qyeae62is1m1vsutndp3bqhudf";

    const log = (msg)=> {
      const el = document.getElementById('errs');
      el.textContent = (el.textContent === '(無)' ? '' : el.textContent + '\n') + msg;
    };

    function readParams() {
      const raw = location.search.startsWith('?') ? location.search.slice(1) : location.search;
      document.getElementById('raw').textContent = raw || '(空)';
      const params = {};
      try {
        const usp = new URLSearchParams(raw);
        usp.forEach((v,k)=>{
          const val = decodeURIComponent((v||'').replace(/\+/g,' '));
          const key = decodeURIComponent((k||'').replace(/\+/g,' '));
          params[key] = val;
        });
      } catch(e) {
        log('URLSearchParams 解析失敗：' + e.message);
      }
      document.getElementById('kv').textContent = Object.keys(params).length
        ? JSON.stringify(params, null, 2)
        : '(空)';
      return params;
    }

    function mapToNeeded(p){
      return {
        order_time:      p.time            || '',
        order_id:        p.order_id        || '',
        email:           p.email           || '',
        store_name:      p.store_name      || '',
        delivery_method: p.delivery_method || '',
        pickup_note:     p.pickup_note     || '',
        orders:          p.order_details   || '',
        shipping_fee:    p.shipping        || '',
        total_amount:    p.total           || ''
      };
    }

    function setCell(id, val){
      const el = document.getElementById(id);
      if (!el){ log('找不到節點 #' + id); return; }
      el.textContent = val || '（空）';
      el.classList.toggle('muted', !val);
    }

    async function postToMake(payload){
      if (!MAKE_WEBHOOK_URL || !/^https?:\/\//.test(MAKE_WEBHOOK_URL)){
        log('MAKE_WEBHOOK_URL 尚未設定或格式不正確');
        return false;
      }
      try{
        const res = await fetch(MAKE_WEBHOOK_URL, {
          method:'POST',
          headers:{'Content-Type':'application/json'},
          body: JSON.stringify({ __emailjs__: payload })
        });
        if (!res.ok) log('Make 回應狀態：' + res.status + '（不一定代表失敗）');
        return true;
      }catch(err){
        log('送到 Make 失敗：' + err.message);
        return false;
      }
    }

    (async ()=>{
      try{
        const all = readParams();
        const data = mapToNeeded(all);

        setCell('order_time',   data.order_time);
        setCell('order_id',     data.order_id);
        setCell('email',        data.email);
        setCell('store_name',   data.store_name);
        setCell('delivery_method', data.delivery_method);
        setCell('pickup_note',  data.pickup_note);
        setCell('orders',       data.orders);
        setCell('shipping_fee', data.shipping_fee);
        setCell('total_amount', data.total_amount);

        if (Object.values(data).every(v => !v)) {
          log('未讀到任何既定欄位（time,order_id,email,store_name,delivery_method,pickup_note,order_details,shipping,total）。');
        }

        const ok = await postToMake(data);
        const line = document.getElementById('resultLine');
        line.innerHTML = ok
          ? '✅ <span class="ok">成功</span>寄通知給總部，明細表請自行留存。'
          : '❌ <span class="bad">失敗</span>寄通知給總部，請稍後重試或聯絡管理員。';
      }catch(e){
        log('頁面執行錯誤：' + e.message);
      }
    })();
  </script>
</body>
</html>
