<script setup>
import { ref } from "vue";

const props = defineProps({
  message: String,
});
</script>

<template>
  <div id="messageBox">{{ message }}</div>
</template>

<style scoped>
#messageBox {
  font-size: 1em;
  text-align: center;
  max-width: 100%;
}
</style>
