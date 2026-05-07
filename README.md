<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>HSK 사이트</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <style>
    body {
      margin: 0;
      font-family: Arial, sans-serif;
      background: #f5f7fa;
    }

    /* 상단 */
    .header {
      width: 100%;
      height: 80px;
      display: flex;
      justify-content: center;
      align-items: center;
    }

    .header h1 {
      color: #007BFF; /* 글씨만 파란색 */
      font-size: 32px;
      margin: 0;
      font-weight: bold;
    }

    /* 내용 */
    .content {
      padding: 20px;
      text-align: center;
    }

    button {
      padding: 12px 20px;
      border: none;
      border-radius: 20px;
      background: #007BFF;
      color: white;
      font-size: 16px;
      cursor: pointer;
    }

    input {
      margin-top: 15px;
      padding: 10px;
      width: 80%;
      font-size: 16px;
      border-radius: 10px;
      border: 1px solid #ccc;
    }
  </style>
</head>

<body>

  <div class="header">
    <h1>HSK</h1>
  </div>

  <div class="content">
    <button onclick="addInput()">추가</button>

    <div id="inputArea"></div>
  </div>

  <script>
    function addInput() {
      const input = document.createElement("input");
      input.placeholder = "텍스트 입력...";
      document.getElementById("inputArea").appendChild(input);
    }
  </script>

</body>
</html>
