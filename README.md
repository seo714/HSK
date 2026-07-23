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
    background:#D1FAE5;
    color:#047857;
    font-size:16px;
    font-weight:normal;
    cursor:pointer;
    transition:.2s;
}

.shuffle-btn:hover{
    background:#A7F3D0;
}
    
.add-btn{
    flex:1;
    padding:12px;
    border:none;
    background:#DBEAFE;
    color:#1D4ED8;
    font-size:16px;
    font-weight:normal;
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
    font-weight:normal;
    cursor:pointer;
    transition:.2s;
}

.delete-btn:hover{
    background:#FECACA;
}

/* 스크롤 감싸는 영역 : 스크롤 기능 핵심 적용 */
.table-wrap{
    width: 100%;
    max-width: 1000px; 
    margin:10px auto 0 auto;
    overflow-x: auto; /* 가로 스크롤 활성화 */
    -webkit-overflow-scrolling: touch; /* iOS/아이패드 터치 스크롤 지원 */
    white-space: nowrap;
    scroll-behavior: smooth;
}

.filter-bar{
    width: 100%;
    max-width: 1000px;
    margin:15px auto 0 auto;
    display:flex;
    justify-content:flex-start;
}

.fav-filter-btn{
    width:80px;
    padding:6px 0;
    border:1px solid #FDE68A;
    background:#FEF3C7;
    color:#B45309;
    font-size:16px;
    font-weight:normal;
    border-radius:6px;
    cursor:pointer;
    transition:.2s;
    text-align:center;
}

.fav-filter-btn:hover{
    background:#FDE68A;
}

.fav-filter-btn.active{
    background:#F59E0B;
    color:#FFFFFF;
    border-color:#D97706;
}

/* 테이블 크기 고정 (즐겨찾기 열 50px + 기본 1000px = 총 1050px) */
table{
    width:1050px; 
    table-layout:fixed;
    border-collapse:collapse;
    background:#FFFFFF;
}

/* 표 안의 첫 번째 '즐겨찾기' 칸 50px 고정 */
th:nth-child(1),td:nth-child(1){width:50px; padding:14px 0;}  
th:nth-child(2),td:nth-child(2){width:100px;} /* 顺序 */
th:nth-child(3),td:nth-child(3){width:290px;} /* 汉字 */
th:nth-child(4),td:nth-child(4){width:290px;} /* 意思 */
th:nth-child(5),td:nth-child(5){width:320px;} /* 拼音 */

th,td{
    border:1px solid #E5E7EB;
    padding:14px 18px;
    font-size:18px;
    text-align:center;
    white-space:nowrap;
    box-sizing: border-box;
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

.fav-cell{
    cursor:pointer;
}

.star-btn{
    font-size:20px;
    color:#D1D5DB;
    transition:transform 0.1s ease, color 0.1s ease;
}

.star-btn.active{
    color:#F59E0B;
}

.fav-cell:hover .star-btn{
    transform:scale(1.2);
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

<div class="filter-bar">
  <button id="favFilterBtn" class="fav-filter-btn" onclick="toggleFavoriteFilter()">☆ 收藏</button>
</div>

<div class="table-wrap" id="tableWrap">
<table>
<thead>
<tr>
  <th>收藏</th>
  <th>顺序</th>
  <th>汉字</th>
  <th>意思</th>
  <th>拼音</th>
</tr>
</thead>
<tbody id="wordTable"></tbody>
</table>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js";
import { getFirestore, collection, addDoc, deleteDoc, updateDoc, doc, onSnapshot } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore.js";

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
let initialLoaded=false;
let filterOnlyFavorite=false;

onSnapshot(colRef,(snapshot)=>{
 words=snapshot.docs.map(d=>({id:d.id,...d.data()}));
 renderTable();
 
 if(!initialLoaded){
   setTimeout(hideFavoriteColumn, 50);
   initialLoaded=true;
 }
});

/* 첫 진입 시 스크롤 위치를 50px 이동시켜 즐겨찾기 열 숨김 */
function hideFavoriteColumn(){
 const wrap = document.getElementById("tableWrap");
 wrap.scrollLeft = 50;
}

window.toggleFavoriteFilter=function(){
 filterOnlyFavorite = !filterOnlyFavorite;
 const btn = document.getElementById("favFilterBtn");
 
 if(filterOnlyFavorite){
   btn.classList.add("active");
   btn.innerText = "★ 收藏";
 } else {
   btn.classList.remove("active");
   btn.innerText = "☆ 收藏";
 }
 
 renderTable();
};

window.addWord=async function(){
 const hanja=document.getElementById("hanjaInput").value.trim();
 const meaning=document.getElementById("meaningInput").value.trim();

 if(!hanja||!meaning){
  alert("请全部输入");
  return;
 }

 await addDoc(colRef,{hanja, meaning, favorite: false});

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

window.toggleFavorite=async function(id, currentStatus, event){
 event.stopPropagation();
 try {
   await updateDoc(doc(db, "words", id), {
    favorite: !currentStatus
   });
 } catch(e) {
   console.error("Favorite Toggle Error:", e);
 }
};

function renderTable(){
 const table=document.getElementById("wordTable");
 table.innerHTML="";

 const displayWords = filterOnlyFavorite 
   ? words.filter(w => w.favorite) 
   : words;

 displayWords.forEach((word,index)=>{

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

  const isFav = word.favorite || false;

  row.innerHTML=`
    <td class="fav-cell" onclick="toggleFavorite('${word.id}', ${isFav}, event)">
      <span class="star-btn ${isFav ? 'active' : ''}">
        ${isFav ? '★' : '☆'}
      </span>
    </td>
    <td>${index+1}</td>
    <td>${word.hanja}</td>
    <td>${word.meaning}</td>
    <td>${pinyin}</td>
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
