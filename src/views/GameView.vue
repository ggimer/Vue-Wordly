<script setup>
import GameBoard from "@/components/GameBoard.vue";
</script>

<template>
  <main>
    <GameBoard />

    <br /><br /><br />
    <div style="color: maroon">
      05/15/2022 . . . The
      <a href="https://dictionaryapi.dev">Dictionary API</a>, used for checking
      guesses, seems to be not working.. If the game isn't working, it's because
      the API is down. Sorry about that. If this isn't resolved soon, I'll have
      to rewrite the game logic so it doesn't depend on that API.
    </div>
  </main>
</template>
