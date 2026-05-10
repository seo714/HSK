<!DOCTYPE html>
<html lang="ko">

<head>
  <meta charset="UTF-8">
  <title>HSK 단어장</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <!-- 병음 라이브러리 -->
  <script src="https://unpkg.com/pinyin-pro"></script>

  <style>
    body {
      margin: 0;
      font-family: Arial, sans-serif;
      background: #f5f7fa;
      text-align: center;
    }

    h1 {
      color: #007BFF;
      margin: 20px 0;
      font-size: 28px;
    }

    .input-box {
      width: 95%;
      max-width: 700px;
      margin: 0 auto;
      border: 1px solid #ccc;
      border-radius: 10px;
      overflow: hidden;
      background: white;
    }

    .row {
      display: flex;
      flex-direction: column;
    }

    .row input {
      padding: 12px;
      border: none;
      outline: none;
      font-size: 16px;
      border-bottom: 1px solid #eee;
    }

    .add-btn {
      width: 100%;
      padding: 12px;
      border: none;
      background: #cfe2ff;
      font-size: 16px;
      cursor: pointer;
    }

    .table-wrap {
      width: 100%;
      margin-top: 20px;
      display: flex;
      justify-content: center;
      overflow-x: auto;
      -webkit-overflow-scrolling: touch;
      padding: 10px;
    }

    table {
      border-collapse: collapse;
      background: white;
      width: max-content;
      min-width: 100%;
    }

    th, td {
      border: 1px solid #ddd;
      padding: 14px 18px;
      font-size: 14px;
      text-align: center;
      white-space: nowrap;
    }

    th {
      background: #f1f1f1;
    }

    .del-btn {
      background: #ff6b6b;
      color: white;
      border: none;
      padding: 6px 10px;
      border-radius: 5px;
      cursor: pointer;
    }

    /* ✅ 삭제 열 고정 */
    th:last-child,
    td:last-child {
      position: sticky;
      right: 0;
      background: white;
      z-index: 2;
    }

    th:last-child {
      background: #f1f1f1;
      z-index: 3;
    }

    /* ✅ 클릭 UX */
    tbody tr {
      cursor: pointer;
    }

    tbody tr:hover {
      background: #f8f9ff;
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
  <button class="add-btn" onclick="addWord()">추가</button>
</div>

<div class="table-wrap">
  <table>
    <thead>
      <tr>
        <th>한자</th>
        <th>병음</th>
        <th>뜻</th>
        <th>삭제</th>
      </tr>
    </thead>
    <tbody id="wordTable"></tbody>
  </table>
</div>

<!-- Firebase -->
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
    apiKey: "AIzaSyC4eVXhqZQUHi5Zfd5eBjHvd5LHC99ueWk",
    authDomain: "hsk905-75140.firebaseapp.com",
    projectId: "hsk905-75140",
    storageBucket: "hsk905-75140.firebasestorage.app",
    messagingSenderId: "113343319154",
    appId: "1:113343319154:web:69a433ee99c4c67dc246eb"
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

  window.addWord = async function () {
    const hanja = document.getElementById("hanjaInput").value.trim();
    const meaning = document.getElementById("meaningInput").value.trim();

    if (!hanja || !meaning) {
      alert("모두 입력하세요!");
      return;
    }

    await addDoc(colRef, { hanja, meaning });

    document.getElementById("hanjaInput").value = "";
    document.getElementById("meaningInput").value = "";
  };

  window.deleteWord = async function (id) {
    await deleteDoc(doc(db, "words", id));
  };

  // 🔊 TTS
  window.speakWord = function (text) {
    const utterance = new SpeechSynthesisUtterance(text);
    utterance.lang = "zh-CN";
    speechSynthesis.speak(utterance);
  };

  function renderTable() {
    const table = document.getElementById("wordTable");
    table.innerHTML = "";

    words.forEach((word) => {
      const row = document.createElement("tr");

      let pinyin = "";
      try {
        if (window.pinyinPro && word.hanja) {
          pinyin = window.pinyinPro.pinyin(word.hanja, {
            toneType: "mark",
            type: "array"
          }).join(" ");
        }
      } catch (e) {}

      row.innerHTML = `
        <td>${word.hanja}</td>
        <td>${pinyin}</td>
        <td>${word.meaning}</td>
        <td><button class="del-btn" onclick="deleteWord('${word.id}')">삭제</button></td>
      `;

      // ✅ 행 클릭 시 발음
      row.addEventListener("click", (e) => {
        if (e.target.tagName === "BUTTON") return;
        speakWord(word.hanja);
      });

      table.appendChild(row);
    });
  }
</script>

</body>
</html>
