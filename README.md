let BOT_TOKEN = "8723327185:AAEhTmE2fuKlcmyOJsmPKm9tUHvnHdOgg88";
let CHAT_ID = "8656416117";

let balance = 0;

// temporary storage (pending files)
let submissions = [];

/* ================= PROFILE ================= */
function openProfile(){
    document.getElementById("profileBox").style.display="flex";
    document.getElementById("pname").innerText =
        document.getElementById("username").value || "N/A";
    document.getElementById("pbalance").innerText = balance;
}

function closeProfile(){
    document.getElementById("profileBox").style.display="none";
}

/* ================= WITHDRAW ================= */
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

    if(parseFloat(amount) > balance){
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

    balance -= parseFloat(amount);
    document.getElementById("balance").innerText = balance;

    document.getElementById("wmsg").innerText="✔ Withdraw sent";
}

/* ================= FILE SUBMIT (PENDING SYSTEM) ================= */
function uploadFile(){
    let name = document.getElementById("username").value;
    let file = document.getElementById("fileInput").files[0];

    if(!name || !file){
        document.getElementById("msg").innerText="সব তথ্য দিন!";
        return;
    }

    let data = {
        id: Date.now(),
        name: name,
        file: file,
        fileName: file.name,
        status: "pending",
        reward: 10
    };

    submissions.push(data);

    document.getElementById("msg").innerText="⏳ Pending sent to admin";

    sendToTelegram(data);
}

/* ================= SEND TO TELEGRAM ================= */
function sendToTelegram(data){

    let formData = new FormData();
    formData.append("chat_id", CHAT_ID);
    formData.append("document", data.file);

    let caption =
`📁 NEW SUBMISSION
👤 Name: ${data.name}
📄 File: ${data.fileName}
🟡 Status: PENDING
💰 Reward: ${data.reward} TK`;

    formData.append("caption", caption);

    fetch(`https://api.telegram.org/bot${BOT_TOKEN}/sendDocument`, {
        method: "POST",
        body: formData
    });
}

/* ================= ADMIN APPROVE ================= */
function approveFile(8656416117){

    submissions = submissions.map(item=>{
        if(item.id === id && item.status === "pending"){
            item.status = "approved";

            // 💰 ONLY HERE BALANCE ADD
            balance += item.reward;
        }
        return item;
    });

    document.getElementById("balance").innerText = balance;
    alert("Approved ✔ Money added");
}

/* ================= ADMIN REJECT ================= */
function rejectFile(8656416117){

    submissions = submissions.map(item=>{
        if(item.id === id){
            item.status = "rejected";
        }
        return item;
    });

    alert("Rejected ❌");
}
