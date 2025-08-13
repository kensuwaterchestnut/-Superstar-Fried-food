<!doctype html>
<html lang="zh-Hant">
<head>
  <meta charset="utf-8" />
  <title>叫貨明細</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <style>
    body { margin:0; background:#ffffff; color:#222222;
           font-family: ui-sans-serif,-apple-system,Segoe UI,Roboto,"Noto Sans TC",Arial; line-height:1.6;}
    .wrap { max-width: 880px; margin: 40px auto; padding: 0 20px; }
    h1 { margin: 0 0 18px; font-size: 26px; font-weight: 700; letter-spacing: .5px; color:#000000; text-align: center; }
    .card { background: #f9f9f9; border:1px solid #cccccc; border-radius: 10px; padding: 20px; }
    table { width:100%; border-collapse: collapse; margin-top: 6px; }
    th, td { padding: 10px 12px; border-bottom: 1px solid #dddddd; vertical-align: top; word-break: break-word; }
    th { width: 28%; color: #444444; font-weight: 600; text-align: left; }
    .footer { margin-top: 16px; color: #555555; font-size: 14px; border-left: 3px solid #0078d7; padding-left:10px; }
    .muted { color: #888888; }
  </style>
</head>
<body>
  <div class="wrap">
    <h1>叫貨明細</h1>
    <div class="card">
      <div style="color:#555555;">以下為本次訂單資訊：</div>
      <table id="kvTable"><tbody><tr><td class="muted" colspan="2">初始化中…</td></tr></tbody></table>
      <div class="footer">已寄通知給總部，明細表請自行留存。</div>
    </div>
  </div>

  <script>
  document.addEventListener('DOMContentLoaded', () => {
    const tbody = document.querySelector('#kvTable tbody');
    const sp = new URLSearchParams(location.search);
    const raw = {};
    for (const [k,v] of sp.entries()) raw[k] = v ?? "";

    // 固定顯示順序，確保 EmailJS 對應
    const keys = [
      ["order_time","訂單時間"],
      ["order_id","訂單編號"],
      ["email","信箱"],
      ["store_name","分店名稱"],
      ["delivery_method","取貨方式"],
      ["pickup_note","取貨備註"],
      ["orders","商品明細"],
      ["name","姓名"],
      ["price","金額"]
    ];

    let rows = "";
    keys.forEach(([eng, label]) => {
      rows += `<tr><th>${label}</th><td>${raw[eng] || "<span class='muted'>（空）</span>"}</td></tr>`;
    });

    tbody.innerHTML = rows;
  });
  </script>
</body>
</html>
