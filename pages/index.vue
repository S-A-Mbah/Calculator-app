<template>
  <div>
    <h1 style="display: flex; justify-content: center">Calculator App</h1>
    <Calculator />
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import Calculator from '~/components/Calculator.vue';

export default defineComponent({
  components: {
    Calculator,
  },
});
</script>

