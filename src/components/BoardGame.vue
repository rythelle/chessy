<template>
  <div class="board">
    <div
      v-for="(item, index) in positionArray"
      :key="index"
      class="square"
      :class="{
        beige: index % 2 === Math.floor(index / 8) % 2,
        brown: index % 2 !== Math.floor(index / 8) % 2
      }"
    >
      <PieceBoard :position="index" />
    </div>
  </div>
</template>

<script setup>
import { reactive } from 'vue'
import PieceBoard from './PieceBoard.vue'

const positionArray = reactive(Array.from({ length: 64 }))
</script>

<style scoped>
.board {
  display: flex;
  flex-wrap: wrap;
  width: 400px;
}

.square {
  width: 50px;
  height: 50px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.beige {
  background-color: beige;
}

.brown {
  background-color: brown;
}
</style>
