# 🚀 Guia de Configuração do GitHub Pages

## ❌ Problema Atual
Erro 404: "There isn't a GitHub Pages site here"

## ✅ Solução: Ativar GitHub Pages

### Passo 1: Ir para Settings
1. Abre o repositório: https://github.com/denysa87/portfolio_management
2. Clica em **"Settings"** (no menu superior)

### Passo 2: Configurar Pages
1. No menu lateral esquerdo, clica em **"Pages"**
2. Em **"Source"**, seleciona **"GitHub Actions"** (NÃO "Deploy from a branch")
3. Clica em **"Save"** se aparecer

### Passo 3: Executar o Workflow Manualmente
1. Vai para a tab **"Actions"** (no menu superior do repositório)
2. No menu lateral esquerdo, clica em **"Deploy to GitHub Pages"**
3. Clica no botão **"Run workflow"** (à direita)
4. Seleciona **"Branch: main"**
5. Clica em **"Run workflow"** (verde)

### Passo 4: Aguardar Deploy
1. Verás um novo workflow a correr (círculo amarelo 🟡)
2. Aguarda 1-2 minutos até ficar verde ✅
3. Se der erro vermelho ❌, clica nele para ver os logs

### Passo 5: Aceder ao Site
Depois do workflow ficar verde:
- **URL**: https://denysa87.github.io/portfolio_management/

---

## 🔍 Verificações Adicionais

### Se o workflow não aparecer em Actions:
```bash
cd "/Users/denysa_/Library/Mobile Documents/com~apple~CloudDocs/Documents/GitHub/portfolio_management"
git add .github/workflows/deploy.yml
git commit -m "fix: adicionar workflow do GitHub Actions"
git push origin main
```

### Se continuar a dar 404:
1. Verifica se o workflow correu com sucesso (verde ✅)
2. Aguarda 5-10 minutos (pode demorar a propagar)
3. Tenta aceder em modo anónimo/incógnito
4. Limpa a cache do browser (Ctrl+Shift+R ou Cmd+Shift+R)

### Se o workflow der erro:
1. Vai para Settings → Actions → General
2. Em "Workflow permissions", seleciona **"Read and write permissions"**
3. Marca a checkbox **"Allow GitHub Actions to create and approve pull requests"**
4. Clica em **"Save"**
5. Volta a executar o workflow manualmente

---

## 📋 Checklist de Verificação

- [ ] Fui para Settings → Pages
- [ ] Selecionei "GitHub Actions" como Source
- [ ] Executei o workflow manualmente em Actions
- [ ] O workflow ficou verde ✅
- [ ] Aguardei 5-10 minutos
- [ ] Tentei aceder ao URL: https://denysa87.github.io/portfolio_management/

---

## 🆘 Se Nada Funcionar

### Opção Alternativa: Deploy from Branch
1. Settings → Pages
2. Source: **"Deploy from a branch"**
3. Branch: **"main"** / **"/ (root)"**
4. Save
5. Aguarda 5 minutos
6. Acede ao URL

**Nota**: Esta opção não usa GitHub Actions, mas funciona para sites estáticos simples.

---

## 📞 Informação de Debug

Se precisares de ajuda, partilha:
1. Screenshot da página Settings → Pages
2. Screenshot da tab Actions (se houver workflows)
3. Logs de erro se o workflow falhar (clica no workflow vermelho → clica no job → copia os logs)
