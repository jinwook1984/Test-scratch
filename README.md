<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>QR 코드 스크래치 테스트</title>
  <style>
    #scratchCard {
      position: relative;
      width: 300px;
      height: 300px;
      border: 2px solid #aaa;
      margin: 50px auto;
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
  </style>
</head>
<body>

<div id="scratchCard">
  <img id="hiddenQR" src="https://api.qrserver.com/v1/create-qr-code/?data=https://fiveon.io&size=300x300" alt="QR Code">
  <canvas id="scratchCanvas"></canvas>
</div>

<script>
  const canvas = document.getElementById("scratchCanvas");
  const ctx = canvas.getContext("2d");
  const container = document.getElementById("scratchCard");

  canvas.width = container.offsetWidth;
  canvas.height = container.offsetHeight;

  // 덮개: 회색
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
    ctx.arc(pos.x, pos.y, 20, 0, 2 * Math.PI);
    ctx.fill();
  }

  canvas.addEventListener("mousedown", () => isDrawing = true);
  canvas.addEventListener("mouseup", () => isDrawing = false);
  canvas.addEventListener("mousemove", draw);

  canvas.addEventListener("touchstart", () => isDrawing = true);
  canvas.addEventListener("touchend", () => isDrawing = false);
  canvas.addEventListener("touchmove", draw);
</script>

</body>
</html>
