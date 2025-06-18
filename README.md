<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Five Exchange - Swap</title>
  <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;500;700&display=swap" rel="stylesheet" />
  <script src="https://unpkg.com/@tonconnect/ui@latest/dist/tonconnect-ui.min.js"></script>
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
    nav {
      display: flex;
      align-items: center;
    }
    nav a {
      color: #ccc;
      margin: 0 12px;
      text-decoration: none;
      font-weight: 500;
      cursor: pointer;
    }
    #wallet-address {
      color: gold;
      font-size: 14px;
      margin-left: 12px;
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
      <a>Swap</a>
      <a>Pools</a>
      <a>Tokens</a>
      <a>Docs</a>
      <a id="connect-btn">Connect Wallet</a>
      <span id="wallet-address"></span>
    </nav>
  </header>

  <div class="container">
    <h2 style="text-align: center; margin-bottom: 30px;">Swap TON → FIVE</h2>
    <div class="swap-box">
      <label for="from-amount">Amount (TON)</label>
      <input type="number" placeholder="Amount" id="from-amount" />
      <button id="swap-btn">Send TON & Receive FIVE</button>
    </div>
  </div>

  <footer>
    &copy; 2025 Five Exchange | Powered by TON
  </footer>

  <script>
    const tonConnectUI = new TON_CONNECT_UI.TonConnectUI({
      manifestUrl: 'https://adminarzfivecoin.github.io/five-exchange/tonconnect-manifest.json',
      buttonRootId: 'connect-btn',
      uiPreferences: { theme: 'DARK' }
    });

    tonConnectUI.onStatusChange(wallet => {
      if (wallet && wallet.account) {
        document.getElementById("wallet-address").textContent = "Wallet: " + wallet.account.address;
      }
    });

    document.getElementById('swap-btn').addEventListener('click', async () => {
      const amount = parseFloat(document.getElementById('from-amount').value);
      if (isNaN(amount) || amount <= 0) {
        alert("Please enter a valid amount of TON.");
        return;
      }

      const tonWallet = await tonConnectUI.connected;
      if (!tonWallet) {
        alert("Connect your wallet first.");
        return;
      }

      const nanoTon = BigInt(Math.floor(amount * 1e9)).toString();

      try {
        await tonConnectUI.sendTransaction({
          validUntil: Math.floor(Date.now() / 1000) + 300, // اعتبار ۵ دقیقه
          messages: [{
            address: "EQBV-msNe9hMxkxeUxUdhrt5xHpoJBVCAgAizzyhqmx56S8g", // آدرس FIVE COIN
            amount: nanoTon,
            payload: null
          }]
        });
        alert("TON sent successfully! You'll receive FIVE shortly.");
      } catch (err) {
        console.error(err);
        alert("Transaction cancelled or failed.");
      }
    });
  </script>
</body>
</html>
