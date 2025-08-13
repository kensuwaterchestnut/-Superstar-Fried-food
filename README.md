<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="UTF-8" />
  <title>叫貨明細（預覽）</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <style>
    body { background:#121212; color:#f0f0f0; font-family:system-ui,Arial; padding:24px; line-height:1.6 }
    h1 { font-size:22px; margin-bottom:12px }
    .param-box { background:#1e1e1e; padding:16px 18px; border-radius:10px; box-shadow:0 0 10px rgba(255,255,255,.06) }
    .row { margin:6px 0; word-break:break-word }
    .ok { color:#27ae60; margin-top:14px }
    .err { color:#e74c3c; margin-top:14px }
    small { opacity:.7 }
    button { margin-top:12px; padding:8px 12px; border:0; border-radius:8px; cursor:pointer }
  </style>
</head>
<body>
  <h1>叫貨明細（預覽）</h1>
  <div class="param-box" id="paramsBox">讀取中...</div>
  <div id="sendStatus"></div>

  <script>
    // 👉 換成你的 Make Webhook（你給的這條）
    const MAKE_WEBHOOK_URL = "https://hook.eu2.make.com/mxd447qyeae62is1m1vsutndp3bqhudf";

    // 1) 正確解析 URL 參數
    function getParams() {
      const sp = new URLSearchParams(location.search);
      // 建議在 Glide 端用這些名稱帶參數（與 EmailJS 模板一致）
      const data = {
        order_time:      sp.get("order_time")      || "",
        order_id:        sp.get("order_id")        || "",
        email:           sp.get("email")           || "",
        store_name:      sp.get("store_name")      || "",
        delivery_method: sp.get("delivery_method") || "",
        pickup_note:     sp.get("pickup_note")     || "",
        orders:          sp.get("orders")          || "",
        name:            sp.get("name")            || "",
        price:           sp.get("price")           || ""
      };
      return data;
    }

    // 2) 畫面呈現
    function render(box, params) {
      const map = {
        order_time: "下單時間",
        order_id: "訂單編號",
        email: "Email",
        store_name: "分店/店名",
        delivery_method: "取貨/配送",
        pickup_note: "備註",
        orders: "品項摘要",
        name: "姓名",
        price: "金額"
      };
      box.innerHTML = "";
      Object.keys(params).forEach(k => {
        const div = document.createElement("div");
        div.className = "row";
        div.innerHTML = `<strong>【${map[k] || k}】</strong>：${params[k] || "<small>（空）</small>"}`;
        box.appendChild(div);
      });
    }

    // 3) 將參數 POST 到 Make Webhook
    async function sendToMake(payload) {
      const status = document.getElementById("sendStatus");
      try {
        const res = await fetch(MAKE_WEBHOOK_URL, {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify(payload)
        });
        if (!res.ok) throw new Error("HTTP " + res.status);
        status.innerHTML = `<div class="ok">已送出至 Make，請稍候查收 Email（EmailJS）。</div>`;
      } catch (e) {
        status.innerHTML = `<div class="err">送出失敗：${e.message}。請稍後重試。</div>
                            <small>若持續失敗，請在 Make 檢查 Webhook 是否啟用 / Scenario 有沒有按「Run once」。</small>`;
      }
    }

    // 4) 執行：渲染 + 送資料
    const params = getParams();
    render(document.getElementById("paramsBox"), params);
    // 預設載入就送；如需手動觸發，可以改成按鈕點擊再呼叫
    sendToMake(params);
  </script>
</body>
</html>
