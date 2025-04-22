# 24-Game-Challenge-
Practice Your Math with Four Cards 
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>24 Game Practice</title>
    <style>
        :root {
            --card-width: 80px;
            --card-height: 120px;
            --gap: 15px;
            --border-radius: 8px;
            --correct-color: #28a745;
            --incorrect-color: #dc3545;
            --invalid-color: #ffc107;
            --text-color: #333;
            --button-bg: #007bff;
            --button-hover-bg: #0056b3;
            --button-text: white;
        }

        body {
            font-family: sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            background-color: #f4f4f4;
            color: var(--text-color);
            padding: 20px;
            box-sizing: border-box;
        }

        .container {
            background-color: #fff;
            padding: 30px;
            border-radius: var(--border-radius);
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
            text-align: center;
            max-width: 90%;
            width: 500px;
        }

        h1 {
            margin-top: 0;
            margin-bottom: 25px;
            color: #555;
        }

        #cards-container {
            display: flex;
            justify-content: center;
            gap: var(--gap);
            margin-bottom: 25px;
            flex-wrap: wrap; /* Allow cards to wrap on smaller screens */
        }

        .card {
            width: var(--card-width);
            height: var(--card-height);
            border: 1px solid #ccc;
            border-radius: var(--border-radius);
            background-color: white;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 2.5em;
            font-weight: bold;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05);
            user-select: none; /* Prevent text selection */
        }

        .input-section {
            display: flex;
            justify-content: center;
            align-items: stretch; /* Make items same height */
            gap: 10px;
            margin-bottom: 20px;
            flex-wrap: wrap; /* Allow input/button to wrap */
        }

        #expression-input {
            flex-grow: 1; /* Take available space */
            padding: 10px 15px;
            border: 1px solid #ccc;
            border-radius: var(--border-radius);
            font-size: 1em;
            min-width: 150px; /* Minimum width before wrapping */
        }

        button {
            padding: 10px 20px;
            font-size: 1em;
            border: none;
            border-radius: var(--border-radius);
            cursor: pointer;
            background-color: var(--button-bg);
            color: var(--button-text);
            transition: background-color 0.2s ease;
            white-space: nowrap; /* Prevent button text wrapping */
        }

        button:hover {
            background-color: var(--button-hover-bg);
        }

        #new-game-btn {
            display: block;
            margin: 25px auto 0; /* Center button */
            background-color: #6c757d;
        }
        #new-game-btn:hover {
            background-color: #5a6268;
        }


        #result-message {
            margin-top: 20px;
            min-height: 1.5em; /* Prevent layout shift */
            font-weight: bold;
            font-size: 1.1em;
        }

        .message-correct {
            color: var(--correct-color);
        }

        .message-incorrect {
            color: var(--incorrect-color);
        }

        .message-invalid {
            color: var(--invalid-color);
        }

        /* Responsive adjustments */
        @media (max-width: 480px) {
            :root {
                --card-width: 60px;
                --card-height: 90px;
                --gap: 10px;
            }
            .card {
                font-size: 2em;
            }
            h1 {
                font-size: 1.8em;
            }
            .container {
                padding: 20px;
            }
            .input-section {
                flex-direction: column; /* Stack input and button vertically */
                align-items: stretch; /* Make them full width */
            }
             #expression-input {
                margin-bottom: 10px; /* Add space when stacked */
            }
        }

    </style>
</head>
<body>

    <div class="container">
        <h1>24 Game Practice</h1>

        <div id="cards-container">
            <div class="card">?</div>
            <div class="card">?</div>
            <div class="card">?</div>
            <div class="card">?</div>
        </div>

        <div class="input-section">
            <input type="text" id="expression-input" placeholder="Enter your expression (e.g., (3+5)* (6/2))">
            <button id="evaluate-btn">Evaluate</button>
        </div>

        <div id="result-message"></div>

        <button id="new-game-btn">New Game</button>
    </div>

    <script>
        const cardsContainer = document.getElementById('cards-container');
        const expressionInput = document.getElementById('expression-input');
        const evaluateBtn = document.getElementById('evaluate-btn');
        const resultMessage = document.getElementById('result-message');
        const newGameBtn = document.getElementById('new-game-btn');
        const cardElements = cardsContainer.querySelectorAll('.card');

        let currentNumbers = [];
        const TARGET_NUMBER = 24;
        const TOLERANCE = 0.0001; // For floating point comparisons

        function getRandomInt(min, max) {
            min = Math.ceil(min);
            max = Math.floor(max);
            return Math.floor(Math.random() * (max - min + 1)) + min;
        }

        function startNewGame() {
            currentNumbers = [];
            for (let i = 0; i < 4; i++) {
                const num = getRandomInt(1, 9);
                currentNumbers.push(num);
                cardElements[i].textContent = num;
            }
            expressionInput.value = '';
            resultMessage.textContent = '';
            resultMessage.className = ''; // Clear result classes
            console.log("New game started. Numbers:", currentNumbers);
        }

        function validateExpressionNumbers(expression, targetNumbers) {
            // Extract all sequences of digits from the expression
            const numbersInExpression = (expression.match(/\d+/g) || []).map(Number);

            // Check if the count of numbers matches
            if (numbersInExpression.length !== targetNumbers.length) {
                console.log("Validation Fail: Number count mismatch.", numbersInExpression, targetNumbers);
                return false;
            }

            // Check if the numbers themselves match (regardless of order)
            const sortedExpressionNumbers = [...numbersInExpression].sort((a, b) => a - b);
            const sortedTargetNumbers = [...targetNumbers].sort((a, b) => a - b);

            const numbersMatch = sortedExpressionNumbers.every((num, index) => num === sortedTargetNumbers[index]);
            if (!numbersMatch) {
                 console.log("Validation Fail: Numbers do not match.", sortedExpressionNumbers, sortedTargetNumbers);
            }
            return numbersMatch;
        }

        function isValidMathematicalSyntax(expression) {
            // 1. Check for invalid characters (allow digits, operators, parentheses, spaces)
            if (/[^0-9+\-*/()\s]/.test(expression)) {
                console.log("Validation Fail: Invalid characters found.");
                return false;
            }

            // 2. Check for balanced parentheses
            let balance = 0;
            for (const char of expression) {
                if (char === '(') balance++;
                else if (char === ')') balance--;
                if (balance < 0) {
                    console.log("Validation Fail: Unbalanced parentheses (early close).");
                    return false; // Closing parenthesis without matching open
                }
            }
            if (balance !== 0) {
                 console.log("Validation Fail: Unbalanced parentheses (not all closed).");
                return false; // Unmatched opening parenthesis
            }

            // 3. Basic checks for consecutive operators or invalid placements
            //    (This is not exhaustive but catches common errors)
            const exprNoSpace = expression.replace(/\s/g, '');
            if (/[\+\-*/]{2,}/.test(exprNoSpace)) { // two or more operators together
                 console.log("Validation Fail: Consecutive operators.");
                 return false;
            }
             if (/^[\+\*/]/.test(exprNoSpace) || /[\+\-*/]$/.test(exprNoSpace)) { // operator at start (except minus) or end
                 console.log("Validation Fail: Operator at start/end.");
                 // Allow leading minus? For now, let's disallow for simplicity unless it's part of a number like -5, which our number extraction handles.
                 // A more complex regex could allow leading minus if needed.
                 return false;
             }
             if (/\(\)/.test(exprNoSpace) || /\)\(/.test(exprNoSpace)) { // empty parens or )(
                 console.log("Validation Fail: Invalid parentheses usage '()' or ')('.");
                 return false;
             }
             if (/\([\+\-*/]/.test(exprNoSpace) || /[\+\-*/]\)/.test(exprNoSpace)) { // Operator right after ( or right before )
                console.log("Validation Fail: Operator adjacent to parenthesis like '(+' or '+)'.");
                return false;
             }


            // 4. Attempt to evaluate in a try-catch as a final syntax check
            try {
                // Use Function constructor for slightly safer evaluation than direct eval
                // It evaluates in the global scope, not the local scope.
                new Function(`return ${expression}`)();
                // If it reaches here, basic syntax seems okay (though math errors like div by zero are still possible)
                 console.log("Syntax check passed (via Function constructor).");
                return true;
            } catch (e) {
                if (e instanceof SyntaxError) {
                    console.log("Validation Fail: SyntaxError during Function constructor check.", e.message);
                    return false;
                }
                 console.log("Potential runtime error during syntax check (like div by zero), but syntax might be ok.", e.message);
                 // Allow evaluation to proceed if it's not a SyntaxError,
                 // the main evaluation will handle runtime errors.
                 return true;
            }
        }

        function evaluateUserExpression() {
            const expression = expressionInput.value.trim();
            resultMessage.textContent = '';
            resultMessage.className = '';

            if (!expression) {
                resultMessage.textContent = 'Please enter an expression.';
                resultMessage.className = 'message-invalid';
                return;
            }

            // Validation Step 1: Check if the mathematical syntax looks plausible
            if (!isValidMathematicalSyntax(expression)) {
                resultMessage.textContent = 'Invalid input. Check operators and parentheses.';
                resultMessage.className = 'message-invalid';
                return;
            }

            // Validation Step 2: Check if the correct numbers are used exactly once
            if (!validateExpressionNumbers(expression, currentNumbers)) {
                resultMessage.textContent = `Invalid input. Use the numbers ${currentNumbers.join(', ')} exactly once.`;
                resultMessage.className = 'message-invalid';
                return;
            }

            // Evaluation Step
            try {
                // Use Function constructor for slightly safer evaluation
                const calculate = new Function(`return ${expression}`);
                const result = calculate();

                console.log("Evaluation Result:", result);

                // Check for non-finite results (Infinity, NaN) which can occur from division by zero
                if (!isFinite(result)) {
                     resultMessage.textContent = 'Invalid calculation (e.g., division by zero).';
                     resultMessage.className = 'message-invalid';
                     return;
                }

                // Check if the result is close enough to the target number
                if (Math.abs(result - TARGET_NUMBER) < TOLERANCE) {
                    resultMessage.textContent = `Correct! ${expression} = ${result}`;
                    resultMessage.className = 'message-correct';
                } else {
                    resultMessage.textContent = `Incorrect. ${expression} = ${result}. Try again!`;
                    resultMessage.className = 'message-incorrect';
                }
            } catch (error) {
                console.error("Evaluation Error:", error);
                 // Catch runtime errors not caught by the syntax check (less likely with Function constructor but possible)
                resultMessage.textContent = 'Error during evaluation. Please check your expression.';
                resultMessage.className = 'message-invalid';
            }
        }

        // Event Listeners
        newGameBtn.addEventListener('click', startNewGame);
        evaluateBtn.addEventListener('click', evaluateUserExpression);
        // Optional: Allow Enter key in input field to trigger evaluation
        expressionInput.addEventListener('keypress', function(event) {
            if (event.key === 'Enter') {
                event.preventDefault(); // Prevent form submission if it were in a form
                evaluateUserExpression();
            }
        });

        // Initialize the first game on page load
        startNewGame();

    </script>

</body>
</html>
