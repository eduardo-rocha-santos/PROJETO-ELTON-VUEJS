<template>
  <div ref="dropdownRef" class="dropdown">
    <button
      type="button"
      class="dropdown-trigger"
      aria-haspopup="menu"
      :aria-expanded="aberto"
      @click="aberto = !aberto"
    >
      <span>{{ label }}</span>
      <span aria-hidden="true">▾</span>
    </button>

    <ul v-if="aberto" class="dropdown-menu-list" role="menu">
      <li v-for="opcao in options" :key="opcao.label" role="none">
        <button
          type="button"
          role="menuitem"
          class="dropdown-option"
          :class="{ active: opcao.active }"
          @click="selecionarOpcao(opcao)"
        >
          <span>{{ opcao.label }}</span>
          <span v-if="opcao.active" aria-hidden="true">✓</span>
        </button>
      </li>
    </ul>
  </div>
</template>

<script>
/**
 * Componente Menu Suspenso (DropdownMenu.vue)
 * Gerencia sua visibilidade via data, emite evento via $emit ao selecionar opção
 * e fecha ao clicar fora utilizando os lifecycle hooks mounted e beforeUnmount.
 */
export default {
  name: 'DropdownMenuComponent',
  props: {
    label: {
      type: String,
      default: 'Opções',
    },
    options: {
      type: Array,
      default: () => [],
    },
  },
  emits: ['select'],
  data() {
    return {
      aberto: false,
    };
  },
  mounted() {
    document.addEventListener('mousedown', this.fecharAoClicarFora);
  },
  beforeUnmount() {
    document.removeEventListener('mousedown', this.fecharAoClicarFora);
  },
  methods: {
    selecionarOpcao(opcao) {
      this.$emit('select', opcao);
      this.aberto = false;
    },
    fecharAoClicarFora(event) {
      if (this.$refs.dropdownRef && !this.$refs.dropdownRef.contains(event.target)) {
        this.aberto = false;
      }
    },
  },
};
</script>
