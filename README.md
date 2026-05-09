<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>HSK 단어장</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

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

    /* 입력 */
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

    /* 테이블 */
    .table-wrap {
      width: 100%;
      overflow-x: auto;
      -webkit-overflow-scrolling: touch;
      margin-top: 20px;
    }

    table {
      border-collapse: collapse;
      background: white;
      min-width: 700px;
      margin: 0 auto;
    }

    th, td {
      border: 1px solid #ddd;
      padding: 12px;
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
  </style>
</head>

<body>

<h1>HSK</h1>

<!-- 입력 -->
<div class="input-box">
  <div class="row">
    <input id="hanjaInput" placeholder="한자 입력">
    <input id="pinyinInput" placeholder="병음 입력">
    <input id="meaningInput" placeholder="뜻 입력">
  </div>
  <button class="add-btn" onclick="addWord()">추가</button>
</div>

<!-- 표 -->
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
  import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.4/firebase-app.js";
  import {
    getFirestore,
    collection,
    addDoc,
    getDocs,
    deleteDoc,
    doc
  } from "https://www.gstatic.com/firebasejs/10.12.4/firebase-firestore.js";

  const firebaseConfig = {
    apiKey: "AIzaSyC4eVXhqZQUHi5Zfd5eBjHvd5LHC99ueWk",
    authDomain: "hsk905-75140.firebaseapp.com",
    projectId: "hsk905-75140",
    storageBucket: "hsk905-75140.firebasestorage.app",
    messagingSenderId: "113343319154",
    appId: "1:113343319154:web:1f55a4efd94ee8c2c246eb"
  };













  

  const app = initializeApp(firebaseConfig);
  const db = getFirestore(app);

  // ➕ 추가
  window.addWord = async function () {
    const hanja = document.getElementById("hanjaInput").value.trim();
    const pinyin = document.getElementById("pinyinInput").value.trim();
    const meaning = document.getElementById("meaningInput").value.trim();

    if (!hanja || !pinyin || !meaning) {
      alert("모두 입력하세요!");
      return;
    }

    await addDoc(collection(db, "words"), {
      hanja,
      pinyin,
      meaning
    });

    document.getElementById("hanjaInput").value = "";
    document.getElementById("pinyinInput").value = "";
    document.getElementById("meaningInput").value = "";

    loadWords();
  };

  // ❌ 삭제
  window.deleteWord = async function (id) {
    await deleteDoc(doc(db, "words", id));
    loadWords();
  };

  // 📥 불러오기 (모든 기기 동일)
  async function loadWords() {
    const table = document.getElementById("wordTable");
    table.innerHTML = "";

    const snapshot = await getDocs(collection(db, "words"));

    snapshot.forEach((item) => {
      const data = item.data();

      const row = document.createElement("tr");

      row.innerHTML = `
        <td>${data.hanja}</td>
        <td>${data.pinyin}</td>
        <td>${data.meaning}</td>
        <td><button class="del-btn" onclick="deleteWord('${item.id}')">삭제</button></td>
      `;

      table.appendChild(row);
    });
  }

  loadWords();
</script>

</body>
</html>
