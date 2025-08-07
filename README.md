<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="UTF-8" />
  <title>訂單送出中...</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  
  <!-- ✅ 自動跳轉的網址 -->
  <meta http-equiv="refresh" content="2; url=https://script.google.com/macros/s/AKfycbz9ZWoO764fjcRI0AN5yv9M-xyP6xf_9O6hd0Tg0q0BvpAmbMAfZCqL6Dg1SY5_rgFe/exec?orderTime=2025-08-07T16%3A42&orderId=ORD20250807-001&email=kensu.water.chestnut%40gmail.com&branch=%E5%8F%B0%E4%B8%AD%E5%BA%97&items=%E8%8F%B1%E8%A7%92%E9%85%A5x2%2C%E7%82%B8%E9%AE%AE%E5%A5%B6x1&delivery=%E5%86%B7%E8%97%8D&note=%E5%8A%A0%E8%BE%A3&fee=100&total=450&authuser=0" />

  <style>
    body {
      margin: 0;
      padding: 0;
      background-color: #111;
      color: #fff;
      font-family: 'Segoe UI', 'Noto Sans TC', sans-serif;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 100vh;
      text-align: center;
    }

    h1 {
      font-size: 2rem;
      margin-bottom: 1rem;
      color: #00ffcc;
    }

    p {
      font-size: 1.2rem;
      opacity: 0.8;
    }

    .loader {
      margin-top: 30px;
      border: 6px solid #333;
      border-top: 6px solid #00ffcc;
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
</head>
<body>
  <h1>訂單送出中...</h1>
  <p>請稍候，我們正在通知總部準備出貨 🛵</p>
  <div class="loader"></div>
</body>
</html>
