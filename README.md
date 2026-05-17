<html lang="ko">

<head>
  <meta charset="UTF-8">
  <title>HSK 단어장</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <script src="https://unpkg.com/pinyin-pro"></script>

  <style>
    body {
      margin: 0;
      padding: 20px;
      font-family: Arial, sans-serif;
      background: #f5f7fa;
      box-sizing: border-box;
    }

    h1 {
      text-align: center;
      color: #007BFF;
      margin: 20px 0;
    }

    /* 
      추가 버튼과 테이블의 가로 길이를 완벽히 일치시키기 위한 공통 컨테이너
      화면 양옆 여백을 제외하고 100% 꽉 차게 늘어납니다.
    */
    .container {
      width: 100%;
      margin: 0 auto;
      box-sizing: border-box;
    }

    .input-box {
      width: 100%;
      background: white;
      border: 1px solid #ccc;
      border-radius: 10px;
      overflow: hidden;
      margin-bottom: 20px;
      box-sizing: border-box;
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

    /* 추가 버튼의 너비를 부모(.input-box)에 100% 맞춤 */
    .add-btn {
      width: 100%;
      padding: 12px;
      border: none;
      background: #cfe2ff;
      font-size: 16px;
      cursor: pointer;
      box-sizing: border-box;
    }

    /* 테이블 감싸는 영역도 부모(.container) 너비에 100% 맞춤으로써 버튼과 우측 라인이 일치함 */
    .table-wrap {
      width: 100%;
      background: white;
      border-radius: 10px;
      overflow: hidden;
      border: 1px solid #ddd;
      box-sizing: border-box;
    }

    table {
      width: 100%;
      border-collapse: collapse;
      table-layout: fixed; /* 정확한 10:30:30:30 비율 보장 */
    }

    th, td {
      border: 1px solid #ddd;
      padding: 14px 8px;
      font-size: 14px;
      text-align: center;
      box-sizing: border-box;
      word-break: break-all;
    }

    /* 지정하신 10 : 30 : 30 : 30 가로 비율 유지 */
    th:nth-child(1), td:nth-child(1) {
      width: 10%;  /* 번호 */
    }

    th:nth-child(2), td:nth-child(2) {
      width: 30%; /* 한자 */
    }

    th:nth-child(3), td:nth-child(3) {
      width: 30%; /* 병음 */
    }

    th:nth-child(4), td:nth-child(4) {
      width: 30%; /* 뜻 */
    }

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

<!-- 입력창과 테이블을 하나의 컨테이너로 묶어 가로정렬 선을 칼같이 일치시킴 -->
<div class="container">

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
          <th>번호</th>
          <th>한자</th>
          <th>병음</th>
          <th>뜻</th>
        </tr>
      </thead>
      <tbody id="wordTable"></tbody>
    </table>
  </div>

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

  window.speakWord = function (text) {
    const utterance = new SpeechSynthesisUtterance(text);
    utterance.lang = "zh-CN";
    speechSynthesis.cancel();
    speechSynthesis.speak(utterance);
  };

  window.deleteWord = async function (id) {
    await deleteDoc(doc(db, "words", id));
  };

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
        speakWord(word.hanja);
      });

      let pressTimer;

      row.addEventListener("mousedown", () => {
        pressTimer = setTimeout(() => {
          if (confirm("삭제할까요?")) deleteWord(word.id);
        }, 700);
      });

      row.addEventListener("mouseup", () => clearTimeout(pressTimer));
      row.addEventListener("mouseleave", () => clearTimeout(pressTimer));
      row.addEventListener("touchstart", () => {
        pressTimer = setTimeout(() => {
          if (confirm("삭제할까요?")) deleteWord(word.id);
        }, 700);
      });
      row.addEventListener("touchend", () => clearTimeout(pressTimer));

      table.appendChild(row);
    });
  }
</script>

</body>
</html>
