# 🧪 Plano de Testes e Validação - Portfolio Management

## 📋 Visão Geral

Este documento define a estratégia completa de testes para garantir a qualidade, fiabilidade e performance do Portfolio Management Dashboard.

---

## 🎯 Objectivos de Qualidade

### Métricas de Sucesso
- ✅ **Taxa de Sucesso**: >95% das operações funcionam correctamente
- ✅ **Performance**: <3s tempo de resposta em 90% dos casos
- ✅ **Compatibilidade**: Funciona em 4+ browsers modernos
- ✅ **Cobertura**: 100% das features críticas testadas
- ✅ **Zero Bugs Críticos**: Nenhum bug que impeça uso básico

---

## 🧪 Tipos de Testes

### 1. Testes Funcionais
Verificar que todas as funcionalidades funcionam como esperado.

### 2. Testes de Integração
Verificar que componentes funcionam bem juntos.

### 3. Testes de Performance
Verificar que a aplicação é rápida e eficiente.

### 4. Testes de Compatibilidade
Verificar que funciona em diferentes browsers/dispositivos.

### 5. Testes de Usabilidade
Verificar que a interface é intuitiva e fácil de usar.

---

## ✅ Checklist de Testes Funcionais

### 📊 Portfolio Management

#### Adicionar Posição
- [ ] Abrir modal de adicionar posição
- [ ] Preencher todos os campos obrigatórios
- [ ] Validação de campos vazios
- [ ] Validação de números negativos
- [ ] Pesquisa de ticker funciona
- [ ] Auto-complete de nome da empresa
- [ ] Inferência automática de moeda
- [ ] Fetch automático de preço actual
- [ ] Guardar posição com sucesso
- [ ] Posição aparece na tabela
- [ ] LocalStorage actualizado
- [ ] Gráficos actualizados
- [ ] KPIs actualizados

**Casos de Teste**:
```javascript
// Teste 1: Adicionar AAPL
Ticker: AAPL
Nome: Apple Inc.
Sector: Tecnologia
Moeda: USD
Quantidade: 10
Preço Médio: 145.00
Preço Actual: (auto-fetch)

Resultado Esperado:
✓ Posição adicionada
✓ Valor total = qty × curPrice
✓ P&L calculado correctamente
✓ Gráfico de alocação actualizado
```

#### Editar Posição
- [ ] Clicar numa posição existente
- [ ] Modal abre com dados preenchidos
- [ ] Alterar quantidade
- [ ] Alterar preço médio
- [ ] Guardar alterações
- [ ] Tabela actualizada
- [ ] Cálculos recalculados

#### Remover Posição
- [ ] Clicar botão de remover
- [ ] Confirmação aparece
- [ ] Cancelar não remove
- [ ] Confirmar remove posição
- [ ] Tabela actualizada
- [ ] LocalStorage actualizado
- [ ] Gráficos actualizados

#### Actualizar Cotações
- [ ] Clicar botão "Actualizar Cotações"
- [ ] Loading state visível
- [ ] Todas as cotações actualizadas
- [ ] Preços actualizados na tabela
- [ ] Variação diária calculada
- [ ] P&L recalculado
- [ ] Timestamp de actualização
- [ ] Indicador "Live" visível

---

### 📈 Mercado

#### Gráfico Histórico
- [ ] Pesquisar ticker (ex: AAPL)
- [ ] Gráfico carrega
- [ ] Dados históricos correctos
- [ ] Mudar período (1M, 3M, 6M, 1A, 5A)
- [ ] Gráfico actualiza
- [ ] Tooltip mostra valores
- [ ] Variação do período calculada
- [ ] Fonte de dados indicada

**Tickers de Teste**:
```
US: AAPL, MSFT, GOOGL, TSLA, NVDA
PT: EDP.LS, GALP.LS, NOS.LS
EU: SAP.DE, AIR.PA, VOD.L
ETF: SPY, QQQ, IWDA.AS
```

#### Pesquisa de Tickers
- [ ] Pesquisar por nome (ex: "Apple")
- [ ] Resultados aparecem
- [ ] Pesquisar por ticker (ex: "AAPL")
- [ ] Pesquisar por ISIN (ex: "US0378331005")
- [ ] Seleccionar resultado
- [ ] Gráfico carrega automaticamente
- [ ] Dropdown fecha

#### Mini Cotações
- [ ] Grid de cotações visível
- [ ] Todas as posições do portfolio
- [ ] Preços actualizados
- [ ] Variação diária visível
- [ ] Clicar abre gráfico

---

### 👁 Watchlist

#### Adicionar à Watchlist
- [ ] Preencher ticker
- [ ] Pesquisa funciona
- [ ] Auto-complete de nome
- [ ] Definir alerta de preço (opcional)
- [ ] Adicionar à watchlist
- [ ] Item aparece na lista
- [ ] Cotação carregada
- [ ] LocalStorage actualizado

#### Remover da Watchlist
- [ ] Clicar botão remover
- [ ] Item removido
- [ ] LocalStorage actualizado

#### Alertas de Preço
- [ ] Definir alerta abaixo do preço actual
- [ ] Alerta fica verde quando atingido
- [ ] Definir alerta acima do preço actual
- [ ] Alerta fica cinzento

---

### 🔍 Análise Fundamentalista

#### Carregar Análise
- [ ] Seleccionar ticker do portfolio
- [ ] Dados carregam
- [ ] Todas as métricas visíveis
- [ ] Score calculado
- [ ] Descrição da empresa
- [ ] Fonte de dados indicada
- [ ] Idade dos dados visível

**Métricas a Verificar**:
- [ ] P/E Ratio
- [ ] EV/EBITDA
- [ ] ROE
- [ ] Dividend Yield
- [ ] Margem Líquida
- [ ] Dívida/EBITDA
- [ ] Market Cap
- [ ] Beta

#### Sistema de Cache
- [ ] Primeira carga busca de API
- [ ] Segunda carga usa cache
- [ ] Cache válido por 24h
- [ ] Cache expirado refaz fetch
- [ ] Indicador mostra "há X horas"

#### Fallback de APIs
- [ ] Yahoo Finance funciona
- [ ] Se Yahoo falha, tenta Alpha Vantage
- [ ] Se AV falha, tenta Finnhub
- [ ] Se Finnhub falha, tenta FMP
- [ ] Se todas falham, mostra erro claro
- [ ] Loading state mostra progresso

#### Tese de Investimento
- [ ] Escrever texto
- [ ] Guardar tese
- [ ] Recarregar página
- [ ] Tese persiste
- [ ] Tese específica por ticker

#### Comparação de Empresas
- [ ] Abrir modal de comparação
- [ ] Tabela com todas as posições
- [ ] Métricas lado-a-lado
- [ ] Scores comparados
- [ ] Scroll horizontal funciona

#### Configuração de API Keys
- [ ] Abrir modal de configuração
- [ ] Inserir API key Alpha Vantage
- [ ] Inserir API key Finnhub
- [ ] Inserir API key FMP
- [ ] Guardar keys
- [ ] Keys persistem em localStorage
- [ ] APIs usam keys configuradas

---

## 🔄 Testes de Integração

### Fluxo Completo: Novo Utilizador
```
1. Abrir dashboard pela primeira vez
   ✓ Dados demo carregados
   ✓ 4 posições de exemplo
   ✓ 2 items na watchlist

2. Actualizar cotações
   ✓ Todas as cotações actualizadas
   ✓ P&L recalculado
   ✓ Gráficos actualizados

3. Adicionar nova posição (NVDA)
   ✓ Pesquisa funciona
   ✓ Preço auto-fetched
   ✓ Posição adicionada
   ✓ Gráficos actualizados

4. Ver análise fundamentalista (NVDA)
   ✓ Dados carregados
   ✓ Score calculado
   ✓ Cache guardado

5. Escrever tese de investimento
   ✓ Texto guardado
   ✓ Persiste após reload

6. Adicionar à watchlist (TSLA)
   ✓ Item adicionado
   ✓ Cotação carregada
   ✓ Alerta configurado

7. Ver gráfico histórico (TSLA)
   ✓ Gráfico carrega
   ✓ Períodos funcionam
   ✓ Dados correctos
```

### Fluxo: Utilizador Avançado
```
1. Importar portfolio de ficheiro
   ✓ Ficheiro JSON válido
   ✓ Dados importados
   ✓ Posições carregadas
   ✓ Watchlist carregada

2. Configurar API keys
   ✓ Keys guardadas
   ✓ APIs funcionam

3. Carregar análise de 10 empresas
   ✓ Cache usado eficientemente
   ✓ Fallback funciona
   ✓ Performance aceitável

4. Comparar empresas
   ✓ Tabela completa
   ✓ Dados correctos

5. Exportar portfolio
   ✓ Ficheiro JSON gerado
   ✓ Todos os dados incluídos
```

---

## ⚡ Testes de Performance

### Métricas Alvo
```
First Paint: <1s
Time to Interactive: <2s
API Response (cotações): <2s
API Response (fundamentais): <3s
Cache Hit Rate: >70%
LocalStorage Size: <5MB
```

### Cenários de Teste

#### 1. Portfolio Pequeno (5-10 posições)
```javascript
// Teste
const positions = generatePositions(10);
console.time('render');
render();
console.timeEnd('render');

// Resultado Esperado: <100ms
```

#### 2. Portfolio Médio (20-50 posições)
```javascript
const positions = generatePositions(50);
console.time('render');
render();
console.timeEnd('render');

// Resultado Esperado: <500ms
```

#### 3. Portfolio Grande (100+ posições)
```javascript
const positions = generatePositions(100);
console.time('render');
render();
console.timeEnd('render');

// Resultado Esperado: <1s
```

#### 4. Actualização de Cotações
```javascript
console.time('refreshAllPrices');
await refreshAllPrices();
console.timeEnd('refreshAllPrices');

// 10 posições: <5s
// 50 posições: <15s
// 100 posições: <30s
```

#### 5. Cache Performance
```javascript
// Primeira carga (sem cache)
console.time('fetchFundamentals-nocache');
await fetchFundamentals('AAPL');
console.timeEnd('fetchFundamentals-nocache');
// Esperado: 1-3s

// Segunda carga (com cache)
console.time('fetchFundamentals-cached');
await fetchFundamentals('AAPL');
console.timeEnd('fetchFundamentals-cached');
// Esperado: <50ms
```

---

## 🌐 Testes de Compatibilidade

### Browsers Desktop

#### Chrome/Edge (Chromium)
- [ ] Versão 90+
- [ ] Todas as funcionalidades
- [ ] Performance aceitável
- [ ] DevTools sem erros
- [ ] LocalStorage funciona
- [ ] Fetch API funciona
- [ ] Chart.js renderiza

#### Firefox
- [ ] Versão 88+
- [ ] Todas as funcionalidades
- [ ] Performance aceitável
- [ ] Console sem erros
- [ ] LocalStorage funciona
- [ ] Fetch API funciona
- [ ] Chart.js renderiza

#### Safari
- [ ] Versão 14+
- [ ] Todas as funcionalidades
- [ ] Performance aceitável
- [ ] Console sem erros
- [ ] LocalStorage funciona
- [ ] Fetch API funciona
- [ ] Chart.js renderiza
- [ ] CORS funciona

### Browsers Mobile

#### Chrome Mobile (Android)
- [ ] Layout responsivo
- [ ] Touch funciona
- [ ] Modals funcionam
- [ ] Gráficos renderizam
- [ ] Performance aceitável

#### Safari Mobile (iOS)
- [ ] Layout responsivo
- [ ] Touch funciona
- [ ] Modals funcionam
- [ ] Gráficos renderizam
- [ ] Performance aceitável
- [ ] LocalStorage funciona

### Resoluções de Ecrã
- [ ] Desktop: 1920×1080
- [ ] Laptop: 1366×768
- [ ] Tablet: 768×1024
- [ ] Mobile: 375×667
- [ ] Mobile Large: 414×896

---

## 🎨 Testes de Usabilidade

### Heurísticas de Nielsen

#### 1. Visibilidade do Estado do Sistema
- [ ] Loading states visíveis
- [ ] Feedback de acções (toasts)
- [ ] Indicadores de progresso
- [ ] Timestamps de actualização

#### 2. Correspondência com o Mundo Real
- [ ] Linguagem clara (PT)
- [ ] Ícones intuitivos
- [ ] Termos financeiros correctos
- [ ] Formatação de moeda

#### 3. Controlo e Liberdade do Utilizador
- [ ] Botão cancelar em modals
- [ ] Confirmação antes de remover
- [ ] Possibilidade de editar
- [ ] Reset de dados demo

#### 4. Consistência e Padrões
- [ ] Botões com estilo consistente
- [ ] Cores consistentes
- [ ] Layout consistente
- [ ] Navegação previsível

#### 5. Prevenção de Erros
- [ ] Validação de inputs
- [ ] Mensagens de erro claras
- [ ] Confirmações importantes
- [ ] Valores default sensatos

#### 6. Reconhecimento em vez de Memorização
- [ ] Dropdowns em vez de texto livre
- [ ] Auto-complete
- [ ] Valores pré-preenchidos
- [ ] Histórico de pesquisas

#### 7. Flexibilidade e Eficiência
- [ ] Atalhos de teclado (Enter, Esc)
- [ ] Pesquisa rápida
- [ ] Actualização em batch
- [ ] Cache inteligente

#### 8. Design Estético e Minimalista
- [ ] Interface limpa
- [ ] Sem informação desnecessária
- [ ] Hierarquia visual clara
- [ ] Espaçamento adequado

#### 9. Ajudar Utilizadores a Reconhecer e Recuperar de Erros
- [ ] Mensagens de erro claras
- [ ] Sugestões de resolução
- [ ] Fallback automático
- [ ] Retry automático

#### 10. Ajuda e Documentação
- [ ] README completo
- [ ] Tooltips informativos
- [ ] Links para documentação
- [ ] FAQ disponível

---

## 🐛 Testes de Casos Extremos

### Dados Vazios
- [ ] Portfolio vazio
- [ ] Watchlist vazia
- [ ] Sem cache
- [ ] Sem API keys

### Dados Inválidos
- [ ] Ticker inexistente
- [ ] ISIN inválido
- [ ] Números negativos
- [ ] Strings em campos numéricos
- [ ] JSON corrompido no localStorage

### Limites
- [ ] 100+ posições
- [ ] Nomes muito longos
- [ ] Valores muito grandes (trilhões)
- [ ] Valores muito pequenos (cêntimos)
- [ ] LocalStorage cheio

### Falhas de Rede
- [ ] Sem internet
- [ ] API timeout
- [ ] API retorna erro 500
- [ ] API retorna dados inválidos
- [ ] CORS bloqueado

### Concorrência
- [ ] Múltiplas tabs abertas
- [ ] Actualizar em múltiplas tabs
- [ ] LocalStorage sync entre tabs

---

## 📊 Matriz de Testes

| Feature | Funcional | Performance | Compatibilidade | Usabilidade | Prioridade |
|---------|-----------|-------------|-----------------|-------------|------------|
| Adicionar Posição | ✅ | ✅ | ✅ | ✅ | 🔴 Alta |
| Editar Posição | ✅ | ✅ | ✅ | ✅ | 🔴 Alta |
| Remover Posição | ✅ | ✅ | ✅ | ✅ | 🔴 Alta |
| Actualizar Cotações | ✅ | ✅ | ✅ | ✅ | 🔴 Alta |
| Gráfico Histórico | ✅ | ✅ | ✅ | ✅ | 🟡 Média |
| Watchlist | ✅ | ✅ | ✅ | ✅ | 🟡 Média |
| Análise Fundamentalista | ✅ | ✅ | ✅ | ✅ | 🔴 Alta |
| Cache System | ✅ | ✅ | ✅ | - | 🔴 Alta |
| API Fallback | ✅ | ✅ | ✅ | - | 🔴 Alta |
| Pesquisa Tickers | ✅ | ✅ | ✅ | ✅ | 🟡 Média |
| Tese Investimento | ✅ | ✅ | ✅ | ✅ | 🟢 Baixa |
| Comparação | ✅ | ✅ | ✅ | ✅ | 🟢 Baixa |
| Configuração APIs | ✅ | ✅ | ✅ | ✅ | 🟡 Média |

---

## 🔧 Ferramentas de Teste

### Manual Testing
```javascript
// Console helpers
window.TEST = {
  // Gerar posições de teste
  generatePositions(n) {
    const tickers = ['AAPL','MSFT','GOOGL','TSLA','NVDA'];
    return Array(n).fill(0).map((_, i) => ({
      ticker: tickers[i % tickers.length],
      name: `Company ${i}`,
      sector: 'Tecnologia',
      currency: 'USD',
      qty: Math.random() * 100,
      avgPrice: Math.random() * 200,
      curPrice: Math.random() * 200
    }));
  },
  
  // Limpar tudo
  reset() {
    localStorage.clear();
    location.reload();
  },
  
  // Verificar performance
  async benchmark() {
    console.time('render');
    render();
    console.timeEnd('render');
    
    console.time('refreshPrices');
    await refreshAllPrices();
    console.timeEnd('refreshPrices');
  },
  
  // Verificar cache
  cacheStats() {
    const keys = Object.keys(localStorage);
    const cacheKeys = keys.filter(k => k.startsWith('fund_cache_'));
    console.log(`Cache entries: ${cacheKeys.length}`);
    cacheKeys.forEach(k => {
      const data = JSON.parse(localStorage.getItem(k));
      const age = Date.now() - data.metadata.fetchedAt;
      console.log(`${data.ticker}: ${Math.round(age/3600000)}h old`);
    });
  }
};
```

### Browser DevTools
- **Console**: Verificar erros e warnings
- **Network**: Verificar chamadas API
- **Application**: Verificar localStorage
- **Performance**: Profiling de performance
- **Lighthouse**: Auditoria automática

### Automated Testing (Futuro)
```javascript
// Jest + Testing Library
describe('Portfolio Management', () => {
  test('adiciona posição', () => {
    // ...
  });
  
  test('actualiza cotações', async () => {
    // ...
  });
});
```

---

## 📝 Template de Relatório de Bug

```markdown
### 🐛 Bug Report

**Título**: [Descrição curta do problema]

**Prioridade**: 🔴 Alta / 🟡 Média / 🟢 Baixa

**Descrição**:
[Descrição detalhada do problema]

**Passos para Reproduzir**:
1. Abrir dashboard
2. Clicar em X
3. Fazer Y
4. Observar Z

**Resultado Esperado**:
[O que deveria acontecer]

**Resultado Actual**:
[O que acontece]

**Screenshots**:
[Se aplicável]

**Ambiente**:
- Browser: Chrome 120
- OS: macOS 14
- Versão: 2.0

**Console Errors**:
```
[Copiar erros do console]
```

**LocalStorage**:
```json
{
  "positions": [...]
}
```
```

---

## ✅ Checklist de Release

### Antes de Deploy
- [ ] Todos os testes funcionais passam
- [ ] Performance aceitável
- [ ] Testado em 3+ browsers
- [ ] Testado em mobile
- [ ] Console sem erros
- [ ] LocalStorage funciona
- [ ] Cache funciona
- [ ] APIs funcionam
- [ ] Documentação actualizada
- [ ] CHANGELOG actualizado
- [ ] Versão incrementada

### Após Deploy
- [ ] Testar em produção
- [ ] Verificar GitHub Pages
- [ ] Testar com dados reais
- [ ] Monitorizar erros
- [ ] Recolher feedback

---

## 📊 Métricas de Qualidade

### Tracking
```javascript
// Métricas a monitorizar
const metrics = {
  // Performance
  avgRenderTime: 0,
  avgAPIResponseTime: 0,
  cacheHitRate: 0,
  
  // Funcionalidade
  apiSuccessRate: 0,
  errorRate: 0,
  
  // Uso
  avgPositions: 0,
  avgWatchlistItems: 0,
  mostUsedFeatures: []
};
```

### Objectivos
- **Performance**: 90% das operações <3s
- **Fiabilidade**: 95% de taxa de sucesso
- **Compatibilidade**: Funciona em 4+ browsers
- **Usabilidade**: 0 bugs críticos de UX

---

## 🎯 Próximos Passos

### Esta Semana
1. Executar todos os testes funcionais
2. Documentar bugs encontrados
3. Corrigir bugs críticos
4. Re-testar após correções

### Próximo Mês
1. Implementar testes automatizados
2. Configurar CI/CD
3. Adicionar monitoring
4. Criar dashboard de métricas

---

**Última Actualização**: 20 Março 2026  
**Versão**: 2.0  
**Status**: ✅ Pronto para Testes
