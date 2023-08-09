<template>
  <div :key="key">
    <img v-if="initialPosition[position]" class="piece-img" :src="initialPosition[position]" />
    <div v-else></div>
  </div>
</template>

<script setup>
import { reactive, defineProps } from 'vue'
import RookBlack from './icons/RookBlack.vue'
import KnightBlack from './icons/KnightBlack.vue'
import BishopBlack from './icons/BishopBlack.vue'
import KingBlack from './icons/KingBlack.vue'
import QueenBlack from './icons/QueenBlack.vue'
import PawnBlack from './icons/PawnBlack.vue'
import PawnWhite from './icons/PawnWhite.vue'
import RookWhite from './icons/RookBlack.vue'
import KnightWhite from './icons/KnightWhite.vue'
import BishopWhite from './icons/BishopWhite.vue'
import KingWhite from './icons/KingWhite.vue'
import QueenWhite from './icons/QueenWhite.vue'

const initialPosition = reactive({
  0: RookBlack,
  1: KnightBlack,
  2: BishopBlack,
  3: KingBlack,
  4: QueenBlack,
  5: BishopBlack,
  6: KnightBlack,
  7: RookBlack,
  8: PawnBlack,
  9: PawnBlack,
  10: PawnBlack,
  11: PawnBlack,
  12: PawnBlack,
  13: PawnBlack,
  14: PawnBlack,
  15: PawnBlack,
  16: '',
  17: '',
  18: '',
  19: '',
  20: '',
  21: '',
  22: '',
  23: '',
  24: '',
  25: '',
  26: '',
  27: '',
  28: '',
  29: '',
  30: '',
  31: '',
  32: '',
  33: '',
  34: '',
  35: '',
  36: '',
  37: '',
  38: '',
  39: '',
  40: '',
  41: '',
  42: '',
  43: '',
  44: '',
  45: '',
  46: '',
  47: '',
  48: PawnWhite,
  49: PawnWhite,
  50: PawnWhite,
  51: PawnWhite,
  52: PawnWhite,
  53: PawnWhite,
  54: PawnWhite,
  55: PawnWhite,
  56: RookWhite,
  57: KnightWhite,
  58: BishopWhite,
  59: KingWhite,
  60: QueenWhite,
  61: BishopWhite,
  62: KnightWhite,
  63: RookWhite
})

const props = defineProps({
  position: Number
})

console.log('123', props.position)
</script>

<style scoped>
.piece-img {
  height: 40px;
  width: 40px;
  margin: 5px;
}
</style>
