<template>
  <div class="accordion">
    <div
      v-for="(item, index) in items"
      :key="item.title"
      class="accordion-item"
      :class="{ open: indiceAberto === index }"
    >
      <h3>
        <button
          type="button"
          class="accordion-trigger"
          :aria-expanded="indiceAberto === index"
          @click="alternarItem(index)"
        >
          <span>{{ item.title }}</span>
          <span class="accordion-icon" aria-hidden="true">▾</span>
        </button>
      </h3>

      <div
        class="accordion-content-wrapper"
        :class="{ open: indiceAberto === index }"
      >
        <div class="accordion-content-inner">
          <p>{{ item.content }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
/**
 * Componente Acordeão (Accordion.vue)
 * Gerencia no data local qual item está aberto (apenas um por vez) e recebe items via props.
 */
export default {
  name: 'AccordionComponent',
  props: {
    items: {
      type: Array,
      default: () => [],
    },
  },
  data() {
    return {
      indiceAberto: 0,
    };
  },
  methods: {
    alternarItem(index) {
      this.indiceAberto = this.indiceAberto === index ? null : index;
    },
  },
};
</script>
