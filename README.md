<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>叫貨明細</title>
  <style>
    :root { --card-w: 820px; --muted:#9aa0a6; }
    *{box-sizing:border-box}
    body{
      margin:0; background:#f4f6f8; color:#222; font:16px/1.6 -apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,"Noto Sans TC",Arial;
      display:flex; min-height:100vh; align-items:flex-start; justify-content:center; padding:40px 16px;
    }
    .card{
      width:100%; max-width:var(--card-w); background:#fff; border-radius:12px;
      box-shadow:0 8px 30px rgba(0,0,0,.06); padding:28px 28px 20px;
      border:1px solid #e5e7eb;
    }
    h1{ margin:0 0 18px; text-align:center; font-size:28px; letter-spacing:.12em }
    .sub{ color:#111; margin:6px 0 14px; font-weight:600 }
    table{ width:100%; border-collapse:separate; border-spacing:0; overflow:hidden; border-radius:8px; border:1px solid #e5e7eb }
    th,td{ padding:10px 12px; vertical-align:top; background:#fff }
    th{ width:140px; background:#f8fafc; text-align:left; white-space:nowrap }
    tr+tr th, tr+tr td{ border-top:1px solid #eef2f7 }
    .muted{ color:var(--muted) }
    .status{ margin-top:14px; padding:10px 12px; border-left:4px solid #ddd; color:#333; background:#fafafa; border-radius:6px }
    .ok{ border-color:#16a34a; }
    .warn{ border-color:#f59e0b; }
    .debug{ margin-top:14px; font-size:13px; color:#666; word-break:break-all; white-space:pre-wrap; background:#fbfbfb; border:1px dashed #e5e7eb; padding:10px; border-radius:8px }
    .mono{ font-family:ui-monospace, SFMono-Regular, Menlo, Monaco, "Liberation Mono", monospace }
  </style>
</head>
<body>
  <div class="card">
    <h1>叫貨明細</h1>

    <div class="sub">以下為本次訂單資訊：</div>
    <table>
      <tr><th>訂單時間</th><td id="order_time" class="muted">（空）</td></tr>
      <tr><th>訂單編號</th><td id="order_id" class="muted">（空）</td></tr>
      <tr><th>信箱</th><td id="email" class="muted">（空）</td></tr>
      <tr><th>分店名稱</th><td id="store_name" class="muted">（空）</td></tr>
      <tr><th>寄貨方式</th><td id="delivery_method" class="muted">（空）</td></tr>
      <tr><th>取貨備註</th><td id="pickup_note" class="muted">（空）</td></tr>
      <tr><th>商品明細</th><td id="order_details" class="muted">（空）</td></tr>
      <tr><th>實際運費</th><td id="shipping" class="muted">（空）</td></tr>
      <tr><th>總計金額</th><td id="total" class="muted">（空）</td></tr>
    </table>

    <div id="status" class="status">正在嘗試寄通知給總部…</div>
    <div id="debug" class="debug mono" style="display:none"></div>
  </div>

  <script>
    (function () {
      "use strict";

      // ====== 1) 讀取網址參數（支援別名）======
      const qs = new URLSearchParams(location.search);
      const get = (k, ...alts) => {
        const keys = [k, ...alts];
        for (const key of keys) {
          const v = qs.get(key);
          if (v !== null && v !== undefined && String(v).trim() !== "") return String(v);
        }
        return "";
      };

      // 將文字安全化（避免特殊字元）但保留換行（換成 <br>）
      const esc = (s) => String(s).replace(/[&<>"']/g, m => ({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'}[m]));
      const toHtml = (s) => esc(s).replace(/\r\n|\r|\n/g, "<br>");

      const fields = {
        order_time:   get("order_time","time","訂單時間"),
        order_id:     get("order_id","id","訂單編號"),
        email:        get("email","信箱"),
        store_name:   get("store_name","分店名稱"),
        delivery_method: get("delivery_method","寄貨方式"),
        pickup_note:  get("pickup_note","取貨備註"),
        order_details:get("order_details","商品明細"),
        shipping:     get("shipping","shipping_fee","實際運費"),
        total:        get("total","total_amount","總計金額")
      };

      // 把資料寫進表格（先顯示，不等待送出）
      for (const [k, v] of Object.entries(fields)) {
        const el = document.getElementById(k);
        if (!el) continue;
        if (k === "order_details" || k === "pickup_note") {
          el.innerHTML = v ? toHtml(v) : '<span class="muted">（空）</span>';
        } else {
          el.textContent = v || "（空）";
          if (!v) el.classList.add("muted");
        }
      }

      // ====== 2) 送到 Make Webhook ======
      const WEBHOOK = "https://hook.eu2.make.com/mxd447qyeae62is1m1vsutndp3bqhudf";
      const statusEl = document.getElementById("status");
      const debugEl = document.getElementById("debug");

      // 準備要給 EmailJS 的欄位（Make 會轉發）
      const payload = {
        // 你在 Make 的 HTTP → EmailJS 會用到的欄位，只帶 template_params 就好
        template_id: "template_ceydmzp",
        user_id: "nMamM2Ecz2ztnkCOV",
        accessToken: "f5omy7Am47n3EeMYtS66J",
        template_params: {
          order_time:   fields.order_time,
          order_id:     fields.order_id,
          email:        fields.email,
          store_name:   fields.store_name,
          delivery_method: fields.delivery_method,
          pickup_note:  fields.pickup_note,
          // 給 EmailJS 的商品明細改成含 <br> 的 HTML，信裡會漂亮換行
          order_details: toHtml(fields.order_details),
          shipping:     fields.shipping,
          total:        fields.total
        }
      };

      // 先 POST JSON；失敗再備援 GET（圖片 ping）
      function sendPost() {
        return fetch(WEBHOOK, {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify(payload)
        });
      }
      function sendGetFallback() {
        return new Promise(resolve => {
          const u = new URL(WEBHOOK);
          for (const [k, v] of Object.entries(fields)) {
            u.searchParams.append(k, v);
          }
          // 也把 emailjs 需要的關鍵值帶上，方便你在 Make 端 Mapping
          u.searchParams.append("template_id", payload.template_id);
          u.searchParams.append("user_id", payload.user_id);
          u.searchParams.append("accessToken", payload.accessToken);

          const img = new Image();
          const done = (tag) => resolve(tag);
          img.onload = () => done("img-ok");
          img.onerror = () => done("img-error");
          img.src = u.toString();
          setTimeout(() => done("timeout"), 4000);
        });
      }

      // 執行：不管結果，都不會影響畫面顯示
      sendPost()
        .then(r => {
          if (!r.ok) throw new Error("POST " + r.status);
          statusEl.classList.add("ok");
          statusEl.textContent = "✅ 成功寄通知給總部，明細表請自行留存。";
        })
        .catch(async (err) => {
          // 備援 GET
          const tag = await sendGetFallback();
          statusEl.classList.add("warn");
          statusEl.textContent = (tag === "img-ok")
            ? "⚠️ 已用備援通道送出通知，請留意信箱是否收到。"
            : "⚠️ 網路不穩，已嘗試送出通知；請稍後再檢查信箱。";

          // 若想除錯，把原始 Query 及錯誤顯示出來：在網址加上 ?debug=1
          if (qs.get("debug") === "1") {
            debugEl.style.display = "block";
            debugEl.textContent =
              "原始 QueryString:\n" + location.search + "\n\n" +
              "解析後:\n" + JSON.stringify(fields, null, 2) + "\n\n" +
              "錯誤: " + (err && err.message ? err.message : String(err)) +
              "\n備援結果: " + tag;
          }
        });
    })();
  </script>
</body>
</html>
