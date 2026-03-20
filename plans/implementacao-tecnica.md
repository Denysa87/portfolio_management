# 💻 Guia de Implementação Técnica

## 📋 Código Completo para Integração Multi-API

### 1. Configuração de API Keys

```javascript
// ═══════════════════════════════════════════════════
// API CONFIGURATION
// ═══════════════════════════════════════════════════
const API_CONFIG = {
  alphavantage: {
    key: localStorage.getItem('api_alphavantage') || 'demo',
    baseUrl: 'https://www.alphavantage.co/query',
    rateLimit: { requests: 5, perMinutes: 1 }
  },
  finnhub: {
    key: localStorage.getItem('api_finnhub') || '',
    baseUrl: 'https://finnhub.io/api/v1',
    rateLimit: { requests: 60, perMinutes: 1 }
  },
  fmp: {
    key: localStorage.getItem('api_fmp') || '',
    baseUrl: 'https://financialmodeprep.com/api/v3',
    rateLimit: { requests: 250, perDay: 1 }
  }
};

// Função para salvar API keys
function saveAPIKey(provider, key) {
  localStorage.setItem(`api_${provider}`, key);
  API_CONFIG[provider].key = key;
  toast(`API key ${provider} guardada ✓`, 'ok');
}
```

### 2. Sistema de Cache Inteligente

```javascript
// ═══════════════════════════════════════════════════
// INTELLIGENT CACHE SYSTEM
// ═══════════════════════════════════════════════════
const FundamentalsCache = {
  // Tempo de validade: 24 horas
  TTL: 24 * 60 * 60 * 1000,
  
  get(ticker) {
    try {
      const key = `fund_cache_${ticker.toUpperCase()}`;
      const cached = localStorage.getItem(key);
      
      if (!cached) return null;
      
      const data = JSON.parse(cached);
      const age = Date.now() - data.metadata.fetchedAt;
      
      // Verifica se cache ainda é válido
      if (age > this.TTL) {
        localStorage.removeItem(key);
        return null;
      }
      
      console.log(`📦 Cache hit for ${ticker} (age: ${Math.round(age/3600000)}h)`);
      return data;
      
    } catch (e) {
      console.error('Cache read error:', e);
      return null;
    }
  },
  
  set(ticker, data, source) {
    try {
      const key = `fund_cache_${ticker.toUpperCase()}`;
      const cacheData = {
        ticker: ticker.toUpperCase(),
        data: data,
        metadata: {
          source: source,
          fetchedAt: Date.now(),
          expiresAt: Date.now() + this.TTL,
          version: '2.0'
        }
      };
      
      localStorage.setItem(key, JSON.stringify(cacheData));
      console.log(`💾 Cached ${ticker} from ${source}`);
      
    } catch (e) {
      console.error('Cache write error:', e);
      // Se localStorage estiver cheio, limpa caches antigos
      this.cleanup();
    }
  },
  
  cleanup() {
    console.log('🧹 Cleaning old cache entries...');
    const keys = Object.keys(localStorage);
    let removed = 0;
    
    keys.forEach(key => {
      if (key.startsWith('fund_cache_')) {
        try {
          const data = JSON.parse(localStorage.getItem(key));
          if (Date.now() > data.metadata.expiresAt) {
            localStorage.removeItem(key);
            removed++;
          }
        } catch (e) {
          localStorage.removeItem(key);
          removed++;
        }
      }
    });
    
    console.log(`Removed ${removed} expired cache entries`);
  },
  
  clear() {
    const keys = Object.keys(localStorage);
    keys.forEach(key => {
      if (key.startsWith('fund_cache_')) {
        localStorage.removeItem(key);
      }
    });
    toast('Cache limpo ✓', 'ok');
  }
};
```

### 3. Adaptadores de Dados por API

```javascript
// ═══════════════════════════════════════════════════
// DATA ADAPTERS - Normaliza dados de diferentes APIs
// ═══════════════════════════════════════════════════

const DataAdapters = {
  
  // Yahoo Finance
  yahoo(result) {
    const stats = result.defaultKeyStatistics || {};
    const fin = result.financialData || {};
    const profile = result.summaryProfile || {};
    const price = result.price || {};
    
    return {
      pe: stats.trailingPE?.raw || stats.forwardPE?.raw || null,
      evEbitda: stats.enterpriseToEbitda?.raw || null,
      roe: fin.returnOnEquity?.raw ? (fin.returnOnEquity.raw * 100) : null,
      divYield: stats.dividendYield?.raw ? (stats.dividendYield.raw * 100) : null,
      netMargin: fin.profitMargins?.raw ? (fin.profitMargins.raw * 100) : null,
      debtEbitda: fin.debtToEquity?.raw || null,
      marketCap: price.marketCap?.raw || stats.marketCap?.raw || null,
      beta: stats.beta?.raw || null,
      description: profile.longBusinessSummary || '',
      sector: profile.sector || '',
      industry: profile.industry || '',
      employees: profile.fullTimeEmployees || null
    };
  },
  
  // Alpha Vantage
  alphavantage(data) {
    const parse = (val) => {
      if (!val || val === 'None' || val === '-') return null;
      const num = parseFloat(val);
      return isNaN(num) ? null : num;
    };
    
    return {
      pe: parse(data.PERatio),
      evEbitda: parse(data.EVToEBITDA),
      roe: parse(data.ReturnOnEquityTTM),
      divYield: parse(data.DividendYield) ? parse(data.DividendYield) * 100 : null,
      netMargin: parse(data.ProfitMargin) ? parse(data.ProfitMargin) * 100 : null,
      debtEbitda: parse(data.DebtToEquity),
      marketCap: parse(data.MarketCapitalization),
      beta: parse(data.Beta),
      description: data.Description || '',
      sector: data.Sector || '',
      industry: data.Industry || '',
      employees: parse(data.FullTimeEmployees)
    };
  },
  
  // Finnhub
  finnhub(data) {
    const metric = data.metric || {};
    const series = data.series || {};
    
    return {
      pe: metric.peNormalizedAnnual || metric.peBasicExclExtraTTM || null,
      evEbitda: metric.enterpriseValueEbitdaTTM || null,
      roe: metric.roeRfy || metric.roeTTM || null,
      divYield: metric.dividendYieldIndicatedAnnual || null,
      netMargin: metric.netProfitMarginTTM || metric.netProfitMarginAnnual || null,
      debtEbitda: metric.totalDebt2TotalEquityQuarterly || null,
      marketCap: metric.marketCapitalization ? metric.marketCapitalization * 1000000 : null,
      beta: metric.beta || null,
      description: '', // Finnhub não fornece descrição na API de métricas
      sector: '',
      industry: '',
      employees: null
    };
  },
  
  // Financial Modeling Prep
  fmp(data) {
    const profile = Array.isArray(data) ? data[0] : data;
    
    return {
      pe: profile.pe || null,
      evEbitda: null, // FMP não fornece diretamente
      roe: profile.roe ? profile.roe * 100 : null,
      divYield: profile.lastDiv ? (profile.lastDiv / profile.price) * 100 : null,
      netMargin: profile.profitMargin ? profile.profitMargin * 100 : null,
      debtEbitda: null,
      marketCap: profile.mktCap || null,
      beta: profile.beta || null,
      description: profile.description || '',
      sector: profile.sector || '',
      industry: profile.industry || '',
      employees: profile.fullTimeEmployees || null
    };
  }
};
```

### 4. Funções de Fetch por API

```javascript
// ═══════════════════════════════════════════════════
// API FETCH FUNCTIONS
// ═══════════════════════════════════════════════════

// Yahoo Finance (mantém a atual, mas melhorada)
async function fetchFromYahooFundamentals(ticker) {
  try {
    const modules = 'defaultKeyStatistics,financialData,summaryProfile,price';
    const url = `https://query2.finance.yahoo.com/v10/finance/quoteSummary/${encodeURIComponent(ticker)}?modules=${modules}`;
    
    const res = await fetch(url, { 
      signal: AbortSignal.timeout(8000),
      headers: { 'User-Agent': 'Mozilla/5.0' }
    });
    
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    
    const json = await res.json();
    const result = json?.quoteSummary?.result?.[0];
    
    if (!result) throw new Error('No data in response');
    
    return DataAdapters.yahoo(result);
    
  } catch (e) {
    console.warn('Yahoo Finance failed:', e.message);
    return null;
  }
}

// Alpha Vantage
async function fetchFromAlphaVantage(ticker) {
  const apiKey = API_CONFIG.alphavantage.key;
  
  if (!apiKey || apiKey === 'demo') {
    console.warn('Alpha Vantage: No API key configured');
    return null;
  }
  
  try {
    // Remove sufixos de bolsa para Alpha Vantage (só funciona com tickers US)
    const cleanTicker = ticker.split('.')[0];
    
    const url = `${API_CONFIG.alphavantage.baseUrl}?function=OVERVIEW&symbol=${cleanTicker}&apikey=${apiKey}`;
    
    const res = await fetch(url, { signal: AbortSignal.timeout(10000) });
    
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    
    const data = await res.json();
    
    // Alpha Vantage retorna objeto vazio ou com erro se ticker inválido
    if (!data.Symbol || data.Note) {
      throw new Error(data.Note || 'Invalid ticker or rate limit');
    }
    
    return DataAdapters.alphavantage(data);
    
  } catch (e) {
    console.warn('Alpha Vantage failed:', e.message);
    return null;
  }
}

// Finnhub
async function fetchFromFinnhub(ticker) {
  const apiKey = API_CONFIG.finnhub.key;
  
  if (!apiKey) {
    console.warn('Finnhub: No API key configured');
    return null;
  }
  
  try {
    const url = `${API_CONFIG.finnhub.baseUrl}/stock/metric?symbol=${encodeURIComponent(ticker)}&metric=all&token=${apiKey}`;
    
    const res = await fetch(url, { signal: AbortSignal.timeout(10000) });
    
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    
    const data = await res.json();
    
    if (!data.metric || Object.keys(data.metric).length === 0) {
      throw new Error('No metrics available');
    }
    
    return DataAdapters.finnhub(data);
    
  } catch (e) {
    console.warn('Finnhub failed:', e.message);
    return null;
  }
}

// Financial Modeling Prep
async function fetchFromFMP(ticker) {
  const apiKey = API_CONFIG.fmp.key;
  
  if (!apiKey) {
    console.warn('FMP: No API key configured');
    return null;
  }
  
  try {
    // Remove sufixos de bolsa
    const cleanTicker = ticker.split('.')[0];
    
    const url = `${API_CONFIG.fmp.baseUrl}/profile/${cleanTicker}?apikey=${apiKey}`;
    
    const res = await fetch(url, { signal: AbortSignal.timeout(10000) });
    
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    
    const data = await res.json();
    
    if (!data || data.length === 0 || data.error) {
      throw new Error(data.error || 'No data available');
    }
    
    return DataAdapters.fmp(data);
    
  } catch (e) {
    console.warn('FMP failed:', e.message);
    return null;
  }
}
```

### 5. Orquestrador Multi-Fonte com Fallback

```javascript
// ═══════════════════════════════════════════════════
// MULTI-SOURCE ORCHESTRATOR
// ═══════════════════════════════════════════════════

async function fetchFundamentals(ticker) {
  // 1. Verifica cache primeiro
  const cached = FundamentalsCache.get(ticker);
  if (cached) {
    displayFundamentals(ticker, cached.data);
    updateDataSourceIndicator(cached.metadata.source, cached.metadata.fetchedAt);
    return;
  }
  
  // 2. Define ordem de prioridade das fontes
  const sources = [
    { name: 'Yahoo Finance', fn: () => fetchFromYahooFundamentals(ticker) },
    { name: 'Alpha Vantage', fn: () => fetchFromAlphaVantage(ticker) },
    { name: 'Finnhub', fn: () => fetchFromFinnhub(ticker) },
    { name: 'FMP', fn: () => fetchFromFMP(ticker) }
  ];
  
  // 3. Tenta cada fonte em ordem
  for (let i = 0; i < sources.length; i++) {
    const source = sources[i];
    
    try {
      // Atualiza UI com progresso
      updateLoadingState(`A carregar de ${source.name}...`, i + 1, sources.length);
      
      const data = await source.fn();
      
      // Valida se dados são úteis (pelo menos P/E ou Market Cap)
      if (data && (data.pe !== null || data.marketCap !== null)) {
        console.log(`✅ Success from ${source.name}`);
        
        // Guarda em cache
        FundamentalsCache.set(ticker, data, source.name);
        
        // Exibe dados
        displayFundamentals(ticker, data);
        updateDataSourceIndicator(source.name, Date.now());
        
        toast(`Dados carregados de ${source.name} ✓`, 'ok');
        return;
      }
      
    } catch (e) {
      console.warn(`${source.name} failed:`, e);
      
      // Se não for a última fonte, continua
      if (i < sources.length - 1) {
        continue;
      }
    }
  }
  
  // 4. Se todas falharam
  console.error('All sources failed for', ticker);
  displayErrorState(ticker);
  toast('Não foi possível carregar dados fundamentais. Tenta outro ticker.', 'err');
}

// Atualiza estado de loading
function updateLoadingState(message, current, total) {
  const progress = Math.round((current / total) * 100);
  
  ['aPE','aEVEBITDA','aROE','aDivYield','aNetMargin','aDebtEBITDA','aMarketCap','aBeta'].forEach(id => {
    document.getElementById(id).innerHTML = `<span class="skeleton" style="display:inline-block;width:60px;height:24px"></span>`;
  });
  
  document.getElementById('aDescription').innerHTML = `
    <div style="color:var(--muted);font-size:0.85rem">
      <span class="spin">⟳</span> ${message} (${progress}%)
    </div>`;
}

// Atualiza indicador de fonte de dados
function updateDataSourceIndicator(source, timestamp) {
  const age = Date.now() - timestamp;
  const hours = Math.round(age / 3600000);
  const ageText = hours < 1 ? 'agora mesmo' : 
                  hours === 1 ? 'há 1 hora' : 
                  `há ${hours} horas`;
  
  const sourceIcons = {
    'Yahoo Finance': '🟣',
    'Alpha Vantage': '🔵',
    'Finnhub': '🟢',
    'FMP': '🟡'
  };
  
  const icon = sourceIcons[source] || '📡';
  
  // Adiciona indicador abaixo do score
  const scoreDiv = document.getElementById('aScore');
  let indicator = scoreDiv.querySelector('.data-source-indicator');
  
  if (!indicator) {
    indicator = document.createElement('div');
    indicator.className = 'data-source-indicator';
    indicator.style.cssText = 'font-size:0.7rem;color:var(--muted);margin-top:8px;text-align:center';
    scoreDiv.appendChild(indicator);
  }
  
  indicator.innerHTML = `${icon} ${source}<br>Actualizado ${ageText}`;
}

// Exibe estado de erro
function displayErrorState(ticker) {
  ['aPE','aEVEBITDA','aROE','aDivYield','aNetMargin','aDebtEBITDA','aMarketCap','aBeta'].forEach(id => {
    document.getElementById(id).textContent = 'N/A';
  });
  
  document.getElementById('aDescription').innerHTML = `
    <div style="color:var(--red);font-size:0.85rem">
      ⚠️ Não foi possível carregar dados para ${ticker}.<br>
      <span style="font-size:0.75rem;color:var(--muted)">
        Verifica se o ticker está correcto ou tenta configurar API keys alternativas.
      </span>
    </div>`;
  
  document.getElementById('aScoreValue').textContent = '—';
  document.getElementById('aScoreLabel').textContent = 'Sem dados';
}
```

### 6. UI para Configurar API Keys

```javascript
// ═══════════════════════════════════════════════════
// API SETTINGS UI
// ═══════════════════════════════════════════════════

function openAPISettings() {
  const currentKeys = {
    alphavantage: API_CONFIG.alphavantage.key || '',
    finnhub: API_CONFIG.finnhub.key || '',
    fmp: API_CONFIG.fmp.key || ''
  };
  
  const html = `
    <div class="modal-overlay open" id="apiSettingsModal" onclick="if(event.target===this) closeAPISettings()">
      <div class="modal" style="max-width:600px" onclick="event.stopPropagation()">
        <h3>🔑 Configurar API Keys</h3>
        
        <div style="font-size:0.85rem;color:var(--muted);margin-bottom:20px;line-height:1.6">
          Para melhorar a fiabilidade dos dados fundamentais, podes configurar API keys gratuitas.
          O dashboard tentará múltiplas fontes automaticamente.
        </div>
        
        <!-- Alpha Vantage -->
        <div class="form-group" style="margin-bottom:16px">
          <label style="display:flex;align-items:center;gap:8px">
            🔵 Alpha Vantage
            <a href="https://www.alphavantage.co/support/#api-key" target="_blank" 
               style="font-size:0.7rem;color:var(--accent);text-decoration:none">
              (obter key gratuita)
            </a>
          </label>
          <input type="text" id="apiKeyAV" 
                 value="${currentKeys.alphavantage}" 
                 placeholder="ex: ABCD1234EFGH5678"
                 style="width:100%;background:var(--bg);border:1px solid var(--border);border-radius:8px;padding:9px 12px;color:var(--text);font-size:0.85rem;font-family:monospace"/>
          <div style="font-size:0.72rem;color:var(--muted);margin-top:4px">
            Limite: 5 req/min, 500 req/dia • Cobertura: US stocks
          </div>
        </div>
        
        <!-- Finnhub -->
        <div class="form-group" style="margin-bottom:16px">
          <label style="display:flex;align-items:center;gap:8px">
            🟢 Finnhub
            <a href="https://finnhub.io/register" target="_blank" 
               style="font-size:0.7rem;color:var(--accent);text-decoration:none">
              (obter key gratuita)
            </a>
          </label>
          <input type="text" id="apiKeyFinnhub" 
                 value="${currentKeys.finnhub}" 
                 placeholder="ex: c1234567890abcdef"
                 style="width:100%;background:var(--bg);border:1px solid var(--border);border-radius:8px;padding:9px 12px;color:var(--text);font-size:0.85rem;font-family:monospace"/>
          <div style="font-size:0.72rem;color:var(--muted);margin-top:4px">
            Limite: 60 req/min • Cobertura: Global
          </div>
        </div>
        
        <!-- FMP -->
        <div class="form-group" style="margin-bottom:16px">
          <label style="display:flex;align-items:center;gap:8px">
            🟡 Financial Modeling Prep
            <a href="https://site.financialmodeprep.com/developer/docs" target="_blank" 
               style="font-size:0.7rem;color:var(--accent);text-decoration:none">
              (obter key gratuita)
            </a>
          </label>
          <input type="text" id="apiKeyFMP" 
                 value="${currentKeys.fmp}" 
                 placeholder="ex: 1234567890abcdef"
                 style="width:100%;background:var(--bg);border:1px solid var(--border);border-radius:8px;padding:9px 12px;color:var(--text);font-size:0.85rem;font-family:monospace"/>
          <div style="font-size:0.72rem;color:var(--muted);margin-top:4px">
            Limite: 250 req/dia • Cobertura: US stocks
          </div>
        </div>
        
        <div style="background:var(--surface2);border:1px solid var(--border);border-radius:8px;padding:12px;margin-bottom:16px">
          <div style="font-size:0.75rem;color:var(--muted);line-height:1.5">
            💡 <strong>Dica:</strong> As API keys são guardadas localmente no teu browser.
            Mesmo sem keys, o dashboard tenta usar Yahoo Finance (sem limites).
          </div>
        </div>
        
        <div class="modal-actions">
          <button class="btn secondary" onclick="closeAPISettings()">Cancelar</button>
          <button class="btn" onclick="saveAPISettings()">💾 Guardar</button>
        </div>
      </div>
    </div>`;
  
  document.body.insertAdjacentHTML('beforeend', html);
}

function closeAPISettings() {
  const modal = document.getElementById('apiSettingsModal');
  if (modal) modal.remove();
}

function saveAPISettings() {
  const av = document.getElementById('apiKeyAV').value.trim();
  const finnhub = document.getElementById('apiKeyFinnhub').value.trim();
  const fmp = document.getElementById('apiKeyFMP').value.trim();
  
  if (av) saveAPIKey('alphavantage', av);
  if (finnhub) saveAPIKey('finnhub', finnhub);
  if (fmp) saveAPIKey('fmp', fmp);
  
  closeAPISettings();
  toast('API keys guardadas ✓ Recarrega os dados para usar.', 'ok');
}
```

### 7. Atualizar HTML - Adicionar Botão de Configuração

```html
<!-- Na secção de Análise, linha ~374 -->
<button class="btn sm secondary" onclick="openAPISettings()">🔑 API Keys</button>
<button class="btn sm secondary" onclick="openCompareModal()">⚖️ Comparar</button>
```

---

## 🧪 Testes Recomendados

### Console do Browser

```javascript
// Testar cache
FundamentalsCache.get('AAPL');
FundamentalsCache.clear();

// Testar APIs individualmente
await fetchFromYahooFundamentals('AAPL');
await fetchFromAlphaVantage('MSFT');
await fetchFromFinnhub('TSLA');

// Testar orquestrador
await fetchFundamentals('GOOGL');

// Verificar configuração
console.log(API_CONFIG);
```

---

## 📊 Ordem de Implementação no Código

1. **Linhas 520-540**: Adicionar `API_CONFIG` e `FundamentalsCache`
2. **Linhas 1360-1400**: Adicionar `DataAdapters`
3. **Linhas 1400-1500**: Adicionar funções `fetchFrom*`
4. **Linhas 1426-1484**: Substituir `fetchFundamentals` atual
5. **Linhas 1600-1700**: Adicionar UI de configuração
6. **Linha 374**: Adicionar botão de API Keys no HTML

---

## ✅ Checklist de Implementação

- [ ] Copiar código de configuração de APIs
- [ ] Copiar sistema de cache
- [ ] Copiar adaptadores de dados
- [ ] Copiar funções de fetch por API
- [ ] Substituir função fetchFundamentals
- [ ] Adicionar funções de UI (settings, indicators)
- [ ] Adicionar botão de configuração no HTML
- [ ] Testar com ticker US (AAPL)
- [ ] Testar com ticker PT (EDP.LS)
- [ ] Configurar pelo menos 1 API key
- [ ] Verificar cache funcionando
- [ ] Verificar fallback funcionando

---

**Próximo Passo**: Mudar para modo Code e implementar as alterações
