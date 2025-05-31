<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>QR 코드 스크래치</title>
  <style>
    body {
      font-family: sans-serif;
      text-align: center;
      padding: 30px;
    }
    h2 {
      margin-bottom: 20px;
      color: #333;
    }
    #scratchCard {
      position: relative;
      width: 300px;
      height: 300px;
      margin: 0 auto;
      border: 2px solid #aaa;
      user-select: none;
    }
    #hiddenQR {
      width: 100%;
      height: 100%;
      display: block;
    }
    canvas {
      position: absolute;
      top: 0;
      left: 0;
      z-index: 2;
    }
    #message {
      margin-top: 20px;
      font-weight: bold;
      color: crimson;
      display: none;
    }
  </style>
</head>
<body>

<h2>아래 박스를 긁으시면 바나힐 입장 QR코드가 나타나며, 이후 환불이 불가합니다</h2>

<div id="scratchCard">
  <img id="hiddenQR" src="https://api.qrserver.com/v1/create-qr-code/?data=https://fiveon.io&size=300x300" alt="QR Code">
  <canvas id="scratchCanvas"></canvas>
</div>

<div id="message">이 상품은 더 이상 환불이 불가합니다</div>

<script>
  const canvas = document.getElementById("scratchCanvas");
  const ctx = canvas.getContext("2d");
  const container = document.getElementById("scratchCard");
  const message = document.getElementById("message");

  canvas.width = container.offsetWidth;
  canvas.height = container.offsetHeight;

  ctx.fillStyle = "#bbb";
  ctx.fillRect(0, 0, canvas.width, canvas.height);
  ctx.globalCompositeOperation = 'destination-out';

  let isDrawing = false;

  function getPos(e) {
    const rect = canvas.getBoundingClientRect();
    return {
      x: (e.touches ? e.touches[0].clientX : e.clientX) - rect.left,
      y: (e.touches ? e.touches[0].clientY : e.clientY) - rect.top
    };
  }

  function draw(e) {
    if (!isDrawing) return;
    e.preventDefault();
    const pos = getPos(e);
    ctx.beginPath();
    ctx.arc(pos.x, pos.y, 20, 0, Math.PI * 2);
    ctx.fill();
    checkRevealProgress();
  }

  canvas.addEventListener("mousedown", () => isDrawing = true);
  canvas.addEventListener("mouseup", () => isDrawing = false);
  canvas.addEventListener("mousemove", draw);

  canvas.addEventListener("touchstart", () => isDrawing = true);
  canvas.addEventListener("touchend", () => isDrawing = false);
  canvas.addEventListener("touchmove", draw);

  function checkRevealProgress() {
    const pixels = ctx.getImageData(0, 0, canvas.width, canvas.height);
    let total = pixels.data.length / 4;
    let transparent = 0;

    for (let i = 0; i < pixels.data.length; i += 4) {
      if (pixels.data[i + 3] === 0) transparent++;
    }

    const percent = transparent / total;

    if (percent > 0.7) {
      canvas.style.display = "none";
      message.style.display = "block";
    }
  }
</script>

</body>
</html>
