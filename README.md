<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Web3 Connect & Approve (BSC)</title>

  <!-- Try CDN first -->
  <script src="https://cdn.jsdelivr.net/npm/ethers@5.7.2/dist/ethers.min.js"></script>

  <style>
    body { font-family: Arial; text-align:center; margin-top:60px; }
    button { padding:14px 18px; margin:10px; font-size:16px; border:none; border-radius:8px; background:#6c5ce7; color:#fff; }
    .secondary { background:#1f6feb; }
    .status { margin-top:14px; font-size:14px; }
  </style>
</head>
<body>

<h2>Web3 Contract Connect (BSC)</h2>

<button onclick="connectWallet()">Connect Wallet</button>
<button class="secondary" onclick="approveUSDT()">Approve 10 USDT</button>

<div class="status" id="status">Status: Not connected</div>

<script>
/* ===== CONFIG ===== */
const CONTRACT_ADDRESS = "0x578b86B3d51fF707a3B4E1272eFfcD03979069e1"; // your contract (spender)
const USDT_ADDRESS     = "0x55d398326f99059fF775485246999027B3197955"; // USDT (BEP20)
const BSC_CHAIN_ID     = "0x38";

/* Approve ABI */
const APPROVE_ABI = [{
  "inputs":[
    {"internalType":"address","name":"spender","type":"address"},
    {"internalType":"uint256","name":"amount","type":"uint256"}
  ],
  "name":"approve",
  "outputs":[{"internalType":"bool","name":"","type":"bool"}],
  "stateMutability":"nonpayable",
  "type":"function"
}];

let provider = null;
let signer   = null;

/* Helper */
function setStatus(t){ document.getElementById('status').innerText = t; }

/* ===== CONNECT ===== */
async function connectWallet(){
  try {
    if (typeof ethers === "undefined") {
      alert("ethers library not loaded. Please refresh once.");
      return;
    }
    if (!window.ethereum) {
      alert("Open this page in Trust Wallet / MetaMask DApp Browser.");
      return;
    }

    await window.ethereum.request({ method: 'eth_requestAccounts' });

    // Optional: check BSC
    const chainId = await window.ethereum.request({ method: 'eth_chainId' });
    if (chainId !== BSC_CHAIN_ID) {
      alert("Please switch wallet network to BSC Mainnet.");
      return;
    }

    provider = new ethers.providers.Web3Provider(window.ethereum, "any");
    signer   = provider.getSigner();

    const addr = await signer.getAddress();
    setStatus("Connected: " + addr.slice(0,6) + "..." + addr.slice(-4));

  } catch (e) {
    alert("Connect error: " + e.message);
  }
}

/* ===== APPROVE ===== */
async function approveUSDT(){
  try {
    if (!signer) {
      alert("Connect wallet first.");
      return;
    }

    const usdt = new ethers.Contract(USDT_ADDRESS, APPROVE_ABI, signer);
    const amount = ethers.utils.parseUnits("10", 18); // 10 USDT

    setStatus("Sending approve tx… Confirm in wallet.");
    const tx = await usdt.approve(CONTRACT_ADDRESS, amount);
    await tx.wait();

    setStatus("Approve successful ✔");

  } catch (e) {
    alert("Approve error: " + e.message);
  }
}
</script>

</body>
</html>
