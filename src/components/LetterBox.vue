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
  background-color: hsla(160, 100%, 37%, 1);
  color: black;
  transition: 0.8s;
}
.yellowLetter {
  background-color: hsla(55, 100%, 37%, 1);
  color: black;
  transition: 0.8s;
}
.grayLetter {
  background-color: gray;
  color: black;
  transition: 0.8s;
}
</style>
