<template>
  <div v-if="tabs.length > 0" class="tabs-container">
    <div class="tab-list" role="tablist" aria-label="Abas de conteúdo">
      <button
        v-for="(aba, index) in tabs"
        :key="aba.title"
        type="button"
        role="tab"
        class="tab-button"
        :class="{ active: abaAtiva === index }"
        :aria-selected="abaAtiva === index"
        @click="abaAtiva = index"
      >
        {{ aba.title }}
      </button>
    </div>

    <div class="tab-panel" role="tabpanel">
      <h3>{{ tabs[abaAtiva].heading }}</h3>
      <p style="margin-top: 0.4rem; color: var(--cor-texto-secundario)">
        {{ tabs[abaAtiva].content }}
      </p>
      <ul v-if="tabs[abaAtiva].topicos" class="tab-topics">
        <li v-for="topico in tabs[abaAtiva].topicos" :key="topico">
          {{ topico }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
/**
 * Componente Abas (Tabs.vue)
 * Gerencia qual aba está ativa em seu data e exibe o conteúdo correspondente recebido via props.
 */
export default {
  name: 'TabsComponent',
  props: {
    tabs: {
      type: Array,
      default: () => [],
    },
  },
  data() {
    return {
      abaAtiva: 0,
    };
  },
};
</script>
