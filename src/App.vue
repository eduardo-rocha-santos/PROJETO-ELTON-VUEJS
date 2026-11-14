<template>
  <div>
    <!-- Cabeçalho Semântico Responsivo -->
    <header class="header">
      <div class="header-inner">
        <div class="brand-group">
          <span class="logo-badge" aria-hidden="true">SF</span>
          <div>
            <span class="brand-name">StudyFlow</span>
            <span class="brand-tag">Painel Vue.js</span>
          </div>
        </div>

        <nav class="nav-links" aria-label="Navegação principal">
          <a href="#secao-tarefas" class="nav-link">Tarefas</a>
          <a href="#secao-conteudos" class="nav-link">Conteúdos (Abas)</a>
          <a href="#secao-faq" class="nav-link">Ajuda (Acordeão)</a>
          <span class="status-pill">
            {{ totalConcluidas }}/{{ tarefas.length }} concluídas
          </span>
        </nav>
      </div>
    </header>

    <!-- Conteúdo Principal -->
    <main class="main-container">
      <!-- =========================================================
           SEÇÃO 1: TAREFAS, BARRA DE PROGRESSO, DROPDOWN E PAGINAÇÃO
           ========================================================= -->
      <section id="secao-tarefas" class="section-block" aria-labelledby="titulo-tarefas">
        <div>
          <h1 id="titulo-tarefas" class="section-title">Minhas Tarefas de Estudo</h1>
          <p class="section-subtitle">
            Gerencie suas atividades com v-model, computed properties e emissão de eventos ($emit).
          </p>
        </div>

        <!-- Cartões de Resumo -->
        <div class="stats-grid">
          <article class="stat-card">
            <span class="stat-label">Total Cadastrado</span>
            <strong class="stat-value">{{ tarefas.length }}</strong>
          </article>
          <article class="stat-card">
            <span class="stat-label">Concluídas</span>
            <strong class="stat-value text-success">{{ totalConcluidas }}</strong>
          </article>
          <article class="stat-card">
            <span class="stat-label">Pendentes</span>
            <strong class="stat-value text-primary">{{ totalPendentes }}</strong>
          </article>
        </div>

        <!-- Componente Obrigatório 3: Barra de Progresso -->
        <div class="card-surface">
          <ProgressBar
            :progress="percentualConcluido"
            label="Progresso geral das tarefas concluídas"
          />
        </div>

        <!-- Formulário com v-model e @submit.prevent -->
        <form class="add-form" novalidate @submit.prevent="adicionarTarefa">
          <input
            v-model="novaTarefa.titulo"
            type="text"
            class="input-field"
            placeholder="Adicionar nova meta ou atividade de estudo..."
            aria-label="Título da nova tarefa"
          />

          <select
            v-model="novaTarefa.disciplina"
            class="select-field"
            aria-label="Tipo de estudo"
          >
            <option v-for="tipo in tiposEstudo" :key="tipo" :value="tipo">
              {{ tipo }}
            </option>
          </select>

          <button type="submit" class="btn-primary">+ Adicionar</button>
        </form>

        <!-- Barra de ferramentas com Componente Menu Suspenso -->
        <div class="toolbar">
          <p class="result-count">
            Mostrando <strong>{{ tarefasPaginadas.length }}</strong> de
            <strong>{{ tarefasFiltradas.length }}</strong> tarefa(s)
          </p>

          <!-- Componente Obrigatório 5: Menu Suspenso -->
          <DropdownMenu
            :label="rotuloFiltro"
            :options="opcoesFiltro"
            @select="aoSelecionarFiltro"
          />
        </div>

        <!-- Lista Paginada de Tarefas -->
        <ul class="task-list">
          <li
            v-for="tarefa in tarefasPaginadas"
            :key="tarefa.id"
            class="task-item"
            :class="{ 'task-done': tarefa.concluida }"
          >
            <label class="task-label">
              <input
                type="checkbox"
                class="checkbox"
                :checked="tarefa.concluida"
                @change="alternarTarefa(tarefa.id)"
              />
              <span class="task-title">{{ tarefa.titulo }}</span>
            </label>

            <div class="task-actions">
              <span class="tag">{{ tarefa.disciplina }}</span>
              <span
                class="tag-status"
                :class="tarefa.concluida ? 'tag-done' : 'tag-pending'"
              >
                {{ tarefa.concluida ? 'Concluída' : 'Pendente' }}
              </span>
              <button
                type="button"
                class="btn-delete"
                @click="removerTarefa(tarefa.id)"
              >
                Remover
              </button>
            </div>
          </li>
        </ul>

        <!-- Componente Obrigatório 2: Paginação -->
        <Pagination
          :current-page="paginaSegura"
          :total-pages="totalPaginas"
          @page-change="mudarPagina"
        />
      </section>

      <!-- =========================================================
           SEÇÃO 2: CONTEÚDOS EM ABAS + SIMULADOR DE BARRA DE PROGRESSO
           ========================================================= -->
      <section id="secao-conteudos" class="section-block" aria-labelledby="titulo-conteudos">
        <div>
          <h2 id="titulo-conteudos" class="section-title">Conteúdos e Entrega</h2>
          <p class="section-subtitle">
            Organização de conteúdos através do componente de Abas e simulador interativo de envio.
          </p>
        </div>

        <!-- Componente Obrigatório 4: Abas (Tabs) -->
        <Tabs :tabs="abasConteudo" />

        <!-- Simulador interativo de upload reutilizando o Componente Barra de Progresso -->
        <div class="card-surface">
          <ProgressBar
            :progress="progressoUpload"
            label="Simulador de Upload do Pacote da Atividade"
          />

          <div class="button-group">
            <button
              type="button"
              class="btn-primary"
              :disabled="enviandoUpload"
              @click="iniciarSimulacaoUpload"
            >
              {{ enviandoUpload ? 'Enviando...' : 'Simular Upload Automático' }}
            </button>
            <button
              type="button"
              class="btn-secondary"
              :disabled="enviandoUpload || progressoUpload >= 100"
              @click="avancarUploadManual"
            >
              +25% Manual
            </button>
            <button type="button" class="btn-secondary" @click="zerarUpload">
              Zerar Barra
            </button>
          </div>
        </div>
      </section>

      <!-- =========================================================
           SEÇÃO 3: ACORDEÃO (FAQ) + DEMONSTRAÇÃO DOS 3 TIPOS DE TOAST
           ========================================================= -->
      <section id="secao-faq" class="section-block" aria-labelledby="titulo-faq">
        <div>
          <h2 id="titulo-faq" class="section-title">Perguntas Frequentes (FAQ)</h2>
          <p class="section-subtitle">
            Seções expansíveis e colapsáveis implementadas com o componente Acordeão.
          </p>
        </div>

        <!-- Componente Obrigatório 1: Acordeão -->
        <Accordion :items="perguntasFaq" />

        <!-- Painel rápido para testar todos os tipos de Notificação Toast -->
        <div class="card-surface">
          <h3>Demonstração de Feedbacks (Componente Toast)</h3>
          <p class="section-subtitle">
            Dispare abaixo qualquer variação da Notificação Toast:
          </p>
          <div class="button-group">
            <button
              type="button"
              class="btn-secondary"
              @click="notificar('Operação concluída com sucesso!', 'sucesso')"
            >
              Testar Toast de Sucesso
            </button>
            <button
              type="button"
              class="btn-secondary"
              @click="notificar('Lembrete: revise os componentes Vue antes da entrega.', 'info')"
            >
              Testar Toast Informativo
            </button>
            <button
              type="button"
              class="btn-secondary"
              @click="notificar('Erro ao salvar: preencha os campos obrigatórios.', 'erro')"
            >
              Testar Toast de Erro
            </button>
          </div>
        </div>
      </section>
    </main>

    <!-- Rodapé Semântico -->
    <footer class="footer">
      <p>StudyFlow — Aplicação Responsiva em Vue.js • Engenharia de Software (UCSAL)</p>
    </footer>

    <!-- Componente Obrigatório 6: Notificação Toast -->
    <Toast
      :message="toast.message"
      :type="toast.type"
      :toast-id="toast.id"
      @close="fecharToast"
    />
  </div>
</template>

<script>
import Accordion from './components/Accordion.vue';
import Pagination from './components/Pagination.vue';
import ProgressBar from './components/ProgressBar.vue';
import Tabs from './components/Tabs.vue';
import DropdownMenu from './components/DropdownMenu.vue';
import Toast from './components/Toast.vue';

const ITENS_POR_PAGINA = 4;

/**
 * Componente Principal (App.vue)
 * Orquestra os 6 componentes visuais aplicando props, data, computed, methods,
 * lifecycle hooks (created e beforeUnmount) e manipulação de eventos (@ / $emit).
 */
export default {
  name: 'App',
  components: {
    Accordion,
    Pagination,
    ProgressBar,
    Tabs,
    DropdownMenu,
    Toast,
  },
  data() {
    return {
      tiposEstudo: [
        'Vue.js',
        'Componentes',
        'Props & Emit',
        'Computed',
        'Lifecycle Hooks',
        'CSS Responsivo',
        'Entrega',
        'Estudo Geral',
      ],
      novaTarefa: {
        titulo: '',
        disciplina: 'Vue.js',
      },
      filtroStatus: 'todas',
      paginaAtual: 1,
      progressoUpload: 0,
      enviandoUpload: false,
      intervaloUpload: null,
      toast: {
        id: 0,
        message: '',
        type: 'info',
      },
      tarefas: [
        {
          id: 1,
          titulo: 'Estruturar componente principal App.vue e importar filhos',
          disciplina: 'Vue.js',
          concluida: true,
        },
        {
          id: 2,
          titulo: 'Passar dados de forma reativa entre componentes com props',
          disciplina: 'Props & Emit',
          concluida: true,
        },
        {
          id: 3,
          titulo: 'Gerenciar estado local com data() e propriedades computed',
          disciplina: 'Computed',
          concluida: true,
        },
        {
          id: 4,
          titulo: 'Emitir eventos customizados dos filhos para o pai com $emit',
          disciplina: 'Props & Emit',
          concluida: false,
        },
        {
          id: 5,
          titulo: 'Utilizar lifecycle hooks (created, mounted e beforeUnmount)',
          disciplina: 'Lifecycle Hooks',
          concluida: true,
        },
        {
          id: 6,
          titulo: 'Manipular eventos de formulário e clique com diretiva @ (v-on)',
          disciplina: 'Componentes',
          concluida: false,
        },
        {
          id: 7,
          titulo: 'Validar fechamento do Menu Suspenso ao clicar fora',
          disciplina: 'Componentes',
          concluida: false,
        },
        {
          id: 8,
          titulo: 'Testar layout responsivo em celular, tablet e desktop',
          disciplina: 'CSS Responsivo',
          concluida: false,
        },
        {
          id: 9,
          titulo: 'Gravar vídeo do Desafio Extra no GitHub Codespaces',
          disciplina: 'Entrega',
          concluida: false,
        },
      ],
      abasConteudo: [
        {
          title: '1. Arquitetura Vue.js',
          heading: 'Single-File Components (.vue), Props e Eventos',
          content:
            'A aplicação utiliza a arquitetura modular do Vue.js onde App.vue centraliza o estado compartilhado e se comunica com os 6 componentes através de props e $emit.',
          topicos: [
            'Comunicação unidirecional: dados descem via props (:prop) e ações sobem via $emit (@evento).',
            'Cálculos derivados automáticos e cacheados utilizando propriedades computed.',
            'Reatividade declarativa nos formulários com v-model.',
          ],
        },
        {
          title: '2. Lifecycle Hooks',
          heading: 'Ciclo de Vida dos Componentes (created, mounted, beforeUnmount)',
          content:
            'Os hooks de ciclo de vida permitem executar lógica na inicialização e registrar/limpar listeners globais do DOM com segurança.',
          topicos: [
            'Hook created() em App.vue inicializando configurações padrão da sessão.',
            'Hooks mounted() e beforeUnmount() em DropdownMenu.vue para detectar cliques externos.',
            'Limpeza automática de temporizadores em Toast.vue e App.vue.',
          ],
        },
        {
          title: '3. Comparativo AngularJS vs Vue.js',
          heading: 'Diretivas e $scope vs Componentes .vue e Reactivity System',
          content:
            'Enquanto o AngularJS baseia-se em $scope, digest cycle e diretivas com isolate scope, o Vue.js oferece componentes encapsulados (.vue) com template, script e estilo claros.',
          topicos: [
            'Sintaxe enxuta com : (v-bind) e @ (v-on) substituindo múltiplos atributos ng-*.',
            'Propriedades computed evitam recálculos desnecessários a cada renderização.',
            'Mesma identidade visual responsiva e semântica HTML5 + ARIA.',
          ],
        },
      ],
      perguntasFaq: [
        {
          title: 'Como os componentes filhos enviam ações de volta para o App.vue?',
          content:
            'Utilizando this.$emit("nome-do-evento", payload), como em Pagination.vue (@page-change), DropdownMenu.vue (@select) e Toast.vue (@close).',
        },
        {
          title: 'Por que usamos computed properties em vez de métodos comuns para os filtros?',
          content:
            'As computed properties são cacheadas pelo Vue.js e só são recalculadas quando suas dependências reativas (como tarefas ou filtroStatus) realmente mudam.',
        },
        {
          title: 'Onde os lifecycle hooks foram aplicados neste projeto?',
          content:
            'O hook created() inicializa o estado em App.vue, enquanto mounted() e beforeUnmount() gerenciam o listener de clique externo no DropdownMenu.vue e a limpeza de timers.',
        },
        {
          title: 'Como a Barra de Progresso se atualiza automaticamente?',
          content:
            'Ela recebe a propriedade computada percentualConcluido via prop (:progress) e aplica o valor diretamente no estilo inline :style="{ width: valorLimitado + \'%\' }".',
        },
      ],
    };
  },
  computed: {
    totalConcluidas() {
      return this.tarefas.filter((t) => t.concluida).length;
    },
    totalPendentes() {
      return this.tarefas.length - this.totalConcluidas;
    },
    percentualConcluido() {
      if (this.tarefas.length === 0) return 0;
      return Math.round((this.totalConcluidas / this.tarefas.length) * 100);
    },
    tarefasFiltradas() {
      return this.tarefas.filter((t) => {
        if (this.filtroStatus === 'concluidas') return t.concluida;
        if (this.filtroStatus === 'pendentes') return !t.concluida;
        return true;
      });
    },
    totalPaginas() {
      return Math.max(1, Math.ceil(this.tarefasFiltradas.length / ITENS_POR_PAGINA));
    },
    paginaSegura() {
      return Math.min(this.paginaAtual, this.totalPaginas);
    },
    tarefasPaginadas() {
      const inicio = (this.paginaSegura - 1) * ITENS_POR_PAGINA;
      return this.tarefasFiltradas.slice(inicio, inicio + ITENS_POR_PAGINA);
    },
    rotuloFiltro() {
      if (this.filtroStatus === 'pendentes') return 'Filtro: Pendentes';
      if (this.filtroStatus === 'concluidas') return 'Filtro: Concluídas';
      return 'Filtro: Todas';
    },
    opcoesFiltro() {
      return [
        {
          value: 'todas',
          label: 'Todas as tarefas',
          active: this.filtroStatus === 'todas',
        },
        {
          value: 'pendentes',
          label: 'Apenas pendentes',
          active: this.filtroStatus === 'pendentes',
        },
        {
          value: 'concluidas',
          label: 'Apenas concluídas',
          active: this.filtroStatus === 'concluidas',
        },
      ];
    },
  },
  created() {
    this.novaTarefa.disciplina = this.tiposEstudo[0];
  },
  beforeUnmount() {
    if (this.intervaloUpload) clearInterval(this.intervaloUpload);
  },
  methods: {
    notificar(message, type = 'info') {
      this.toast = {
        id: Date.now(),
        message,
        type,
      };
    },
    fecharToast() {
      this.toast.message = '';
    },
    adicionarTarefa() {
      const tituloLimpo = this.novaTarefa.titulo.trim();
      if (!tituloLimpo) {
        this.notificar('Digite o título da tarefa antes de adicionar.', 'erro');
        return;
      }

      this.tarefas.unshift({
        id: Date.now(),
        titulo: tituloLimpo,
        disciplina: this.novaTarefa.disciplina,
        concluida: false,
      });

      this.novaTarefa.titulo = '';
      this.paginaAtual = 1;
      this.notificar(`Tarefa adicionada em "${this.novaTarefa.disciplina}"!`, 'sucesso');
    },
    alternarTarefa(id) {
      const alvo = this.tarefas.find((t) => t.id === id);
      if (!alvo) return;

      alvo.concluida = !alvo.concluida;
      this.notificar(
        alvo.concluida
          ? 'Tarefa marcada como concluída!'
          : 'Tarefa marcada como pendente.',
        alvo.concluida ? 'sucesso' : 'info'
      );
    },
    removerTarefa(id) {
      this.tarefas = this.tarefas.filter((t) => t.id !== id);
      this.notificar('Tarefa removida da lista.', 'info');
    },
    mudarPagina(novaPagina) {
      this.paginaAtual = novaPagina;
    },
    aoSelecionarFiltro(opcao) {
      this.filtroStatus = opcao.value;
      this.paginaAtual = 1;
      this.notificar(`Exibindo: ${opcao.label}`, 'info');
    },
    iniciarSimulacaoUpload() {
      if (this.intervaloUpload) clearInterval(this.intervaloUpload);
      this.progressoUpload = 0;
      this.enviandoUpload = true;
      this.notificar('Iniciando envio do pacote da atividade...', 'info');

      this.intervaloUpload = setInterval(() => {
        this.progressoUpload = Math.min(100, this.progressoUpload + 20);
        if (this.progressoUpload >= 100) {
          clearInterval(this.intervaloUpload);
          this.intervaloUpload = null;
          this.enviandoUpload = false;
          this.notificar('Arquivo enviado com 100% de conclusão!', 'sucesso');
        }
      }, 400);
    },
    avancarUploadManual() {
      if (this.intervaloUpload) {
        clearInterval(this.intervaloUpload);
        this.intervaloUpload = null;
      }
      this.enviandoUpload = false;
      this.progressoUpload = Math.min(100, this.progressoUpload + 25);
      if (this.progressoUpload === 100) {
        this.notificar('Progresso atingiu 100%!', 'sucesso');
      }
    },
    zerarUpload() {
      if (this.intervaloUpload) {
        clearInterval(this.intervaloUpload);
        this.intervaloUpload = null;
      }
      this.enviandoUpload = false;
      this.progressoUpload = 0;
      this.notificar('Barra de progresso reiniciada.', 'info');
    },
  },
};
</script>
