<script setup>
import { ref } from "vue";

const props = defineProps({
  message: String,
});
</script>

<template>
  <div id="messageBox">{{ message }}</div>
</template>

<style>
#messageBox {
  font-size: 3em;
}
</style>
