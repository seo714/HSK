<html lang="ko">
<head>
<meta charset="UTF-8">
<title>词汇本</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<script src="https://unpkg.com/pinyin-pro"></script>
<style>
body{margin:0;font-family:Arial,sans-serif;background:#f5f7fa;}
h1{color:#007BFF;margin:20px 0;text-align:center;}
.input-box{width:100%;max-width:700px;margin:0 auto;border:1px solid #ccc;border-radius:10px;overflow:hidden;background:#fff;}
.row{display:flex;flex-direction:column;}
.row input{padding:12px;border:none;outline:none;font-size:16px;border-bottom:1px solid #eee;}
.btn-row{display:flex;}
.shuffle-btn{width:60px;border:none;background:#ffe599;font-size:16px;cursor:pointer;}
.add-btn{flex:1;padding:12px;border:none;background:#cfe2ff;font-size:16px;cursor:pointer;}
.delete-btn{width:60px;border:none;background:#f8d7da;font-size:16px;cursor:pointer;}
.table-wrap{width:1000%;margin-top:20px;}
table{width:1000px;margin:0 auto;table-layout:fixed;}
th:nth-child(1),td:nth-child(1){width:200px;}
th:nth-child(2),td:nth-child(2){width:240px;}
th:nth-child(3),td:nth-child(3){width:260px;}
th:nth-child(4),td:nth-child(4){width:300px;}
th,td{border:1px solid #ddd;padding:14px 18px;font-size:14px;text-align:center;white-space:nowrap;}
th{background:#f1f1f1;}
tbody tr{cursor:pointer;user-select:none;}
tbody tr:hover{background:#f8f9ff;}
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
