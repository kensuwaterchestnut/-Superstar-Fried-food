<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>叫貨明細</title>
  <style>
    :root{
      --card-w: 880px;
      --border:#e5e7eb;
      --muted:#9ca3af;
      --text:#111827;
      --bg:#ffffff;
      --shadow: 0 8px 24px rgba(0,0,0,.06);
      --primary:#111827;
      --ok:#059669;
      --bad:#dc2626;
    }
    *{box-sizing:border-box}
    body{
      margin:0;
      background:#f8fafc;
      color:var(--text);
      font-family:-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,Helvetica,Arial,"Noto Sans","Apple Color Emoji","Segoe UI Emoji";
      line-height:1.6;
    }
    .wrap{
      max-width:var(--card-w);
      margin:56px auto;
      padding:0 16px;
    }
    .card{
      background:var(--bg);
      border:1px solid var(--border);
      border-radius:14px;
      box-shadow:var(--shadow);
      overflow:hidden;
    }
    .card-header{
      text-align:center;
      padding:28px 24px 8px;
      border-bottom:1px solid var(--border);
    }
    .title{
      margin:0;
      font-size:34px;
      letter-spacing:.08em;
      font-weight:900;
      color:var(--primary);
    }
    .card-body{ padding:22px; }

    table{
      width:100%;
      border-collapse:separate;
      border-spacing:0;
      border:1px solid var(--border);
      border-radius:10px;
      overflow:hidden;
    }
    th,td{
      padding:14px 16px;
      border-bottom:1px solid var(--border);
      border-right:1px solid var(--border);
      vertical-align:top;
      background:#fff;
    }
    th{
      width:140px;
      background:#f9fafb;
      text-align:left;
      font-weight:700;
    }
    tr:last-child th, tr:last-child td{ border-bottom:none; }
    tr th:last-child, tr td:last-child{ border-right:none; }

    .muted{ color:var(--muted); }
    .nowrap{ white-space:nowrap; }
    /* 讓商品明細可自動換行、保留斷行 */
    .prewrap{ white-space:pre-wrap; word-break:break-word; }

    .note{
      display:flex;
      align-items:center;
      gap:8px;
      margin-top:16px;
      padding-left:6px;
      border-left:4px solid var(--border);
      color:#374151;
      font-size:15px;
    }
    .ok{ color:var(--ok); }
    .bad{ color:var(--bad); }
    .spinner{
      width:14px; height:14px; border:2px solid #cbd5e1; border-top-color:#64748b;
      border-radius:50%; display:inline-block; animation:spin .8s linear infinite;
    }
    @keyframes spin{to{transform:rotate(1turn)}}
  </style>
</head>
<body>
  <div class="wrap">
    <div class="card">
      <div class="card-header">
        <h1 class="title">叫貨明細</h1>
      </div>

      <div class="card-body">
        <p class="muted">以下為本次訂單資訊：</p>

        <table>
          <tbody>
            <tr><th class="nowrap">訂單時間</th><td id="order_time"      class="muted">(空)</td></tr>
            <tr><th class="nowrap">訂單編號</th><td id="order_id"        class="muted">(空)</td></tr>
            <tr><th class="nowrap">信箱</th>    <td id="email"           class="muted">(空)</td></tr>
            <tr><th class="nowrap">分店名稱</th><td id="store_name"      class="muted">(空)</td></tr>
            <tr><th class="nowrap">寄貨方式</th><td id="delivery_method" class="muted">(空)</td></tr>
            <tr><th class="nowrap">取貨備註</th><td id="pickup_note"     class="muted">(空)</td></tr>
            <tr><th class="nowrap">商品明細</th><td id="order_details"   class="muted prewrap">(空)</td></tr>
            <tr><th class="nowrap">實際運費</th><td id="shipping"        class="muted">(空)</td></tr>
            <tr><th class="nowrap">總計金額</th><td id="total"           class="muted">(空)</td></tr>
          </tbody>
        </table>

        <div id="status" class="note">
          <span class="spinner" aria-hidden="true"></span>
          <span>正在將這筆訂單通知總部⋯</span>
        </div>
      </div>
    </div>
  </div>

<script>
(function(){
  const WEBHOOK = 'https://hook.eu2.make.com/mxd447qyeae62is1m1vsutndp3bqhudf';

  // 期望收到的英文字段
  const keys = [
    'order_time','order_id','email','store_name',
    'delivery_method','pickup_note','order_details',
    'shipping','total'
  ];

  // 讀取網址參數：同時支援「正常」與「顛倒(k=v 其實是 v=k)」兩種格式
  function readParams(){
    const usp = new URLSearchParams(location.search);
    const out = Object.fromEntries(keys.map(k => [k, '']));

    // 先嘗試標準 key=value
    let matchedNormal = false;
    for (const k of keys){
      if (usp.has(k)){
        out[k] = decodeURIComponent(usp.get(k) || '');
        matchedNormal = true;
      }
    }
    if (matchedNormal) return out;

    // 若沒命中，嘗試「顛倒」：value 其實是英文 key，name 是實際值
    // e.g.  ?2025/8/13%20...=order_time&abc@xx.com=email
    for (const [name, value] of usp.entries()){
      const maybeKey = decodeURIComponent(value || '');
      if (keys.includes(maybeKey)){
        out[maybeKey] = decodeURIComponent(name || '');
      }
    }
    return out;
  }

  // 將值填入表格
  function fillTable(data){
    for (const k of keys){
      const el = document.getElementById(k);
      const v = (data[k] || '').trim();
      el.textContent = v || '(空)';
      el.classList.toggle('muted', !v);
    }
  }

  // 將資料 POST 到 Make
  async function notifyMake(data){
    const status = document.getElementById('status');
    try{
      const res = await fetch(WEBHOOK, {
        method: 'POST',
        headers: {'Content-Type':'application/json'},
        body: JSON.stringify(data)
      });
      if (!res.ok) throw new Error('HTTP '+res.status);
      status.innerHTML = '✅ <span class="ok">成功</span>寄通知給總部，明細表請自行留存。';
    }catch(err){
      status.innerHTML = '❌ <span class="bad">失敗</span>寄通知給總部，請稍後重試或聯絡管理員。';
      console.error('Webhook error:', err);
    }
  }

  const data = readParams();
  fillTable(data);
  notifyMake(data);
})();
</script>
</body>
</html>
