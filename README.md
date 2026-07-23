<html lang="ko">
<head>
<meta charset="UTF-8">
<title>词汇本</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<script src="https://unpkg.com/pinyin-pro"></script>

<!-- SUIT 폰트 웹폰트 CDN -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/sun-api/SUIT/fonts/static/woff2/SUIT.css">
    
<style>
/* 모든 요소에 SUIT 폰트 기본 적용 */
* {
    font-family: 'SUIT', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif !important;
    box-sizing: border-box;
}

body{
    margin:0;
    padding-bottom: 80px;
    background:#F8FAFC;
    -webkit-tap-highlight-color: transparent;
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

/* 패드/모바일 드래그 스크롤 */
.table-wrap{
    width: 100%;
    max-width: 1000px; 
    margin:10px auto 0 auto;
    overflow-x: auto; 
    overflow-y: hidden;
    -webkit-overflow-scrolling: touch;
    touch-action: pan-x pan-y;
    white-space: nowrap;
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

table{
    width:1050px; 
    table-layout:fixed;
    border-collapse:collapse;
    background:#FFFFFF;
}

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

/* ===================================================
   🎵 우측 하단 노래 가사 플로팅 탭 스타일
   =================================================== */
.lyrics-fab {
    position: fixed;
    right: 20px;
    bottom: 20px;
    background: #2563EB;
    color: #FFFFFF;
    border: none;
    padding: 12px 20px;
    border-radius: 30px;
    font-size: 15px;
    font-weight: 600;
    cursor: pointer;
    box-shadow: 0 4px 14px rgba(37, 99, 235, 0.35);
    transition: all 0.25s ease;
    z-index: 999;
    display: flex;
    align-items: center;
    gap: 6px;
}

.lyrics-fab:hover {
    background: #1D4ED8;
    transform: translateY(-2px);
    box-shadow: 0 6px 18px rgba(37, 99, 235, 0.45);
}

/* 📱 화면 전체를 꽉 채우는 가사 패널 */
.lyrics-panel {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    background: #FFFFFF;
    display: none;
    flex-direction: column;
    z-index: 2000;
    overflow: hidden;
}

.lyrics-panel.show {
    display: flex;
    animation: fadeIn 0.2s ease-out;
}

@keyframes fadeIn {
    from { opacity: 0; transform: scale(0.98); }
    to { opacity: 1; transform: scale(1); }
}

.lyrics-header {
    background: #EFF6FF;
    padding: 16px 20px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    border-bottom: 1px solid #DBEAFE;
}

.lyrics-header h3 {
    margin: 0;
    font-size: 18px;
    color: #1E3A8A;
}

.lyrics-close-btn {
    background: none;
    border: none;
    font-size: 24px;
    color: #6B7280;
    cursor: pointer;
    padding: 4px 8px;
    line-height: 1;
}

.lyrics-body {
    flex: 1;
    padding: 20px;
    overflow-y: auto;
    display: flex;
    flex-direction: column;
    gap: 16px;
    max-width: 800px;
    margin: 0 auto;
    width: 100%;
}

/* 노래 선택 드롭다운 */
.lyrics-select {
    width: 100%;
    padding: 14px;
    border: 1px solid #DBEAFE;
    border-radius: 10px;
    font-size: 16px;
    outline: none;
    background: #F8FAFC;
    color: #1E3A8A;
    font-weight: 600;
    cursor: pointer;
}

.lyrics-select:focus {
    border-color: #2563EB;
    background: #FFFFFF;
}

/* 가사 본문 영역 (전체 화면 대응) */
.lyrics-display {
    white-space: pre-wrap;
    font-size: 16px;
    line-height: 2;
    color: #1E293B;
    background: #F8FAFC;
    padding: 20px;
    border-radius: 12px;
    border: 1px solid #E2E8F0;
    flex: 1;
    overflow-y: auto;
}

.lyrics-empty-msg {
    text-align: center;
    color: #9CA3AF;
    font-size: 16px;
    margin-top: 60px;
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

<!-- 🎵 우측 하단 노래 가사 플로팅 버튼 & 전체화면 패널 -->
<button class="lyrics-fab" onclick="toggleLyricsPanel()">
  🎵 歌词
</button>

<div class="lyrics-panel" id="lyricsPanel">
  <div class="lyrics-header">
    <h3>🎵 歌词本 (가사)</h3>
    <button class="lyrics-close-btn" onclick="toggleLyricsPanel()">✕</button>
  </div>
  
  <div class="lyrics-body">
    <!-- 노래 선택 셀렉트박스 -->
    <select id="songSelect" class="lyrics-select" onchange="onSongSelect(this.value)">
      <option value="">-- 选择歌曲 --</option>
    </select>
    
    <!-- 가사 표시창 -->
    <div class="lyrics-display" id="lyricDisplay">
      <div class="lyrics-empty-msg">请选择歌曲<br></div>
    </div>
  </div>
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

/* ★ GitHub 설정 ★ */
const GITHUB_USER = "YOUR_GITHUB_USERNAME";
const GITHUB_REPO = "YOUR_REPO_NAME";

const SONG_LIST = [
  {
    title: "超感",
    file: "chaogan.txt",
    url: `https://raw.githubusercontent.com/seo714/HSK/main/chaogan.txt`
  },
  {
    title: "想见你想见你想见你",
    file: "xiangjianni.txt",
    url: `https://raw.githubusercontent.com/seo714/HSK/main/xiangjianni.txt`
  }
];

// 단어 Firestore 연동
onSnapshot(colRef,(snapshot)=>{
 words=snapshot.docs.map(d=>({id:d.id,...d.data()}));
 renderTable();
 
 if(!initialLoaded){
   setTimeout(hideFavoriteColumn, 100);
   initialLoaded=true;
 }
});

// 페이지 로드 시 셀렉트박스 옵션 채우기
window.addEventListener("DOMContentLoaded", () => {
  populateSongSelect();
});

function populateSongSelect() {
  const select = document.getElementById("songSelect");
  select.innerHTML = `<option value="">-- 选择歌曲 (노래 선택) --</option>`;
  
  SONG_LIST.forEach((song, index) => {
    const opt = document.createElement("option");
    opt.value = index;
    opt.textContent = song.title;
    select.appendChild(opt);
  });
}

// 노래 선택 시 GitHub Raw URL에서 txt 텍스트 불러오기
window.onSongSelect = async function(index) {
  const display = document.getElementById("lyricDisplay");
  
  if(index === "" || !SONG_LIST[index]) {
    display.innerHTML = `<div class="lyrics-empty-msg">请选择歌曲<br>(노래를 선택해 주세요)</div>`;
    return;
  }
  
  const targetSong = SONG_LIST[index];
  display.innerText = "가사를 불러오는 중입니다...";
  
  try {
    const res = await fetch(targetSong.url);
    if(!res.ok) throw new Error("파일을 불러올 수 없습니다.");
    
    const textData = await res.text();
    display.innerText = textData;
  } catch(e) {
    console.error(e);
    display.innerHTML = `<div class="lyrics-empty-msg" style="color:#EF4444;">가사를 불러오지 못했습니다.<br>GitHub ID 및 Repository 주소를 확인해 주세요.</div>`;
  }
};

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

window.toggleLyricsPanel = function() {
  const panel = document.getElementById("lyricsPanel");
  panel.classList.toggle("show");
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
