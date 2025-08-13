<!doctype html>
<html lang="zh-Hant">
<head>
  <meta charset="utf-8" />
  <title>叫貨明細 - 預覽</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <style>
    :root {
      --bg: #0f1217;
      --card: #141a22;
      --text: #e9eef5;
      --muted: #9fb0c3;
      --accent: #3aa3ff;
      --border: #223043;
    }
    * {box-sizing:border-box}
    body {
      margin:0; background:var(--bg); color:var(--text);
      font-family: ui-sans-serif, -apple-system, Segoe UI, Roboto, "Noto Sans TC", Arial;
      line-height:1.6;
    }
    .wrap {
      max-width: 880px; margin: 40px auto; padding: 0 20px;
    }
    h1 {
      margin: 0 0 18px; font-size: 26px; font-weight: 700;
      letter-spacing: .5px;
    }
    .card {
      background: var(--card); border:1px solid var(--border);
      border-radius: 14px; padding: 20px;
      box-shadow: 0 10px 30px rgba(0,0,0,.25);
    }
    table {
      width:100%; border-collapse: collapse; margin-top: 6px;
    }
    th, td {
      padding: 10px 12px; border-bottom: 1px solid var(--border);
      vertical-align: top; word-break: break-word;
    }
    th {
      width: 24%; color: var(--muted); font-weight: 600; text-align: left;
    }
    .footer {
      margin-top: 16px; color: var(--muted);
      font-size: 14px; border-left: 3px solid var(--accent);
      padding-left:10px;
    }
    .muted { color: var(--muted); }
  </style>
</head>
<body>
  <div class="wrap">
    <h1>叫貨明細（預覽）</h1>
    <div class="card">
      <div class="muted">以下為本次訂單資訊：</div>
      <table id="kvTable">
        <tbody>
          <tr><td class="muted" colspan="2">讀取中…</td></tr>
        </tbody>
      </table>
      <div class="footer">已寄通知給總部，明細表請自行留存。</div>
    </div>
  </div>

  <script>
    // 你的 Make Webhook（靜默送出，不顯示結果）
    const MAKE_WEBHOOK_URL = "https://hook.eu2.make.com/mxd447qyeae62is1m1vsutndp3bqhudf";

    // 1) 解析網址參數（Glide 會把所有欄位都附在網址上）
    function getAllQueryParams() {
      const sp = new URLSearchParams(location.search);
      const obj = {};
      // 逐一取出所有 key/value
      for (const [k, v] of sp.entries()) {
        // 轉回正常字串（確保中文、空白、符號都正確顯示）
        obj[k] = v ?? "";
      }
      return obj;
    }

    // 2) 轉換對 EmailJS 需要的欄位（英文鍵名），其餘保持原樣
    //    你可以依實際欄位名在這裡擴充映射
    function buildEmailJsPayloadFrom(params) {
      // 映射：把常見中文鍵 → EmailJS 模板用的英文鍵
      // 若 Glide 直接給英文（order_time 等），會保留英文
      const mapToEnglish = {
        "訂單時間": "order_time",
        "下單時間": "order_time",
        "訂單編號": "order_id",
        "信箱": "email",
        "買家Email": "email",
        "分店名稱": "store_name",
        "店名": "store_name",
        "取貨方式": "delivery_method",
        "寄貨方式": "delivery_method",
        "取貨備註": "pickup_note",
        "備註": "pickup_note",
        "商品明細": "orders",
        "品項摘要": "orders",
        "姓名": "name",
        "聯絡人": "name",
        "合計金額": "price",
        "總計": "price",
        // 你若還有：order_details / shipping / total 等，也可映射：
        "商品明細表": "orders",
        "運送方式": "delivery_method"
      };

      // 先把所有參數列一份副本，保留原始鍵名
      const allForMake = {...params};

      // 針對 EmailJS 模板需要的幾個英文鍵名，嘗試從參數找對應
      const wantKeys = ["order_time","order_id","email","store_name","delivery_method","pickup_note","orders","name","price"];
      const english = {};
      for (const [k, v] of Object.entries(params)) {
        if (wantKeys.includes(k)) {
          english[k] = v; // 已是英文鍵，直接採用
          continue;
        }
        // 若是中文鍵，嘗試映射
        if (mapToEnglish[k]) {
          const ek = mapToEnglish[k];
          if (!english[ek]) english[ek] = v;
        }
      }

      return { allForMake, englishForEmailJS: english };
    }

    // 3) 呈現在表格（所有欄位都顯示）
    function renderTable(kv) {
      const tbody = document.querySelector("#kvTable tbody");
      tbody.innerHTML = "";
      if (!Object.keys(kv).length) {
        tbody.innerHTML = '<tr><td class="muted" colspan="2">（沒有偵測到任何參數）</td></tr>';
        return;
      }
      // 讓重要欄位靠前顯示
      const priority = ["order_time","訂單時間","下單時間","order_id","訂單編號","email","信箱","買家Email","store_name","分店名稱","name","姓名","delivery_method","取貨方式","寄貨方式","pickup_note","取貨備註","orders","商品明細","品項摘要","price","合計金額","總計"];
      const keys = Object.keys(kv).sort((a,b) => (priority.indexOf(a) === -1 ? 999 : priority.indexOf(a)) - (priority.indexOf(b) === -1 ? 999 : priority.indexOf(b)));

      for (const k of keys) {
        const tr = document.createElement("tr");
        const th = document.createElement("th");
        const td = document.createElement("td");
        th.textContent = k;
        td.textContent = kv[k] ?? "";
        tr.appendChild(th); tr.appendChild(td);
        tbody.appendChild(tr);
      }
    }

    // 4) 送資料到 Make（靜默）
    async function postToMake(payload) {
      try {
        await fetch(MAKE_WEBHOOK_URL, {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify(payload)
        });
        // 靜默，不顯示成功/失敗（若要 debug 可打開 console）
      } catch (e) {
        // 靜默；如需除錯可 console.error(e);
      }
    }

    // —— 主流程 ——
    const rawParams = getAllQueryParams();
    renderTable(rawParams);

    // 依 EmailJS 需求組 payload，送到 Make：
    const { allForMake, englishForEmailJS } = buildEmailJsPayloadFrom(rawParams);
    // 給 Make：把所有欄位都送過去（保留最完整資訊）
    // 同時附上 EmailJS 需要的英文鍵一份，Make 端直接用。
    const toMake = {
      ...allForMake,
      __emailjs__: englishForEmailJS   // 讓 Make 在 Body 裡用 {{1.__emailjs__.order_time}} 這樣取
    };
    postToMake(toMake);
  </script>
</body>
</html>
