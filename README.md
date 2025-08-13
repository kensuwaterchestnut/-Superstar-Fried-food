<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>叫貨明細</title>
  <style>
    :root{
      --card-w: 820px;
      --radius: 12px;
      --shadow: 0 8px 24px rgba(0,0,0,.08);
      --line: #e8e8e8;
      --muted:#6b7280;
      --ok:#10b981;
      --error:#ef4444;
    }
    html,body{margin:0}
    body{
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Noto Sans TC", "PingFang TC", "Microsoft JhengHei", sans-serif;
      color:#111827; background:#f6f7fb;
    }
    .wrap{
      min-height:100dvh; display:flex; align-items:flex-start; justify-content:center;
      padding:56px 16px;
    }
    .card{
      width: min(100%, var(--card-w));
      background:#fff; border-radius:var(--radius); box-shadow:var(--shadow);
      padding:28px; border:1px solid #f0f0f0;
    }
    h1{
      margin:0 0 18px; text-align:center; letter-spacing:.1em;
      font-size:28px; font-weight:800;
    }
    .hint{font-size:14px; color:var(--muted); margin-bottom:12px}
    .table{
      width:100%; border-collapse:collapse; border-radius:10px; overflow:hidden;
      border:1px solid var(--line); background:#fff;
    }
    .table th,.table td{
      border-bottom:1px solid var(--line); padding:14px 16px; text-align:left; vertical-align:top;
    }
    .table th{width:140px; color:#374151; background:#fafafa; font-weight:700}
    .table tr:last-child th,.table tr:last-child td{border-bottom:none}
    .mono{font-family: ui-monospace, SFMono-Regular, Menlo, Consolas, monospace}
    .muted{color:var(--muted)}
    .prewrap{white-space:pre-wrap; word-break:break-word}
    .badge{
      display:inline-flex; align-items:center; gap:8px; margin-top:14px; font-size:14px;
      color:var(--muted);
    }
    .badge.ok{color:var(--ok)} .badge.err{color:var(--error)}
    .dot{width:10px; height:10px; border-radius:50%}
    .ok .dot{background:var(--ok)} .err .dot{background:var(--error)}
    .loading .dot{
      background:linear-gradient(90deg,#9ca3af,#d1d5db,#9ca3af);
      background-size:200% 100%; animation:shimmer 1.2s infinite linear;
    }
    @keyframes shimmer{0%{background-position:0 0}100%{background-position:200% 0}}
  </style>
</head>
<body>
  <div class="wrap">
    <article class="card">
      <h1>叫貨明細</h1>
      <p class="hint">以下為本次訂單資訊：</p>

      <table class="table" id="table">
        <tbody>
          <tr><th>訂單時間</th><td id="order_time" class="mono muted">（空）</td></tr>
          <tr><th>訂單編號</th><td id="order_id" class="mono muted">（空）</td></tr>
          <tr><th>信箱</th><td id="email" class="mono muted">（空）</td></tr>
          <tr><th>分店名稱</th><td id="store_name" class="mono muted">（空）</td></tr>
          <tr><th>寄貨方式</th><td id="delivery_method" class="mono muted">（空）</td></tr>
          <tr><th>取貨備註</th><td id="pickup_note" class="mono muted">（空）</td></tr>
          <tr><th>商品明細</th><td id="orders" class="prewrap mono muted">（空）</td></tr>
          <tr><th>實際運費</th><td id="shipping_fee" class="mono muted">（空）</td></tr>
          <tr><th>總計金額</th><td id="total_amount" class="mono muted">（空）</td></tr>
        </tbody>
      </table>

      <p id="status" class="badge loading">
        <span class="dot" aria-hidden="true"></span>
        <span>正在寄送通知給總部…</span>
      </p>
    </article>
  </div>

  <script>
    // ======= 1) 這裡換成你的 Make Webhook =======
    const MAKE_WEBHOOK = "MAKE_WEBHOOK_URL_HERE";

    // ======= 2) 解析 Glide 產生的網址參數（支援中文與雙重編碼）=======
    function safeDecode(v){ try{ return decodeURIComponent(v) }catch{ return v } }
    function getParams(){
      const raw = window.location.search.startsWith("?")
        ? window.location.search.slice(1) : window.location.search;
      const out = {};
      // Glide 有時候是 key=value，沒問題；你以前也用過 value=key 的版本，這裡都相容
      raw.split("&").forEach(pair=>{
        if(!pair) return;
        const [k0,v0] = pair.split("=");
        const k = safeDecode(k0||"").trim();
        const v = safeDecode(safeDecode(v0||"")).trim(); // double decode
        if(!k) return;
        // 允許兩種命名：舊(time/order_details/total/…)與新(order_time/orders/total_amount…)
        const map = {
          time:"order_time", order_time:"order_time",
          order_id:"order_id",
          email:"email",
          store_name:"store_name",
          delivery_method:"delivery_method",
          pickup_note:"pickup_note",
          order_details:"orders", orders:"orders",
          shipping:"shipping_fee", shipping_fee:"shipping_fee",
          total:"total_amount", total_amount:"total_amount"
        };
        const key = map[k] || k;
        out[key] = v;
      });
      return out;
    }

    // ======= 3) 商品明細換行標準化（支援逗號、分號、換行）=======
    function normalizeOrders(s){
      if(!s) return "";
      // 把常見分隔符轉換為換行
      const unified = s
        .replace(/\\n/g, "\n")
        .replace(/<br\s*\/?>/gi, "\n")
        .replace(/[,，；;]+/g, "\n");
      // 合併多重空白行
      return unified.split("\n").map(x=>x.trim()).filter(Boolean).join("\n");
    }

    // ======= 4) 將資料填入畫面 =======
    function fill(id, val){
      const el = document.getElementById(id);
      if(!el) return;
      if(val && val.trim()){
        el.textContent = val;
        el.classList.remove("muted");
      }else{
        el.textContent = "（空）";
        el.classList.add("muted");
      }
    }

    // ======= 5) 送到 Make（用 __emailjs__ 負載，方便在 Make 裡選路徑）=======
    async function sendToMake(payload){
      try{
        const res = await fetch(MAKE_WEBHOOK, {
          method:"POST",
          headers:{ "Content-Type":"application/json" },
          body: JSON.stringify({ __emailjs__: payload })
        });
        return res.ok;
      }catch(e){
        console.error("Send error:", e);
        return false;
      }
    }

    (async function init(){
      const p = getParams();
      p.orders = normalizeOrders(p.orders || "");

      // 畫面填值
      fill("order_time", p.order_time||"");
      fill("order_id", p.order_id||"");
      fill("email", p.email||"");
      fill("store_name", p.store_name||"");
      fill("delivery_method", p.delivery_method||"");
      fill("pickup_note", p.pickup_note||"");
      fill("orders", p.orders||"");
      fill("shipping_fee", p.shipping_fee||"");
      fill("total_amount", p.total_amount||"");

      // 寄送狀態 DOM
      const st = document.getElementById("status");
      const setStatus = (ok)=>{
        st.classList.remove("loading");
        st.classList.toggle("ok", ok);
        st.classList.toggle("err", !ok);
        st.innerHTML = ok
          ? <span class="dot" aria-hidden="true"></span><span>✅ 成功寄通知給總部，明細表請自行留存。</span>
          : `<span class="dot" aria-hidden="true"></span><span>❌ 寄送失敗，請稍後重試或聯絡管理者。</span>`;
      };

      // 只有在關鍵欄位存在時才送（避免空包）
      const essential = ["order_time","order_id","email","store_name","orders"];
      const hasEssential = essential.every(k => (p[k]||"").trim().length>0);

      if(!MAKE_WEBHOOK || /^MAKE_WEBHOOK_URL_HERE/.test(MAKE_WEBHOOK)){
        console.warn("請設定你的 Make Webhook URL");
        setStatus(false);
        return;
      }

      if(!hasEssential){
        console.warn("缺少必要欄位，跳過送出：", p);
        setStatus(false);
        return;
      }

      const ok = await sendToMake(p);
      setStatus(ok);
    })();
  </script>
</body>
</html>
