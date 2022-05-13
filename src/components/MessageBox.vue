<script setup>
const props = defineProps({
  message: String,
});
</script>

<template>
  <div id="messageBox">{{ props.message }}</div>
</template>

<style scoped>
#messageBox {
  font-size: 1em;
  text-align: center;
  max-width: 100%;
}
</style>
