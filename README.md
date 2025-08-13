<!DOCTYPE html>
<html>
<head>
  <title>D20 Dice Roller - Mobile Friendly</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.4.2/p5.min.js"></script>
  <style>
    body {
      margin: 0;
      padding: 10px;
      background-color: #f0f0f0;
      font-family: Arial, sans-serif;
      display: flex;
      flex-direction: column;
      align-items: center;
      min-height: 100vh;
    }
    canvas {
      border: 2px solid black;
      width: 80vw;
      max-width: 300px;
      height: auto;
      margin-bottom: 20px;
    }
    #controls {
      display: flex;
      flex-direction: column;
      align-items: center;
      width: 90%;
      max-width: 400px;
      margin-bottom: 20px;
    }
    #controls label {
      font-size: 5vw;
      margin-bottom: 5px;
    }
    #controls input[type="number"] {
      width: 100%;
      padding: 10px;
      font-size: 5vw;
      margin-bottom: 15px;
      border: 1px solid #ccc;
      border-radius: 5px;
    }
    #controls input[type="checkbox"] {
      transform: scale(1.5);
      margin-left: 10px;
      margin-bottom: 15px;
    }
    #controls button {
      padding: 12px 24px;
      font-size: 5vw;
      background-color: #4CAF50;
      color: white;
      border: none;
      border-radius: 5px;
      cursor: pointer;
      width: 100%;
      max-width: 200px;
    }
    #controls button:hover {
      background-color: #45a049;
    }
    #result {
      font-size: 4.5vw;
      text-align: center;
      width: 90%;
      max-width: 400px;
      line-height: 1.5;
    }
    @media (max-width: 600px) {
      canvas {
        width: 90vw;
      }
      #controls label, #result {
        font-size: 6vw;
      }
      #controls input[type="number"], #controls button {
        font-size: 6vw;
      }
    }
  </style>
</head>
<body>
  <div id="controls">
    <label for="modifier">Modifier</label>
    <input type="number" id="modifier" value="0">
    <label for="numRolls">Number of Rolls</label>
    <input type="number" id="numRolls" value="1" min="1">
    <label for="guidance">Guidance (+1d4)<input type="checkbox" id="guidance"></label>
    <button onclick="startRoll()">Roll d20</button>
  </div>
  <div id="result"></div>
  <script>
    let rolling = false;
    let rollResult = 0;
    let guidanceResult = 0;
    let displayNumber = 0;
    let currentRoll = 0;
    let totalRolls = 1;
    let modifier = 0;
    let useGuidance = false;
    let animationFrame = 0;

    function setup() {
      let canvasSize = min(windowWidth * 0.8, 300);
      createCanvas(canvasSize, canvasSize);
      textAlign(CENTER, CENTER);
      textSize(canvasSize * 0.24); // 24% dari ukuran kanvas
    }

    function windowResized() {
      let canvasSize = min(windowWidth * 0.8, 300);
      resizeCanvas(canvasSize, canvasSize);
      textSize(canvasSize * 0.24);
    }

    function draw() {
      background(255);
      fill(0);
      if (rolling) {
        displayNumber = floor(random(1, 21));
        animationFrame++;
        if (animationFrame > 30) {
          rolling = false;
          displayNumber = rollResult;
          showResult();
          currentRoll++;
          if (currentRoll < totalRolls) {
            setTimeout(startNextRoll, 1000);
          }
        }
      }
      fill(200, 50, 50);
      rect(width * 0.25, height * 0.25, width * 0.5, height * 0.5, 20);
      fill(255);
      text(displayNumber, width * 0.5, height * 0.5);
    }

    function rollD20() {
      const rand = random();
      if (rand < 0.7) {
        return floor(random(11, 21)); // 70% untuk 11-20 (masing-masing 7%)
      } else {
        return floor(random(1, 11)); // 30% untuk 1-10 (masing-masing 3%)
      }
    }

    function rollD4() {
      return floor(random(1, 5));
    }

    function startRoll() {
      if (rolling) return;
      modifier = parseInt(document.getElementById('modifier').value) || 0;
      totalRolls = parseInt(document.getElementById('numRolls').value) || 1;
      useGuidance = document.getElementById('guidance').checked;
      currentRoll = 0;
      document.getElementById('result').innerHTML = '';
      startNextRoll();
    }

    function startNextRoll() {
      rolling = true;
      rollResult = rollD20();
      guidanceResult = useGuidance ? rollD4() : 0;
      animationFrame = 0;
    }

    function showResult() {
      let total = rollResult + modifier + guidanceResult;
      let resultText = `Roll ${currentRoll + 1}: d20 = ${rollResult}`;
      if (useGuidance) resultText += `, Guidance (1d4) = ${guidanceResult}`;
      if (modifier !== 0) resultText += `, Modifier = ${modifier}`;
      resultText += `<br>Total: ${total}`;
      if (rollResult === 20) resultText += '<br><strong>Critical Success!</strong>';
      else if (rollResult === 1) resultText += '<br><strong>Critical Failure!</strong>';
      document.getElementById('result').innerHTML += resultText + '<br><br>';
    }
  </script>
</body>
</html>
