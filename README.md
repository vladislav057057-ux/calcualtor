
<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Calculator</title>

  <style>
    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
    }

    body {
      min-height: 100vh;
      display: flex;
      align-items: center;
      justify-content: center;
      background: linear-gradient(135deg, #0f172a, #312e81);
      font-family: Arial, sans-serif;
      padding: 20px;
    }

    .calculator {
      width: 100%;
      max-width: 360px;
      padding: 24px;
      background: rgba(17, 24, 39, 0.96);
      border: 1px solid #374151;
      border-radius: 24px;
      box-shadow: 0 25px 60px #0006;
    }

    h1 {
      color: white;
      font-size: 25px;
      margin-bottom: 22px;
    }

    .screen {
      background: #1f2937;
      color: white;
      border-radius: 14px;
      padding: 20px 16px;
      margin-bottom: 18px;
      text-align: right;
      overflow: hidden;
    }

    #history {
      color: #9ca3af;
      min-height: 20px;
      font-size: 14px;
      margin-bottom: 8px;
    }

    #display {
      font-size: 36px;
      font-weight: bold;
      overflow-wrap: anywhere;
    }

    .buttons {
      display: grid;
      grid-template-columns: repeat(4, 1fr);
      gap: 10px;
    }

    button {
      border: none;
      border-radius: 12px;
      min-height: 58px;
      font-size: 20px;
      font-weight: bold;
      color: white;
      background: #374151;
      cursor: pointer;
      transition: 0.15s;
    }

    button:hover {
      filter: brightness(1.25);
      transform: translateY(-2px);
    }

    button:active {
      transform: scale(0.95);
    }

    .utility {
      background: #475569;
    }

    .operator {
      background: #2563eb;
    }

    .equals {
      background: #0d9488;
    }

    @media (max-width: 400px) {
      .calculator {
        padding: 16px;
      }

      button {
        min-height: 52px;
      }
    }
  </style>
</head>

<body>
  <main class="calculator">
    <h1>🧮 Calculator</h1>

    <section class="screen">
      <div id="history">Готов к вычислениям</div>
      <div id="display">0</div>
    </section>

    <section class="buttons">
      <button class="utility" onclick="clearAll()">AC</button>
      <button class="utility" onclick="changeSign()">±</button>
      <button class="utility" onclick="percent()">%</button>
      <button class="operator" onclick="chooseOperator('/')">÷</button>

      <button onclick="digit('7')">7</button>
      <button onclick="digit('8')">8</button>
      <button onclick="digit('9')">9</button>
      <button class="operator" onclick="chooseOperator('*')">×</button>

      <button onclick="digit('4')">4</button>
      <button onclick="digit('5')">5</button>
      <button onclick="digit('6')">6</button>
      <button class="operator" onclick="chooseOperator('-')">−</button>

      <button onclick="digit('1')">1</button>
      <button onclick="digit('2')">2</button>
      <button onclick="digit('3')">3</button>
      <button class="operator" onclick="chooseOperator('+')">+</button>

      <button class="utility" onclick="clearEntry()">C</button>
      <button onclick="digit('0')">0</button>
      <button onclick="decimal()">.</button>
      <button class="equals" onclick="calculate()">=</button>
    </section>
  </main>

  <script>
    const display = document.getElementById("display");
    const history = document.getElementById("history");

    let current = "0";
    let previous = null;
    let operator = null;
    let resetNext = false;

    function update() {
      display.textContent = current;
    }

    function digit(n) {
      if (resetNext || current === "Ошибка") {
        current = n;
        resetNext = false;
      } else {
        current = current === "0" ? n : current + n;
      }
      update();
    }

    function decimal() {
      if (resetNext || current === "Ошибка") {
        current = "0";
        resetNext = false;
      }
      if (!current.includes(".")) current += ".";
      update();
    }

    function clearAll() {
      current = "0";
      previous = null;
      operator = null;
      resetNext = false;
      history.textContent = "Готов к вычислениям";
      update();
    }

    function clearEntry() {
      current = "0";
      update();
    }

    function changeSign() {
      if (current !== "0" && current !== "Ошибка") {
        current = String(-Number(current));
        update();
      }
    }

    function percent() {
      if (current !== "Ошибка") {
        current = String(Number(current) / 100);
        update();
      }
    }

    function chooseOperator(op) {
      if (current === "Ошибка") return;

      if (operator && !resetNext) calculate();

      previous = Number(current);
      operator = op;
      resetNext = true;

      const symbols = { "+": "+", "-": "−", "*": "×", "/": "÷" };
      history.textContent = `${current} ${symbols[op]}`;
    }

    function calculate() {
      if (operator === null || previous === null) return;

      const a = previous;
      const b = Number(current);
      let result;

      if (operator === "+") result = a + b;
      if (operator === "-") result = a - b;
      if (operator === "*") result = a * b;

      if (operator === "/") {
        if (b === 0) {
          current = "Ошибка";
          history.textContent = "Деление на ноль";
          previous = null;
          operator = null;
          resetNext = true;
          update();
          return;
        }
        result = a / b;
      }

      const symbols = { "+": "+", "-": "−", "*": "×", "/": "÷" };
      history.textContent = `${a} ${symbols[operator]} ${b} =`;

      current = String(Number(result.toPrecision(12)));
      previous = null;
      operator = null;
      resetNext = true;
      update();
    }

    document.addEventListener("keydown", (event) => {
      if (/^[0-9]$/.test(event.key)) digit(event.key);
      else if (event.key === ".") decimal();
      else if (["+", "-", "*", "/"].includes(event.key)) {
        chooseOperator(event.key);
      }
      else if (event.key === "Enter" || event.key === "=") calculate();
      else if (event.key === "Escape") clearAll();
      else if (event.key === "Backspace") {
        current = current.length > 1
          ? current.slice(0, -1)
          : "0";
        update();
      }
    });
  </script>
</body>
</html>
