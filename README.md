<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<title>HSK 단어장</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<script src="https://unpkg.com/pinyin-pro"></script>

<style>
body{margin:0;font-family:Arial,sans-serif;background:#f5f7fa;}
h1{color:#007BFF;margin:20px 0;font-size:28px;text-align:center;}

.input-box{
  width:100%;
  max-width:700px;
  margin:0 auto;
  border:1px solid #ccc;
  border-radius:10px;
  overflow:hidden;
  background:white;
}

.row{display:flex;flex-direction:column;}

.row input{
  padding:12px;
  border:none;
  outline:none;
  font-size:16px;
  border-bottom:1px solid #eee;
}

.btn-row{display:flex;}

.add-btn{
  flex:1;
  padding:12px;
  border:none;
  background:#cfe2ff;
  font-size:16px;
  cursor:pointer;
}

.shuffle-btn{
  width:60px;
  border:none;
  background:#ffe599;
  font-size:20px;
  cursor:pointer;
}

.table-wrap{
  width:100%;
  margin-top:20px;
  padding:0;
}

table{
  width:400px;
  margin:0 auto;
  table-layout:fixed;
}

th:nth-child(1), td:nth-child(1){width:40px;}
th:nth-child(2), td:nth-child(2){width:90px;}
th:nth-child(3), td:nth-child(3){width:120px;}
th:nth-child(4), td:nth-child(4){width:150px;}

th,td{
  border:1px solid #ddd;
  padding:14px 18px;
  font-size:14px;
  text-align:center;
  white-space:nowrap;
}

th{background:#f1f1f1;}

tbody tr{
  cursor:pointer;
  user-select:none;
}

tbody tr:hover{
  background:#f8f9ff;
}
</style>
</head>

<body>

<h1>HSK</h1>

<div class="input-box">
  <div class="row">
    <input id="hanjaInput" placeholder="한자 입력">
    <input id="meaningInput" placeholder="뜻 입력">
  </div>

  <div class="btn-row">
    <button class="add-btn" onclick="addWord()">추가</button>
    <button class="shuffle-btn" onclick="shuffleWords()">🔀</button>
  </div>
</div>

<div class="table-wrap">
<table>
<thead>
<tr>
<th>No</th>
<th>한자</th>
<th>병음</th>
<th>뜻</th>
</tr>
</thead>
<tbody id="wordTable"></tbody>
</table>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js";
import {
  getFirestore,
  collection,
  addDoc,
  deleteDoc,
  doc,
  onSnapshot,
  updateDoc,
  query,
  orderBy,
  getDocs
} from "https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore.js";

const firebaseConfig = {
  apiKey: "AIzaSyC4eVXhqZQUHi5Zfd5eBjHvd5LHC99ueWk",
  authDomain: "hsk905-75140.firebaseapp.com",
  projectId: "hsk905-75140",
  storageBucket: "hsk905-75140.firebasestorage.app",
  messagingSenderId: "113343319154",
  appId: "1:113343319154:web:69a433ee99c4c67dc246eb"
};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);

const colRef = collection(db,"words");
const q = query(colRef, orderBy("order"));

let words = [];

onSnapshot(q,(snapshot)=>{
  words = snapshot.docs.map(d=>({
    id:d.id,
    ...d.data()
  }));
  renderTable();
});

window.addWord = async function(){

  const hanja=document.getElementById("hanjaInput").value.trim();
  const meaning=document.getElementById("meaningInput").value.trim();

  if(!hanja || !meaning){
    alert("모두 입력하세요!");
    return;
  }

  const snap = await getDocs(colRef);

  await addDoc(colRef,{
    hanja,
    meaning,
    order:snap.size
  });

  document.getElementById("hanjaInput").value="";
  document.getElementById("meaningInput").value="";
};

window.shuffleWords = async function(){

  const shuffled=[...words];

  for(let i=shuffled.length-1;i>0;i--){
    const j=Math.floor(Math.random()*(i+1));
    [shuffled[i],shuffled[j]]=[shuffled[j],shuffled[i]];
  }

  for(let i=0;i<shuffled.length;i++){
    await updateDoc(
      doc(db,"words",shuffled[i].id),
      {order:i}
    );
  }
};

window.speakWord=function(text){
  const utterance=new SpeechSynthesisUtterance(text);
  utterance.lang="zh-CN";
  speechSynthesis.cancel();
  speechSynthesis.speak(utterance);
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
      if(window.pinyinPro && word.hanja){
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

    row.addEventListener("click",()=>{
      speakWord(word.hanja);
    });

    let pressTimer;

    row.addEventListener("mousedown",()=>{
      pressTimer=setTimeout(()=>{
        if(confirm("이 단어를 삭제할까요?")){
          deleteWord(word.id);
        }
      },700);
    });

    row.addEventListener("mouseup",()=>clearTimeout(pressTimer));
    row.addEventListener("mouseleave",()=>clearTimeout(pressTimer));

    row.addEventListener("touchstart",()=>{
      pressTimer=setTimeout(()=>{
        if(confirm("이 단어를 삭제할까요?")){
          deleteWord(word.id);
        }
      },700);
    });

    row.addEventListener("touchend",()=>clearTimeout(pressTimer));

    table.appendChild(row);
  });
}
</script>
</body>
</html>
