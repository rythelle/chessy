<script setup>
import { ref, reactive, watchEffect } from 'vue'
import RookBlack from '../assets/rook-black.svg'
import KnightBlack from '../assets/knight-black.svg'
import BishopBlack from '../assets/bishop-black.svg'
import KingBlack from '../assets/king-black.svg'
import QueenBlack from '../assets/queen-black.svg'
import PawnBlack from '../assets/pawn-black.svg'
import PawnWhite from '../assets/pawn-white.svg'
import RookWhite from '../assets/rook-white.svg'
import KnightWhite from '../assets/knight-white.svg'
import BishopWhite from '../assets/bishop-white.svg'
import KingWhite from '../assets/king-white.svg'
import QueenWhite from '../assets/queen-white.svg'

let nameOfPiece = ref('')

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
  16: null,
  17: null,
  18: null,
  19: null,
  20: null,
  21: null,
  22: null,
  23: null,
  24: null,
  25: null,
  26: null,
  27: null,
  28: null,
  29: null,
  30: null,
  31: null,
  32: null,
  33: null,
  34: null,
  35: null,
  36: null,
  37: null,
  38: null,
  39: null,
  40: null,
  41: null,
  42: null,
  43: null,
  44: null,
  45: null,
  46: null,
  47: null,
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

watchEffect(() => {
  const [, , , path] = initialPosition[props.position]
    ? initialPosition[props.position].split('/')
    : [null, null, null, '']

  nameOfPiece.value = path.replace(/\.svg$/, '')
})
</script>

<template>
  <img
    v-if="initialPosition[props.position]"
    class="piece-img"
    :src="initialPosition[props.position]"
    :alt="nameOfPiece"
    draggable="false"
  />
</template>

<style scoped>
.piece-img {
  height: 40px;
  width: 40px;
  position: relative;
  z-index: -10;
}
</style>
