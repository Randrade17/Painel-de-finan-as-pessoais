# 💰 Painel de finanças pessoais

Um painel financeiro pessoal feito com JavaScript puro, permitindo o controle de receitas e despesas de forma simples e visual.

# 🚀 Funcionalidades
- Adicionar, editar e remover transações
- Filtros por categoria e data
- Dashboard com gráficos (Chart.js)
- Saldo total atualizado automaticamente
- Modo claro/escuro
- Dados salvos no navegador via LocalStorage

# 🛠️ Tecnologias
- HTML5
- CSS3
- JavaScript ES6+
- Chart.js

# **Código abaixo**

 index.html 
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Personal Finance Dashboard</title>
  <link rel="stylesheet" href="css/style.css"/>
</head>
<body>
  <header>
    <h1>Personal Finance Dashboard</h1>
    <button id="toggle-theme">Toggle Theme</button>
  </header>

  <main>
    <section id="balance">
      <h2>Current Balance: <span id="balance-amount">$0.00</span></h2>
    </section>

    <section id="form-section">
      <h2>Add Transaction</h2>
      <form id="transaction-form">
        <input type="text" id="description" placeholder="Description" required />
        <input type="number" id="amount" placeholder="Amount" required />
        <select id="type">
          <option value="income">Income</option>
          <option value="expense">Expense</option>
        </select>
        <button type="submit">Add</button>
      </form>
    </section>

    <section id="transactions">
      <h2>Transactions</h2>
      <ul id="transaction-list"></ul>
    </section>

    <section id="charts">
      <h2>Overview</h2>
      <canvas id="chart"></canvas>
    </section>
  </main>

  <script type="module" src="js/app.js"></script>
</body>
</html>

:root {
  --bg-color: #ffffff;
  --text-color: #333333;
  --accent-color: #4caf50;
  --expense-color: #f44336;
}

body.dark {
  --bg-color: #121212;
  --text-color: #f0f0f0;
}

body {
  margin: 0;
  font-family: Arial, sans-serif;
  background: var(--bg-color);
  color: var(--text-color);
}

header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem;
  background: var(--accent-color);
  color: white;
}

main {
  padding: 1rem;
  max-width: 800px;
  margin: auto;
}

section {
  margin-bottom: 2rem;
}

form {
  display: flex;
  flex-wrap: wrap;
  gap: 0.5rem;
}

form input,
form select,
form button {
  padding: 0.5rem;
  font-size: 1rem;
}

#transaction-list {
  list-style: none;
  padding: 0;
}

#transaction-list li {
  display: flex;
  justify-content: space-between;
  margin-bottom: 0.5rem;
  padding: 0.5rem;
  border: 1px solid #ddd;
  border-radius: 4px;
}

#transaction-list li.income {
  border-left: 4px solid var(--accent-color);
}

#transaction-list li.expense {
  border-left: 4px solid var(--expense-color);
}


js/
├── app.js
├── storage.js
├── transactions.js

📂 js/storage.js

export default class Storage {
  static getTransactions() {
    return JSON.parse(localStorage.getItem("transactions")) || [];
  }

  static saveTransactions(transactions) {
    localStorage.setItem("transactions", JSON.stringify(transactions));
  }
}

📂 js/transactions.js

export default class Transactions {
  constructor() {
    this.transactions = [];
  }

  load(transactions) {
    this.transactions = transactions;
  }

  add(transaction) {
    this.transactions.push(transaction);
  }

  remove(index) {
    this.transactions.splice(index, 1);
  }

  getBalance() {
    return this.transactions.reduce((total, t) => {
      return t.type === "income"
        ? total + t.amount
        : total - t.amount;
    }, 0);
  }
}

📂 js/app.js

import Storage from './storage.js';
import Transactions from './transactions.js';

const transactionForm = document.getElementById('transaction-form');
const descriptionInput = document.getElementById('description');
const amountInput = document.getElementById('amount');
const typeSelect = document.getElementById('type');
const transactionList = document.getElementById('transaction-list');
const balanceAmount = document.getElementById('balance-amount');
const toggleTheme = document.getElementById('toggle-theme');

const transactions = new Transactions();
transactions.load(Storage.getTransactions());

function updateUI() {
  transactionList.innerHTML = "";
  transactions.transactions.forEach((t, i) => {
    const li = document.createElement('li');
    li.className = t.type;
    li.innerHTML = `
      <span>${t.description}: $${t.amount.toFixed(2)}</span>
      <button data-index="${i}">Delete</button>
    `;
    transactionList.appendChild(li);
  });
  
  balanceAmount.textContent = `$${transactions.getBalance().toFixed(2)}`;
  Storage.saveTransactions(transactions.transactions);
  }

transactionForm.addEventListener('submit', (e) => {
  e.preventDefault();
  const transaction = {
    description: descriptionInput.value,
    amount: parseFloat(amountInput.value),
    type: typeSelect.value
  };
  transactions.add(transaction);
  updateUI();
  transactionForm.reset();
});

transactionList.addEventListener('click', (e) => {
  if (e.target.tagName === 'BUTTON') {
    const index = e.target.dataset.index;
    transactions.remove(index);
    updateUI();
  }
});

toggleTheme.addEventListener('click', () => {
  document.body.classList.toggle('dark');
});

updateUI();
