<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>訂單送出中...</title>
  <style>
    body {
      background-color: #111;
      color: #fff;
      font-family: 'Segoe UI', 'Helvetica Neue', sans-serif;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      height: 100vh;
      margin: 0;
    }
    h1 {
      font-size: 2rem;
      color: #00f7ff;
      margin-bottom: 1rem;
    }
    p {
      font-size: 1.2rem;
      color: #ccc;
    }
    .loader {
      margin-top: 30px;
      border: 6px solid #333;
      border-top: 6px solid #00f7ff;
      border-radius: 50%;
      width: 40px;
      height: 40px;
      animation: spin 1s linear infinite;
    }
    @keyframes spin {
      0% { transform: rotate(0deg); }
      100% { transform: rotate(360deg); }
    }
  </style>
  <script>
    // 跳轉連結設定在這裡 ↓↓↓
    const redirectUrl = "https://script.google.com/macros/s/AKfycbz9ZWoO764fjcRI0AN5yv9M-xyP6xf_9O6hd0Tg0q0BvpAmbMAfZCqL6Dg1SY5_rgFe/exec";

    // 自動跳轉
    setTimeout(() => {
      window.location.href = redirectUrl;
    }, 1500);
  </script>
</head>
<body>
  <h1>訂單送出中...</h1>
  <p>請稍候，我們正在通知總部準備出貨 🚚</p>
  <div class="loader"></div>
</body>
</html>
