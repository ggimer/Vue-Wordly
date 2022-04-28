<script setup>
import { ref } from "vue";
const props = defineProps({
  letter: String,
  color: String,
});
</script>

<template>
  <div :class="`letterBox ` + color + `Letter`">{{ props.letter }}</div>
</template>

<style>
.greenLetter {
  background-color: green;
}
.yellowLetter {
  background-color: brown;
}
.grayLetter {
  background-color: gray;
}
</style>
