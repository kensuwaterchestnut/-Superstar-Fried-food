<script>
  // 你的 Make Webhook（你給的那條）
  const MAKE_WEBHOOK_URL = "https://hook.eu2.make.com/mxd447qyeae62is1m1vsutndp3bqhudf";

  // 1) 讀參數（都是實際值，因為 Glide 已插入欄位）
  const qs = new URLSearchParams(location.search);
  const payload = {
    order_time:      qs.get("order_time")      || "",
    order_id:        qs.get("order_id")        || "",
    email:           qs.get("email")           || "",
    store_name:      qs.get("store_name")      || "",
    delivery_method: qs.get("delivery_method") || "",
    pickup_note:     qs.get("pickup_note")     || "",
    orders:          qs.get("orders")          || "",
    name:            qs.get("name")            || "",
    price:           qs.get("price")           || ""
  };

  // 2) 顯示在畫面（可留可刪）
  const box = document.getElementById('paramsBox');
  const label = {
    order_time:"下單時間",order_id:"訂單編號",email:"Email",store_name:"分店/店名",
    delivery_method:"取貨/配送",pickup_note:"備註",orders:"品項摘要",name:"姓名",price:"金額"
  };
  box.innerHTML = "";
  Object.keys(payload).forEach(k=>{
    const p=document.createElement('p');
    p.innerHTML=`<strong>【${label[k]}】</strong>：${payload[k]||'（空）'}`;
    box.appendChild(p);
  });

  // 3) 送到 Make
  fetch(MAKE_WEBHOOK_URL, {
    method: 'POST',
    headers: {'Content-Type':'application/json'},
    body: JSON.stringify(payload)
  })
  .then(r=>{ if(!r.ok) throw new Error(`HTTP ${r.status}`); })
  .catch(e=>{ console.error('Send failed:', e); });
</script>
