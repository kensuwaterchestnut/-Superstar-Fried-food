<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="utf-8" />
  <title>叫貨明細</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <!-- build: v-stable-2025-08-13 -->
  <style>
    html,body{margin:0;padding:0;background:#fff;color:#222;
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
    td.product-details{white-space:pre-wrap;word-break:break-word}
    .footer{margin-top:16px;color:#555;font-size:14px;border-left:3px solid #0078d7;padding-left:10px}
    .footer.err{border-left-color:#c00;color:#b10000}
    .muted{color:#888}
  </style>
</head>
<body>
  <div class="wrap">
    <div class="card">
      <h1>叫貨明細</h1>
      <div style="color:#555;">以下為本次訂單資訊：</div>
      <table id="kvTable">
        <tbody><tr><td class="muted" colspan="2">初始化中…</td></tr></tbody>
      </table>
      <div id="footer" class="footer">正在嘗試寄通知給總部…</div>
    </div>
  </div>

  <script>
  (function(){
    const $ = (sel)=>document.querySelector(sel);
    const tbody  = $('#kvTable tbody');
    const footer = $('#footer');

    function setError(msg){
      footer.classList.add('err');
      footer.textContent = "❌ 發生錯誤： " + msg;
    }

    try {
      // 1) 讀網址參數
      const sp = new URLSearchParams(location.search);
      const raw = {};
      sp.forEach((v,k)=>{ raw[k]=v==null?"":v; });

      // 2) 欄位設定（你要的9個）
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

      // 3) 自動翻轉（Glide 反向 key=value）
      const allWords = {};
      Object.keys(alias).forEach(k=>alias[k].forEach(w=>{allWords[w]=true;}));
      const fixed = Object.assign({}, raw);
      Object.keys(raw).forEach(k=>{
        const v = raw[k];
        if (!allWords[k] && allWords[v]) fixed[v]=k;
      });

      // 4) 輔助
      function hasOwn(o,k){ return Object.prototype.hasOwnProperty.call(o,k); }
      function pick(obj, list){
        for (var i=0;i<list.length;i++){
          var k=list[i];
          if (hasOwn(obj,k) && obj[k]!=="" && obj[k]!=null) return obj[k];
        }
        return "";
      }
      function esc(s){
        return String(s).replace(/&/g,"&amp;").replace(/</g,"&lt;")
                        .replace(/>/g,"&gt;").replace(/"/g,"&quot;").replace(/'/g,"&#39;");
      }
      function formatOrders(val){
        if (!val) return "";
        var txt = String(val);
        txt = txt.replace(/\r\n/g,"\n").replace(/\\n/g,"\n").replace(/<br\s*\/?>/gi,"\n");
        if (!/\n/.test(txt)) txt = txt.replace(/\s*[,，、;；\/]\s*/g,"\n");
        return esc(txt).replace(/\n/g,"<br>");
      }

      // 5) 畫面
      var english = {};
      var orderKeys = ["order_time","order_id","email","store_name","delivery_method","pickup_note","orders","shipping_fee","total_amount"];
      var rows = "";
      for (var i=0;i<orderKeys.length;i++){
        var key = orderKeys[i];
        var val = english[key] = pick(fixed, alias[key]);
        if (key==="orders"){
          var html = val ? formatOrders(val) : "<span class='muted'>（空）</span>";
          rows += "<tr><th>"+labels[key]+"</th><td class=\"product-details\">"+html+"</td></tr>";
        }else{
          rows += "<tr><th>"+labels[key]+"</th><td>"+(val?esc(val)+"":"<span class='muted'>（空）</span>")+"</td></tr>";
        }
      }
      tbody.innerHTML = rows;

      // 6) 傳到 Make（多層備援）
      var MAKE_WEBHOOK_URL = "https://hook.eu2.make.com/mxd447qyeae62is1m1vsutndp3bqhudf"; // ← 確認這是你的 Webhook
      var payload = {};
      // 合併 fixed 與 __emailjs__
      for (var k in fixed){ if (hasOwn(fixed,k)) payload[k]=fixed[k]; }
      payload.__emailjs__ = english;

      function toQuery(obj, prefix){
        var pairs=[], k, key, val;
        for (k in obj){
          if (!hasOwn(obj,k)) continue;
          key = prefix ? (prefix+"."+k) : k;
          val = obj[k];
          if (val && typeof val === "object"){
            pairs.push(toQuery(val, key));
          }else{
            pairs.push(encodeURIComponent(key)+"="+encodeURIComponent(val==null?"":val));
          }
        }
        return pairs.join("&");
      }

      function fetchWithTimeout(url, options, ms){
        return new Promise(function(resolve,reject){
          var ctrl = new AbortController();
          var t = setTimeout(function(){ ctrl.abort(); reject(new Error("timeout")); }, ms||6000);
          fetch(url, Object.assign({}, options||{}, {signal: ctrl.signal}))
            .then(function(res){ clearTimeout(t); resolve(res); })
            .catch(function(err){ clearTimeout(t); reject(err); });
        });
      }

      (function sendToMake(){
        var ok = false;
        var jsonStr = JSON.stringify(payload);

        // A. sendBeacon
        try{
          if ('sendBeacon' in navigator){
            var blob = new Blob([jsonStr], { type: 'application/json' });
            ok = navigator.sendBeacon(MAKE_WEBHOOK_URL, blob);
          }
        }catch(e){ /* ignore */ }

        // B. fetch(text/plain)
        function afterBeacon(){
          if (ok){
            footer.textContent = "✅ 成功寄通知給總部，明細表請自行留存。";
            return;
          }
          fetchWithTimeout(MAKE_WEBHOOK_URL, {
            method: "POST",
            headers: {"Content-Type":"text/plain;charset=UTF-8"},
            body: jsonStr
          }, 7000).then(function(res){
            footer.textContent = res && res.ok
              ? "✅ 成功寄通知給總部，明細表請自行留存。"
              : "❌ 失敗寄通知給總部（HTTP）。";
          }).catch(function(){
            // C. 影像 GET ping
            try{
              var img = new Image();
              img.onload = function(){ footer.textContent = "✅ 成功寄通知給總部，明細表請自行留存。"; };
              img.onerror = function(){ setError("Send 失敗（圖片 ping）"); };
              img.src = MAKE_WEBHOOK_URL + "?" + toQuery(payload);
            }catch(e){
              setError("Send 失敗（例外）");
            }
          });
        }

        // sendBeacon 是同步回傳 boolean，所以直接接續
        afterBeacon();
      })();

    } catch (e) {
      setError(e && e.message ? e.message : String(e));
    }
  })();
  </script>
</body>
</html>
