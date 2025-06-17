# five-coin-exchange-
Official five coin cryptocurrency exchange
<!DOCTYPE html><html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Five Exchange - Swap</title>
  <style>
    body {
      margin: 0;
      font-family: 'Poppins', sans-serif;
      background-color: #111;
      color: #f5f5f5;
    }
    header {
      background-color: #1a1a1a;
      padding: 20px;
      display: flex;
      justify-content: space-between;
      align-items: center;
    }
    header img {
      height: 40px;
    }
    nav a {
      color: #ccc;
      margin: 0 12px;
      text-decoration: none;
      font-weight: 500;
    }
    .container {
      max-width: 480px;
      margin: 60px auto;
      padding: 20px;
      background-color: #1e1e1e;
      border-radius: 12px;
      box-shadow: 0 0 20px rgba(0,0,0,0.4);
    }
    .swap-box label {
      display: block;
      margin-bottom: 6px;
      color: #aaa;
    }
    .swap-box input, .swap-box select {
      width: 100%;
      padding: 12px;
      margin-bottom: 16px;
      border-radius: 8px;
      border: none;
      background-color: #2c2c2c;
      color: #fff;
    }
    button {
      width: 100%;
      padding: 12px;
      border: none;
      border-radius: 8px;
      background-color: gold;
      font-weight: bold;
      cursor: pointer;
    }
    footer {
      margin-top: 40px;
      padding: 20px;
      text-align: center;
      font-size: 14px;
      color: #888;
    }
  </style>
</head>
<body>
  <header>
    <img src="logo.png" alt="Five Exchange Logo" />
    <nav>
      <a href="#">Swap</a>
      <a href="#">Pools</a>
      <a href="#">Tokens</a>
      <a href="#">Docs</a>
      <a href="#">Connect Wallet</a>
    </nav>
  </header>
  <div class="container">
    <h2 style="text-align: center; margin-bottom: 30px;">Swap Your Tokens</h2>
    <div class="swap-box">
      <label for="from-token">You Pay</label>
      <select id="from-token">
        <option value="FIVE">FIVE</option>
        <option value="TON">TON</option>
      </select>
      <input type="number" placeholder="Amount" /><label for="to-token">You Receive</label>
  <select id="to-token">
    <option value="TON">TON</option>
    <option value="FIVE">FIVE</option>
  </select>
  <input type="text" placeholder="Estimated Amount" disabled />

  <button id="swap-btn">Swap</button>
</div>

  </div>
  <footer>
    &copy; 2025 Five Exchange | Powered by TON
  </footer>  <script>
    document.getElementById('swap-btn').addEventListener('click', () => {
      alert('Swap initiated (simulation only). Connect wallet feature pending.');
    });
  </script></body>
</html>
