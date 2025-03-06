<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>DBATU SGPA & CGPA Calculator</title>
    <link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;500;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary-color: #2962ff;
            --secondary-color: #455a64;
            --background: #f5f5f5;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
        }

        body {
            font-family: 'Roboto', sans-serif;
            background-color: var(--background);
            line-height: 1.6;
            padding: 20px;
        }

        .container {
            max-width: 800px;
            margin: 0 auto;
            background: white;
            padding: 2rem;
            border-radius: 12px;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }

        h2 {
            color: var(--primary-color);
            margin-bottom: 1.5rem;
            font-size: 1.8rem;
        }

        .input-group {
            margin: 1rem 0;
            display: flex;
            flex-wrap: wrap;
            gap: 1rem;
            align-items: center;
        }

        label {
            font-weight: 500;
            color: var(--secondary-color);
            min-width: 180px;
        }

        select, input {
            padding: 0.75rem;
            border: 1px solid #ddd;
            border-radius: 6px;
            font-size: 1rem;
            transition: border-color 0.3s ease;
        }

        select:focus, input:focus {
            outline: none;
            border-color: var(--primary-color);
            box-shadow: 0 0 0 2px rgba(41, 98, 255, 0.1);
        }

        table {
            width: 100%;
            margin: 1.5rem 0;
            border-collapse: collapse;
            background: white;
            border-radius: 8px;
            overflow: hidden;
            box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
        }

        th, td {
            padding: 1rem;
            text-align: left;
            border-bottom: 1px solid #eee;
        }

        th {
            background-color: var(--primary-color);
            color: white;
            font-weight: 500;
        }

        tr:nth-child(even) {
            background-color: #f8f9fa;
        }

        button {
            background-color: var(--primary-color);
            color: white;
            padding: 0.75rem 1.5rem;
            border: none;
            border-radius: 6px;
            font-size: 1rem;
            cursor: pointer;
            transition: background-color 0.3s ease;
            margin: 1rem 0;
        }

        button:hover {
            background-color: #1565c0;
        }

        .result-box {
            background: #f8f9fa;
            padding: 1.5rem;
            border-radius: 8px;
            margin: 1.5rem 0;
        }

        .result-box h3 {
            color: var(--secondary-color);
            margin-bottom: 0.5rem;
            font-size: 1.1rem;
        }

        .result-value {
            font-size: 1.5rem;
            color: var(--primary-color);
            font-weight: 700;
        }

        hr {
            margin: 2rem 0;
            border: 0;
            border-top: 1px solid #eee;
        }

        @media (max-width: 768px) {
            .container {
                padding: 1rem;
            }

            table {
                display: block;
                overflow-x: auto;
                white-space: nowrap;
            }

            .input-group {
                flex-direction: column;
                align-items: stretch;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <h2>DBATU SGPA Calculator</h2>
        
        <div class="input-group">
            <label for="subjectCount">Number of Subjects:</label>
            <input type="number" id="subjectCount" min="1" max="10" value="5" onchange="generateInputs()">
        </div>

        <table>
            <thead>
                <tr>
                    <th>Subject</th>
                    <th>Credits</th>
                    <th>Grade</th>
                </tr>
            </thead>
            <tbody id="subjectTable"></tbody>
        </table>

        <button onclick="calculateSGPA()">Calculate SGPA</button>
        
        <div class="result-box">
            <h3>SGPA</h3>
            <div class="result-value" id="sgpaResult">-</div>
            <div style="margin-top: 1rem;">
                <h3>Total Grade Points: <span id="totalGradePoints">-</span></h3>
                <h3>Total Credits: <span id="totalCredits">-</span></h3>
            </div>
        </div>

        <hr>

        <h2>DBATU CGPA Calculator</h2>
        
        <div class="input-group">
            <label for="semesterCount">Number of Semesters:</label>
            <input type="number" id="semesterCount" min="1" max="8" value="1" onchange="generateCGPAInputs()">
        </div>

        <table>
            <thead>
                <tr>
                    <th>Semester</th>
                    <th>SGPA</th>
                    <th>Total Credits</th>
                </tr>
            </thead>
            <tbody id="cgpaTable"></tbody>
        </table>

        <button onclick="calculateCGPA()">Calculate CGPA</button>
        
        <div class="result-box">
            <h3>CGPA</h3>
            <div class="result-value" id="cgpaResult">-</div>
        </div>
    </div>

    <script>
        // (Keep the JavaScript code exactly the same as original)
        const gradePoints = {
            "EX": 10.0, "AA": 9.0, "AB": 8.5, "BB": 8.0,
            "BC": 7.5, "CC": 7.0, "CD": 6.5, "DD": 6.0,
            "DE": 5.5, "EE": 5.0, "FF": 0.0, "-": 0.0
        };

        function generateInputs() {
            let subjectCount = document.getElementById("subjectCount").value;
            let tableBody = document.getElementById("subjectTable");
            tableBody.innerHTML = "";

            for (let i = 0; i < subjectCount; i++) {
                let row = `<tr>
                    <td>Subject ${i + 1}</td>
                    <td>
                        <select class="credit" onchange="updateGradeDropdown(${i})">
                            <option value="1">1</option>
                            <option value="2">2</option>
                            <option value="3" selected>3</option>
                            <option value="4">4</option>
                            <option value="5">5</option>
                            <option value="0">Audit Subject</option>
                        </select>
                    </td>
                    <td>
                        <select class="grade">
                            ${Object.keys(gradePoints).filter(g => g !== "-").map(grade => `<option value="${grade}">${grade}</option>`).join("")}
                        </select>
                    </td>
                </tr>`;
                tableBody.innerHTML += row;
            }
        }

        function updateGradeDropdown(index) {
            let creditDropdowns = document.querySelectorAll(".credit");
            let gradeDropdowns = document.querySelectorAll(".grade");

            if (creditDropdowns[index].value === "0") {
                gradeDropdowns[index].innerHTML = `<option value="-">-</option>`;
            } else {
                gradeDropdowns[index].innerHTML = Object.keys(gradePoints)
                    .filter(g => g !== "-")
                    .map(grade => `<option value="${grade}">${grade}</option>`)
                    .join("");
            }
        }

        function calculateSGPA() {
            let credits = document.querySelectorAll(".credit");
            let grades = document.querySelectorAll(".grade");

            let totalCredits = 0, totalPoints = 0;

            grades.forEach((grade, index) => {
                let gradeValue = gradePoints[grade.value];
                let creditValue = parseFloat(credits[index].value);

                if (creditValue !== 0) { 
                    totalPoints += gradeValue * creditValue;
                    totalCredits += creditValue;
                }
            });

            let sgpa = totalCredits > 0 ? (totalPoints / totalCredits).toFixed(2) : "0.00";
            document.getElementById("sgpaResult").innerText = sgpa;
            document.getElementById("totalGradePoints").innerText = totalPoints.toFixed(2);
            document.getElementById("totalCredits").innerText = totalCredits;
        }

        function generateCGPAInputs() {
            let semesterCount = document.getElementById("semesterCount").value;
            let tableBody = document.getElementById("cgpaTable");
            tableBody.innerHTML = "";

            for (let i = 0; i < semesterCount; i++) {
                let row = `<tr>
                    <td>Semester ${i + 1}</td>
                    <td><input type="number" class="sgpaInput" step="0.01" min="0" max="10" value="0"></td>
                    <td><input type="number" class="creditInput" min="1" max="30" value="20"></td>
                </tr>`;
                tableBody.innerHTML += row;
            }
        }

        function calculateCGPA() {
            let sgpaInputs = document.querySelectorAll(".sgpaInput");
            let creditInputs = document.querySelectorAll(".creditInput");

            let totalCredits = 0, weightedSGPA = 0;

            sgpaInputs.forEach((sgpaInput, index) => {
                let sgpaValue = parseFloat(sgpaInput.value);
                let creditValue = parseFloat(creditInputs[index].value);

                if (creditValue > 0) {
                    weightedSGPA += sgpaValue * creditValue;
                    totalCredits += creditValue;
                }
            });

            let cgpa = totalCredits > 0 ? (weightedSGPA / totalCredits).toFixed(2) : "0.00";
            document.getElementById("cgpaResult").innerText = cgpa;
        }

        window.onload = function() {
            generateInputs();
            generateCGPAInputs();
        };
    </script>
</body>
</html><!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>DBATU SGPA & CGPA Calculator</title>
    <link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;500;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary-color: #2962ff;
            --secondary-color: #455a64;
            --background: #f5f5f5;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
        }

        body {
            font-family: 'Roboto', sans-serif;
            background-color: var(--background);
            line-height: 1.6;
            padding: 20px;
        }

        .container {
            max-width: 800px;
            margin: 0 auto;
            background: white;
            padding: 2rem;
            border-radius: 12px;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }

        h2 {
            color: var(--primary-color);
            margin-bottom: 1.5rem;
            font-size: 1.8rem;
        }

        .input-group {
            margin: 1rem 0;
            display: flex;
            flex-wrap: wrap;
            gap: 1rem;
            align-items: center;
        }

        label {
            font-weight: 500;
            color: var(--secondary-color);
            min-width: 180px;
        }

        select, input {
            padding: 0.75rem;
            border: 1px solid #ddd;
            border-radius: 6px;
            font-size: 1rem;
            transition: border-color 0.3s ease;
        }

        select:focus, input:focus {
            outline: none;
            border-color: var(--primary-color);
            box-shadow: 0 0 0 2px rgba(41, 98, 255, 0.1);
        }

        table {
            width: 100%;
            margin: 1.5rem 0;
            border-collapse: collapse;
            background: white;
            border-radius: 8px;
            overflow: hidden;
            box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
        }

        th, td {
            padding: 1rem;
            text-align: left;
            border-bottom: 1px solid #eee;
        }

        th {
            background-color: var(--primary-color);
            color: white;
            font-weight: 500;
        }

        tr:nth-child(even) {
            background-color: #f8f9fa;
        }

        button {
            background-color: var(--primary-color);
            color: white;
            padding: 0.75rem 1.5rem;
            border: none;
            border-radius: 6px;
            font-size: 1rem;
            cursor: pointer;
            transition: background-color 0.3s ease;
            margin: 1rem 0;
        }

        button:hover {
            background-color: #1565c0;
        }

        .result-box {
            background: #f8f9fa;
            padding: 1.5rem;
            border-radius: 8px;
            margin: 1.5rem 0;
        }

        .result-box h3 {
            color: var(--secondary-color);
            margin-bottom: 0.5rem;
            font-size: 1.1rem;
        }

        .result-value {
            font-size: 1.5rem;
            color: var(--primary-color);
            font-weight: 700;
        }

        hr {
            margin: 2rem 0;
            border: 0;
            border-top: 1px solid #eee;
        }

        @media (max-width: 768px) {
            .container {
                padding: 1rem;
            }

            table {
                display: block;
                overflow-x: auto;
                white-space: nowrap;
            }

            .input-group {
                flex-direction: column;
                align-items: stretch;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <h2>DBATU SGPA Calculator</h2>
        
        <div class="input-group">
            <label for="subjectCount">Number of Subjects:</label>
            <input type="number" id="subjectCount" min="1" max="10" value="5" onchange="generateInputs()">
        </div>

        <table>
            <thead>
                <tr>
                    <th>Subject</th>
                    <th>Credits</th>
                    <th>Grade</th>
                </tr>
            </thead>
            <tbody id="subjectTable"></tbody>
        </table>

        <button onclick="calculateSGPA()">Calculate SGPA</button>
        
        <div class="result-box">
            <h3>SGPA</h3>
            <div class="result-value" id="sgpaResult">-</div>
            <div style="margin-top: 1rem;">
                <h3>Total Grade Points: <span id="totalGradePoints">-</span></h3>
                <h3>Total Credits: <span id="totalCredits">-</span></h3>
            </div>
        </div>

        <hr>

        <h2>DBATU CGPA Calculator</h2>
        
        <div class="input-group">
            <label for="semesterCount">Number of Semesters:</label>
            <input type="number" id="semesterCount" min="1" max="8" value="1" onchange="generateCGPAInputs()">
        </div>

        <table>
            <thead>
                <tr>
                    <th>Semester</th>
                    <th>SGPA</th>
                    <th>Total Credits</th>
                </tr>
            </thead>
            <tbody id="cgpaTable"></tbody>
        </table>

        <button onclick="calculateCGPA()">Calculate CGPA</button>
        
        <div class="result-box">
            <h3>CGPA</h3>
            <div class="result-value" id="cgpaResult">-</div>
        </div>
    </div>

    <script>
        // (Keep the JavaScript code exactly the same as original)
        const gradePoints = {
            "EX": 10.0, "AA": 9.0, "AB": 8.5, "BB": 8.0,
            "BC": 7.5, "CC": 7.0, "CD": 6.5, "DD": 6.0,
            "DE": 5.5, "EE": 5.0, "FF": 0.0, "-": 0.0
        };

        function generateInputs() {
            let subjectCount = document.getElementById("subjectCount").value;
            let tableBody = document.getElementById("subjectTable");
            tableBody.innerHTML = "";

            for (let i = 0; i < subjectCount; i++) {
                let row = `<tr>
                    <td>Subject ${i + 1}</td>
                    <td>
                        <select class="credit" onchange="updateGradeDropdown(${i})">
                            <option value="1">1</option>
                            <option value="2">2</option>
                            <option value="3" selected>3</option>
                            <option value="4">4</option>
                            <option value="5">5</option>
                            <option value="0">Audit Subject</option>
                        </select>
                    </td>
                    <td>
                        <select class="grade">
                            ${Object.keys(gradePoints).filter(g => g !== "-").map(grade => `<option value="${grade}">${grade}</option>`).join("")}
                        </select>
                    </td>
                </tr>`;
                tableBody.innerHTML += row;
            }
        }

        function updateGradeDropdown(index) {
            let creditDropdowns = document.querySelectorAll(".credit");
            let gradeDropdowns = document.querySelectorAll(".grade");

            if (creditDropdowns[index].value === "0") {
                gradeDropdowns[index].innerHTML = `<option value="-">-</option>`;
            } else {
                gradeDropdowns[index].innerHTML = Object.keys(gradePoints)
                    .filter(g => g !== "-")
                    .map(grade => `<option value="${grade}">${grade}</option>`)
                    .join("");
            }
        }

        function calculateSGPA() {
            let credits = document.querySelectorAll(".credit");
            let grades = document.querySelectorAll(".grade");

            let totalCredits = 0, totalPoints = 0;

            grades.forEach((grade, index) => {
                let gradeValue = gradePoints[grade.value];
                let creditValue = parseFloat(credits[index].value);

                if (creditValue !== 0) { 
                    totalPoints += gradeValue * creditValue;
                    totalCredits += creditValue;
                }
            });

            let sgpa = totalCredits > 0 ? (totalPoints / totalCredits).toFixed(2) : "0.00";
            document.getElementById("sgpaResult").innerText = sgpa;
            document.getElementById("totalGradePoints").innerText = totalPoints.toFixed(2);
            document.getElementById("totalCredits").innerText = totalCredits;
        }

        function generateCGPAInputs() {
            let semesterCount = document.getElementById("semesterCount").value;
            let tableBody = document.getElementById("cgpaTable");
            tableBody.innerHTML = "";

            for (let i = 0; i < semesterCount; i++) {
                let row = `<tr>
                    <td>Semester ${i + 1}</td>
                    <td><input type="number" class="sgpaInput" step="0.01" min="0" max="10" value="0"></td>
                    <td><input type="number" class="creditInput" min="1" max="30" value="20"></td>
                </tr>`;
                tableBody.innerHTML += row;
            }
        }

        function calculateCGPA() {
            let sgpaInputs = document.querySelectorAll(".sgpaInput");
            let creditInputs = document.querySelectorAll(".creditInput");

            let totalCredits = 0, weightedSGPA = 0;

            sgpaInputs.forEach((sgpaInput, index) => {
                let sgpaValue = parseFloat(sgpaInput.value);
                let creditValue = parseFloat(creditInputs[index].value);

                if (creditValue > 0) {
                    weightedSGPA += sgpaValue * creditValue;
                    totalCredits += creditValue;
                }
            });

            let cgpa = totalCredits > 0 ? (weightedSGPA / totalCredits).toFixed(2) : "0.00";
            document.getElementById("cgpaResult").innerText = cgpa;
        }

        window.onload = function() {
            generateInputs();
            generateCGPAInputs();
        };
    </script>
</body>
</html>
