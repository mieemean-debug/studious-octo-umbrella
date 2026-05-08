<!DOCTYPE html>
<html lang="bn">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Earning File System</title>

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

.file-item{
    background:#0f172a;
    padding:10px;
    margin-top:10px;
    border-radius:8px;
    border:1px solid #334155;
}

.status-pending{color:orange;}
.status-approved{color:lime;}
.status-rejected{color:red;}

.balance{
    font-size:20px;
    color:lime;
}
</style>
</head>

<body>

<div class="container">

<!-- USER -->
<div class="card">
<h3>👤 User Profile</h3>
<input type="text" id="username" placeholder="আপনার নাম">
<p>💰 Balance: <span class="balance" id="balance">0</span> টাকা</p>
</div>

<!-- UPLOAD -->
<div class="card">
<h3>📁 File Submit</h3>
<input type="file" id="fileInput">
<button onclick="uploadFile()">Submit</button>
<p id="msg"></p>
</div>

<!-- HISTORY -->
<div class="card">
<h3>📜 History</h3>
<div id="history"></div>
</div>

<!-- ADMIN -->
<div class="card">
<h3>🛠 Admin Panel</h3>
<div id="admin"></div>
</div>

</div>

<script>

let submissions = JSON.parse(localStorage.getItem("submissions")) || [];
let balance = parseFloat(localStorage.getItem("balance")) || 0;

function updateUI(){
    document.getElementById("balance").innerText = balance;
}

function uploadFile(){
    let name = document.getElementById("username").value;
    let file = document.getElementById("fileInput").files[0];

    if(!name || !file){
        document.getElementById("msg").innerText = "সব তথ্য দিন!";
        return;
    }

    let data = {
        id: Date.now(),
        name: name,
        fileName: file.name,
        status: "pending",
        reward: 10   // প্রতি ফাইল = 10 টাকা (pending থাকবে)
    };

    submissions.push(data);
    localStorage.setItem("submissions", JSON.stringify(submissions));

    document.getElementById("msg").innerText = "✔ সাবমিট হয়েছে";
    render();
}

function approve(id){
    submissions = submissions.map(item=>{
        if(item.id === id && item.status === "pending"){
            item.status = "approved";
            balance += item.reward;
        }
        return item;
    });

    localStorage.setItem("submissions", JSON.stringify(submissions));
    localStorage.setItem("balance", balance);

    render();
}

function reject(id){
    submissions = submissions.map(item=>{
        if(item.id === id){
            item.status = "rejected";
        }
        return item;
    });

    localStorage.setItem("submissions", JSON.stringify(submissions));
    render();
}

function render(){

    let history = document.getElementById("history");
    let admin = document.getElementById("admin");

    history.innerHTML = "";
    admin.innerHTML = "";

    submissions.forEach(item=>{

        // USER HISTORY
        history.innerHTML += `
        <div class="file-item">
            👤 ${item.name}<br>
            📄 ${item.fileName}<br>
            💰 Reward: ${item.reward} টাকা<br>
            🟡 Status: <span class="status-${item.status}">${item.status}</span>
        </div>`;

        // ADMIN PANEL
        admin.innerHTML += `
        <div class="file-item">
            👤 ${item.name}<br>
            📄 ${item.fileName}<br>
            💰 ${item.reward} টাকা<br>
            <button onclick="approve(${item.id})">Approve</button>
            <button onclick="reject(${item.id})" style="background:red;">Reject</button>
        </div>`;
    });

    updateUI();
}

render();

</script>

</body>
</html>
