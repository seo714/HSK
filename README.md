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

    /* 상단 HSK (하나만 유지) */
    h1 {
      color: #007BFF;
      margin: 20px 0;
      font-size: 28px;
    }

    /* 입력 박스 */
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

    .row input:last-child {
      border-bottom: none;
    }

    .add-btn {
      width: 100%;
      padding: 12px;
      border: none;
      background: #cfe2ff;
      font-size: 16px;
      cursor: pointer;
    }

    /* 표 */
    .table-wrap {
      width: 100%;
      margin-top: 20px;
      overflow-x: auto; /* 모바일 가로 스크롤 */
    }

    table {
      width: 100%;
      min-width: 500px; /* 가로 넓게 */
      border-collapse: collapse;
      background: white;
    }

    th, td {
      border: 1px solid #ddd;
      padding: 10px;
      font-size: 14px;
      text-align: center;
    }

    th {
      background: #f1f1f1;
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

      const newWord = {
        hanja,
        pinyin,
        meaning
      };

      words.push(newWord);
      localStorage.setItem("words", JSON.stringify(words));

      document.getElementById("hanjaInput").value = "";
      document.getElementById("pinyinInput").value = "";
      document.getElementById("meaningInput").value = "";

      renderTable();
    }

    function renderTable() {
      const table = document.getElementById("wordTable");
      table.innerHTML = "";

      words.forEach(word => {
        const row = document.createElement("tr");

        row.innerHTML = `
          <td>${word.hanja}</td>
          <td>${word.pinyin}</td>
          <td>${word.meaning}</td>
        `;

        table.appendChild(row);
      });
    }

    renderTable();
  </script>

</body>
</html>
