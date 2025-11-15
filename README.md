# Financial-and-regulatory-impact-simulator-
Financial and regulatory impact simulator.
Intelligent Investment Agent (IIA) - Financial & Regulatory Impact Simulator
This repository contains the Capstone Project for the Intelligent Investment Agent (IIA), a full-stack proof-of-concept designed to integrate advanced financial analysis with real-time regulatory compliance and fiscal intelligence.
The core application simulates stock purchase and sale transactions to provide an immediate, consolidated view of their impact across critical financial statements and regulatory obligations for an assessee (individual, firm, or company).
💡 Key Features
Real-Time P&L Impact: Instantly calculates and displays realized capital gains/losses upon sale transactions.
Balance Sheet Visualization: Tracks total investment cost basis (Book Value) and current market value (Asset).
Cash Flow Statement (CFS) Tracking: Provides Year-to-Date (YTD) net cash flow from investing activities.
Proactive Regulatory Alerts: The system triggers mandatory compliance alerts based on transaction data:
Income Tax (ITR): Calculates and highlights STCG (Short-Term Capital Gains) and LTCG (Long-Term Capital Gains) liability using FIFO (First-In, First-Out) logic.
GST Obligations: Highlights GST paid on transaction fees, prompting the assessee to check ITC (Input Tax Credit) eligibility.
IND AS / GAAP: Provides reminders on accounting standards (e.g., Fair Value treatment under IND AS 109).
Company Law / SEBI: Triggers alerts for significant transaction values that may require mandatory regulatory disclosure.
YTD Transaction History: Logs all transactions for audit and reporting purposes.
🛠️ Technology Stack
The simulator is built entirely within a single file for maximum portability and ease of demonstration.
Frontend: HTML5
Styling: Tailwind CSS (via CDN)
Logic: Vanilla JavaScript (ES Modules)
🚀 Getting Started
Since this is a single HTML file, setup is minimal.
Prerequisites
You only need a modern web browser (Chrome, Firefox, Edge, Safari).
Installation and Usage
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>IIA - Financial & Regulatory Impact Simulator</title>
    <!-- Load Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.fonts.gstatic.com/s/inter/v13/U6HMAJ5XyIHRxY0-JQ.woff2?text=Inter');
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f7f9fc;
        }
        .card {
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -2px rgba(0, 0, 0, 0.06);
        }
        .positive { color: #10b981; } /* Emerald Green */
        .negative { color: #ef4444; } /* Red */
    </style>
</head>
<body class="p-4 md:p-8">

    <div id="app" class="max-w-7xl mx-auto">
        <h1 class="text-3xl font-extrabold text-gray-900 mb-6 border-b pb-2">
            Intelligent Investment Agent (IIA) Simulation
        </h1>
        <p class="text-gray-600 mb-8">
            Simulate stock transactions to view the real-time impact on financial statements, YTD transactions, and regulatory obligations (Income Tax, GST, IND AS, Company Law).
        </p>

        <!-- Main Input and Control Panel -->
        <div class="card bg-white p-6 rounded-xl mb-8 border border-gray-100">
            <h2 class="text-xl font-bold mb-4 text-indigo-700">Transaction Panel</h2>
            <div class="grid grid-cols-1 md:grid-cols-5 gap-4">
                <input type="text" id="ticker" placeholder="Stock Ticker (e.g., GOOGL)" value="TATA" class="col-span-1 p-3 border border-gray-300 rounded-lg focus:ring-indigo-500 focus:border-indigo-500">
                <input type="number" id="quantity" placeholder="Quantity" value="10" min="1" class="col-span-1 p-3 border border-gray-300 rounded-lg focus:ring-indigo-500 focus:border-indigo-500">
                <input type="number" id="price" placeholder="Price" value="1500" min="0.01" step="0.01" class="col-span-1 p-3 border border-gray-300 rounded-lg focus:ring-indigo-500 focus:border-indigo-500">
                <input type="date" id="date" class="col-span-1 p-3 border border-gray-300 rounded-lg focus:ring-indigo-500 focus:border-indigo-500">
                <div class="col-span-1 flex space-x-2">
                    <button onclick="handleTransaction('BUY')" class="flex-1 bg-green-600 text-white p-3 rounded-lg font-semibold hover:bg-green-700 transition duration-150">BUY</button>
                    <button onclick="handleTransaction('SELL')" class="flex-1 bg-red-600 text-white p-3 rounded-lg font-semibold hover:bg-red-700 transition duration-150">SELL</button>
                </div>
            </div>
        </div>

        <!-- Output Sections Grid -->
        <div class="grid grid-cols-1 lg:grid-cols-3 gap-8">
            
            <!-- Financial Statement Impact -->
            <div class="lg:col-span-2">
                <div class="card bg-white p-6 rounded-xl mb-8 border border-gray-100">
                    <h2 class="text-xl font-bold mb-4 text-indigo-700">Real-Time Financial Statement Impact</h2>
                    <div class="space-y-4">
                        <!-- Balance Sheet -->
                        <div class="border-b pb-3">
                            <h3 class="font-semibold text-lg text-gray-800">Balance Sheet (BS) Impact</h3>
                            <p class="text-sm text-gray-600">Total Investment Value (Asset): <span id="bs-value" class="font-bold positive">₹ 0.00</span></p>
                            <p class="text-sm text-gray-600">Total Cost Basis (Book Value): <span id="bs-cost" class="font-bold text-gray-700">₹ 0.00</span></p>
                            <p class="text-xs text-gray-500 mt-1">*For Firm/Company: Unrealized Gain/Loss may be booked to OCI or P&L (IND AS 109).</p>
                        </div>
                        
                        <!-- P&L Statement -->
                        <div class="border-b pb-3">
                            <h3 class="font-semibold text-lg text-gray-800">Profit & Loss (P&L) Impact</h3>
                            <p class="text-sm text-gray-600">Total YTD Realized Gain/Loss: <span id="pl-realized" class="font-bold positive">₹ 0.00</span></p>
                            <p class="text-sm text-gray-600">Brokerage/Fees (Expense): <span id="pl-fees" class="font-bold negative">₹ 0.00</span></p>
                            <p class="text-xs text-gray-500 mt-1">*Realized gains are booked upon sale. Unrealized gains are excluded from P&L (unless FVTPL).</p>
                        </div>

                         <!-- Cash Flow Statement -->
                         <div>
                            <h3 class="font-semibold text-lg text-gray-800">Cash Flow Statement (CFS) Impact (YTD)</h3>
                            <p class="text-sm text-gray-600">Cash Flow from Investing (Net): <span id="cfs-net" class="font-bold negative">₹ 0.00</span></p>
                            <p class="text-xs text-gray-500 mt-1">*Net of purchase outflow and sale inflow.</p>
                        </div>
                    </div>
                </div>

                <!-- Year to Date Transactions -->
                <div class="card bg-white p-6 rounded-xl border border-gray-100">
                    <h2 class="text-xl font-bold mb-4 text-indigo-700">Year-to-Date Transaction History</h2>
                    <div class="overflow-x-auto">
                        <table class="min-w-full divide-y divide-gray-200">
                            <thead>
                                <tr>
                                    <th class="px-3 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Date</th>
                                    <th class="px-3 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Type</th>
                                    <th class="px-3 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Ticker</th>
                                    <th class="px-3 py-3 text-right text-xs font-medium text-gray-500 uppercase tracking-wider">Qty</th>
                                    <th class="px-3 py-3 text-right text-xs font-medium text-gray-500 uppercase tracking-wider">Price</th>
                                    <th class="px-3 py-3 text-right text-xs font-medium text-gray-500 uppercase tracking-wider">Total Value</th>
                                </tr>
                            </thead>
                            <tbody id="transaction-log" class="bg-white divide-y divide-gray-200">
                                <!-- Transactions will be inserted here -->
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>

            <!-- Regulatory Obligations -->
            <div>
                <div class="card bg-white p-6 rounded-xl border border-gray-100">
                    <h2 class="text-xl font-bold mb-4 text-indigo-700">Regulatory Trigger Alerts</h2>
                    <div id="alerts-container" class="space-y-4">
                        <div id="tax-alert" class="p-3 rounded-lg bg-gray-100 text-gray-700">
                            <p class="font-semibold">Income Tax (ITR) / Capital Gains</p>
                            <span class="text-sm">No Sale Transaction Initiated.</span>
                        </div>
                        <div id="gst-alert" class="p-3 rounded-lg bg-gray-100 text-gray-700">
                            <p class="font-semibold">GST Obligations</p>
                            <span class="text-sm">GST of ₹ 0.00 paid on fees. Check ITC eligibility.</span>
                        </div>
                        <div id="indas-gaar-alert" class="p-3 rounded-lg bg-gray-100 text-gray-700">
                            <p class="font-semibold">IND AS / GAAR Regulations</p>
                            <span class="text-sm">Assessing for fair value and anti-avoidance rationale.</span>
                        </div>
                        <div id="company-law-alert" class="p-3 rounded-lg bg-gray-100 text-gray-700">
                            <p class="font-semibold">Company Law / SEBI</p>
                            <span class="text-sm">No significant stake crossing threshold.</span>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script type="module">
        // Global State
        let portfolio = {}; // { ticker: { qty: N, costBasis: C, buyDates: [{ qty: Q, price: P, date: D }] } }
        let transactions = []; // Full log for YTD view
        let realizedPnl = 0;
        let totalFees = 0;
        const COMMISSION_RATE = 0.001; // 0.1% transaction fee
        const GST_RATE = 0.18; // 18% GST on Commission
        const STCG_RATE = 0.20; // Short-Term Capital Gains Tax Rate
        const LTCG_RATE = 0.125; // Long-Term Capital Gains Tax Rate (Simplified rate for example)

        // Utility: Format currency
        const formatter = new Intl.NumberFormat('en-IN', {
            style: 'currency',
            currency: 'INR',
        });

        // Utility: Convert date string to Date object
        function parseDate(dateString) {
            const [year, month, day] = dateString.split('-');
            return new Date(year, month - 1, day);
        }

        // 1. Transaction Handler
        function handleTransaction(type) {
            const ticker = document.getElementById('ticker').value.toUpperCase();
            const quantity = parseInt(document.getElementById('quantity').value);
            const price = parseFloat(document.getElementById('price').value);
            const dateStr = document.getElementById('date').value;

            if (!ticker || isNaN(quantity) || quantity <= 0 || isNaN(price) || price <= 0 || !dateStr) {
                console.error("Invalid input. Please check all fields.");
                return;
            }

            const transactionDate = parseDate(dateStr);
            const transactionValue = quantity * price;
            const commission = transactionValue * COMMISSION_RATE;
            const gst = commission * GST_RATE;

            totalFees += commission + gst;

            // Add to transaction log (YTD)
            transactions.push({
                date: dateStr,
                type: type,
                ticker: ticker,
                quantity: quantity,
                price: price,
                value: transactionValue
            });

            // 2. Core Logic: FIFO (First-In, First-Out) for tracking cost and calculating P&L/Tax
            if (type === 'BUY') {
                if (!portfolio[ticker]) {
                    portfolio[ticker] = { qty: 0, costBasis: 0, buyDates: [] };
                }

                portfolio[ticker].qty += quantity;
                portfolio[ticker].costBasis += transactionValue;
                portfolio[ticker].buyDates.push({ qty: quantity, price: price, date: transactionDate });
                portfolio[ticker].buyDates.sort((a, b) => a.date - b.date); // Keep FIFO order

                document.getElementById('alerts-container').innerHTML = `
                    <p class="p-3 bg-blue-100 text-blue-700 font-semibold rounded-lg">
                        ✅ BUY Transaction: Capital invested. Balance Sheet Asset Value increased.
                    </p>
                `;

            } else if (type === 'SELL') {
                if (!portfolio[ticker] || portfolio[ticker].qty < quantity) {
                    // Custom Modal/Alert for error, as alert() is forbidden
                    displayCustomAlert(`Error: Cannot sell ${quantity} shares of ${ticker}. Only ${portfolio[ticker] ? portfolio[ticker].qty : 0} available.`);
                    return;
                }

                let remainingSellQty = quantity;
                let totalGain = 0;
                let stcgTax = 0;
                let ltcgTax = 0;
                const ONE_YEAR_MS = 365 * 24 * 60 * 60 * 1000;

                // FIFO selling logic
                while (remainingSellQty > 0 && portfolio[ticker].buyDates.length > 0) {
                    let oldestLot = portfolio[ticker].buyDates[0];
                    let lotQtyToSell = Math.min(remainingSellQty, oldestLot.qty);

                    const lotCost = lotQtyToSell * oldestLot.price;
                    const lotSaleValue = lotQtyToSell * price;
                    const lotGain = lotSaleValue - lotCost;

                    // Tax Classification Check (LTCG > 12 months)
                    const holdingPeriodMs = transactionDate - oldestLot.date;
                    const isLTCG = holdingPeriodMs > ONE_YEAR_MS;
                    
                    if (isLTCG) {
                        ltcgTax += lotGain > 0 ? lotGain * LTCG_RATE : 0;
                    } else {
                        stcgTax += lotGain > 0 ? lotGain * STCG_RATE : 0;
                    }

                    totalGain += lotGain;
                    remainingSellQty -= lotQtyToSell;

                    // Update Lot
                    if (lotQtyToSell === oldestLot.qty) {
                        portfolio[ticker].buyDates.shift(); // Remove lot
                    } else {
                        oldestLot.qty -= lotQtyToSell; // Decrease quantity in lot
                    }
                }

                // Update Portfolio Totals
                const totalCostSold = transactionValue - totalGain;
                portfolio[ticker].qty -= quantity;
                portfolio[ticker].costBasis -= totalCostSold;
                
                realizedPnl += totalGain;

                // 3. Update Regulatory Alerts
                updateRegulatoryAlerts(totalGain, stcgTax, ltcgTax, commission, gst, quantity, transactionValue);
            }

            // 4. Update UI
            updateFinancialStatementImpact();
            updateTransactionLog();
        }

        function updateRegulatoryAlerts(gain, stcgTax, ltcgTax, commission, gst, quantity, value) {
            const taxLiability = stcgTax + ltcgTax;
            const gainStatus = gain >= 0 ? 'Gain' : 'Loss';
            const gainClass = gain >= 0 ? 'bg-green-100 text-green-700' : 'bg-red-100 text-red-700';
            
            // Income Tax (ITR) Alert
            document.getElementById('tax-alert').className = `p-3 rounded-lg font-semibold ${gainClass}`;
            document.getElementById('tax-alert').innerHTML = `
                <p class="font-bold">Income Tax (ITR) / Capital Gains</p>
                <p class="text-sm">Realized ${gainStatus} of ${formatter.format(Math.abs(gain))}.</p>
                <ul class="text-xs list-disc list-inside">
                    <li>STCG Tax Liability: ${formatter.format(stcgTax)} (Taxed @ ${STCG_RATE * 100}%)</li>
                    <li>LTCG Tax Liability: ${formatter.format(ltcgTax)} (Taxed @ ${LTCG_RATE * 100}%)</li>
                    <li><span class="font-bold text-red-600">Total Tax Payable: ${formatter.format(taxLiability)}</span></li>
                </ul>
            `;

            // GST Alert
            document.getElementById('gst-alert').className = `p-3 rounded-lg bg-yellow-100 text-yellow-700`;
            document.getElementById('gst-alert').innerHTML = `
                <p class="font-bold">GST Obligations</p>
                <span class="text-sm">GST of ${formatter.format(gst)} paid on this commission. <br> <span class="font-semibold">Action:</span> Firm/Company assess ITC eligibility.</span>
            `;

            // Company Law / SEBI Alert
            const disclosureThreshold = 1000000; // Simplified threshold for example (e.g., transaction value exceeds 10L)
            if (value > disclosureThreshold) {
                document.getElementById('company-law-alert').className = `p-3 rounded-lg bg-red-200 text-red-800`;
                document.getElementById('company-law-alert').innerHTML = `
                    <p class="font-bold">🚨 Company Law / SEBI DISCLOSURE TRIGGER</p>
                    <span class="text-sm">Transaction Value ${formatter.format(value)} exceeds ${formatter.format(disclosureThreshold)}. <span class="font-semibold">Action:</span> Check for mandatory SEBI/Company Law reporting requirements (e.g., significant stake acquisition/disposal).</span>
                `;
            } else {
                document.getElementById('company-law-alert').className = `p-3 rounded-lg bg-gray-100 text-gray-700`;
                document.getElementById('company-law-alert').innerHTML = `
                    <p class="font-semibold">Company Law / SEBI</p>
                    <span class="text-sm">No significant stake crossing threshold.</span>
                `;
            }
            
            // IND AS / GAAR Alert (General check)
            document.getElementById('indas-gaar-alert').className = `p-3 rounded-lg bg-orange-100 text-orange-700`;
            document.getElementById('indas-gaar-alert').innerHTML = `
                <p class="font-bold">IND AS / GAAR Regulations</p>
                <span class="text-sm">P&L booked as realized gain. <span class="font-semibold">IND AS 109:</span> Investments should be valued at Fair Value on BS. <span class="font-semibold">GAAR:</span> Ensure genuine commercial rationale for tax loss/gain transactions.</span>
            `;

        }

        // 4. Financial Statement Update
        function updateFinancialStatementImpact() {
            let totalMarketValue = 0;
            let totalCostBasis = 0;
            let netCashFlow = 0;

            // Calculate current portfolio value
            for (const ticker in portfolio) {
                const stock = portfolio[ticker];
                // For simplicity, we use the last transaction price as the "current price"
                const currentPrice = parseFloat(document.getElementById('price').value); 
                
                if(stock.qty > 0) {
                    totalMarketValue += stock.qty * currentPrice;
                    totalCostBasis += stock.costBasis;
                }
            }

            // Calculate Net Cash Flow from Investing (YTD)
            transactions.forEach(t => {
                const value = t.value + (t.value * COMMISSION_RATE * (1 + GST_RATE)); // Include full transaction cost
                if (t.type === 'BUY') {
                    netCashFlow -= value; // Outflow
                } else {
                    netCashFlow += value; // Inflow
                }
            });

            // Render values
            document.getElementById('bs-value').textContent = formatter.format(totalMarketValue);
            document.getElementById('bs-value').className = totalMarketValue >= totalCostBasis ? 'font-bold positive' : 'font-bold negative';
            document.getElementById('bs-cost').textContent = formatter.format(totalCostBasis);
            
            document.getElementById('pl-realized').textContent = formatter.format(realizedPnl);
            document.getElementById('pl-realized').className = realizedPnl >= 0 ? 'font-bold positive' : 'font-bold negative';
            document.getElementById('pl-fees').textContent = formatter.format(totalFees);
            
            document.getElementById('cfs-net').textContent = formatter.format(netCashFlow);
            document.getElementById('cfs-net').className = netCashFlow <= 0 ? 'font-bold negative' : 'font-bold positive';

        }

        // 5. Transaction Log Update
        function updateTransactionLog() {
            const logElement = document.getElementById('transaction-log');
            logElement.innerHTML = '';
            
            transactions.sort((a, b) => new Date(b.date) - new Date(a.date)); // Sort latest first

            transactions.forEach(t => {
                const row = logElement.insertRow();
                cons
The MIT License (MIT)
Copyright (c) 2025 [Megha Bhatia ]
Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:
The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.
THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.

