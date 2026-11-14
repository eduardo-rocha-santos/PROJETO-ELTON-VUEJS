<template>
  <nav class="pagination" aria-label="Navegação entre páginas">
    <button
      type="button"
      class="nav-btn"
      :disabled="currentPage <= 1"
      @click="mudarPagina(currentPage - 1)"
    >
      ← Anterior
    </button>

    <button
      v-for="num in paginas"
      :key="num"
      type="button"
      class="page-btn"
      :class="{ active: num === currentPage }"
      :aria-current="num === currentPage ? 'page' : undefined"
      @click="mudarPagina(num)"
    >
      {{ num }}
    </button>

    <button
      type="button"
      class="nav-btn"
      :disabled="currentPage >= totalSeguro"
      @click="mudarPagina(currentPage + 1)"
    >
      Próxima →
    </button>
  </nav>
</template>

<script>
/**
 * Componente Paginação (Pagination.vue)
 * Recebe currentPage e totalPages via props e emite o evento 'page-change' via $emit.
 */
export default {
  name: 'PaginationComponent',
  props: {
    currentPage: {
      type: Number,
      default: 1,
    },
    totalPages: {
      type: Number,
      default: 1,
    },
  },
  emits: ['page-change'],
  computed: {
    totalSeguro() {
      return Math.max(1, this.totalPages);
    },
    paginas() {
      const lista = [];
      for (let i = 1; i <= this.totalSeguro; i++) {
        lista.push(i);
      }
      return lista;
    },
  },
  methods: {
    mudarPagina(novaPagina) {
      if (novaPagina < 1 || novaPagina > this.totalSeguro) return;
      this.$emit('page-change', novaPagina);
    },
  },
};
</script>
