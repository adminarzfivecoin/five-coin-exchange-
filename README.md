<!DOCTYPE html>
<html lang="fa">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Five Exchange - سواپ پیشرفته</title>
  <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;500;700&display=swap" rel="stylesheet" />
  <script src="https://unpkg.com/@tonconnect/ui@latest/dist/tonconnect-ui.min.js"></script>
  <style>
    body {
      margin: 0; font-family: 'Poppins', sans-serif;
      background-color: #111; color: #f5f5f5;
      direction: rtl;
    }
    header {
      background: #1a1a1a; padding: 20px;
      display: flex; justify-content: space-between; align-items: center;
      flex-wrap: wrap;
    }
    header img {
      height: 40px;
    }
    nav {
      display: flex; align-items: center; flex-wrap: wrap;
    }
    nav a {
      color: #ccc; margin: 0 10px; text-decoration: none; font-weight: 500; cursor: pointer;
    }
    #wallet-address {
      color: gold; font-size: 14px; margin-right: 12px;
      user-select: all;
      word-break: break-all;
    }
    .container {
      max-width: 480px; margin: 40px auto; padding: 20px;
      background-color: #1e1e1e; border-radius: 12px;
      box-shadow: 0 0 20px rgba(0,0,0,0.4);
    }
    .swap-box label {
      display: block; margin-bottom: 6px; color: #aaa;
    }
    .swap-box select, .swap-box input {
      width: 100%; padding: 12px; margin-bottom: 16px;
      border-radius: 8px; border: none; background-color: #2c2c2c; color: #fff;
      font-size: 16px;
    }
    button {
      width: 100%; padding: 14px; border: none;
      border-radius: 8px; background-color: gold;
      font-weight: bold; font-size: 18px; cursor: pointer;
      transition: background-color 0.3s ease;
    }
    button:disabled {
      background-color: #555;
      cursor: not-allowed;
    }
    button:hover:not(:disabled) {
      background-color: #d4af37;
    }
    #tx-status {
      margin-top: 12px; font-size: 14px; color: #eee;
      word-break: break-word;
      min-height: 1.5em;
    }
    #rate-info, #fee-info {
      margin-bottom: 12px; font-size: 14px; color: #ccc;
    }
    #history {
      margin-top: 30px; font-size: 14px; color: #ccc;
      max-height: 200px; overflow-y: auto;
      border: 1px solid #333; padding: 10px; border-radius: 8px;
      background-color: #2a2a2a;
    }
    #history h4 {
      margin-top: 0;
    }
    #confirm-dialog {
      position: fixed;
      top: 50%; left: 50%;
      transform: translate(-50%, -50%);
      background-color: #222;
      padding: 20px; border-radius: 12px;
      box-shadow: 0 0 25px rgba(255, 215, 0, 0.8);
      z-index: 1000;
      max-width: 350px;
      display: none;
      color: #fff;
      text-align: center;
    }
    #confirm-dialog button {
      margin-top: 20px;
      width: 48%;
      font-size: 16px;
      border-radius: 8px;
      border: none;
      cursor: pointer;
    }
    #confirm-yes {
      background-color: gold;
      font-weight: bold;
      color: #000;
    }
    #confirm-no {
      background-color: #555;
      color: #eee;
    }
    #overlay {
      position: fixed; top: 0; left: 0; width: 100%; height: 100%;
      background: rgba(0,0,0,0.7);
      z-index: 999;
      display: none;
    }

    /* Responsive */
    @media (max-width: 520px) {
      header {
        justify-content: center;
      }
      nav a {
        margin: 8px;
      }
      .container {
        margin: 20px 10px;
      }
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
    <a id="connect-btn">اتصال کیف پول</a>
    <span id="wallet-address" title="آدرس کیف پول"></span>
  </nav>
</header>

<div class="container">
  <h2 style="text-align:center; margin-bottom:20px;">سواپ توکن‌ها</h2>

  <div id="rate-info">نرخ تبدیل: در حال بارگذاری...</div>
  <div id="fee-info">کارمزد تخمینی: -- TON</div>

  <div class="swap-box">
    <label for="from-token">از توکن:</label>
    <select id="from-token">
      <option value="TON">TON</option>
      <option value="FIVE">FIVE</option>
    </select>

    <label for="from-amount">مقدار:</label>
    <input type="number" id="from-amount" placeholder="مقدار را وارد کنید" min="0" step="any" />

    <label for="to-token">به توکن:</label>
    <select id="to-token">
      <option value="FIVE">FIVE</option>
      <option value="TON">TON</option>
    </select>

    <label for="to-amount">مقدار دریافت شده (تخمینی):</label>
    <input type="text" id="to-amount" disabled placeholder="--" />
  </div>

  <button id="swap-btn" disabled>سواپ کن</button>
  <div id="tx-status"></div>

  <div id="history">
    <h4>تاریخچه تراکنش‌ها:</h4>
    <ul id="history-list"></ul>
  </div>
</div>

<!-- پنجره تأیید تراکنش -->
<div id="overlay"></div>
<div id="confirm-dialog">
  <h3>تأیید تراکنش</h3>
  <p id="confirm-text"></p>
  <button id="confirm-yes">تأیید</button>
  <button id="confirm-no">انصراف</button>
</div>

<script>
  const FIVE_COIN_ADDRESS = "EQBV-msNe9hMxkxeUxUdhrt5xHpoJBVCAgAizzyhqmx56S8g";
  const estimatedFee = 0.01; // TON

  // نرخ تبدیل فرضی (در واقع باید از API گرفته شود)
  const exchangeRates = {
    "TON->FIVE": 100,
    "FIVE->TON": 0.0099
  };

  let txHistory = JSON.parse(localStorage.getItem('txHistory') || '[]');

  const fromToken = document.getElementById('from-token');
  const toToken = document.getElementById('to-token');
  const fromAmount = document.getElementById('from-amount');
  const toAmount = document.getElementById('to-amount');
  const swapBtn = document.getElementById('swap-btn');
  const txStatus = document.getElementById('tx-status');
  const rateInfo = document.getElementById('rate-info');
  const feeInfo = document.getElementById('fee-info');
  const historyList = document.getElementById('history-list');

  const overlay = document.getElementById('overlay');
  const confirmDialog = document.getElementById('confirm-dialog');
  const confirmText = document.getElementById('confirm-text');
  const confirmYes = document.getElementById('confirm-yes');
  const confirmNo = document.getElementById('confirm-no');

  // TON Connect UI
  const tonConnectUI = new TON_CONNECT_UI.TonConnectUI({
    manifestUrl: 'https://adminarzfivecoin.github.io/five-exchange/tonconnect-manifest.json',
    buttonRootId: 'connect-btn',
    uiPreferences: { theme: 'DARK' }
  });

  let connectedWallet = null;

  function updateToAmount() {
    let fToken = fromToken.value;
    let tToken = toToken.value;
    let amount = parseFloat(fromAmount.value);
    if (!amount || amount <= 0) {
      toAmount.value = "";
      swapBtn.disabled = true;
      rateInfo.textContent = "مقدار معتبر وارد کنید.";
      return;
    }
    if (fToken === tToken) {
      toAmount.value = amount.toFixed(9);
      swapBtn.disabled = true;
      rateInfo.textContent = "توکن‌های مشابه انتخاب شده‌اند.";
      return;
    }
    let rateKey = `${fToken}->${tToken}`;
    let rate = exchangeRates[rateKey];
    if (!rate) {
      toAmount.value = "";
      rateInfo.textContent = "نرخ تبدیل نامشخص است.";
      swapBtn.disabled = true;
      return;
    }
    let received = amount * rate;
    toAmount.value = received.toFixed(6);
    rateInfo.textContent = `نرخ تبدیل: 1 ${fToken} = ${rate} ${tToken}`;
    feeInfo.textContent = `کارمزد تخمینی: ${estimatedFee} TON (تقریبی)`;
    swapBtn.disabled = !connectedWallet; // فعال فقط در صورت اتصال کیف پول
  }

  // تغییر توکن‌ها باعث جلوگیری از انتخاب مشابه و بروزرسانی مقدار تخمینی
  from
