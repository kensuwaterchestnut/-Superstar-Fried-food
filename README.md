<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="utf-8" />
  <title>叫貨明細</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <!-- build: v-wrap-product-unwrap-2025-08-13 -->
  <style>
    body{margin:0;background:#fff;color:#222;
      font-family:ui-sans-serif,-apple-system,Segoe UI,Roboto,"Noto Sans TC",Arial;line-height:1.6}
    .wrap{max-width:1000px;margin:32px auto;padding:0 16px}
    .card{
      background:#f9f9f9;border:1px solid #ccc;border-radius:10px;padding:20px;
      max-width:720px;margin:0 auto;box-shadow:0 2px 8px rgba(0,0,0,.04)
    }
    h1{margin:0 0 18px;font-size:26px;font-weight:700;letter-spacing:.5px;text-align:center;color:#000}
    table{width:100%;border-collapse:collapse;margin-top:6px}
    th,td{padding:10px 12px;border-bottom:1px solid #ddd;vertical-align:top;word-break:break-word}
    th{width:28%;color:#444;font-weight:600;text-align:left}
    /* 商品明細：保留換行，自動斷行 */
    td.product-details{white-space:pre-wrap;word-break:break-word}
    .footer{margin-top:16px;color:#555;font-size:14px;border-left:3px solid #0078d7;padding-left:10px}
    .muted{color:#888}
  </style>
</head>
<body>
  <div class="wrap">
    <div class="card">
      <h1>叫貨明細</h1>
      <div style="color:#555;">以下為本次訂單資訊：</div>
      <table id="kvTable"><tbody><tr><td class="muted" colspan="2">初始化中…</td></tr></tbody></table>
      <div id="footer" class="footer">正在嘗試寄通知給總部…</div>
    </div>
  </div>

  <script>
  document.addEventListener('DOMContentLoaded', async () => {
    const tbody  = document.querySelector('#kvTable tbody');
    const footer = document.getElementById('footer');

    // 讀網址參數
    const sp = new URLSearchParams(location.search);
    const raw = {}; for (const [k,v] of sp.entries()) raw[k] = v ?? "";

    // 欄位設定（你要的 9 個）
    const alias = {
      order_time:      ["time","訂單時間"],
      order_id:        ["order_id","訂單編號"],
      email:           ["email","信箱"],
      store_name:      ["store_name","分店名稱"],
      delivery_method: ["delivery_method","寄貨方式"],
      pickup_note:     ["pickup_note","取貨備註"],
      orders:          ["order_details","商品明細"],
      shipping_fee:    ["shipping","實際運費"],
      total_amount:    ["total","總計金額"]
    };
    const labels = {
      order_time:"訂單時間",order_id:"訂單編號",email:"信箱",store_name:"分店名稱",
      delivery_method:"寄貨方式",pickup_note:"取貨備註",orders:"商品明細",
      shipping_fee:"實際運費",total_amount:"總計金額"
    };
    const allAliasWords = new Set(Object.values(alias).flat());

    // 自動翻轉 Glide 反向參數（值=欄位名）
    const fixed = {...raw};
    for (const [k,v] of Object.entries(raw)) {
      if (!allAliasWords.has(k) && allAliasWords.has(v)) fixed[v] = k;
    }

    // 工具：輸出安全文字（避免 HTML 注入）
    const escapeHTML = (s) => String(s)
      .replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;')
      .replace(/"/g,'&quot;').replace(/'/g,'&#39;');

    // 專門格式化「商品明細」
    function formatOrders(val){
      if (!val) return "";
      let txt = String(val);

      // 還原常見換行標記：\r\n、\n、字面上的 \n、<br>
      txt = txt.replace(/\r\n/g, '\n');
      txt = txt.replace(/\\n/g, '\n');         // 字面上的 \n
      txt = txt.replace(/<br\s*\/?>/gi, '\n'); // 轉成換行

      // 如果仍沒有換行，用常見分隔符切行
      if (!/\n/.test(txt)) {
        txt = txt.replace(/\s*[,，、;；\/]\s*/g, '\n');
      }

      // 安全：先 escape 再把換行轉成 <br>
      return escapeHTML(txt).replace(/\n/g, '<br>');
    }

    const orderKeys = [
      "order_time","order_id","email","store_name","delivery_method",
      "pickup_note","orders","shipping_fee","total_amount"
    ];
    function pick(obj, keys){ for (const k of keys){ if (obj[k]!==undefined && obj[k]!=="") return obj[k]; } return ""; }

    const english = {};
    let rows = "";
    orderKeys.forEach(key=>{
      const value = english[key] = pick(fixed, alias[key]);
      if (key === "orders"){
        const html = value ? formatOrders(value) : "<span class='muted'>（空）</span>";
        rows += `<tr><th>${labels[key]}</th><td class="product-details">${html}</td></tr>`;
      } else {
        rows += `<tr><th>${labels[key]}</th><td>${escapeHTML(value||"")} ${value?"":"<span class='muted'>（空）</span>"}</td></tr>`;
      }
    });
    tbody.innerHTML = rows;

    // 送到 Make，頁尾動態顯示成功/失敗
    const MAKE_WEBHOOK_URL = "https://hook.eu2.make.com/mxd447qyeae62is1m1vsutndp3bqhudf";
    const payload = { ...fixed, __emailjs__: english };
    try {
      const resp = await fetch(MAKE_WEBHOOK_URL, {
        method:'POST',
        headers:{'Content-Type':'application/json'},
        body: JSON.stringify(payload)
      });
      footer.textContent = resp.ok
        ? "✅ 成功寄通知給總部，明細表請自行留存。"
        : "❌ 失敗寄通知給總部，請稍後再試或聯絡管理員。";
    } catch {
      footer.textContent = "❌ 失敗寄通知給總部，請檢查網路或稍後再試。";
    }
  });
  </script>
</body>
</html>
