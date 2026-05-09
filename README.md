<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>HSK 단어장</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <style>
    /* 요청하신 대로 기존 스타일/너비/배열은 1px도 수정하지 않았습니다. */
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
    <!-- 버튼 클릭 시 addWord 함수 실행 -->
    <button class="add-btn" id="submitBtn">추가</button>
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

  <script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
    import { getDatabase, ref, push, onValue, remove } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

    // 🔴 중요: 이 부분을 본인의 Firebase 정보로 정확히 채워넣으세요!
    const firebaseConfig = {
      apiKey: "AIzaSyC4eVXhqZQUHi5Zfd5eBjHvd5LHC99ueWk",
      authDomain: "hsk905-75140.firebaseapp.com",
      databaseURL: "https://console.firebase.google.com/project/hsk905-75140/database/hsk905-75140-default-rtdb/data/~2F?utm_source=chatgpt.com",
      projectId: "hsk905-75140",
      storageBucket: "hsk905-75140.firebasestorage.app",
      messagingSenderId: "113343319154",
      appId: "1:113343319154:web:69a433ee99c4c67dc246eb"
    };

    const app = initializeApp(firebaseConfig);
    const db = getDatabase(app);
    const wordsRef = ref(db, 'words');

    // 추가 기능 (onclick 대신 addEventListener를 사용하여 module 에러 방지)
    document.getElementById('submitBtn').addEventListener('click', async () => {
      const hanja = document.getElementById("hanjaInput").value.trim();
      const pinyin = document.getElementById("pinyinInput").value.trim();
      const meaning = document.getElementById("meaningInput").value.trim();

      if (!hanja || !pinyin || !meaning) {
        alert("모두 입력하세요!");
        return;
      }

      try {
        await push(wordsRef, {
          hanja: hanja,
          pinyin: pinyin,
          meaning: meaning,
          date: Date.now()
        });
        
        // 입력 칸 비우기
        document.getElementById("hanjaInput").value = "";
        document.getElementById("pinyinInput").value = "";
        document.getElementById("meaningInput").value = "";
      } catch (e) {
        alert("데이터 저장 실패! 파이어베이스 설정(Rules)을 확인하세요.");
      }
    });

    // 실시간 동기화 및 테이블 자동 생성
    onValue(wordsRef, (snapshot) => {
      const tableBody = document.getElementById("wordTable");
      tableBody.innerHTML = ""; // 기존 테이블 비우기
      
      const data = snapshot.val();
      if (data) {
        // 데이터가 있으면 반복문 돌려서 테이블에 추가
        Object.keys(data).forEach((id) => {
          const word = data[id];
          const row = document.createElement("tr");
          row.innerHTML = `
            <td>${word.hanja}</td>
            <td>${word.pinyin}</td>
            <td>${word.meaning}</td>
            <td><button class="del-btn" data-id="${id}">삭제</button></td>
          `;
          tableBody.appendChild(row);
        });

        // 삭제 버튼 클릭 이벤트 연결
        document.querySelectorAll('.del-btn').forEach(btn => {
          btn.onclick = (e) => {
            const id = e.target.getAttribute('data-id');
            if(confirm("정말 삭제하시겠습니까?")) {
              remove(ref(db, `words/${id}`));
            }
          };
        });
      }
    });
  </script>
</body>
</html>
