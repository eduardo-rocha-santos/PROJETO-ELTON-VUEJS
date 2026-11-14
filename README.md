# StudyFlow — Painel de Estudos em Vue.js (Desafio Extra)

Aplicação web responsiva desenvolvida com **Vue.js** para o Desafio Extra (+0,3 pontos) da disciplina de **Engenharia de Software (UCSAL — Prof. Elton Figueiredo da Silva)**.

## Arquitetura de Componentes e Conceitos Vue Aplicados

- **Componente Principal ([`src/App.vue`](./src/App.vue)):** Orquestra a aplicação utilizando `data()`, `computed`, `methods`, o lifecycle hook `created()`, bindings `:prop` e escuta de eventos `@evento`.
- **Os 6 Componentes Visuais Obrigatórios (`src/components/`):**
  1. **[`Accordion.vue`](./src/components/Accordion.vue):** Controla qual item está expandido no `data` local e recebe `items` via `props`.
  2. **[`Pagination.vue`](./src/components/Pagination.vue):** Recebe `currentPage` e `totalPages` via `props`, calcula as páginas via `computed` e emite `$emit('page-change', num)`.
  3. **[`ProgressBar.vue`](./src/components/ProgressBar.vue):** Recebe `progress` (0–100) via `props` e atualiza a barra reativamente com `:style`.
  4. **[`Tabs.vue`](./src/components/Tabs.vue):** Gerencia a aba ativa em `data` e exibe o painel correspondente.
  5. **[`DropdownMenu.vue`](./src/components/DropdownMenu.vue):** Gerencia a visibilidade em `data`, emite `$emit('select', opcao)` e fecha ao clicar fora usando os lifecycle hooks `mounted()` e `beforeUnmount()`.
  6. **[`Toast.vue`](./src/components/Toast.vue):** Exibe notificações temporárias (`sucesso`, `info`, `erro`) com temporizador automático e `$emit('close')`.

## Como Executar (Localmente ou no GitHub Codespaces)

```bash
npm install
npm run dev
```
