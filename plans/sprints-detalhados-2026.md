# 🏃 Plano de Sprints Detalhados - Portfolio Management 2026

## 📋 Visão Geral

**Metodologia**: Sprints de 1 semana  
**Duração Total**: 12 semanas (3 meses)  
**Objectivo**: Implementar features prioritárias do roadmap

---

## 🎯 Sprint 0: Preparação (20-27 Mar 2026)

### Objectivo
Preparar ambiente e implementar features críticas de backup.

### Tarefas

#### 1. Exportação de Dados
**Prioridade**: 🔴 Crítica  
**Tempo Estimado**: 2-3 horas

**Implementação**:
```javascript
// Adicionar após linha 2340 (antes do </script>)

// ═══════════════════════════════════════════════════
// EXPORT / IMPORT SYSTEM
// ═══════════════════════════════════════════════════

function exportPortfolio() {
  const data = {
    version: '2.0',
    exportDate: new Date().toISOString(),
    positions: positions,
    watchlist: watchlist,
    fundamentals: fundamentals,
    thesisNotes: thesisNotes,
    apiKeys: {
      alphavantage: API_CONFIG.alphavantage.key !== 'demo' ? '***' : '',
      finnhub: API_CONFIG.finnhub.key ? '***' : '',
      fmp: API_CONFIG.fmp.key ? '***' : ''
    }
  };
  
  const blob = new Blob([JSON.stringify(data, null, 2)], 
    { type: 'application/json' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = `portfolio-backup-${Date.now()}.json`;
  a.click();
  URL.revokeObjectURL(url);
  
  toast('Portfolio exportado com sucesso ✓', 'ok');
}

function importPortfolio() {
  const input = document.createElement('input');
  input.type = 'file';
  input.accept = '.json';
  
  input.onchange = (e) => {
    const file = e.target.files[0];
    if (!file) return;
    
    const reader = new FileReader();
    reader.onload = (event) => {
      try {
        const data = JSON.parse(event.target.result);
        
        // Validação
        if (!data.version || !data.positions) {
          throw new Error('Ficheiro inválido');
        }
        
        // Confirmação
        if (!confirm(`Importar ${data.positions.length} posições? Isto vai substituir os dados actuais.`)) {
          return;
        }
        
        // Importar dados
        positions = data.positions || [];
        watchlist = data.watchlist || [];
        fundamentals = data.fundamentals || {};
        thesisNotes = data.thesisNotes || {};
        
        // Guardar
        save();
        saveWL();
        saveFundamentals();
        saveThesisNotes();
        
        // Actualizar UI
        render();
        renderWatchlist();
        
        toast(`${positions.length} posições importadas com sucesso ✓`, 'ok');
        
      } catch (e) {
        console.error('Import error:', e);
        toast('Erro ao importar ficheiro. Verifica o formato.', 'err');
      }
    };
    
    reader.readAsText(file);
  };
  
  input.click();
}

function exportToCSV() {
  // Header
  let csv = 'Ticker,Nome,Sector,Moeda,Quantidade,Preço Médio,Preço Actual,Valor,P&L,P&L %\n';
  
  // Dados
  positions.forEach(p => {
    const value = p.qty * p.curPrice;
    const cost = p.qty * p.avgPrice;
    const pnl = value - cost;
    const pnlPct = cost > 0 ? (pnl / cost) * 100 : 0;
    
    csv += `${p.ticker},"${p.name}",${p.sector},${p.currency},${p.qty},${p.avgPrice},${p.curPrice},${value.toFixed(2)},${pnl.toFixed(2)},${pnlPct.toFixed(2)}\n`;
  });
  
  const blob = new Blob([csv], { type: 'text/csv' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = `portfolio-${Date.now()}.csv`;
  a.click();
  URL.revokeObjectURL(url);
  
  toast('CSV exportado com sucesso ✓', 'ok');
}
```

**HTML** (adicionar no header, linha ~162):
```html
<button class="refresh-btn" onclick="exportPortfolio()" title="Exportar dados">💾 Exportar</button>
<button class="refresh-btn" onclick="importPortfolio()" title="Importar dados">📂 Importar</button>
<button class="refresh-btn" onclick="exportToCSV()" title="Exportar CSV">📊 CSV</button>
```

#### 2. Melhorar README
**Tempo Estimado**: 1 hora

- [ ] Adicionar badges (versão, licença)
- [ ] Actualizar screenshots
- [ ] Adicionar secção de backup/restore
- [ ] Documentar como obter API keys
- [ ] Adicionar FAQ

#### 3. Testes de Validação
**Tempo Estimado**: 2 horas

- [ ] Testar exportação/importação
- [ ] Testar com 10+ tickers diferentes
- [ ] Validar todas as APIs
- [ ] Verificar performance
- [ ] Testar em 3 browsers

### Critérios de Sucesso
- ✅ Exportação funciona (JSON + CSV)
- ✅ Importação funciona com validação
- ✅ README actualizado
- ✅ Testes passam

### Entregáveis
- Funcionalidade de backup completa
- Documentação actualizada
- Relatório de testes

---

## 🏃 Sprint 1: Histórico de Transacções (27 Mar - 3 Abr 2026)

### Objectivo
Implementar sistema de tracking de transacções.

### Tarefas

#### 1. Modelo de Dados
**Tempo Estimado**: 1 hora

```javascript
// Estrutura de transacções
let transactions = JSON.parse(localStorage.getItem('transactions_v1') || '[]');

// Tipos de transacção
const TX_TYPES = {
  BUY: 'BUY',
  SELL: 'SELL',
  DIVIDEND: 'DIVIDEND',
  SPLIT: 'SPLIT',
  FEE: 'FEE'
};

// Exemplo de transacção
const transaction = {
  id: 'tx_' + Date.now(),
  type: 'BUY',
  ticker: 'AAPL',
  date: '2024-01-15',
  qty: 10,
  price: 145.00,
  fees: 5.00,
  total: 1455.00,
  currency: 'USD',
  notes: 'Compra inicial'
};
```

#### 2. Nova Página "Histórico"
**Tempo Estimado**: 3 horas

**HTML** (adicionar após linha 467):
```html
<!-- ══════════════════════════════════════════ -->
<!-- PAGE: HISTÓRICO                           -->
<!-- ══════════════════════════════════════════ -->
<div class="page" id="page-historico">
  
  <!-- Adicionar Transacção -->
  <div class="card" style="margin-bottom:20px">
    <div class="section-title">
      <span class="dot" style="background:var(--green)"></span>
      Nova Transacção
    </div>
    <div class="form-grid">
      <div class="form-group">
        <label>Tipo</label>
        <select id="txType">
          <option value="BUY">Compra</option>
          <option value="SELL">Venda</option>
          <option value="DIVIDEND">Dividendo</option>
        </select>
      </div>
      <div class="form-group">
        <label>Ticker</label>
        <input type="text" id="txTicker" placeholder="ex: AAPL"/>
      </div>
      <div class="form-group">
        <label>Data</label>
        <input type="date" id="txDate"/>
      </div>
      <div class="form-group">
        <label>Quantidade</label>
        <input type="number" id="txQty" placeholder="10" step="any"/>
      </div>
      <div class="form-group">
        <label>Preço</label>
        <input type="number" id="txPrice" placeholder="145.00" step="any"/>
      </div>
      <div class="form-group">
        <label>Comissões</label>
        <input type="number" id="txFees" placeholder="5.00" step="any" value="0"/>
      </div>
    </div>
    <div style="margin-top:12px">
      <button class="btn" onclick="addTransaction()">➕ Adicionar Transacção</button>
    </div>
  </div>
  
  <!-- Tabela de Transacções -->
  <div class="card">
    <div class="section-title">
      <span class="dot" style="background:var(--accent2)"></span>
      Histórico de Transacções
    </div>
    <div class="table-wrap">
      <table>
        <thead>
          <tr>
            <th>Data</th>
            <th>Tipo</th>
            <th>Ticker</th>
            <th>Qtd.</th>
            <th>Preço</th>
            <th>Comissões</th>
            <th>Total</th>
            <th></th>
          </tr>
        </thead>
        <tbody id="transactionsTable">
          <tr><td colspan="8"><div class="empty"><div class="icon">📜</div>Sem transacções registadas.</div></td></tr>
        </tbody>
      </table>
    </div>
  </div>
  
  <!-- Estatísticas -->
  <div class="grid-4" style="margin-top:20px">
    <div class="card">
      <div class="card-title">Total Investido</div>
      <div class="card-value" id="txTotalInvested">—</div>
    </div>
    <div class="card">
      <div class="card-title">Total Vendido</div>
      <div class="card-value" id="txTotalSold">—</div>
    </div>
    <div class="card">
      <div class="card-title">P&L Realizado</div>
      <div class="card-value" id="txRealizedPnL">—</div>
    </div>
    <div class="card">
      <div class="card-title">Dividendos Recebidos</div>
      <div class="card-value" id="txDividends">—</div>
    </div>
  </div>
</div>
```

#### 3. Funções JavaScript
**Tempo Estimado**: 2 horas

```javascript
function addTransaction() {
  const type = document.getElementById('txType').value;
  const ticker = document.getElementById('txTicker').value.trim().toUpperCase();
  const date = document.getElementById('txDate').value;
  const qty = parseFloat(document.getElementById('txQty').value);
  const price = parseFloat(document.getElementById('txPrice').value);
  const fees = parseFloat(document.getElementById('txFees').value) || 0;
  
  if (!ticker || !date || isNaN(qty) || isNaN(price)) {
    toast('Preenche todos os campos obrigatórios.', 'err');
    return;
  }
  
  const total = (type === 'BUY' ? qty * price + fees : qty * price - fees);
  
  const tx = {
    id: 'tx_' + Date.now(),
    type,
    ticker,
    date,
    qty,
    price,
    fees,
    total,
    currency: 'USD',
    notes: ''
  };
  
  transactions.push(tx);
  transactions.sort((a, b) => new Date(b.date) - new Date(a.date));
  
  localStorage.setItem('transactions_v1', JSON.stringify(transactions));
  
  renderTransactions();
  
  // Limpar form
  document.getElementById('txTicker').value = '';
  document.getElementById('txQty').value = '';
  document.getElementById('txPrice').value = '';
  document.getElementById('txFees').value = '0';
  
  toast('Transacção adicionada ✓', 'ok');
}

function renderTransactions() {
  const tbody = document.getElementById('transactionsTable');
  
  if (transactions.length === 0) {
    tbody.innerHTML = '<tr><td colspan="8"><div class="empty"><div class="icon">📜</div>Sem transacções.</div></td></tr>';
    return;
  }
  
  tbody.innerHTML = transactions.map((tx, i) => {
    const typeLabel = {
      'BUY': '🟢 Compra',
      'SELL': '🔴 Venda',
      'DIVIDEND': '💰 Dividendo'
    }[tx.type];
    
    return `
      <tr>
        <td>${new Date(tx.date).toLocaleDateString('pt-PT')}</td>
        <td>${typeLabel}</td>
        <td><span class="ticker-badge">${tx.ticker}</span></td>
        <td>${tx.qty}</td>
        <td>${fmtC(tx.price, tx.currency)}</td>
        <td>${fmtC(tx.fees, tx.currency)}</td>
        <td style="font-weight:600">${fmtC(tx.total, tx.currency)}</td>
        <td>
          <button class="btn danger" onclick="deleteTransaction(${i})">✕</button>
        </td>
      </tr>`;
  }).join('');
  
  updateTransactionStats();
}

function updateTransactionStats() {
  const stats = transactions.reduce((acc, tx) => {
    if (tx.type === 'BUY') acc.invested += tx.total;
    if (tx.type === 'SELL') acc.sold += tx.total;
    if (tx.type === 'DIVIDEND') acc.dividends += tx.total;
    return acc;
  }, { invested: 0, sold: 0, dividends: 0 });
  
  const realizedPnL = stats.sold - stats.invested;
  
  document.getElementById('txTotalInvested').textContent = fmtC(stats.invested);
  document.getElementById('txTotalSold').textContent = fmtC(stats.sold);
  document.getElementById('txDividends').textContent = fmtC(stats.dividends);
  
  const pnlEl = document.getElementById('txRealizedPnL');
  pnlEl.textContent = (realizedPnL >= 0 ? '+' : '') + fmtC(realizedPnL);
  pnlEl.className = 'card-value ' + (realizedPnL >= 0 ? 'up' : 'down');
}

function deleteTransaction(i) {
  if (!confirm('Remover esta transacção?')) return;
  transactions.splice(i, 1);
  localStorage.setItem('transactions_v1', JSON.stringify(transactions));
  renderTransactions();
}
```

#### 4. Adicionar Tab de Navegação
**Tempo Estimado**: 15 min

Linha ~168:
```html
<button class="nav-tab" onclick="showPage('historico')">📜 Histórico</button>
```

Actualizar função `showPage()`:
```javascript
if (id === 'historico') renderTransactions();
```

### Critérios de Sucesso
- ✅ Adicionar transacções funciona
- ✅ Tabela mostra histórico
- ✅ Estatísticas calculadas correctamente
- ✅ Persistência em localStorage
- ✅ Exportação inclui transacções

### Entregáveis
- Página de histórico funcional
- Sistema de transacções completo
- Estatísticas de P&L realizado

---

## 🏃 Sprint 2: Sistema de Dividendos (3-10 Abr 2026)

### Objectivo
Implementar tracking de dividendos e yield on cost.

### Tarefas

#### 1. Adicionar Campo de Dividendos às Posições
**Tempo Estimado**: 2 horas

```javascript
// Actualizar estrutura de posição
position = {
  // ... campos existentes
  dividendYield: 0.52,
  annualDividend: 0.98,
  lastDividendDate: '2024-02-15',
  nextDividendDate: '2024-05-15',
  dividendsReceived: 0
};
```

#### 2. Widget de Dividendos
**Tempo Estimado**: 2 horas

Adicionar na página Portfolio:
```html
<div class="card" style="margin-bottom:24px">
  <div class="section-title">
    <span class="dot" style="background:var(--green)"></span>
    💰 Rendimento de Dividendos
  </div>
  <div class="grid-4">
    <div class="card">
      <div class="card-title">Rendimento Anual Projectado</div>
      <div class="card-value" id="divAnnualIncome">—</div>
    </div>
    <div class="card">
      <div class="card-title">Yield on Cost Médio</div>
      <div class="card-value" id="divYieldOnCost">—</div>
    </div>
    <div class="card">
      <div class="card-title">Dividendos Recebidos (YTD)</div>
      <div class="card-value" id="divReceived">—</div>
    </div>
    <div class="card">
      <div class="card-title">Próximo Pagamento</div>
      <div class="card-value" id="divNext">—</div>
    </div>
  </div>
</div>
```

### Critérios de Sucesso
- ✅ Yield on cost calculado
- ✅ Rendimento anual projectado
- ✅ Dividendos recebidos tracked

### Entregáveis
- Widget de dividendos funcional
- Cálculos de yield on cost

---

## 🏃 Sprint 3: Alertas e Notificações (10-17 Abr 2026)

### Objectivo
Expandir sistema de alertas além de preço.

### Tarefas

#### 1. Sistema de Alertas Expandido
**Tempo Estimado**: 3 horas

```javascript
const alerts = [
  {
    id: 'alert_001',
    ticker: 'AAPL',
    type: 'PRICE_BELOW',
    value: 150,
    active: true,
    triggered: false
  }
];

const ALERT_TYPES = {
  PRICE_BELOW: 'Preço abaixo de',
  PRICE_ABOVE: 'Preço acima de',
  PE_BELOW: 'P/E abaixo de',
  DIVIDEND_YIELD_ABOVE: 'Dividend Yield acima de'
};
```

### Critérios de Sucesso
- ✅ Múltiplos tipos de alertas
- ✅ Verificação automática
- ✅ Notificações visuais

---

## 🏃 Sprint 4: Análise de Performance (17-24 Abr 2026)

### Objectivo
Adicionar métricas de performance e comparação com benchmarks.

### Tarefas

#### 1. Gráfico de Evolução do Portfolio
**Tempo Estimado**: 3 horas

```javascript
const portfolioHistory = [
  {
    date: '2024-01-01',
    totalValue: 10000,
    totalCost: 9500,
    pnl: 500
  }
];

function takeSnapshot() {
  const totalValue = positions.reduce((s,p) => s + p.qty * p.curPrice, 0);
  const totalCost = positions.reduce((s,p) => s + p.qty * p.avgPrice, 0);
  
  portfolioHistory.push({
    date: new Date().toISOString().split('T')[0],
    totalValue,
    totalCost,
    pnl: totalValue - totalCost
  });
  
  localStorage.setItem('portfolio_history_v1', JSON.stringify(portfolioHistory));
}
```

### Critérios de Sucesso
- ✅ Gráfico de evolução funciona
- ✅ Comparação com benchmarks

---

## 🏃 Sprint 5: Melhorias na Pesquisa (24 Abr - 1 Mai 2026)

### Objectivo
Adicionar screener e filtros avançados.

### Tarefas

#### 1. Screener de Acções
**Tempo Estimado**: 4 horas

```javascript
const screenCriteria = {
  peMin: 0,
  peMax: 25,
  divYieldMin: 2,
  roeMin: 15,
  sector: 'Tecnologia'
};
```

---

## 🏃 Sprint 6-12: Features Adicionais

### Sprint 6: Integração com Brokers (CSV Import)
- Importar transacções de Degiro
- Importar de Interactive Brokers

### Sprint 7: Análise Técnica Básica
- RSI, MACD
- Médias móveis

### Sprint 8: Portfolio Optimization
- Sugestões de rebalanceamento
- Análise de diversificação

### Sprint 9: Modo Multi-Portfolio
- Criar múltiplos portfolios
- Comparar performance

### Sprint 10: PWA e Offline
- Service Worker
- Offline support

### Sprint 11: Melhorias de UX
- Modo escuro/claro
- Animações

### Sprint 12: Polimento e Testes
- Testes completos
- Release v3.0

---

## 📊 Resumo do Plano

```
Sprint 0 (Mar 20-27): Exportação/Importação ✅
Sprint 1 (Mar 27-Abr 3): Histórico de Transacções
Sprint 2 (Abr 3-10): Sistema de Dividendos
Sprint 3 (Abr 10-17): Alertas e Notificações
Sprint 4 (Abr 17-24): Análise de Performance
Sprint 5 (Abr 24-Mai 1): Melhorias na Pesquisa
Sprint 6-12 (Mai-Jun): Features Adicionais
```

## 🎯 Próxima Acção

**Começar Sprint 0** implementando a funcionalidade de Exportação/Importação.

Mudar para **modo Code** e seguir as instruções detalhadas acima.

---

**Última Actualização**: 20 Março 2026  
**Versão**: 2.0 → 3.0  
**Duração Total**: 12 semanas
