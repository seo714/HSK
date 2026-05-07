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

  /* 🔥 핵심 1: 스크롤 컨테이너 */
  .table-wrap {
    width: 100%;
    overflow-x: auto;
    -webkit-overflow-scrolling: touch;
  }

  /* 🔥 핵심 2: "딱 3칸만 보이게" 강제 */
  table {
    border-collapse: collapse;
    background: white;

    /* 중요: 화면보다 확실히 크게 */
    width: max-content;
    min-width: 100%;
  }

  th, td {
    border: 1px solid #ddd;
    padding: 12px;
    font-size: 14px;
    text-align: center;
    white-space: nowrap;
  }

  /* 🔥 핵심 3: 3개 컬럼만 화면에 맞춤 */
  th:nth-child(1), td:nth-child(1) { width: 120px; }
  th:nth-child(2), td:nth-child(2) { width: 120px; }
  th:nth-child(3), td:nth-child(3) { width: 160px; }

  /* 삭제는 "완전히 밖으로 밀기" */
  th:nth-child(4), td:nth-child(4) {
    width: 120px;
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
