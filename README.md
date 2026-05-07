<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>HSK 단어장</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <!-- 🔥 병음 라이브러리 -->
  <script src="https://cdn.jsdelivr.net/npm/pinyin-pro@3.14.0/dist/pinyin-pro.min.js"></script>

  <style>
    body {
      margin: 0;
      font-family: Arial;
      background: #f5f7fa;
      text-align: center;
    }

    h1 {
      color: #007BFF;
      margin-top: 20px;
    }

    .input-box {
      width: 80%;
      margin: 20px auto;
      border: 1px solid #ccc;
      border-radius: 10px;
      overflow: hidden;
    }

    .row {
      display: flex;
    }

    .row input {
      flex: 1;
      padding: 12px;
      border: none;
      outline: none;
      font-size: 16px;
    }

    .divider {
      width: 1px;
      background: #ccc;
    }

    .add-btn {
      width: 100%;
      padding: 12px;
      border: none;
      background: #cfe2ff;
      cursor: pointer;
      font-size: 16px;
    }

    table {
      width: 90%;
      margin: 20px auto;
      border-collapse: collapse;
      background: white;
    }

    th, td {
      border: 1px solid #ddd;
      padding: 10px;
    }

    th {
      background: #f1f1f1;
    }
  </style>
</head>

<body>

  <h1>HSK</h1>

  <div class="input-box">
    <div class="row">
      <input id="hanjaInput" placeholder="한자 입력 (실시간 병음 자동)">
      <div class="divider"></div>
      <input id="meaningInput" placeholder="뜻 입력">
    </div>
    <button class="add-btn" onclick="addWord()">추가</button>
  </div>

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

  <script>
    let words = JSON.parse(localStorage.getItem("words")) || [];

    function getPinyin(text) {
      return pinyinPro.pinyin(text, { toneType: "mark" });
    }

    function addWord() {
      const hanja = document.getElementById("hanjaInput").value.trim();
      const meaning = document.getElementById("meaningInput").value.trim();

      if (!hanja || !meaning) {
        alert("모두 입력하세요!");
        return;
      }

      const pinyin = getPinyin(hanja);

      const newWord = {
        hanja,
        pinyin,
        meaning
      };

      words.push(newWord);
      localStorage.setItem("words", JSON.stringify(words));

      document.getElementById("hanjaInput").value = "";
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
