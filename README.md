
!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<title>Card Table Highlighter</title>
<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;600&display=swap');

body {
font-family: 'Poppins', sans-serif;
background: #121212;
color: #eee;
margin: 20px;
display: flex;
flex-direction: column;
align-items: center;
min-height: 100vh;
}

h2 {
font-weight: 600;
margin-bottom: 10px;
color: #81c784;
}

button {
background-color: #81c784;
color: #121212;
border: none;
border-radius: 8px;
padding: 10px 20px;
font-weight: 600;
cursor: pointer;
transition: background-color 0.3s ease;
box-shadow: 0 2px 5px rgba(129, 199, 132, 0.6);
user-select: none;
margin: 10px 5px;
}

button:hover {
background-color: #66bb6a;
box-shadow: 0 4px 12px rgba(102, 187, 106, 0.8);
}

#tableContainer {
margin-top: 30px;
width: 100%;
max-width: 900px;
overflow-x: auto;
box-shadow: 0 4px 20px rgba(0,0,0,0.6);
border-radius: 12px;
background: #1e1e1e;
}

table {
width: 100%;
border-collapse: separate;
border-spacing: 0 10px;
font-size: 14px;
}

th, td {
padding: 12px 15px;
text-align: center;
color: #ddd;
user-select: none;
white-space: nowrap;
}

th {
background: #2e7d32;
color: #d0f0c0;
font-weight: 600;
border-top-left-radius: 12px;
border-top-right-radius: 12px;
}

td:first-child {
text-align: left;
font-weight: 600;
color: #81c784;
}

tr {
background: #272727;
border-radius: 8px;
transition: background-color 0.3s ease;
}

tr:hover {
background: #333;
}

.green {
background-color: #388e3c !important;
color: #e8f5e9 !important;
font-weight: 700;
box-shadow: 0 0 10px #66bb6a;
border-radius: 6px;
}

.yellow {
background-color: #fbc02d !important;
color: #3e2723 !important;
font-weight: 700;
box-shadow: 0 0 10px #ffeb3b;
border-radius: 6px;
}

.blue {
background-color: #1976d2 !important;
color: #e3f2fd !important;
font-weight: 600;
box-shadow: 0 0 8px #64b5f6;
border-radius: 6px;
}

.orange {
background-color: #f57c00 !important;
color: #fff3e0 !important;
font-weight: 600;
box-shadow: 0 0 8px #ffb74d;
border-radius: 6px;
}

.card-btn {
background: none;
border: none;
color: inherit;
font: inherit;
cursor: pointer;
padding: 8px 10px;
border-radius: 6px;
transition: background 0.2s ease, box-shadow 0.2s ease;
width: 100%;
height: 100%;
font-weight: 500;
}

.card-btn:hover {
background-color: #333;
box-shadow: 0 0 5px #81c784;
}

#history {
margin-top: 40px;
max-width: 900px;
width: 100%;
background: #1e1e1e;
padding: 20px;
border-radius: 12px;
box-shadow: 0 4px 20px rgba(0,0,0,0.7);
font-size: 16px;
color: #c8e6c9;
user-select: none;
}

#history ul {
padding-left: 20px;
list-style-type: decimal;
color: #c8e6c9;
margin-top: 0;
}
</style>
</head>
<body>

<h2>Click a Card to Select</h2>
<button onclick="endSession()">Stop Session</button>

<div id="tableContainer"></div>
<div id="history"></div>

<script>
const cardOrder = ['A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K'];
const suits = ['hearts', 'diamonds', 'clubs', 'spades'];
let history = [];

function getNeighbors(index) {
const above = index > 0 ? cardOrder[index - 1] : cardOrder[cardOrder.length - 1]; // Wrap from A to K
const below = index < cardOrder.length - 1 ? cardOrder[index + 1] : cardOrder[0]; // Wrap from K to A
return [above, below];
}


function processCardClick(value, suit) {
const selectedCard = `${value} of ${suit}`;
history.push(selectedCard);
drawTable();
}

function drawTable() {
const table = document.createElement("table");
const headerRow = document.createElement("tr");

const th = document.createElement("th");
th.textContent = "Value";
headerRow.appendChild(th);

suits.forEach(suit => {
const th = document.createElement("th");
th.textContent = suit.charAt(0).toUpperCase() + suit.slice(1);
headerRow.appendChild(th);
});
table.appendChild(headerRow);

// Collect highlight info per card in history
let highlights = [];

history.forEach((cardStr, idx) => {
const [val, , suit] = cardStr.split(" ");
const index = cardOrder.indexOf(val);
const [above, below] = getNeighbors(index);
highlights.push({
value: val,
suit: suit,
above,
below,
isLatest: idx === history.length - 1,
});
});

cardOrder.forEach(value => {
const row = document.createElement("tr");
const tdLabel = document.createElement("td");
tdLabel.textContent = value;
row.appendChild(tdLabel);

suits.forEach(suit => {
const cardText = `${value} of ${suit}`;
const td = document.createElement("td");

const btn = document.createElement("button");
btn.textContent = cardText;
btn.onclick = () => processCardClick(value, suit);
btn.classList.add("card-btn");

let appliedClass = "";

for (let h of highlights) {
if (value === h.value) {
appliedClass = h.isLatest ? "green" : "blue";
} else if ((value === h.above || value === h.below) && suit === h.suit) {
appliedClass = h.isLatest ? "yellow" : "orange";
}
if (h.isLatest && appliedClass) break;
}

if (appliedClass) btn.classList.add(appliedClass);

td.appendChild(btn);
row.appendChild(td);
});

table.appendChild(row);
});

document.getElementById("tableContainer").innerHTML = "";
document.getElementById("tableContainer").appendChild(table);
}

function endSession() {
if (history.length === 0) {
alert("No cards were selected.");
return;
}

let historyHTML = "<h3>Session Ended. Card History:</h3><ul>";
history.forEach((card) => {
historyHTML += `<li>${card}</li>`;
});
historyHTML += "</ul>";
document.getElementById("history").innerHTML = historyHTML;

document.querySelectorAll(".card-btn").forEach(btn => btn.disabled = true);
}

// Initial render
drawTable();
</script>

</body>
</html>
