# Aufgabe
Aufgabe
<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="utf-8" />
    <title>Römischer Taschenrechner</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            margin: 0;
            padding: 20px;
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        .calculator-container {
            background: white;
            border-radius: 15px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.1);
            padding: 30px;
            max-width: 600px;
            width: 100%;
        }

        h1 {
            text-align: center;
            color: #333;
            margin-bottom: 30px;
            font-size: 2.2em;
            font-weight: 300;
        }

        .input-group {
            margin-bottom: 20px;
        }

        label {
            display: block;
            margin-bottom: 8px;
            font-weight: 600;
            color: #555;
            font-size: 1.1em;
        }

        input[type="text"] {
            width: 100%;
            padding: 12px 15px;
            border: 2px solid #e1e5e9;
            border-radius: 8px;
            font-size: 1.1em;
            transition: border-color 0.3s ease;
            box-sizing: border-box;
        }

        input[type="text"]:focus {
            outline: none;
            border-color: #667eea;
            box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
        }

        .operation-buttons {
            display: flex;
            gap: 15px;
            margin: 25px 0;
            justify-content: center;
        }

        button {
            padding: 12px 25px;
            font-size: 1.2em;
            font-weight: 600;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            transition: all 0.3s ease;
            min-width: 80px;
        }

        .btn-operation {
            background: linear-gradient(135deg, #667eea, #764ba2);
            color: white;
        }

        .btn-operation:hover {
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(102, 126, 234, 0.3);
        }

        .btn-clear {
            background: #f8f9fa;
            color: #6c757d;
            border: 2px solid #e9ecef;
        }

        .btn-clear:hover {
            background: #e9ecef;
            transform: translateY(-1px);
        }

        .result-section {
            background: #f8f9fa;
            border-radius: 10px;
            padding: 25px;
            margin-top: 25px;
            border-left: 5px solid #667eea;
        }

        .result-item {
            margin-bottom: 15px;
            font-size: 1.1em;
        }

        .result-label {
            font-weight: 600;
            color: #495057;
            margin-bottom: 5px;
        }

        .result-value {
            font-size: 1.3em;
            font-weight: bold;
            color: #343a40;
            background: white;
            padding: 10px 15px;
            border-radius: 6px;
            border: 1px solid #dee2e6;
        }

        .error {
            color: #dc3545;
            font-weight: 600;
            text-align: center;
            margin-top: 15px;
            padding: 10px;
            background: #f8d7da;
            border: 1px solid #f5c6cb;
            border-radius: 6px;
        }

        .help-text {
            font-size: 0.9em;
            color: #6c757d;
            margin-top: 5px;
            font-style: italic;
        }

        .calculation-display {
            text-align: center;
            font-size: 1.2em;
            color: #495057;
            margin: 20px 0;
            font-weight: 500;
        }
    </style>
</head>
<body>
    <div class="calculator-container">
        <h1>🏛️ Römischer Taschenrechner</h1>
        
        <div class="input-group">
            <label for="number1">Erste Zahl (arabisch oder römisch):</label>
            <input type="text" id="number1" placeholder="z.B. 5, V, 15, XV" />
            <div class="help-text">Geben Sie eine Zahl zwischen 1-1000 ein (1=I, 5=V, 10=X, 50=L, 100=C, 500=D, 1000=M)</div>
        </div>
        <div class="input-group">
            <label for="number2">Zweite Zahl (arabisch oder römisch):</label>
            <input type="text" id="number2" placeholder="z.B. 3, III, 12, XII" />
            <div class="help-text">Geben Sie eine Zahl zwischen 1-1000 ein</div>
        </div>

        <div class="operation-buttons">
            <button class="btn-operation" onclick="calculate('add')">➕ Addition</button>
            <button class="btn-operation" onclick="calculate('subtract')">➖ Subtraktion</button>
            <button class="btn-clear" onclick="clearAll()">🗑️ Löschen</button>
        </div>

        <div id="calculationDisplay" class="calculation-display"></div>

        <div id="resultSection" class="result-section" style="display: none;">
            <div class="result-item">
                <div class="result-label">Ergebnis (Arabisch):</div>
                <div id="arabicResult" class="result-value"></div>
            </div>
            <div class="result-item">
                <div class="result-label">Ergebnis (Römisch):</div>
                <div id="romanResult" class="result-value"></div>
            </div>
        </div>

        <div id="errorMessage" class="error" style="display: none;"></div>
    </div>

    <script>
        // Roman to Arabic conversion
        function romanToArabic(roman) {
            if (!roman || typeof roman !== 'string') return null;
            
            roman = roman.toUpperCase().trim();
            const romanNumerals = {
                'I': 1, 'V': 5, 'X': 10, 'L': 50,
                'C': 100, 'D': 500, 'M': 1000
            };
            
            let result = 0;
            let prevValue = 0;
            
            for (let i = roman.length - 1; i >= 0; i--) {
                const currentValue = romanNumerals[roman[i]];
                if (!currentValue) return null; // Invalid character
                
                if (currentValue < prevValue) {
                    result -= currentValue;
                } else {
                    result += currentValue;
                }
                prevValue = currentValue;
            }
            
            // Validate the result by converting back
            if (arabicToRoman(result) !== roman) {
                return null; // Invalid roman numeral format
            }
            
            return result > 0 && result <= 1000 ? result : null;
        }

        // Arabic to Roman conversion
        function arabicToRoman(num) {
            if (num <= 0 || num > 1000) return null;
            
            const values = [1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1];
            const numerals = ['M', 'CM', 'D', 'CD', 'C', 'XC', 'L', 'XL', 'X', 'IX', 'V', 'IV', 'I'];
            
            let result = '';
            for (let i = 0; i < values.length; i++) {
                while (num >= values[i]) {
                    result += numerals[i];
                    num -= values[i];
                }
            }
            return result;
        }

        // Parse input (can be arabic or roman)
        function parseInput(input) {
            if (!input || input.trim() === '') return null;
            
            input = input.trim();
            
            // Try to parse as arabic number
            const arabicNum = parseInt(input);
            if (!isNaN(arabicNum) && arabicNum > 0 && arabicNum <= 1000) {
                return arabicNum;
            }
            
            // Try to parse as roman number
            return romanToArabic(input);
        }

        // Calculate function
        function calculate(operation) {
            clearError();
            
            const input1 = document.getElementById('number1').value;
            const input2 = document.getElementById('number2').value;
            
            const num1 = parseInput(input1);
            const num2 = parseInput(input2);
            
            if (num1 === null) {
                showError(`Ungültige erste Zahl: "${input1}". Bitte geben Sie eine Zahl zwischen 1-1000 ein.`);
                return;
            }
            
            if (num2 === null) {
                showError(`Ungültige zweite Zahl: "${input2}". Bitte geben Sie eine Zahl zwischen 1-1000 ein.`);
                return;
            }
            
            let result;
            let operationSymbol;
            
            if (operation === 'add') {
                result = num1 + num2;
                operationSymbol = '+';
            } else if (operation === 'subtract') {
                result = num1 - num2;
                operationSymbol = '-';
            }
            
            if (result <= 0) {
                showError('Das Ergebnis muss eine positive Zahl sein. Römische Zahlen können nicht null oder negativ sein.');
                return;
            }
            
            if (result > 1000) {
                showError('Das Ergebnis überschreitet 1000. Diese Anwendung unterstützt nur Zahlen bis 1000 (M).');
                return;
            }
            
            // Display calculation
            const romanNum1 = arabicToRoman(num1);
            const romanNum2 = arabicToRoman(num2);
            document.getElementById('calculationDisplay').textContent = 
                `${num1} (${romanNum1}) ${operationSymbol} ${num2} (${romanNum2}) =`;
            
            // Display results
            displayResult(result);
        }

        // Display result function
        function displayResult(result) {
            const romanResult = arabicToRoman(result);
            
            document.getElementById('arabicResult').textContent = result.toString();
            document.getElementById('romanResult').textContent = romanResult;
            document.getElementById('resultSection').style.display = 'block';
        }

        // Error handling
        function showError(message) {
            document.getElementById('errorMessage').textContent = message;
            document.getElementById('errorMessage').style.display = 'block';
            document.getElementById('resultSection').style.display = 'none';
            document.getElementById('calculationDisplay').textContent = '';
        }

        function clearError() {
            document.getElementById('errorMessage').style.display = 'none';
        }

        // Clear all function
        function clearAll() {
            document.getElementById('number1').value = '';
            document.getElementById('number2').value = '';
            document.getElementById('resultSection').style.display = 'none';
            document.getElementById('calculationDisplay').textContent = '';
            clearError();
        }

        // Add enter key support
        document.addEventListener('DOMContentLoaded', function() {
            const inputs = document.querySelectorAll('input[type="text"]');
            inputs.forEach(input => {
                input.addEventListener('keypress', function(e) {
                    if (e.key === 'Enter') {
                        calculate('add'); // Default to addition on Enter
                    }
                });
            });
        });
    </script>
</body>
</html>
