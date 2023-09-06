<script setup>
import BoardGame from './components/BoardGame.vue'
</script>

<template>
  <div class="board-game">
    <BoardGame />
  </div>
</template>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.board-game {
  height: 100vh;
  width: 100vw;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
