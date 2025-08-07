<script>
  // Step 1: 解析網址參數
  function parseQueryParams() {
    const params = {};
    const query = window.location.search.substring(1);
    const pairs = query.split('&');
    for (const pair of pairs) {
      const [key, value] = pair.split('=');
      if (key) {
        params[decodeURIComponent(key)] = decodeURIComponent(value || '');
      }
    }
    return params;
  }

  // Step 2: 傳送資料到 Web App
  function sendToWebApp(data) {
    fetch("https://script.google.com/macros/s/AKfycbyQNTF0rHwaTR-rBaTnnOvi-yipqqCaPH2w9NvHFofZn0b9vCgdkS7tpGUo38uiysQS/exec", {
      method: 'POST',
      body: JSON.stringify(data),
      headers: {
        'Content-Type': 'application/json'
      }
    })
    .then(response => {
      if (response.ok) {
        console.log("✅ Web App 收到訂單資料！");
      } else {
        console.error("❌ Web App 回傳錯誤", response.status);
      }
    })
    .catch(error => {
      console.error("❌ 傳送到 Web App 失敗", error);
    });
  }

  // Step 3: 執行流程
  const queryParams = parseQueryParams();
  sendToWebApp(queryParams);
</script>
