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
      box-sizing: border-box;
      width: 100%;
    }

    h1 {
      text-align: center;
      color: #007BFF;
      margin: 20px 0;
    }

    /* 
      전체 컨테이너: 테이블이 화면보다 길어질 때 
      오른쪽 레이아웃이 깨지지 않고 가로 스크롤이 되도록 방어해 줍니다.
    */
    .container {
      width: 100%;
      margin: 0;
      padding: 0;
      box-sizing: border-box;
      overflow-x: auto; 
    }

    /* 입력 박스 및 추가 버튼: 화면 가로 너비(100%)를 유지 */
    .input-box {
      width: 100%;
      background: white;
      border-bottom: 1px solid #ccc;
      margin-bottom: 20px;
      box-sizing: border-box;
    }

    .row {
      display: flex;
      flex-direction: column;
    }

    .row input {
      padding: 16px;
      border: none;
      outline: none;
      font-size: 16px;
      border-bottom: 1px solid #eee;
    }

    .add-btn {
      width: 100%;
      padding: 16px;
      border: none;
      background: #cfe2ff;
      font-size: 16px;
      cursor: pointer;
      box-sizing: border-box;
    }

    /* 
      [최종 반영] 테이블 영역을 추가 버튼(100%)보다 가로로 더 길게 늘림 (120%)
      더 길게 늘리고 싶으시다면 이 숫자를 130%, 150% 등으로 키우시면 됩니다.
    */
    .table-wrap {
      width: 120%; 
      background: white;
      box-sizing: border-box;
      border-top: 1px solid #ddd;
      border-bottom: 1px solid #ddd;
    }

    table {
      width: 100%;
      border-collapse: collapse;
      table-layout: fixed; /* 설정한 10:30:30:30 비율을 무조건 강제 고정 */
    }

    th, td {
      border: 1px solid #ddd;
      padding: 16px 8px;
      font-size: 15px;
      text-align: center;
      box-sizing: border-box;
      word-break: break-all;
    }

    /* 늘어난 테이블 가로 길이 안에서 요청하신 비율 완벽 적용 */
    th:nth-child(1), td:nth-child(1) {
      width: 10%;  /* 번호 열 */
    }

    th:nth-child(2), td:nth-child(2) {
      width: 30%; /* 한자 열 */
    }

    th:nth-child(3), td:nth-child(3) {
      width: 30%; /* 병음 열 */
    }

    th:nth-child(4), td:nth-child(4) {
      width: 30%; /* 뜻 열 */
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

<div class="container">

  <!-- 입력 박스와 추가 버튼 (화면 너비 100%) -->
  <div class="input-box">
    <div class="row">
      <input id="hanjaInput" placeholder="한자 입력">
      <input id="meaningInput" placeholder="뜻 입력">
    </div>
    <button class="add-btn" onclick="addWord()">추가</button>
  </div>

  <!-- 단어 테이블 (버튼보다 가로로 더 긴 너비 120%) -->
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
