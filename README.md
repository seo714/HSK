<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<title>한자 학습</title>
<style>
  body {
    margin: 0;
    padding: 20px;
    background: #f5f5f5;
    font-family: sans-serif;
  }

  .container {
    max-width: 600px;
    margin: 0 auto;
  }

  table {
    width: 100%;
    border-collapse: collapse;
    background: white;
    table-layout: fixed;
  }

  td {
    border: 1px solid #ddd;
    padding: 15px;
    text-align: center;
    font-size: 20px;
    cursor: pointer;
    user-select: none;
  }

  td:active {
    background: #eee;
  }
</style>
</head>
<body>

<div class="container">
  <table id="hanjaTable">
    <tr>
      <td>你</td>
      <td>好</td>
      <td>学</td>
    </tr>
    <tr>
      <td>我</td>
      <td>们</td>
      <td>是</td>
    </tr>
  </table>
</div>

<script>
const table = document.getElementById("hanjaTable");

let pressTimer = null;
let isLongPress = false;

// 🔊 TTS 함수
function speak(text) {
  const utterance = new SpeechSynthesisUtterance(text);
  utterance.lang = "zh-CN";
  speechSynthesis.speak(utterance);
}

// 📱 이벤트 처리
table.addEventListener("mousedown", startPress);
table.addEventListener("touchstart", startPress);

table.addEventListener("mouseup", endPress);
table.addEventListener("mouseleave", endPress);
table.addEventListener("touchend", endPress);

function startPress(e) {
  const cell = e.target.closest("td");
  if (!cell) return;

  isLongPress = false;

  pressTimer = setTimeout(() => {
    isLongPress = true;

    const confirmDelete = confirm("이 셀을 삭제할까요?");
    if (confirmDelete) {
      cell.remove();
    }
  }, 600);
}

function endPress(e) {
  const cell = e.target.closest("td");
  if (!cell) return;

  clearTimeout(pressTimer);

  // 길게 누른게 아니면 → TTS 실행
  if (!isLongPress) {
    speak(cell.innerText);
  }
}
</script>

</body>
</html>
