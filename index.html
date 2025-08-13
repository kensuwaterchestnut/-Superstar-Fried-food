<!DOCTYPE html>
<html lang="zh-Hant">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>叫貨明細</title>
<style>
  :root{--card-w:880px;--border:#e5e7eb;--muted:#9ca3af;--text:#111827;--bg:#fff;--shadow:0 8px 24px rgba(0,0,0,.06);--ok:#059669;--bad:#dc2626;}
  *{box-sizing:border-box} body{margin:0;background:#f8fafc;color:var(--text);font-family:-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,Helvetica,Arial}
  .wrap{max-width:var(--card-w);margin:56px auto;padding:0 16px}
  .card{background:var(--bg);border:1px solid var(--border);border-radius:14px;box-shadow:var(--shadow);overflow:hidden}
  .card-header{padding:28px 24px 8px;border-bottom:1px solid var(--border);text-align:center}
  .title{margin:0;font-size:34px;letter-spacing:.08em;font-weight:900}
  .card-body{padding:22px}
  table{width:100%;border-collapse:separate;border-spacing:0;border:1px solid var(--border);border-radius:10px;overflow:hidden}
  th,td{padding:14px 16px;border-bottom:1px solid var(--border);border-right:1px solid var(--border);vertical-align:top;background:#fff}
  th{width:140px;background:#f9fafb;text-align:left;font-weight:700}
  tr:last-child th, tr:last-child td{border-bottom:none} tr th:last-child, tr td:last-child{border-right:none}
  .muted{color:var(--muted)} .nowrap{white-space:nowrap} .prewrap{white-space:pre-wrap;word-break:break-word}
  .note{display:flex;align-items:center;gap:8px;margin-top:16px;padding-left:6px;border-left:4px solid var(--border);font-size:15px}
  .ok{color:var(--ok)} .bad{color:var(--bad)}
  .spinner{width:14px;height:14px;border:2px solid #cbd5e1;border-top-color:#64748b;border-radius:50%;display:inline-block;animation:spin .8s linear infinite}
  @keyframes spin{to{transform:rotate(1turn)}}
  /* Debug 區域 */
  #debug{display:none;margin-top:16px;padding:12px;border:1px dashed #cbd5e1;background:#fff;border-radius:8px;font-family:ui-monospace,SFMono-Regular,Menlo,Consolas,monospace}
  #debug pre{white-space:pre-wrap;word-break:break-word;margin:6px 0 0}
</style>
</head>
<body>
  <div class="wrap">
    <div class="card">
      <div class="card-header"><h1 class="title">叫貨明細</h1></div>
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

        <!-- Debug 面板（加上 &debug=1 會顯示） -->
        <div id="debug">
          <strong>Debug</strong>
          <div>Raw QueryString:</div>
          <pre id="dbg-raw"></pre>
          <div>Parsed Params:</div>
          <pre id="dbg-parsed"></pre>
          <div>Webhook Result:</div>
          <pre id="dbg-webhook"></pre>
        </div>
      </div>
    </div>
  </div>

<script>
(function(){
  const WEBHOOK = 'https://hook.eu2.make.com/mxd447qyeae62is1m1vsutndp3bqhudf';
  const KEYS = ['order_time','order_id','email','store_name','delivery_method','pickup_note','order_details','shipping','total'];

  const usp = new URLSearchParams(location.search);
  const DEBUG = usp.get('debug') === '1';
  const $ = id => document.getElementById(id);

  function readParams(){
    const out = Object.fromEntries(KEYS.map(k => [k, '']));
    // 1) 先吃正常 k=v
    let hit = false;
    for (const k of KEYS){
      if (usp.has(k)){ out[k] = decodeURIComponent(usp.get(k) || ''); hit = true; }
    }
    if (hit) return out;
    // 2) 吃顛倒 v 是 key 的格式
    for (const [name, value] of usp.entries()){
      const maybeKey = decodeURIComponent(value || '');
      if (KEYS.includes(maybeKey)){ out[maybeKey] = decodeURIComponent(name || ''); }
    }
    return out;
  }

  function fillTable(data){
    for (const k of KEYS){
      const el = $(k);
      const v = (data[k] || '').trim();
      el.textContent = v || '(空)';
      el.classList.toggle('muted', !v);
    }
  }

  async function sendToMake(data){
    try{
      const res = await fetch(WEBHOOK, {
        method:'POST',
        headers:{'Content-Type':'application/json'},
        body: JSON.stringify(data)
      });
      const txt = await res.text().catch(()=> '');
      if (!res.ok) throw new Error('HTTP '+res.status+' '+txt);
      $('status').innerHTML = '✅ <span class="ok">成功</span>寄通知給總部，明細表請自行留存。';
      if (DEBUG) $('dbg-webhook').textContent = 'OK ' + txt;
    }catch(err){
      $('status').innerHTML = '❌ <span class="bad">失敗</span>寄通知給總部，請稍後重試或聯絡管理員。';
      if (DEBUG) $('dbg-webhook').textContent = (err && err.message) ? err.message : String(err);
      console.error(err);
    }
  }

  const data = readParams();
  fillTable(data);
  if (DEBUG){
    $('debug').style.display = 'block';
    $('dbg-raw').textContent = location.search || '(空)';
    $('dbg-parsed').textContent = JSON.stringify(data, null, 2);
  }
  sendToMake(data);
})();
</script>
</body>
</html>
