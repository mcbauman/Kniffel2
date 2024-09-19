<script setup>
const props = defineProps(["variable", "specificfunction", "text"]);
console.log(props.variable);

</script>
<template>
  <span v-if="variable || variable === 0">{{ variable }}</span>
  <button v-else @click.prevent="specificfunction">{{ text }}</button>
</template>
