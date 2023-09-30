<template>
  <div class="screen">
    <BoardGame />
  </div>
</template>

<script setup>
import BoardGame from './components/BoardGame.vue'
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.screen {
  height: 100vh;
  width: 100vw;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  background-color: rgb(117, 117, 117);
}
</style>
