<html lang="ko">
<head>
<meta charset="UTF-8">
<title>词汇本</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<script src="https://unpkg.com/pinyin-pro"></script>

<style>
/* ====== 브라우저별 글자 크기 강제 확대 방지 (깃허브 크기 깨짐 해결) ====== */
html {
  -webkit-text-size-adjust: 100%;
  text-size-adjust: 100%;
}

body {
  margin: 0;
  padding: 20px 0; /* 좌우 패딩을 없애고 위아래 여백만 제공 */
  font-family: Arial, sans-serif;
  background: #f5f7fa;
  box-sizing: border-box;
}

h1 { color: #007BFF; margin: 20px 0; text-align: center; }

/* ====== 입력창 스타일 (너비를 컴팩트하게 제한) ====== */
.input-box {
  width: 90%;       /* 모바일 화면 기준 너비 */
  max-width: 600px; /* PC 화면에서 입력창 최대 크기 (테이블보다 좁게) */
  margin: 0 auto 30px auto; /* 아래 테이블과의 여백 */
  border: 1px solid #ccc;
  border-radius: 10px;
  overflow: hidden;
  background: #fff;
  box-shadow: 0 2px 8px rgba(0,0,0,0.05);
}
.row { display: flex; flex-direction: column; }
.row input { padding: 14px; border: none; outline: none; font-size: 16px; border-bottom: 1px solid #eee; }
.btn-row { display: flex; }
.shuffle-btn { width: 70px; border: none; background: #ffe599; font-size: 16px; cursor: pointer; }
.add-btn { flex: 1; padding: 14px; border: none; background: #cfe2ff; font-size: 16px; cursor: pointer; font-weight: bold; }
.delete-btn { width: 70px; border: none; background: #f8d7da; font-size: 16px; cursor: pointer; }

/* ====== 테이블 스타일 (입력창보다 확실하게 좌우로 튀어나옴) ====== */
.table-wrap {
  width: 95%;        /* 입력창(90%)보다 무조건 더 넓게 가로를 차지 */
  max-width: 1100px; /* PC 화면에서 테이블이 펼쳐질 최대 너비 */
  margin: 0 auto;
  overflow-x: auto;  /* 아주 작은 화면에서 터짐 방지 스크롤 */
}

table {
  width: 100%; 
  border-collapse: collapse;
  table-layout: fixed; /* 컬럼 너비를 비율대로 절대 고정 */
}

/* 컬럼별 너비 비율 고정 */
th:nth-child(1), td:nth-child(1) { width: 12%; } /* 순서 */
th:nth-child(2), td:nth-child(2) { width: 28%; } /* 한자 */
th:nth-child(3), td:nth-child(3) { width: 30%; } /* 拼音 */
th:nth-child(4), td:nth-child(4) { width: 30%; } /* 뜻 */

th, td {
  border: 1px solid #ddd;
  padding: 14px 8px;
  font-size: 14px;
  text-align: center;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis; /* 셀 공간보다 글자가 길어지면 ... 처리 */
}
th { background: #f1f1f1; font-weight: bold; }
tbody tr { cursor: pointer; user-select: none; }
tbody tr:hover { background: #f8f9ff; }

/* ====== PC 전용 레이아웃 최적화 ====== */
@media (min-width: 600px) {
  .row { flex-direction: row; } /* PC에선 입력창이 가로로 나란히 배치 */
  .row input { flex: 1; border-bottom: none; }
  .row input:first-child { border-right: 1px solid #eee; }
  
  /* 입력창(600px)과 테이블(1100px)의 너비 차이를 주어 좌우로 튀어나온 연출 극대화 */
  .input-box { max-width: 600px; }
  .table-wrap { max-width: 1100px; } 
}
</style>
</head>

<body>

<h1>词汇本</h1>

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
    if (!deleteMode) speakWord(word.hanja);
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

  row.addEventListener("mouseup", () => clearTimeout(pressTimer));
  row.addEventListener("mouseleave", () => clearTimeout(pressTimer));

  row.addEventListener("touchstart", () => {
    if (!deleteMode) return;

    pressTimer = setTimeout(() => {
      if (confirm("删除这个单词吗?")) {
        deleteWord(word.id);
      }
    }, 700);
  });

  row.addEventListener("touchend", () => clearTimeout(pressTimer));

  table.appendChild(row);
});
}
</script>

</body>
</html>
