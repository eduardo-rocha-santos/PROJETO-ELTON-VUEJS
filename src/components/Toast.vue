<template>
  <div
    v-if="visivel && message"
    class="toast"
    :class="type"
    role="status"
    aria-live="polite"
  >
    <span class="toast-icon" aria-hidden="true">{{ icone }}</span>
    <p class="toast-message">{{ message }}</p>
    <button
      type="button"
      class="toast-close"
      aria-label="Fechar notificação"
      @click="fecharManual"
    >
      ✕
    </button>
  </div>
</template>

<script>
/**
 * Componente Notificação Toast (Toast.vue)
 * Gerencia sua visibilidade via data, recebe message e type via props
 * e desaparece automaticamente após alguns segundos ou ao clicar em fechar.
 */
export default {
  name: 'ToastComponent',
  props: {
    message: {
      type: String,
      default: '',
    },
    type: {
      type: String,
      default: 'info',
    },
    toastId: {
      type: Number,
      default: 0,
    },
  },
  emits: ['close'],
  data() {
    return {
      visivel: false,
      timerId: null,
    };
  },
  computed: {
    icone() {
      if (this.type === 'sucesso') return '✓';
      if (this.type === 'erro') return '!';
      return 'i';
    },
  },
  watch: {
    toastId() {
      this.iniciarTemporizador();
    },
    message(novoValor) {
      if (novoValor) {
        this.iniciarTemporizador();
      } else {
        this.visivel = false;
      }
    },
  },
  beforeUnmount() {
    if (this.timerId) clearTimeout(this.timerId);
  },
  methods: {
    iniciarTemporizador() {
      if (!this.message) return;
      if (this.timerId) clearTimeout(this.timerId);

      this.visivel = true;
      this.timerId = setTimeout(() => {
        this.visivel = false;
        this.$emit('close');
      }, 3500);
    },
    fecharManual() {
      if (this.timerId) clearTimeout(this.timerId);
      this.visivel = false;
      this.$emit('close');
    },
  },
};
</script>
