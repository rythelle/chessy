<template>
  <div>
    <img
      v-if="initialPosition[props.position]"
      class="piece-img"
      :src="initialPosition[props.position]"
      alt="Not found"
    />
  </div>
</template>

<script setup>
import { reactive } from 'vue'
import RookBlack from '../assets/rook-black.svg'
import KnightBlack from '../assets/knight-black.svg'
import BishopBlack from '../assets/bishop-black.svg'
import KingBlack from '../assets/king-black.svg'
import QueenBlack from '../assets/queen-black.svg'
import PawnBlack from '../assets/pawn-black.svg'
import PawnWhite from '../assets/pawn-white.svg'
import RookWhite from '../assets/rook-black.svg'
import KnightWhite from '../assets/knight-white.svg'
import BishopWhite from '../assets/bishop-white.svg'
import KingWhite from '../assets/king-white.svg'
import QueenWhite from '../assets/queen-white.svg'

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
</script>

<style scoped>
.piece-img {
  height: 40px;
  width: 40px;
  margin: 40px;
}
</style>
