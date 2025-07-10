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

pip install pandas matplotlib

import pandas as pd
import matplotlib.pyplot as plt

# === Entradas de dados (simuladas) ===
dados = {
    'Data': ['2025-07-01', '2025-07-03', '2025-07-05', '2025-07-07', '2025-07-09'],
    'Descrição': ['Salário', 'Supermercado', 'Aluguel', 'Transporte', 'Lazer'],
    'Categoria': ['Receita', 'Despesa', 'Despesa', 'Despesa', 'Despesa'],
    'Valor': [5000.00, -450.00, -1500.00, -300.00, -200.00]
}

df = pd.DataFrame(dados)
df['Data'] = pd.to_datetime(df['Data'])

# === Saldo atual ===
saldo = df['Valor'].sum()

# === Agrupar por categoria ===
gastos_por_categoria = df[df['Valor'] < 0].groupby('Categoria')['Valor'].sum()

# === Exibir resumo ===
print("=== RESUMO FINANCEIRO ===")
print(df)
print("\nSaldo atual: R$ {:.2f}".format(saldo))
print("\nGastos por categoria:")
print(gastos_por_categoria.abs())

# === Gráfico de gastos ===
plt.figure(figsize=(8,5))
gastos_por_categoria.abs().plot(kind='bar', color='tomato')
plt.title('Gastos por Categoria')
plt.ylabel('Valor (R$)')
plt.xlabel('Categoria')
plt.tight_layout()
plt.grid(axis='y')
plt.show()
