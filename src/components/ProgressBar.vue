<template>
  <div class="progress-wrapper">
    <div class="progress-header">
      <span class="progress-label">{{ label }}</span>
      <span class="progress-badge">{{ valorLimitado }}%</span>
    </div>

    <div
      class="progress-track"
      role="progressbar"
      :aria-label="label"
      :aria-valuenow="valorLimitado"
      aria-valuemin="0"
      aria-valuemax="100"
    >
      <div
        class="progress-fill"
        :style="{ width: valorLimitado + '%' }"
      ></div>
    </div>
  </div>
</template>

<script>
/**
 * Componente Barra de Progresso (ProgressBar.vue)
 * Recebe progress (0 a 100) via props e atualiza o preenchimento com computed property.
 */
export default {
  name: 'ProgressBarComponent',
  props: {
    progress: {
      type: Number,
      default: 0,
    },
    label: {
      type: String,
      default: 'Progresso',
    },
  },
  computed: {
    valorLimitado() {
      return Math.min(100, Math.max(0, Math.round(this.progress)));
    },
  },
};
</script>
