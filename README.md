<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>FIVE Exchange</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <script src="https://unpkg.com/@tonconnect/sdk@latest"></script>
  <style>
    body {
      font-family: Arial, sans-serif;
      background-color: #121212;
      color: #ffffff;
      margin: 0;
      padding: 20px;
    }

    .container {
      max-width: 900px;
      margin: auto;
    }

    h1 {
      color: #03DAC6;
      text-align: center;
      margin-bottom: 30px;
    }

    .chart-container {
      background-color: #1e1e1e;
      padding: 20px;
      border-radius: 12px;
      margin-bottom: 40px;
    }

    .swap-form, .history {
      background-color: #1e1e1e;
      padding: 20px;
      border-radius: 12px;
      margin-bottom: 40px;
    }

    .swap-form label, .history h2 {
      display: block;
      margin: 12px 0 6px;
    }

    input, select, button {
      padding: 10px;
      margin-bottom: 15px;
      width: 100%;
      border: none;
      border-radius: 6px;
    }

    input, select {
      background-color: #2a2a2a;
      color: white;
    }

    button {
      background-color: #03DAC6;
      color: #000;
      cursor: pointer;
      font-weight: bold;
    }

    button:hover {
      background-color: #01c1b0;
    }

    .ton-connect {
      text-align: center;
      margin-top: 20px;
    }

    .history ul {
      list-style: none;
      padding: 0;
    }

    .history li {
      background: #2a2a2a;
      padding: 10px;
      border-radius: 6px;
      margin-bottom: 10px;
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>FIVE Exchange</h1>

    <div class="chart-container">
      <canvas id="priceChart"></canvas>
    </div>

    <div class="swap-form">
      <h2>Swap Tokens</h2>
      <label for="tokenFrom">From Token:</label>
      <select id="tokenFrom">
        <option value="FIVE">FIVE</option>
        <option value="TON">TON</option>
      </select>

      <label for="tokenTo">To Token:</label>
      <select id="tokenTo">
        <option value="TON">TON</option>
        <option value="FIVE">FIVE</option>
      </select>

      <label for="amount">Amount:</label>
      <input type="number" id="amount" placeholder="Enter amount" />

      <button onclick="calculateRate()">Get Rate</button>
      <div id="rateResult"></div>

      <button onclick="swapTokens()">Swap</button>
    </div>

    <div class="ton-connect">
      <button id="connectWallet">Connect TON Wallet</button>
      <p id="walletAddress">Not connected</p>
    </div>

    <div class="history">
      <h2>Transaction History</h2>
      <ul id="transactionHistory"></ul>
    </div>
  </div>

  <script>
    // Sample Chart.js data
    const ctx = document.getElementById('priceChart').getContext('2d');
    new Chart(ctx, {
      type: 'line',
      data: {
        labels: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
        datasets: [{
          label: 'FIVE Price (USD)',
          data: [0.5, 0.6, 0.58, 0.62, 0.65, 0.66, 0.7],
          backgroundColor: 'rgba(3, 218, 198, 0.2)',
          borderColor: '#03DAC6',
          borderWidth: 2
        }]
      },
      options: {
        scales: {
          x: { ticks: { color: '#ffffff' }},
          y: { ticks: { color: '#ffffff' }}
        },
        plugins: {
          legend: {
            labels: { color: '#ffffff' }
          }
        }
      }
    });

    // TonConnect integration
    const tonConnect = new TonConnectSDK.TonConnect();
    const connectWalletBtn = document.getElementById('connectWallet');
    const walletAddress = document.getElementById('walletAddress');

    connectWalletBtn.onclick = async () => {
      try {
        await tonConnect.connectWallet();
        const account = await tonConnect.account();
        walletAddress.textContent = `Connected: ${account.address}`;
      } catch (error) {
        console.error('Connection error:', error);
        walletAddress.textContent = 'Connection failed';
      }
    };

    // Token swap logic (mocked)
    function calculateRate() {
      const from = document.getElementById('tokenFrom').value;
      const to = document.getElementById('tokenTo').value;
      const amount = parseFloat(document.getElementById('amount').value);
      let rate;

      if (from === to) {
        document.getElementById('rateResult').textContent = "Cannot swap the same token.";
        return;
      }

      // Mock exchange rates
      if (from === "FIVE" && to === "TON") rate = 0.1;
      else if (from === "TON" && to === "FIVE") rate = 10;

      const result = amount * rate;
      document.getElementById('rateResult').textContent = `You will receive ${result} ${to}`;
    }

    function swapTokens() {
      const from = document.getElementById('tokenFrom').value;
      const to = document.getElementById('tokenTo').value;
      const amount = parseFloat(document.getElementById('amount').value);

      if (from === to || isNaN(amount) || amount <= 0) {
        alert("Invalid swap parameters.");
        return;
      }

      const timestamp = new Date().toLocaleString();
      const li = document.createElement("li");
      li.textContent = `${timestamp}: Swapped ${amount} ${from} to ${to}`;
      document.getElementById('transactionHistory').appendChild(li);
      alert("Swap simulated successfully!");
    }
  </script>
</body>
</html>
