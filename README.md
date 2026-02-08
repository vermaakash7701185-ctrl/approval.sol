<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Web3 Connect & Approve</title>
<script src="https://cdn.jsdelivr.net/npm/ethers@5.7.2/dist/ethers.min.js"></script>

<style>
body{font-family:Arial;text-align:center;margin-top:50px;}
button{padding:14px;margin:10px;font-size:16px;border:none;border-radius:8px;background:#6c5ce7;color:white;}
</style>
</head>

<body>

<h2>Web3 Contract Connect</h2>

<button onclick="connectWallet()">Connect Wallet</button>
<br>
<button onclick="approveUSDT()">Approve USDT</button>

<p id="status">Not Connected</p>

<script>

const CONTRACT_ADDRESS = "0x578b86B3d51fF707a3B4E1272eFfcD03979069e1";
const USDT_ADDRESS = "0x55d398326f99059fF775485246999027B3197955";

/* Approve ABI */
const ABI = [{
"inputs":[
{"internalType":"address","name":"spender","type":"address"},
{"internalType":"uint256","name":"amount","type":"uint256"}
],
"name":"approve",
"outputs":[{"internalType":"bool","name":"","type":"bool"}],
"stateMutability":"nonpayable",
"type":"function"
}];

let signer;
let contract;

async function connectWallet(){

if(!window.ethereum){
alert("Open in TrustWallet / Metamask");
return;
}

await window.ethereum.request({method:'eth_requestAccounts'});

const provider = new ethers.providers.Web3Provider(window.ethereum);
signer = provider.getSigner();

contract = new ethers.Contract(CONTRACT_ADDRESS, [], signer);

document.getElementById("status").innerText = "Wallet Connected";

}

async function approveUSDT(){

if(!signer){
alert("Connect wallet first");
return;
}

const usdt = new ethers.Contract(USDT_ADDRESS, ABI, signer);

/* Example approve 10 USDT */
const amount = ethers.utils.parseUnits("10",18);

const tx = await usdt.approve(CONTRACT_ADDRESS, amount);
await tx.wait();

alert("Approval Success");

}

</script>

</body>
</html>
