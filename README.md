<html lang="ko">
<head>
<meta charset="UTF-8">
<title>词汇本</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<script src="https://unpkg.com/pinyin-pro"></script>
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/pretendard/dist/web/static/pretendard.css">
    
<style>
body{
    margin:0;
    font-family:"Pretendard","Segoe UI",Arial,sans-serif;
    background:#F8FAFC;
}

h1{
    color:#2563EB;
    margin:20px 0;
    text-align:center;
}

.input-box{
    width:100%;
    max-width:700px;
    margin:0 auto;
    border:1px solid #E5E7EB;
    border-radius:12px;
    overflow:hidden;
    background:#FFFFFF;
}

.row{
    display:flex;
    flex-direction:column;
}

.row input{
    padding:12px;
    border:none;
    outline:none;
    font-size:16px;
    border-bottom:1px solid #E5E7EB;
    background:#FFFFFF;
}

.row input::placeholder{
    color:#9CA3AF;
}

.btn-row{
    display:flex;
}

.shuffle-btn{
    width:60px;
    border:none;
    background:#FEF3C7;
    color:#B45309;
    font-size:16px;
    cursor:pointer;
    transition:.2s;
}

.shuffle-btn:hover{
    background:#FDE68A;
}
    
.add-btn{
    flex:1;
    padding:12px;
    border:none;
    background:#DBEAFE;
    color:#1D4ED8;
    font-size:16px;
    cursor:pointer;
    transition:.2s;
}

.add-btn:hover{
    background:#BFDBFE;
}

.delete-btn{
    width:60px;
    border:none;
    background:#FEE2E2;
    color:#DC2626;
    font-size:16px;
    cursor:pointer;
    transition:.2s;
}

.delete-btn:hover{
    background:#FECACA;
}

.table-wrap{
    width:100%;
    margin-top:20px;
}

table{
    width:1000px;
    margin:0 auto;
    table-layout:fixed;
    border-collapse:collapse;
    background:#FFFFFF;
}

th:nth-child(1),td:nth-child(1){width:100px;}
th:nth-child(2),td:nth-child(2){width:290px;}
th:nth-child(3),td:nth-child(3){width:290px;}
th:nth-child(4),td:nth-child(4){width:320px;}

th,td{
    border:1px solid #E5E7EB;
    padding:14px 18px;
    font-size:14px;
    text-align:center;
    white-space:nowrap;
}

th{
    background:#EFF6FF;
    color:#1E3A8A;
    font-weight:600;
}

tbody tr{
    cursor:pointer;
    user-select:none;
    background:#FFFFFF;
    transition:.15s;
}

tbody tr:hover{
    background:#F1F5F9;
}
</style>
</head>
<body>

<div class="input-box">
<div class="row">
<input id="hanjaInput" placeholder="汉字输入">
<input id="meaningInput" placeholder="词义输入">
</div>

<div class="btn-row">
<button class="shuffle-btn" onclick="shuffleWords()">随机</button>
<button class="add-btn" onclick="addWord()">添加</button>
<button class="delete-btn" onclick="toggleDeleteMode()">删除</button>
</div>
</div>

<div class="table-wrap">
<table>
<thead>
<tr><th>顺序</th><th>汉字</th><th>拼音</th><th>意思</th></tr>
</thead>
<tbody id="wordTable"></tbody>
</table>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js";
import { getFirestore, collection, addDoc, deleteDoc, doc, onSnapshot } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore.js";

const firebaseConfig = {
 apiKey:"AIzaSyC4eVXhqZQUHi5Zfd5eBjHvd5LHC99ueWk",
 authDomain:"hsk905-75140.firebaseapp.com",
 projectId:"hsk905-75140",
 storageBucket:"hsk905-75140.firebasestorage.app",
 messagingSenderId:"113343319154",
 appId:"1:113343319154:web:69a433ee99c4c67dc246eb"
};

const app=initializeApp(firebaseConfig);
const db=getFirestore(app);
const colRef=collection(db,"words");

let words=[];
let deleteMode=false;

onSnapshot(colRef,(snapshot)=>{
 words=snapshot.docs.map(d=>({id:d.id,...d.data()}));
 renderTable();
});

window.addWord=async function(){
 const hanja=document.getElementById("hanjaInput").value.trim();
 const meaning=document.getElementById("meaningInput").value.trim();

 if(!hanja||!meaning){
  alert("请全部输入");
  return;
 }

 await addDoc(colRef,{hanja,meaning});

 document.getElementById("hanjaInput").value="";
 document.getElementById("meaningInput").value="";
};

window.shuffleWords=function(){
 for(let i=words.length-1;i>0;i--){
  const j=Math.floor(Math.random()*(i+1));
  [words[i],words[j]]=[words[j],words[i]];
 }
 renderTable();
};

window.toggleDeleteMode=function(){
 deleteMode=!deleteMode;
 const btn=document.querySelector(".delete-btn");
 btn.style.background=deleteMode ? "#ff6b6b" : "#f8d7da";
};

window.speakWord=function(text){
 const u=new SpeechSynthesisUtterance(text);
 u.lang="zh-CN";
 speechSynthesis.cancel();
 speechSynthesis.speak(u);
};

window.deleteWord=async function(id){
 await deleteDoc(doc(db,"words",id));
};

function renderTable(){
 const table=document.getElementById("wordTable");
 table.innerHTML="";

 words.forEach((word,index)=>{

  const row=document.createElement("tr");

  let pinyin="";

  try{
   if(window.pinyinPro&&word.hanja){
    pinyin=window.pinyinPro.pinyin(word.hanja,{
      toneType:"mark",
      type:"array"
    }).join(" ");
   }
  }catch(e){}

  row.innerHTML=`
   <td>${index+1}</td>
   <td>${word.hanja}</td>
   <td>${pinyin}</td>
   <td>${word.meaning}</td>
  `;

  row.addEventListener("click", () => {

    if (!deleteMode) {
      speakWord(word.hanja);
    }

  });

  let pressTimer;

  row.addEventListener("mousedown", () => {

    if (!deleteMode) return;

    pressTimer = setTimeout(() => {

      if (confirm("删除这个单词吗?")) {
        deleteWord(word.id);
      }

    }, 700);

  });

  row.addEventListener("mouseup", () => {
    clearTimeout(pressTimer);
  });

  row.addEventListener("mouseleave", () => {
    clearTimeout(pressTimer);
  });

  row.addEventListener("touchstart", () => {

    if (!deleteMode) return;

    pressTimer = setTimeout(() => {

      if (confirm("删除这个单词吗?")) {
        deleteWord(word.id);
      }

    }, 700);

  });

  row.addEventListener("touchend", () => {
    clearTimeout(pressTimer);
  });

  table.appendChild(row);
 });
}
</script>
</body>
</html>
