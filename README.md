<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>EMI Calculator (USD)</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        * {
            box-sizing: border-box;
        }
        body {
            font-family: 'Arial', sans-serif;
            line-height: 1.5;
            margin: 0;
            padding: 10px;
            background-color: #f5f7fa;
            color: #333;
            font-size: 14px;
        }
        .container {
            max-width: 100%;
            margin: 0 auto;
            background: white;
            padding: 15px;
            border-radius: 8px;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
        }
        h1 {
            text-align: center;
            color: #2c3e50;
            margin: 10px 0 20px;
            font-size: 1.5rem;
        }
        .input-group {
            margin-bottom: 15px;
        }
        label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
            color: #2c3e50;
            font-size: 0.9rem;
        }
        input, select {
            width: 100%;
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 5px;
            font-size: 1rem;
        }
        button {
            background-color: #3498db;
            color: white;
            border: none;
            padding: 12px;
            border-radius: 5px;
            cursor: pointer;
            font-size: 1rem;
            width: 100%;
            margin-top: 5px;
        }
        .result-section {
            margin-top: 20px;
            display: none;
        }
        .result-card {
            background: #f8f9fa;
            padding: 15px;
            border-radius: 8px;
            margin-bottom: 15px;
        }
        .result-value {
            font-size: 1.2rem;
            font-weight: bold;
            color: #2c3e50;
            margin: 5px 0;
        }
        .summary-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 10px;
        }
        .chart-container {
            display: flex;
            flex-direction: column;
            margin-top: 20px;
        }
        .chart-box {
            width: 100%;
            margin-bottom: 15px;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 15px;
            font-size: 0.8rem;
        }
        th, td {
            padding: 8px;
            text-align: left;
            border-bottom: 1px solid #ddd;
        }
        th {
            background-color: #f2f2f2;
            font-weight: bold;
        }
        .toggle-container {
            display: flex;
            align-items: center;
            margin-bottom: 10px;
        }
        .toggle-label {
            margin-right: 8px;
            font-size: 0.9rem;
        }
        .switch {
            position: relative;
            display: inline-block;
            width: 50px;
            height: 25px;
        }
        .slider {
            position: absolute;
            cursor: pointer;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background-color: #ccc;
            transition: .4s;
            border-radius: 25px;
        }
        .slider:before {
            position: absolute;
            content: "";
            height: 18px;
            width: 18px;
            left: 4px;
            bottom: 4px;
            background-color: white;
            transition: .4s;
            border-radius: 50%;
        }
        input:checked + .slider {
            background-color: #3498db;
        }
        input:checked + .slider:before {
            transform: translateX(24px);
        }
        .prepayment-section {
            margin-top: 15px;
            padding: 10px;
            background-color: #f0f8ff;
            border-radius: 5px;
            border-left: 3px solid #3498db;
        }
        .prepayment-section h3 {
            margin-top: 0;
            font-size: 1rem;
        }
        @media (min-width: 600px) {
            .container {
                padding: 20px;
                max-width: 500px;
            }
            .summary-grid {
                grid-template-columns: 1fr 1fr 1fr;
            }
            .chart-container {
                flex-direction: row;
                flex-wrap: wrap;
                justify-content: space-between;
            }
            .chart-box {
                width: 48%;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>EMI Calculator (USD)</h1>
        
        <div class="input-group">
            <label for="loanAmount">Loan Amount ($)</label>
            <input type="number" id="loanAmount" placeholder="e.g., 25000" min="1000" step="1000">
        </div>
        
        <div class="input-group">
            <label for="interestRate">Annual Interest Rate (%)</label>
            <input type="number" id="interestRate" placeholder="e.g., 7.5" min="0.1" step="0.01">
        </div>
        
        <div class="toggle-container">
            <span class="toggle-label">Tenure in:</span>
            <label class="switch">
                <input type="checkbox" id="tenureToggle">
                <span class="slider"></span>
            </label>
            <span class="toggle-label" id="tenureLabel">Years</span>
        </div>
        
        <div class="input-group">
            <label for="loanTenure" id="tenureInputLabel">Loan Tenure (Years)</label>
            <input type="number" id="loanTenure" placeholder="e.g., 5" min="1" step="1">
        </div>
        
        <div class="prepayment-section">
            <h3>Prepayment (Optional)</h3>
            <div class="input-group">
                <label for="prepaymentAmount">Prepayment Amount ($)</label>
                <input type="number" id="prepaymentAmount" placeholder="e.g., 5000" min="0" step="1000">
            </div>
            <div class="input-group">
                <label for="prepaymentAfter">After Month</label>
                <input type="number" id="prepaymentAfter" placeholder="e.g., 12" min="1" step="1">
            </div>
        </div>
        
        <button id="calculateBtn">Calculate EMI</button>
        
        <div class="result-section" id="resultSection">
            <div class="result-card">
                <h2>Loan Summary</h2>
                <div class="summary-grid">
                    <div>
                        <label>Monthly EMI</label>
                        <div class="result-value" id="emiValue">$0</div>
                    </div>
                    <div>
                        <label>Total Interest</label>
                        <div class="result-value" id="totalInterest">$0</div>
                    </div>
                    <div>
                        <label>Total Payment</label>
                        <div class="result-value" id="totalPayment">$0</div>
                    </div>
                </div>
            </div>
            
            <div class="chart-container">
                <div class="chart-box">
                    <canvas id="pieChart"></canvas>
                </div>
                <div class="chart-box">
                    <canvas id="paymentChart"></canvas>
                </div>
            </div>
            
            <h2>Amortization Schedule</h2>
            <div style="overflow-x: auto;">
                <table id="amortizationTable">
                    <thead>
                        <tr>
                            <th>Month</th>
                            <th>EMI</th>
                            <th>Principal</th>
                            <th>Interest</th>
                            <th>Balance</th>
                        </tr>
                    </thead>
                    <tbody id="amortizationBody">
                        <!-- Rows will be inserted here by JavaScript -->
                    </tbody>
                </table>
            </div>
        </div>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', function() {
            // DOM Elements
            const loanAmount = document.getElementById('loanAmount');
            const interestRate = document.getElementById('interestRate');
            const loanTenure = document.getElementById('loanTenure');
            const tenureToggle = document.getElementById('tenureToggle');
            const tenureLabel = document.getElementById('tenureLabel');
            const tenureInputLabel = document.getElementById('tenureInputLabel');
            const prepaymentAmount = document.getElementById('prepaymentAmount');
            const prepaymentAfter = document.getElementById('prepaymentAfter');
            const calculateBtn = document.getElementById('calculateBtn');
            const resultSection = document.getElementById('resultSection');
            const emiValue = document.getElementById('emiValue');
            const totalInterest = document.getElementById('totalInterest');
            const totalPayment = document.getElementById('totalPayment');
            const amortizationBody = document.getElementById('amortizationBody');
            
            // Charts
            let pieChart, paymentChart;
            
            // Toggle between years and months
            tenureToggle.addEventListener('change', function() {
                if (this.checked) {
                    tenureLabel.textContent = 'Months';
                    tenureInputLabel.textContent = 'Loan Tenure (Months)';
                } else {
                    tenureLabel.textContent = 'Years';
                    tenureInputLabel.textContent = 'Loan Tenure (Years)';
                }
            });
            
            // Calculate EMI
            calculateBtn.addEventListener('click', calculateEMI);
            
            function calculateEMI() {
                // Validate inputs
                if (!loanAmount.value || !interestRate.value || !loanTenure.value) {
                    alert('Please fill all required fields');
                    return;
                }
                
                // Get input values
                const P = parseFloat(loanAmount.value);
                const r = parseFloat(interestRate.value) / 12 / 100;
                let n = parseFloat(loanTenure.value);
                
                // Convert to months if tenure is in years
                if (!tenureToggle.checked) {
                    n = n * 12;
                }
                
                // Calculate EMI
                const emi = P * r * Math.pow(1 + r, n) / (Math.pow(1 + r, n) - 1);
                
                // Get prepayment details
                const prepayment = prepaymentAmount.value ? parseFloat(prepaymentAmount.value) : 0;
                const prepaymentMonth = prepaymentAfter.value ? parseInt(prepaymentAfter.value) : 0;
                
                // Generate amortization schedule
                const schedule = generateAmortizationSchedule(P, r, n, emi, prepayment, prepaymentMonth);
                
                // Update UI
                emiValue.textContent = '$' + emi.toFixed(2);
                totalInterest.textContent = '$' + schedule.totalInterest.toFixed(2);
                totalPayment.textContent = '$' + (P + schedule.totalInterest).toFixed(2);
                
                // Update amortization table
                updateAmortizationTable(schedule.payments);
                
                // Update charts
                updateCharts(P, schedule.totalInterest);
                
                // Show results
                resultSection.style.display = 'block';
                
                // Scroll to results
                resultSection.scrollIntoView({ behavior: 'smooth' });
            }
            
            function generateAmortizationSchedule(P, r, n, emi, prepayment = 0, prepaymentMonth = 0) {
                let balance = P;
                let totalInterest = 0;
                const payments = [];
                let prepaymentDone = false;
                
                for (let month = 1; month <= n; month++) {
                    // Check if prepayment should be applied this month
                    const applyPrepayment = !prepaymentDone && prepayment > 0 && month >= prepaymentMonth;
                    
                    // Calculate interest and principal for this month
                    const interest = balance * r;
                    let principal = emi - interest;
                    
                    // Apply prepayment if needed
                    if (applyPrepayment) {
                        const newBalanceAfterPayment = balance - principal;
                        if (newBalanceAfterPayment <= prepayment) {
                            principal = balance; // Pay off entire remaining balance
                        } else {
                            principal += prepayment;
                        }
                        prepaymentDone = true;
                    }
                    
                    // Adjust for final payment
                    if (month === n || balance - principal < 0.1) {
                        principal = balance;
                    }
                    
                    // Update balance
                    balance -= principal;
                    if (balance < 0) balance = 0;
                    
                    // Track total interest
                    totalInterest += interest;
                    
                    // Add to payment schedule
                    payments.push({
                        month,
                        emi: month === n ? (principal + interest) : emi,
                        principal,
                        interest,
                        balance
                    });
                    
                    // Break if loan is paid off
                    if (balance <= 0) break;
                }
                
                return {
                    payments,
                    totalInterest
                };
            }
            
            function updateAmortizationTable(payments) {
                // Clear existing rows
                amortizationBody.innerHTML = '';
                
                // Add new rows (show first 6 and last 6 months for mobile)
                const showAll = payments.length <= 12;
                const displayPayments = showAll ? payments : [
                    ...payments.slice(0, 3),
                    { month: '...', emi: '', principal: '', interest: '', balance: '' },
                    ...payments.slice(-3)
                ];
                
                displayPayments.forEach(payment => {
                    const row = document.createElement('tr');
                    
                    if (payment.month === '...') {
                        row.innerHTML = `<td colspan="5" style="text-align: center;">... (${payments.length - 6} months hidden) ...</td>`;
                    } else {
                        row.innerHTML = `
                            <td>${payment.month}</td>
                            <td>$${payment.emi.toFixed(2)}</td>
                            <td>$${payment.principal.toFixed(2)}</td>
                            <td>$${payment.interest.toFixed(2)}</td>
                            <td>$${payment.balance.toFixed(2)}</td>
                        `;
                    }
                    
                    amortizationBody.appendChild(row);
                });
            }
            
            function updateCharts(P, totalInterest) {
                // Destroy existing charts if they exist
                if (pieChart) pieChart.destroy();
                if (paymentChart) paymentChart.destroy();
                
                // Pie Chart (Principal vs Interest)
                const pieCtx = document.getElementById('pieChart').getContext('2d');
                pieChart = new Chart(pieCtx, {
                    type: 'pie',
                    data: {
                        labels: ['Principal', 'Interest'],
                        datasets: [{
                            data: [P, totalInterest],
                            backgroundColor: ['#3498db', '#e74c3c'],
                            borderWidth: 1
                        }]
                    },
                    options: {
                        responsive: true,
                        maintainAspectRatio: false,
                        plugins: {
                            title: {
                                display: true,
                                text: 'Payment Composition',
                                font: {
                                    size: 14
                                }
                            },
                            legend: {
                                position: 'bottom'
                            }
                        }
                    }
                });
                
                // Payment Schedule Chart
                const paymentCtx = document.getElementById('paymentChart').getContext('2d');
                paymentChart = new Chart(paymentCtx, {
                    type: 'bar',
                    data: {
                        labels: ['Principal', 'Interest'],
                        datasets: [{
                            label: 'Amount ($)',
                            data: [P, totalInterest],
                            backgroundColor: ['#3498db', '#e74c3c'],
                            borderWidth: 1
                        }]
                    },
                    options: {
                        responsive: true,
                        maintainAspectRatio: false,
                        plugins: {
                            title: {
                                display: true,
                                text: 'Total Payment Breakdown',
                                font: {
                                    size: 14
                                }
                            },
                            legend: {
                                display: false
                            }
                        },
                        scales: {
                            y: {
                                beginAtZero: true,
                                ticks: {
                                    callback: function(value) {
                                        return '$' + value.toLocaleString();
                                    }
                                }
                            }
                        }
                    }
                });
            }
        });
    </script>
</body>
</html>
