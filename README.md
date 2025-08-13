<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="utf-8" />
  <title>叫貨明細</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <!-- build: v-fix-left-alias-2025-08-13 -->
  <style>
    body{margin:0;background:#fff;color:#222;
      font-family:ui-sans-serif,-apple-system,Segoe UI,Roboto,"Noto Sans TC",Arial;line-height:1.6}
    .wrap{max-width:1000px;margin:24px auto;padding:0 16px}
    h1{margin:0 0 18px;font-size:26px;font-weight:700;letter-spacing:.5px;text-align:center;color:#000}
    .card{background:#f9f9f9;border:1px solid #ccc;border-radius:10px;padding:20px;
      /* 靠左：卡片固定寬度，與左邊留同樣內距 */
      max-width:680px;margin-left:0}
    table{width:100%;border-collapse:collapse;margin-top:6px}
    th,td{padding:10px 12px;border-bottom:1px solid #ddd;vertical-align:top;word-break:break-word}
    th{width:28%;color:#444;font-weight:600;text-align:left}
    .footer{margin-top:16px;color:#555;font-size:14px;border-left:3px solid #0078d7;padding-left:10px}
    .muted{color:#888}
  </style>
</head>
<body>
  <div class="wrap">
    <h1>叫貨明細</h1>
    <div class="card">
      <div style="color:#555;">以下為本次訂單資訊：</div>
      <table id="kvTable"><tbody><tr><td class="muted" colspan="2">初始化中…</td></tr></tbody></table>
      <div class="footer">已寄通知給總部，明細表請自行留存。</div>
    </div>
  </div>

  <script>
  document.addEventListener('DOMContentLoaded', async () => {
    const tbody = document.querySelector('#kvTable tbody');

    // 1) 讀取所有網址參數
    const sp = new URLSearchParams(location.search);
    const raw = {};
    for (const [k,v] of sp.entries()) raw[k] = v ?? "";

    // 2) 欄位別名（支援中英混用）
    const alias = {
      order_time:      ["order_time","訂單時間","下單時間"],
      order_id:        ["order_id","訂單編號"],
      email:           ["email","信箱","買家Email","Email","電子郵件"],
      store_name:      ["store_name","分店名稱","店名"],
      delivery_method: ["delivery_method","取貨方式","寄貨方式","配送方式","運送方式","shipping"],
      pickup_note:     ["pickup_note","取貨備註","備註","備註說明"],
      orders:          ["orders","商品明細","品項摘要","商品明細表","品項","order_details"],
      name:            ["name","姓名","聯絡人","客戶姓名"],
      price:           ["price","金額","合計金額","總計","total"]
    };
    const labels = {
      order_time:"訂單時間",order_id:"訂單編號",email:"信箱",store_name:"分店名稱",
      delivery_method:"取貨方式",pickup_note:"取貨備註",orders:"商品明細",name:"姓名",price:"金額"
    };
    function pick(obj, keys){
      for (const k of keys){ if (obj[k] !== undefined && obj[k] !== "") return obj[k]; }
      return "";
    }

    // 3) 產出 EmailJS 需要的英文鍵，同時用表格顯示
    const english = {};
    const order = ["order_time","order_id","email","store_name","delivery_method","pickup_note","orders","name","price"];
    let rows = "";
    order.forEach(key=>{
      const val = english[key] = pick(raw, alias[key]);
      rows += `<tr><th>${labels[key]}</th><td>${val || "<span class='muted'>（空）</span>"}</td></tr>`;
    });
    tbody.innerHTML = rows;

    // 4) 靜默送到 Make（把所有原始欄位 + 一份 __emailjs__ 英文鍵傳過去）
    const MAKE_WEBHOOK_URL = "https://hook.eu2.make.com/mxd447qyeae62is1m1vsutndp3bqhudf";
    const payload = { ...raw, __emailjs__: english };
    try {
      await fetch(MAKE_WEBHOOK_URL, {
        method:'POST',
        headers:{'Content-Type':'application/json'},
        body: JSON.stringify(payload)
      });
    } catch(e){ /* 靜默，不影響顯示 */ }
  });
  </script>
</body>
</html>
