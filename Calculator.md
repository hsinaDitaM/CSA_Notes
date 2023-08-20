---
layout: default
title: Calculator
---
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Neumorphic Calculator</title>
<style>
  .calculator {
    background-color: #f2f2f2;
    border-radius: 20px;
    box-shadow: 10px 10px 20px rgba(163, 177, 198, 0.5),
                -10px -10px 20px rgba(255, 255, 255, 0.5);
    padding: 20px;
    width: 300px;
    text-align: center;
  }
  
  .output {
    font-size: 24px;
    margin-bottom: 10px;
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 10px;
    background-color: #f2f2f2;
    box-shadow: inset 4px 4px 6px rgba(163, 177, 198, 0.5),
                inset -4px -4px 6px rgba(255, 255, 255, 0.5);
  }
  
  .buttons {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 10px;
  }
  
  .button {
    padding: 10px;
    font-size: 18px;
    border: none;
    border-radius: 10px;
    cursor: pointer;
    background-color: #f2f2f2;
    box-shadow: 5px 5px 10px rgba(163, 177, 198, 0.5),
                -5px -5px 10px rgba(255, 255, 255, 0.5);
    transition: box-shadow 0.2s;
  }
  
  .button:hover {
    box-shadow: 3px 3px 6px rgba(163, 177, 198, 0.5),
                -3px -3px 6px rgba(255, 255, 255, 0.5);
  }
</style>
</head>
<body>
  <div class="calculator">
    <div class="output" id="output">0</div>
    <div class="buttons">
      <button class="button" onclick="appendNumber('7')">7</button>
      <button class="button" onclick="appendNumber('8')">8</button>
      <button class="button" onclick="appendNumber('9')">9</button>
      <button class="button" onclick="appendOperator('+')">+</button>
      <button class="button" onclick="appendNumber('4')">4</button>
      <button class="button" onclick="appendNumber('5')">5</button>
      <button class="button" onclick="appendNumber('6')">6</button>
      <button class="button" onclick="appendOperator('-')">-</button>
      <button class="button" onclick="appendNumber('3')">3</button>
      <button class="button" onclick="appendNumber('2')">2</button>
      <button class="button" onclick="appendNumber('1')">1</button>
      <button class="button" onclick="appendOperator('*')">*</button>
      <button class="button" onclick="appendNumber('0')">0</button>
      <button class="button" onclick="clearOutput()">C</button>
      <button class="button" onclick="calculate()">=</button>
      <button class="button" onclick="appendOperator('/')">/</button>
    </div>
  </div>

  <script>
    let currentInput = '';
    let currentOperator = '';
    let previousInput = '';

    function updateOutput() {
      document.getElementById('output').textContent = currentInput !== '' ? currentInput : '0';
    }

    function appendNumber(number) {
      currentInput += number;
      updateOutput();
    }

    function appendOperator(operator) {
      if (currentInput !== '') {
        currentOperator = operator;
        previousInput = currentInput;
        currentInput = '';
        updateOutput();
      }
    }

    function calculate() {
      if (currentInput !== '' && currentOperator !== '') {
        switch (currentOperator) {
          case '+':
            currentInput = (parseFloat(previousInput) + parseFloat(currentInput)).toString();
            break;
          case '-':
            currentInput = (parseFloat(previousInput) - parseFloat(currentInput)).toString();
            break;
          case '*':
            currentInput = (parseFloat(previousInput) * parseFloat(currentInput)).toString();
            break;
          case '/':
            currentInput = (parseFloat(previousInput) / parseFloat(currentInput)).toString();
            break;
        }
        currentOperator = '';
        previousInput = '';
        updateOutput();
      }
    }

    function clearOutput() {
      currentInput = '';
      currentOperator = '';
      previousInput = '';
      updateOutput();
    }
  </script>
</body>
</html>
