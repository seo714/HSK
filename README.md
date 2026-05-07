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

    /* 입력 박스 (패드 기준 중앙 정렬) */
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

    /* 🔥 핵심: 반응형 테이블 */
    .table-wrap {
      width: 100%;
      margin-top: 20px;
      overflow-x: auto;
      -webkit-overflow-scrolling: touch;
    }

    table {
      border-collapse: collapse;
      background: white;

      /* 🔥 패드 대응 핵심 */
      width: 100%;
      min-width: 650px;
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

    /* 열 비율 */
    th:nth-child(1), td:nth-child(1) { width: 25%; }
    th:nth-child(2), td:nth-child(2) { width: 25%; }
    th:nth-child(3), td:nth-child(3) { width: 30%; }
    th:nth-child(4), td:nth-child(4) { width: 20%; }

    /* 삭제 버튼 */
    .del-btn {
      background: #ff6b6b;
      color: white;
      border: none;
      padding: 6px 10px;
      border-radius: 5px;
      cursor: pointer;
    }

    /* 📲 패드 이상 */
    @media (min-width: 768px) {
      .input-box {
        width: 80%;
      }

      table {
        min-width: 700px;
      }

      h1 {
        font-size: 34px;
      }
    }

    /* 💻 데스크탑 */
    @media (min-width: 1024px) {
      .input-box {
        width: 60%;
      }

      table {
        min-width: 800px;
      }
    }
  </style>
</head>

<body>

  <h1>HSK</h1>

  <div class="input-box">
    <div class="row">
      <input id="hanjaInput" placeholder="한자 입력">
      <input id="pinyinInput" placeholder="병음 입력">
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

  <script>
    let words = JSON.parse(localStorage.getItem("words")) || [];

    function addWord() {
      const hanja = document.getElementById("hanjaInput").value.trim();
      const pinyin = document.getElementById("pinyinInput").value.trim();
      const meaning = document.getElementById("meaningInput").value.trim();

      if (!hanja || !pinyin || !meaning) {
        alert("모두 입력하세요!");
        return;
      }

      words.push({ hanja, pinyin, meaning });
      localStorage.setItem("words", JSON.stringify(words));

      document.getElementById("hanjaInput").value = "";
      document.getElementById("pinyinInput").value = "";
      document.getElementById("meaningInput").value = "";

      renderTable();
    }

    function deleteWord(index) {
      words.splice(index, 1);
      localStorage.setItem("words", JSON.stringify(words));
      renderTable();
    }

    function renderTable() {
      const table = document.getElementById("wordTable");
      table.innerHTML = "";

      words.forEach((word, index) => {
        const row = document.createElement("tr");

        row.innerHTML = `
          <td>${word.hanja}</td>
          <td>${word.pinyin}</td>
          <td>${word.meaning}</td>
          <td><button class="del-btn" onclick="deleteWord(${index})">삭제</button></td>
        `;

        table.appendChild(row);
      });
    }

    renderTable();
  </script>

</body>
</html>
