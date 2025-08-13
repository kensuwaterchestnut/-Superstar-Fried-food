<!doctype html>
<html lang="zh-Hant">
<head>
  <meta charset="utf-8" />
  <title>叫貨明細 - 預覽</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <style>
    :root {
      --bg: #0f1217; --card: #141a22; --text: #e9eef5; --muted: #9fb0c3;
      --accent: #3aa3ff; --border: #223043;
    }
    * {box-sizing: border-box}
    body { margin:0; background:var(--bg); color:var(--text);
           font-family: ui-sans-serif,-apple-system,Segoe UI,Roboto,"Noto Sans TC",Arial; line-height:1.6;}
    .wrap { max-width: 880px; margin: 40px auto; padding: 0 20px; }
    h1 { margin: 0 0 18px; font-size: 26px; font-weight: 700; letter-spacing: .5px; }
    .card { background: var(--card); border:1px solid var(--border); border-radius: 14px; padding: 20px; box-shadow: 0 10px 30px rgba(0,0,0,.25); }
    table { width:100%; border-collapse: collapse; margin-top: 6px; }
    th, td { padding: 10px 12px; border-bottom: 1px solid var(--border); vertical-align: top; word-break: break-word; }
    th { width: 28%; color: var(--muted); font-weight: 600; text-align: left; }
    .footer { margin-top: 16px; color: var(--muted); font-size: 14px; border-left: 3px solid var(--accent); padding-left:10px; }
    .muted { color: var(--muted); }
    .err { color: #ffb3b3; margin-top: 12px; }
  </style>
</head>
<body>
  <div class="wrap">
    <h1>叫貨明細（預覽）</h1>
    <div class="card">
      <div class="muted">以下為本次訂單資訊：</div>
      <table id="kvTable"><tbody><tr><td class="muted" colspan="2">初始化中…</td></tr></tbody></table>
      <div id="errBox" class="err"></div>
      <div class="footer">已寄通知給總部，明細表請自行留存。</div>
    </div>
  </div>

  <script>
  document.addEventListener('DOMContentLoaded', async () => {
    const errBox = document.getElementById('errBox');
    const tbody  = document.querySelector('#kvTable tbody');

    function safeSetTable(html){
      try { tbody.innerHTML = html; } catch(e) {}
    }

    try {
      // 1) 讀參數（無論多少都照單全收）
      const sp = new URLSearchParams(location.search);
      const raw = {};
      for (const [k,v] of sp.entries()) raw[k] = v ?? "";

      // 2) 顯示表格（即使沒有參數也會有提示）
      if (Object.keys(raw).length === 0) {
        safeSetTable('<tr><td class="muted" colspan="2">（網址中沒有任何參數）</td></tr>');
      } else {
        // 讓常見鍵先顯示
        const priority = ["order_time","訂單時間","下單時間","order_id","訂單編號","email","信箱","買家Email","store_name","分店名稱","name","姓名","delivery_method","取貨方式","寄貨方式","pickup_note","取貨備註","orders","商品明細","品項摘要","price","合計金額","總計"];
        const keys = Object.keys(raw).sort((a,b)=>(priority.indexOf(a)===-1?999:priority.indexOf(a))-(priority.indexOf(b)===-1?999:priority.indexOf(b)));
        let rows = "";
        for (const k of keys) rows += `<tr><th>${k}</th><td>${(raw[k]??"")||"<span class='muted'>（空）</span>"}</td></tr>`;
        safeSetTable(rows);
      }

      // 3) 構出 EmailJS 需要的英文鍵（如果你本來就是英文鍵名，會直接沿用）
      const mapToEnglish = {
        "訂單時間":"order_time","下單時間":"order_time",
        "訂單編號":"order_id","信箱":"email","買家Email":"email",
        "分店名稱":"store_name","店名":"store_name",
        "取貨方式":"delivery_method","寄貨方式":"delivery_method",
        "取貨備註":"pickup_note","備註":"pickup_note",
        "商品明細":"orders","品項摘要":"orders",
        "姓名":"name","聯絡人":"name",
        "合計金額":"price","總計":"price"
      };
      const english = {};
      const need = ["order_time","order_id","email","store_name","delivery_method","pickup_note","orders","name","price"];
      for (const [k,v] of Object.entries(raw)) {
        if (need.includes(k)) english[k] = v;
        else if (mapToEnglish[k]) english[mapToEnglish[k]] = english[mapToEnglish[k]] || v;
      }

      // 4) 靜默 POST 到 Make（帶上所有欄位 + 一份 __emailjs__）
      const MAKE_WEBHOOK_URL = "https://hook.eu2.make.com/mxd447qyeae62is1m1vsutndp3bqhudf";
      const payload = { ...raw, __emailjs__: english };
      // 若你想先確認送出的內容，可以打開下一行 console：
      // console.log('POST to Make:', payload);
      try {
        await fetch(MAKE_WEBHOOK_URL, { method:'POST', headers:{'Content-Type':'application/json'}, body: JSON.stringify(payload) });
      } catch (postErr) {
        // 不阻塞畫面，只在底下顯示提示
        errBox.textContent = "⚠️ 向 Make 傳送失敗（不影響頁面顯示）。";
      }
    } catch (e) {
      safeSetTable('<tr><td class="muted" colspan="2">（初始化失敗）</td></tr>');
      errBox.textContent = "錯誤：" + (e && e.message ? e.message : String(e));
    }
  });
  </script>
</body>
</html>
