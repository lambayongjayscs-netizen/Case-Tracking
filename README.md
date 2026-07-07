<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Case Tracking System</title>

<style>
*{
    margin:0;
    padding:0;
    box-sizing:border-box;
    font-family:Arial,Helvetica,sans-serif;
}

body{
    background:#f2f4f8;
    padding:30px;
}

.container{
    max-width:1200px;
    margin:auto;
}

h1{
    text-align:center;
    margin-bottom:20px;
    color:#003366;
}

.form{
    background:white;
    padding:20px;
    border-radius:10px;
    margin-bottom:25px;
    box-shadow:0 0 10px rgba(0,0,0,.1);
}

.form input,
.form textarea,
.form select{
    width:100%;
    padding:10px;
    margin-top:10px;
    margin-bottom:15px;
    border:1px solid #ccc;
    border-radius:5px;
}

button{
    background:#0077cc;
    color:white;
    border:none;
    padding:12px 20px;
    cursor:pointer;
    border-radius:5px;
}

button:hover{
    background:#005fa3;
}

.case{
    background:white;
    margin-bottom:20px;
    padding:20px;
    border-left:6px solid #0077cc;
    border-radius:8px;
    box-shadow:0 0 8px rgba(0,0,0,.08);
}

.case h2{
    color:#003366;
}

.info{
    margin-top:8px;
    color:#555;
}

.timeline{
    background:#f7f7f7;
    padding:10px;
    border-radius:5px;
    margin-top:15px;
}

.timeline ul{
    margin-left:20px;
}

.update-box{
    margin-top:15px;
}

.update-box textarea{
    width:100%;
    height:70px;
    padding:10px;
}

.status{
    font-weight:bold;
    color:green;
}
</style>

</head>
<body>

<div class="container">

<h1>Case Tracking System</h1>

<div class="form">

<input type="text" id="caseNumber" placeholder="Case Number">

<input type="text" id="customer" placeholder="Customer Name">

<input type="text" id="agent" placeholder="Assigned Agent">

<select id="status">
<option>Open</option>
<option>Pending</option>
<option>Waiting for Customer</option>
<option>Escalated</option>
<option>Resolved</option>
<option>Closed</option>
</select>

<textarea id="notes" placeholder="Initial Notes"></textarea>

<button onclick="createCase()">Create Case</button>

</div>

<div id="caseList"></div>

</div>

<script>

let cases = JSON.parse(localStorage.getItem("cases")) || [];

function saveCases(){
    localStorage.setItem("cases",JSON.stringify(cases));
}

function createCase(){

    let newCase={
        id:Date.now(),
        caseNumber:document.getElementById("caseNumber").value,
        customer:document.getElementById("customer").value,
        agent:document.getElementById("agent").value,
        created:new Date().toLocaleString(),
        updated:new Date().toLocaleString(),
        status:document.getElementById("status").value,
        history:[
            document.getElementById("notes").value
        ]
    };

    cases.unshift(newCase);

    saveCases();

    renderCases();

    document.getElementById("caseNumber").value="";
    document.getElementById("customer").value="";
    document.getElementById("agent").value="";
    document.getElementById("notes").value="";
}

function renderCases(){

    let list=document.getElementById("caseList");

    list.innerHTML="";

    cases.forEach((item,index)=>{

        let historyHTML="";

        item.history.forEach(step=>{
            historyHTML+=`<li>${step}</li>`;
        });

        list.innerHTML+=`

        <div class="case">

        <h2>Case #${item.caseNumber}</h2>

        <div class="info"><b>Customer:</b> ${item.customer}</div>

        <div class="info"><b>Assigned Agent:</b> ${item.agent}</div>

        <div class="info"><b>Date Created:</b> ${item.created}</div>

        <div class="info"><b>Last Updated:</b> ${item.updated}</div>

        <div class="info"><b>Status:</b>
        <span class="status">${item.status}</span>
        </div>

        <div class="timeline">

        <b>Steps Taken</b>

        <ul>
        ${historyHTML}
        </ul>

        </div>

        <div class="update-box">

        <textarea id="update${index}" placeholder="Add new update..."></textarea>

        <br><br>

        <button onclick="addUpdate(${index})">Add Update</button>

        </div>

        </div>

        `;

    });

}

function addUpdate(index){

    let text=document.getElementById("update"+index).value;

    if(text=="") return;

    cases[index].history.push(text);

    cases[index].updated=new Date().toLocaleString();

    saveCases();

    renderCases();

}

renderCases();

</script>

</body>
</html>
