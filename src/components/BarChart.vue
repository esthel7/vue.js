<template>
  <div class="bar-chart">
    <div class="bar-chart_container">
      <span class="bar-chart_container_value">{{ data }}%</span>
      <div class="bar-chart_container_base">
        <div v-if="data >= 80" class="gauge" :style="{ width: data + '%' }" />
        <div
          v-else
          class="gauge"
          :style="{ width: data + '%', backgroundColor: '#D92D20' }"
        />
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { toRefs } from 'vue';

interface Props {
  data: number;
}

const props = defineProps<Props>();
const { data } = toRefs(props);
</script>

<style lang="scss" scoped>
@keyframes Fill {
  0% {
    width: 0;
  }
  100% {
    transform: scaleX(1);
  }
}

.bar-chart {
  @include flex-center;
  width: 100%;

  &_container {
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    justify-content: center;
    width: 100%;
    gap: 4px;

    &_value {
      font-size: 16px;
    }

    &_base {
      position: relative;
      width: 100%;
      height: 12px;
      background-color: rgba($color-white-200, 0.3);
      border-radius: 10px;

      .gauge {
        position: absolute;
        height: 12px;
        background-color: $color-green-100;
        border-radius: 10px;
        animation-name: Fill;
        animation-duration: 1s;
      }
    }
  }
}
</style>
