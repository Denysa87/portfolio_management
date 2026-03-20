# 🚀 Plano de Sprints - Correção Análise Fundamentalista

## 📊 Visão Geral

**Objetivo**: Corrigir problemas de CORS e implementar sistema multi-fonte resiliente para dados fundamentais

**Duração Total**: 4 sprints
**Prioridade**: Alta (funcionalidade crítica não está a funcionar)

---

## 🏃 Sprint 1: Fundação e Cache (CRÍTICO)

**Objetivo**: Estabelecer base sólida com cache e melhorar Yahoo Finance atual

**Duração**: 1 sessão de trabalho

### Tarefas

#### 1.1 Adicionar Configuração de APIs
- [ ] Adicionar objeto `API_CONFIG` após linha 527
- [ ] Adicionar função `saveAPIKey()`
- **Ficheiros**: [`dashboard.html`](/Users/denysa_/Library/Mobile Documents/com~apple~CloudDocs/Documents/Projetos VS Code/Finance/dashboard.html:527)
- **Código**: Ver [`implementacao-tecnica.md`](/Users/denysa_/Library/Mobile Documents/com~apple~CloudDocs/Documents/Projetos VS Code/Finance/plans/implementacao-tecnica.md:10) linhas 10-35

#### 1.2 Implementar Sistema de Cache
- [ ] Adicionar objeto `FundamentalsCache` após `API_CONFIG`
- [ ] Implementar métodos: `get()`, `set()`, `cleanup()`, `clear()`
- **Ficheiros**: [`dashboard.html`](/Users/denysa_/Library/Mobile Documents/com~apple~CloudDocs/Documents/Projetos VS Code/Finance/dashboard.html:530)
- **Código**: Ver [`implementacao-tecnica.md`](/Users/denysa_/Library/Mobile Documents/com~apple~CloudDocs/Documents/Projetos VS Code/Finance/plans/implementacao-tecnica.md:37) linhas 37-95

#### 1.3 Melhorar fetchFundamentals Atual
- [ ] Adicionar verificação de cache no início
- [ ] Melhorar tratamento de erros
- [ ] Adicionar timeout mais robusto
- **Ficheiros**: [`dashboard.html`](/Users/denysa_/Library/Mobile Documents/com~apple~CloudDocs/Documents/Projetos VS Code/Finance/dashboard.html:1426)
- **Localização**: Substituir função atual (linhas 1426-1484)

### Critérios de Sucesso Sprint 1
- ✅ Cache funciona (dados persistem entre reloads)
- ✅ Yahoo Finance funciona melhor que antes
- ✅ Mensagens de erro mais claras
- ✅ Testar com AAPL, MSFT

### Entregáveis
- Sistema de cache operacional
- Yahoo Finance com melhor error handling
- Base para adicionar outras APIs

---

## 🏃 Sprint 2: APIs Alternativas (ALTA PRIORIDADE)

**Objetivo**: Adicionar Alpha Vantage e Finnhub como fontes alternativas

**Duração**: 1-2 sessões de trabalho

### Tarefas

#### 2.1 Criar Adaptadores de Dados
- [ ] Adicionar objeto `DataAdapters` com 4 adaptadores
- [ ] Implementar normalização Yahoo, Alpha Vantage, Finnhub, FMP
- **Ficheiros**: [`dashboard.html`](/Users/denysa_/Library/Mobile Documents/com~apple~CloudDocs/Documents/Projetos VS Code/Finance/dashboard.html:1360)
- **Código**: Ver [`implementacao-tecnica.md`](/Users/denysa_/Library/Mobile Documents/com~apple~CloudDocs/Documents/Projetos VS Code/Finance/plans/implementacao-tecnica.md:97) linhas 97-180

#### 2.2 Implementar Alpha Vantage
- [ ] Criar função `fetchFromAlphaVantage(ticker)`
- [ ] Testar com API key demo
- [ ] Validar normalização de dados
- **Ficheiros**: [`dashboard.html`](/Users/denysa_/Library/Mobile Documents/com~apple~CloudDocs/Documents/Projetos VS Code/Finance/dashboard.html:1400)
- **Código**: Ver [`implementacao-tecnica.md`](/Users/denysa_/Library/Mobile Documents/com~apple~CloudDocs/Documents/Projetos VS Code/Finance/plans/implementacao-tecnica.md:220) linhas 220-250

#### 2.3 Implementar Finnhub
- [ ] Criar função `fetchFromFinnhub(ticker)`
- [ ] Configurar API key (obter em finnhub.io)
- [ ] Validar normalização de dados
- **Ficheiros**: [`dashboard.html`](/Users/denysa_/Library/Mobile Documents/com~apple~CloudDocs/Documents/Projetos VS Code/Finance/dashboard.html:1450)
- **Código**: Ver [`implementacao-tecnica.md`](/Users/denysa_/Library/Mobile Documents/com~apple~CloudDocs/Documents/Projetos VS Code/Finance/plans/implementacao-tecnica.md:252) linhas 252-280

#### 2.4 Implementar FMP (Backup)
- [ ] Criar função `fetchFromFMP(ticker)`
- [ ] Configurar como última opção de fallback
- **Ficheiros**: [`dashboard.html`](/Users/denysa_/Library/Mobile Documents/com~apple~CloudDocs/Documents/Projetos VS Code/Finance/dashboard.html:1480)
- **Código**: Ver [`implementacao-tecnica.md`](/Users/denysa_/Library/Mobile Documents/com~apple~CloudDocs/Documents/Projetos VS Code/Finance/plans/implementacao-tecnica.md:282) linhas 282-310

### Critérios de Sucesso Sprint 2
- ✅ 4 fontes de dados funcionais
- ✅ Dados normalizados corretamente
- ✅ Cada API testada individualmente
- ✅ Testar com tickers: AAPL, GOOGL, TSLA

### Entregáveis
- 4 adaptadores de dados
- 4 funções de fetch operacionais
- Dados normalizados em formato único

---

## 🏃 Sprint 3: Orquestração e Fallback (ALTA PRIORIDADE)

**Objetivo**: Implementar lógica de fallback automático entre APIs

**Duração**: 1 sessão de trabalho

### Tarefas

#### 3.1 Criar Orquestrador Multi-Fonte
- [ ] Substituir `fetchFundamentals()` atual
- [ ] Implementar loop de tentativas por ordem de prioridade
- [ ] Adicionar validação de qualidade de dados
- **Ficheiros**: [`dashboard.html`](/Users/denysa_/Library/Mobile Documents/com~apple~CloudDocs/Documents/Projetos VS Code/Finance/dashboard.html:1426)
- **Código**: Ver [`implementacao-tecnica.md`](/Users/denysa_/Library/Mobile Documents/com~apple~CloudDocs/Documents/Projetos VS Code/Finance/plans/implementacao-tecnica.md:312) linhas 312-380

#### 3.2 Adicionar Funções de Feedback
- [ ] Criar `updateLoadingState(message, current, total)`
- [ ] Criar `updateDataSourceIndicator(source, timestamp)`
- [ ] Criar `displayErrorState(ticker)`
- **Ficheiros**: [`dashboard.html`](/Users/denysa_/Library/Mobile Documents/com~apple~CloudDocs/Documents/Projetos VS Code/Finance/dashboard.html:1500)
- **Código**: Ver [`implementacao-tecnica.md`](/Users/denysa_/Library/Mobile Documents/com~apple~CloudDocs/Documents/Projetos VS Code/Finance/plans/implementacao-tecnica.md:382) linhas 382-450

#### 3.3 Testar Cenários de Fallback
- [ ] Simular falha de Yahoo → deve usar Alpha Vantage
- [ ] Simular falha de 2 APIs → deve usar 3ª
- [ ] Simular todas falharem → deve mostrar erro claro
- [ ] Verificar cache sendo usado corretamente

### Critérios de Sucesso Sprint 3
- ✅ Fallback automático funciona
- ✅ Utilizador vê progresso de loading
- ✅ Indicador mostra qual fonte foi usada
- ✅ Erro claro quando todas APIs falham
- ✅ Cache reduz chamadas em 70%+

### Entregáveis
- Orquestrador multi-fonte operacional
- Sistema de fallback resiliente
- Feedback visual claro ao utilizador

---

## 🏃 Sprint 4: UI e Polimento (MÉDIA PRIORIDADE)

**Objetivo**: Melhorar experiência do utilizador e adicionar configurações

**Duração**: 1 sessão de trabalho

### Tarefas

#### 4.1 Criar UI de Configuração de API Keys
- [ ] Criar função `openAPISettings()`
- [ ] Criar função `closeAPISettings()`
- [ ] Criar função `saveAPISettings()`
- [ ] Adicionar modal com formulário de API keys
- **Ficheiros**: [`dashboard.html`](/Users/denysa_/Library/Mobile Documents/com~apple~CloudDocs/Documents/Projetos VS Code/Finance/dashboard.html:1600)
- **Código**: Ver [`implementacao-tecnica.md`](/Users/denysa_/Library/Mobile Documents/com~apple~CloudDocs/Documents/Projetos VS Code/Finance/plans/implementacao-tecnica.md:452) linhas 452-550

#### 4.2 Adicionar Botão de Configuração
- [ ] Adicionar botão "🔑 API Keys" na secção Análise
- [ ] Posicionar ao lado do botão "Comparar"
- **Ficheiros**: [`dashboard.html`](/Users/denysa_/Library/Mobile Documents/com~apple~CloudDocs/Documents/Projetos VS Code/Finance/dashboard.html:374)
- **Código**: 
```html
<button class="btn sm secondary" onclick="openAPISettings()">🔑 API Keys</button>
```

#### 4.3 Melhorar Indicadores Visuais
- [ ] Adicionar ícones por fonte de dados (🟣 Yahoo, 🔵 AV, 🟢 Finnhub, 🟡 FMP)
- [ ] Mostrar idade dos dados ("há 2 horas")
- [ ] Adicionar indicador de qualidade de dados
- **Ficheiros**: [`dashboard.html`](/Users/denysa_/Library/Mobile Documents/com~apple~CloudDocs/Documents/Projetos VS Code/Finance/dashboard.html:390)

#### 4.4 Adicionar Botão de Limpar Cache
- [ ] Adicionar botão "🗑️ Limpar Cache" nas configurações
- [ ] Implementar confirmação antes de limpar
- **Código**:
```javascript
function clearFundamentalsCache() {
  if (confirm('Limpar cache de dados fundamentais?')) {
    FundamentalsCache.clear();
  }
}
```

### Critérios de Sucesso Sprint 4
- ✅ Utilizador consegue configurar API keys facilmente
- ✅ Interface mostra claramente qual fonte está a usar
- ✅ Feedback visual durante loading é claro
- ✅ Utilizador pode limpar cache quando necessário

### Entregáveis
- Modal de configuração de API keys
- Indicadores visuais melhorados
- Documentação de uso para utilizador final

---

## 🧪 Sprint 5: Testes e Validação (OPCIONAL)

**Objetivo**: Garantir qualidade e cobertura global

**Duração**: 1 sessão de trabalho

### Tarefas

#### 5.1 Testes com Tickers Internacionais
- [ ] **US**: AAPL, MSFT, GOOGL, TSLA, NVDA
- [ ] **Portugal**: EDP.LS, GALP.LS, NOS.LS
- [ ] **Europa**: SAP.DE, AIR.PA, VOD.L
- [ ] **ETFs**: SPY, QQQ, IWDA.AS

#### 5.2 Testes de Cenários
- [ ] Cache vazio → deve buscar de API
- [ ] Cache válido → deve usar cache
- [ ] Cache expirado (>24h) → deve refrescar
- [ ] API 1 falha → deve tentar API 2
- [ ] Todas APIs falham → deve mostrar erro
- [ ] Ticker inválido → deve mostrar mensagem apropriada
- [ ] Sem API keys → deve funcionar só com Yahoo

#### 5.3 Testes de Performance
- [ ] Medir tempo de resposta (deve ser <3s em 90% dos casos)
- [ ] Verificar cache hit rate (deve ser >70% após uso)
- [ ] Testar com 10 tickers seguidos
- [ ] Verificar uso de localStorage (não deve exceder limite)

#### 5.4 Documentação Final
- [ ] Criar guia de uso para utilizador
- [ ] Documentar como obter API keys
- [ ] Criar FAQ de troubleshooting
- [ ] Adicionar comentários no código

### Critérios de Sucesso Sprint 5
- ✅ Funciona com tickers de 3+ países
- ✅ Taxa de sucesso >95%
- ✅ Performance consistente
- ✅ Documentação completa

### Entregáveis
- Relatório de testes
- Documentação de utilizador
- Lista de tickers validados

---

## 📋 Resumo de Implementação

### Ordem Recomendada

1. **Sprint 1** (FAZER PRIMEIRO) → Base funcional com cache
2. **Sprint 2** (FAZER SEGUNDO) → Adicionar APIs alternativas
3. **Sprint 3** (FAZER TERCEIRO) → Orquestração e fallback
4. **Sprint 4** (FAZER QUARTO) → UI e polimento
5. **Sprint 5** (OPCIONAL) → Testes extensivos

### Dependências

```
Sprint 1 (Cache + Base)
    ↓
Sprint 2 (APIs Alternativas)
    ↓
Sprint 3 (Orquestração)
    ↓
Sprint 4 (UI)
    ↓
Sprint 5 (Testes)
```

### Pontos de Verificação

Após cada sprint, verificar:
- ✅ Código funciona sem erros
- ✅ Console do browser sem warnings críticos
- ✅ Testar com pelo menos 2 tickers diferentes
- ✅ Fazer commit/backup do código

---

## 🎯 Métricas de Sucesso Final

### Antes da Implementação
- ❌ Análise não funciona (CORS)
- ❌ 0% de taxa de sucesso
- ❌ Sem alternativas
- ❌ Experiência frustrante

### Depois da Implementação
- ✅ 4 fontes de dados independentes
- ✅ >95% de taxa de sucesso
- ✅ Cache reduz 80% das chamadas
- ✅ Funciona com tickers globais
- ✅ Feedback claro ao utilizador
- ✅ Resiliente a falhas de API

---

## 🚀 Como Começar

### Passo 1: Preparação
1. Fazer backup do [`dashboard.html`](/Users/denysa_/Library/Mobile Documents/com~apple~CloudDocs/Documents/Projetos VS Code/Finance/dashboard.html) atual
2. Abrir [`implementacao-tecnica.md`](/Users/denysa_/Library/Mobile Documents/com~apple~CloudDocs/Documents/Projetos VS Code/Finance/plans/implementacao-tecnica.md) para referência
3. Mudar para modo **Code**

### Passo 2: Sprint 1
1. Copiar código de `API_CONFIG` (linhas 10-35)
2. Copiar código de `FundamentalsCache` (linhas 37-95)
3. Melhorar `fetchFundamentals` atual
4. Testar com AAPL

### Passo 3: Continuar Sprints
Seguir ordem: Sprint 2 → Sprint 3 → Sprint 4 → Sprint 5

---

**Próximo Passo**: Mudar para modo Code e começar Sprint 1
