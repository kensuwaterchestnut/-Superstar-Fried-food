<!DOCTYPE html>
<html lang="zh-Hant">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>叫貨明細</title>
<style>
  body { font-family: Arial, sans-serif; background-color: #f8f8f8; padding: 20px; }
  .container { max-width: 800px; margin: auto; background: #fff; border-radius: 8px; padding: 20px; box-shadow: 0 0 10px rgba(0,0,0,0.1); }
  h1 { text-align: center; color: #222; }
  table { width: 100%; border-collapse: collapse; margin-top: 20px; }
  td { border: 1px solid #ddd; padding: 10px; vertical-align: top; word-break: break-word; }
  td:first-child { font-weight: bold; background-color: #f4f4f4; width: 20%; }
  #status { margin-top: 15px; font-weight: bold; }
</style>
</head>
<body>

<div class="container">
  <h1>叫貨明細</h1>
  <p>以下為本次訂單資訊：</p>
  <table>
    <tr><td>訂單時間</td><td id="order_time">(空)</td></tr>
    <tr><td>訂單編號</td><td id="order_id">(空)</td></tr>
    <tr><td>信箱</td><td id="email">(空)</td></tr>
    <tr><td>分店名稱</td><td id="store_name">(空)</td></tr>
    <tr><td>寄貨方式</td><td id="delivery_method">(空)</td></tr>
    <tr><td>取貨備註</td><td id="pickup_note">(空)</td></tr>
    <tr><td>商品明細</td><td id="order_details" style="white-space: pre-wrap;">(空)</td></tr>
    <tr><td>實際運費</td><td id="shipping">(空)</td></tr>
    <tr><td>總計金額</td><td id="total">(空)</td></tr>
  </table>
  <div id="status">正在寄送通知給總部...</div>
</div>

<script>
  function readParams(){
    const TARGETS = [
      'order_time','order_id','email','store_name',
      'delivery_method','pickup_note','order_details','shipping','total'
    ];
    const ALIASES = {
      order_time:       ['order_time','time','訂單時間','orderTime'],
      order_id:         ['order_id','id','訂單編號','orderId'],
      email:            ['email','信箱','mail','e_mail'],
      store_name:       ['store_name','store','分店名稱','branch','shop'],
      delivery_method:  ['delivery_method','delivery','寄貨方式','shipping_method'],
      pickup_note:      ['pickup_note','note','備註','取貨備註'],
      order_details:    ['order_details','orders','明細','商品明細','items','order_lines'],
      shipping:         ['shipping','運費','shipping_fee','實際運費'],
      total:            ['total','total_amount','金額','總計金額','amount']
    };

    const usp = new URLSearchParams(location.search);
    const out = Object.fromEntries(TARGETS.map(k => [k,'']));

    const tryGet = (alias) => {
      if (usp.has(alias)) return decodeURIComponent(usp.get(alias) || '');
      for (const [name, value] of usp.entries()){
        if (decodeURIComponent(value || '') === alias) {
          return decodeURIComponent(name || '');
        }
      }
      return '';
    };

    for (const key of TARGETS){
      const names = ALIASES[key];
      for (const a of names){
        const v = tryGet(a);
        if (v) { out[key] = v; break; }
      }
    }
    return out;
  }

  document.addEventListener('DOMContentLoaded', () => {
    const data = readParams();
    for (const [key, value] of Object.entries(data)){
      const td = document.getElementById(key);
      if (td) td.textContent = value || '(空)';
    }

    const success = Object.values(data).some(v => v); 
    const status = document.getElementById('status');
    if (status) {
      if (success) {
        status.innerHTML = '✅ 成功寄通知給總部，明細表請自行留存。';
      } else {
        status.innerHTML = '❌ 失敗，未能寄通知給總部，請檢查資料來源。';
      }
    }
  });
</script>

</body>
</html>
