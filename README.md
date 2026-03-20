# 💼 Portfolio Management Dashboard

Dashboard financeiro completo com análise fundamentalista, gestão de portfolio e cotações em tempo real.

![Portfolio Dashboard](https://img.shields.io/badge/version-2.0-blue)
![License](https://img.shields.io/badge/license-MIT-green)

## 🚀 Funcionalidades

### 📊 Gestão de Portfolio
- Adicionar/editar/remover posições
- Cálculo automático de P&L (lucro/prejuízo)
- Visualização por sector e activo
- Gráficos interactivos (Chart.js)
- Suporte para múltiplas moedas (USD, EUR, GBP)

### 📈 Mercado
- Gráficos históricos de preços (1M, 3M, 6M, 1A, 5A)
- Cotações em tempo real
- Pesquisa inteligente de tickers (ISIN, nome, símbolo)
- Suporte para mercados globais (US, PT, EU, UK)

### 👁 Watchlist
- Acompanhamento de tickers favoritos
- Alertas de preço configuráveis
- Cotações actualizadas automaticamente

### 🔍 Análise Fundamentalista
- **Métricas financeiras**: P/E, ROE, EV/EBITDA, Dividend Yield, etc.
- **Score de atractividade** automático (0-100)
- **Sistema multi-API** com fallback automático
- **Cache inteligente** (24h) para reduzir chamadas
- **Tese de investimento** personalizável por empresa
- **Comparação** entre múltiplas empresas

## 🌐 Demo Online

Acede ao dashboard: **[GitHub Pages](https://seu-usuario.github.io/portfolio_management/dashboard.html)**

## 🔑 APIs Suportadas

O dashboard funciona **sem configuração** usando Yahoo Finance. Para maior fiabilidade, podes adicionar API keys gratuitas:

| API | Limite Gratuito | Cobertura | Link |
|-----|----------------|-----------|------|
| **Yahoo Finance** | Ilimitado* | Global | Sem key necessária |
| **Alpha Vantage** | 500 req/dia | US stocks | [Obter key](https://www.alphavantage.co/support/#api-key) |
| **Finnhub** | 60 req/min | Global | [Obter key](https://finnhub.io/register) |
| **FMP** | 250 req/dia | US stocks | [Obter key](https://site.financialmodeprep.com/developer/docs) |

*Não oficial, mas sem limites conhecidos

### Como Configurar API Keys

1. Abre o dashboard
2. Vai para a página **"🔍 Análise"**
3. Clica em **"🔑 API Keys"**
4. Insere as keys obtidas nos links acima
5. Clica em **"💾 Guardar"**

As keys são guardadas localmente no teu browser (localStorage).

## 📦 Instalação

### Opção 1: Usar Directamente
```bash
# Clone o repositório
git clone https://github.com/seu-usuario/portfolio_management.git

# Abre o ficheiro no browser
open dashboard.html
```

### Opção 2: GitHub Pages
1. Fork este repositório
2. Vai para Settings → Pages
3. Source: main branch
4. Acede em: `https://seu-usuario.github.io/portfolio_management/dashboard.html`

### Opção 3: Live Server (Recomendado para desenvolvimento)
1. Instala extensão "Live Server" no VS Code
2. Clica direito em `dashboard.html` → "Open with Live Server"
3. Abre automaticamente em `http://localhost:5500`

## 🛠️ Tecnologias

- **HTML5 + CSS3** - Interface responsiva
- **JavaScript (Vanilla)** - Sem dependências externas
- **Chart.js** - Gráficos interactivos
- **LocalStorage** - Persistência de dados
- **Fetch API** - Chamadas HTTP assíncronas

## 📊 Arquitectura

```
Dashboard
    ↓
Cache (24h) → Se válido → Exibe dados
    ↓ Se expirado
Yahoo Finance API (tentativa 1)
    ↓ Se falhar
Alpha Vantage API (tentativa 2)
    ↓ Se falhar
Finnhub API (tentativa 3)
    ↓ Se falhar
FMP API (tentativa 4)
    ↓
Dados Normalizados → Cache → Exibe
```

## 📝 Dados Demo

O dashboard inclui dados demo para teste:
- **AAPL** - Apple Inc.
- **MSFT** - Microsoft Corp.
- **EDP.LS** - EDP Renováveis (Portugal)
- **AMZN** - Amazon.com Inc.

Clica em "↺ Reset Demo" para restaurar dados iniciais.

## 🌍 Tickers Suportados

### Estados Unidos
- Sem sufixo: `AAPL`, `MSFT`, `GOOGL`, `TSLA`

### Portugal
- Sufixo `.LS`: `EDP.LS`, `GALP.LS`, `NOS.LS`

### Europa
- Alemanha `.DE`: `SAP.DE`, `VOW3.DE`
- França `.PA`: `AIR.PA`, `MC.PA`
- Reino Unido `.L`: `VOD.L`, `BP.L`

### ETFs
- `SPY`, `QQQ`, `IWDA.AS`

## 📚 Documentação

- [Plano de Arquitectura](plans/analise-fundamentalista-fix.md)
- [Implementação Técnica](plans/implementacao-tecnica.md)
- [Sprints de Desenvolvimento](plans/sprints.md)

## 🤝 Contribuir

Contribuições são bem-vindas! Por favor:

1. Fork o projecto
2. Cria um branch (`git checkout -b feature/nova-funcionalidade`)
3. Commit as mudanças (`git commit -m 'Adiciona nova funcionalidade'`)
4. Push para o branch (`git push origin feature/nova-funcionalidade`)
5. Abre um Pull Request

## 📄 Licença

Este projecto está sob a licença MIT. Vê o ficheiro [LICENSE](LICENSE) para mais detalhes.

## 🙏 Agradecimentos

- [Chart.js](https://www.chartjs.org/) - Biblioteca de gráficos
- [Yahoo Finance](https://finance.yahoo.com/) - Dados de mercado
- [Alpha Vantage](https://www.alphavantage.co/) - API financeira
- [Finnhub](https://finnhub.io/) - Dados de mercado
- [Financial Modeling Prep](https://financialmodeprep.com/) - Dados fundamentais

## 📧 Contacto

Para questões ou sugestões, abre uma [issue](https://github.com/seu-usuario/portfolio_management/issues).

---

**Desenvolvido com ❤️ para investidores**
