<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>HSK 단어장</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <style>
    /* 디자인/너비/배열 - 요청하신 대로 절대 수정하지 않았습니다 */
    body { margin: 0; font-family: Arial, sans-serif; background: #f5f7fa; text-align: center; }
    h1 { color: #007BFF; margin: 20px 0; font-size: 28px; }
    .input-box { width: 95%; max-width: 700px; margin: 0 auto; border: 1px solid #ccc; border-radius: 10px; overflow: hidden; background: white; }
    .row { display: flex; flex-direction: column; }
    .row input { padding: 12px; border: none; outline: none; font-size: 16px; border-bottom: 1px solid #eee; }
    .add-btn { width: 100%; padding: 12px; border: none; background: #cfe2ff; font-size: 16px; cursor: pointer; }
    .table-wrap { width: 100%; margin-top: 20px; overflow-x: auto; -webkit-overflow-scrolling: touch; }
    table { border-collapse: collapse; background: white; width: 100%; min-width: 650px; margin: 0 auto; }
    th, td { border: 1px solid #ddd; padding: 12px; font-size: 14px; text-align: center; white-space: nowrap; }
    th { background: #f1f1f1; }
    th:nth-child(1), td:nth-child(1) { width: 30%; }
    th:nth-child(2), td:nth-child(2) { width: 30%; }
    th:nth-child(3), td:nth-child(3) { width: 30%; }
    th:nth-child(4), td:nth-child(4) { width: 10%; }
    .del-btn { background: #ff6b6b; color: white; border: none; padding: 6px 10px; border-radius: 5px; cursor: pointer; }
    @media (min-width: 768px) { .input-box { width: 80%; } table { min-width: 700px; } h1 { font-size: 34px; } }
    @media (min-width: 1024px) { .input-box { width: 60%; } table { min-width: 800px; } }
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

  <!-- Realtime Database SDK 연결 -->
  <script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
    import { getDatabase, ref, push, onValue, remove } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

    // 🔴 본인의 설정값으로 교체 (콘솔의 프로젝트 설정에서 복사)
    const firebaseConfig = {
      apiKey: "본인의값",
      authDomain: "본인의값",
      databaseURL: "https://프로젝트ID.firebaseio.com", // Realtime DB는 이 주소가 꼭 필요합니다
      projectId: "본인의값",
      storageBucket: "본인의값",
      messagingSenderId: "본인의값",
      appId: "본인의값"
    };

    const app = initializeApp(firebaseConfig);
    const db = getDatabase(app);
    const wordsRef = ref(db, 'words');

    // 추가 기능
    window.addWord = function() {
      const hanja = document.getElementById("hanjaInput").value.trim();
      const pinyin = document.getElementById("pinyinInput").value.trim();
      const meaning = document.getElementById("meaningInput").value.trim();

      if (!hanja || !pinyin || !meaning) {
        alert("모두 입력하세요!");
        return;
      }

      push(wordsRef, {
        hanja: hanja,
        pinyin: pinyin,
        meaning: meaning
      });

      document.getElementById("hanjaInput").value = "";
      document.getElementById("pinyinInput").value = "";
      document.getElementById("meaningInput").value = "";
    };

    // 삭제 기능
    window.deleteWord = function(id) {
      if(confirm("정말 삭제하시겠습니까?")) {
        const itemRef = ref(db, `words/${id}`);
        remove(itemRef);
      }
    };

    // 실시간 동기화 (이게 핵심: 어떤 기기로 들어가도 똑같이 보임)
    onValue(wordsRef, (snapshot) => {
      const tableBody = document.getElementById("wordTable");
      tableBody.innerHTML = "";
      
      const data = snapshot.val();
      if (data) {
        // 객체를 배열로 변환해서 화면에 출력
        Object.keys(data).forEach((id) => {
          const word = data[id];
          const row = document.createElement("tr");
          row.innerHTML = `
            <td>${word.hanja}</td>
            <td>${word.pinyin}</td>
            <td>${word.meaning}</td>
            <td><button class="del-btn" onclick="deleteWord('${id}')">삭제</button></td>
          `;
          tableBody.appendChild(row);
        });
      }
    });
  </script>
</body>
</html>
