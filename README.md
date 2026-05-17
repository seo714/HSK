<!DOCTYPE html>
<html lang="ko">

<head>
<meta charset="UTF-8">
<title>HSK 단어장</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<script src="https://unpkg.com/pinyin-pro"></script>

<style>
body {
  margin: 0;
  padding: 0;
  font-family: Arial, sans-serif;
  background: #f5f7fa;
}

/* 🔥 핵심: 화면 끝까지 강제 */
.table-wrap {
  width: 100vw;
  margin: 0;
  padding: 0;
}

/* 🔥 핵심: 테이블도 100vw */
table {
  width: 100vw;
  border-collapse: collapse;
  table-layout: fixed;
}

th, td {
  border: 1px solid #ddd;
  padding: 14px 8px;
  font-size: 14px;
  text-align: center;
  box-sizing: border-box;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}

/* 🔥 정확히 100% 분할 */
th:nth-child(1), td:nth-child(1) { width: 10%; }
th:nth-child(2), td:nth-child(2) { width: 30%; }
th:nth-child(3), td:nth-child(3) { width: 30%; }
th:nth-child(4), td:nth-child(4) { width: 30%; }

tbody tr:hover {
  background: #f8f9ff;
  cursor: pointer;
}
</style>
</head>

<body>

<h1 style="text-align:center;">HSK</h1>

<div class="table-wrap">
<table>
  <thead>
    <tr>
      <th>번호</th>
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
  onSnapshot
} from "https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore.js";

const firebaseConfig = {
  apiKey: "AIzaSy...",
  authDomain: "hsk905-75140.firebaseapp.com",
  projectId: "hsk905-75140",
};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);
const colRef = collection(db, "words");

let words = [];

onSnapshot(colRef, (snapshot) => {
  words = snapshot.docs.map(doc => ({
    id: doc.id,
    ...doc.data()
  }));
  renderTable();
});

function renderTable() {
  const table = document.getElementById("wordTable");
  table.innerHTML = "";

  words.forEach((word, index) => {

    let pinyin = "";
    try {
      if (window.pinyinPro && word.hanja) {
        pinyin = window.pinyinPro.pinyin(word.hanja, {
          toneType: "mark",
          type: "array"
        }).join(" ");
      }
    } catch (e) {}

    const row = document.createElement("tr");

    row.innerHTML = `
      <td>${index + 1}</td>
      <td>${word.hanja}</td>
      <td>${pinyin}</td>
      <td>${word.meaning}</td>
    `;

    row.addEventListener("click", () => {
      const utterance = new SpeechSynthesisUtterance(word.hanja);
      utterance.lang = "zh-CN";
      speechSynthesis.cancel();
      speechSynthesis.speak(utterance);
    });

    table.appendChild(row);
  });
}
</script>

</body>
</html>
