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
      /* body 전체가 가로로 흔들리는 것을 방지 */
      overflow-x: hidden; 
    }

    h1 {
      text-align: center;
      color: #007BFF;
      margin: 20px 0;
    }

    /* 상단 입력 박스와 추가 버튼: 화면 가로 폭(100%)에 깔끔하게 맞춤 */
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
    }

    /* 
      [최종 디자인 반영] 테이블 전용 가로 스크롤 및 중앙 정렬 틀
      - 둥근 모서리와 은은한 그림자로 고급스러운 앱 스타일 연출
    */
    .table-wrap {
      width: 150%; 
      margin: 30px auto; /* 위아래 여백을 주어 버튼 영역과 분리 */
      position: relative;
      left: 50%;
      transform: translateX(-50%); /* 화면 정확히 중앙에 배치 */
      overflow-x: auto; /* 오직 테이블 내부에서만 가로 스크롤 활성화 */
      background: white;
      border-radius: 12px;
      box-shadow: 0 4px 16px rgba(0, 0, 0, 0.05);
      box-sizing: border-box;
      -webkit-overflow-scrolling: touch; /* 태블릿 환경에서 부드러운 스크롤 */
    }

    table {
      width: 100%;
      border-collapse: collapse;
      table-layout: fixed; /* 10:30:30:30 가로 비율 강제 고정 */
    }

    /* 
      헤더 디자인 (번호, 한자, 병음, 뜻 제목 칸)
      - 바둑판 선을 없애고 세련된 블루그레이 배경과 묵직한 글자색 적용
    */
    th {
      background: #f1f5f9;
      color: #334155;
      font-weight: 600;
      font-size: 14px;
      padding: 18px 8px;
      border-bottom: 2px solid #e2e8f0; /* 헤더 아래쪽만 짙은 구분선 */
      text-align: center;
    }

    /* 
      데이터 셀 디자인 (단어들이 들어가는 칸)
      - 답답한 좌우 테두리를 과감히 삭제하고 가로 선만 남겨 시원하게 처리
    */
    td {
      padding: 20px 8px; /* 터치하기 쉽도록 위아래 여백을 넉넉하게 배치 */
      font-size: 16px;
      color: #1e293b;
      border-bottom: 1px solid #f1f5f9; /* 은은하고 얇은 가로 경계선 */
      text-align: center;
      word-break: break-all;
    }

    /* 늘어난 전체 가로 폭 대비 요청하신 10:30:30:30 가로 비율 고정 */
    th:nth-child(1), td:nth-child(1) { width: 10%; } /* 번호 */
    th:nth-child(2), td:nth-child(2) { width: 30%; font-weight: bold; color: #007BFF; } /* 한자 강조 */
    th:nth-child(3), td:nth-child(3) { width: 30%; color: #64748b; } /* 병음은 차분한 그레이 */
    th:nth-child(4), td:nth-child(4) { width: 30%; } /* 뜻 */

    /* 줄을 누르거나 호버했을 때 피드백 효과 */
    tbody tr {
      cursor: pointer;
      transition: background 0.2s ease;
    }

    tbody tr:hover {
      background: #f4f7ff; /* 부드러운 스카이 블루 톤 */
    }
  </style>
</head>

<body>

<h1>HSK</h1>

<!-- 1. 패드 가로 화면에 딱 맞게 고정된 버튼 영역 -->
<div class="input-box">
  <div class="row">
    <input id="hanjaInput" placeholder="한자 입력">
    <input id="meaningInput" placeholder="뜻 입력">
  </div>
  <button class="add-btn" onclick="addWord()">추가</button>
</div>

<!-- 2. 버튼보다 더 넓고 중앙에 정렬되어 내부 스크롤이 되는 모던 디자인 테이블 -->
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
