<script setup>
const props = defineProps(["variable", "specificfunction", "text", "compareValue"]);
console.log(props.variable);

</script>
<template>
  <span v-if="variable || variable === 0" :class="(variable >= compareValue)? 'green' : 'red'" >{{ variable }}</span>
  <button v-else @click.prevent="specificfunction">{{ text }}</button>
</template>
