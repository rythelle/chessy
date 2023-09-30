<template>
  <div class="board">
    <div
      v-for="(item, index) in positionArray"
      :key="index"
      class="square"
      :square-id="index"
      :class="{
        beige: index % 2 === Math.floor(index / 8) % 2,
        brown: index % 2 !== Math.floor(index / 8) % 2
      }"
      @dragover="onDrop($event)"
      @drop="dragDrop($event)"
    >
      <div
        v-if="initialPosition[index]"
        class="piece"
        :id="getPieceName(initialPosition[index])"
        :class="getColorPiece(initialPosition[index])"
        draggable="true"
        @dragstart="startDrag($event)"
      >
        <img
          class="piece-img"
          :src="initialPosition[index]"
          :alt="getPieceName(initialPosition[index])"
          draggable="false"
        />
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, watch, onMounted } from 'vue'
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

const positionArray = reactive(Array.from({ length: 64 }))

const playerGo = ref('')
const warnings = ref('')
const width = ref(8)

let draggedElement = ref()
let startPositionId = ref()

watch(warnings, (current) => {
  const squares = document.querySelectorAll('.square')

  squares.forEach((square) => {
    if (current) {
      square.classList.add('blinking')

      setTimeout(() => {
        square.classList.remove('blinking')
      }, 500)

      warnings.value = ''
    }
  })
})

onMounted(() => {
  playerGo.value = 'black'
})

watch(playerGo, (current) => {
  const piecesPlayerGo = document.querySelectorAll(`.${current}`)
  const squares = document.querySelectorAll('.square')

  squares.forEach((square) => {
    square.style.backgroundColor = null
    square.style.border = null
    square.style.borderStyle = null
    square.style.borderColor = null
  })

  piecesPlayerGo.forEach((piece) => {
    const parentElement = piece.parentNode

    parentElement.style.backgroundColor = 'blueviolet'
    parentElement.style.border = '1px'
    parentElement.style.borderStyle = 'solid'
    parentElement.style.borderColor = 'black'
  })
})

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

const getPieceName = (fullPath) => {
  const [, , , path] = fullPath.split('/')

  const [piece] = path.replace(/\.svg$/, '').split('-')

  return piece
}

const getColorPiece = (fullPath) => {
  const [, , , path] = fullPath.split('/')

  const [, color] = path.replace(/\.svg$/, '').split('-')

  return color
}

const startDrag = (event) => {
  startPositionId.value = event.target.parentNode.getAttribute('square-id')
  draggedElement.value = event.target
  event.dataTransfer.dropEffect = 'move'
  event.dataTransfer.effectAllowed = 'move'
}

const onDrop = (event) => {
  event.preventDefault()
}

const dragDrop = (event) => {
  event.stopPropagation()

  const taken = event.target.classList.contains('piece')

  console.log('testando', { oi: event })

  const valid = checkIfValid(event.target)

  const opponentGo = playerGo.value === 'white' ? 'black' : 'white'

  const takenByOpponent = event.target.classList.contains(opponentGo)

  if (takenByOpponent && valid) {
    event.target.parentNode.append(draggedElement.value)
    event.target.remove()

    checkForWin()

    return changePlayer()
  }

  if (taken && !takenByOpponent) {
    warnings.value = 'Jogada não aceita!'

    return
  }

  if (valid) {
    event.target.append(draggedElement.value)
    checkForWin()
    return changePlayer()
  }
}

const checkIfValid = (target) => {
  const targetId =
    Number(target.getAttribute('square-id')) || Number(target.parentNode.getAttribute('square-id'))

  const startId = Number(startPositionId.value)

  const piece = draggedElement.value.id

  const startedRow = [8, 9, 10, 11, 12, 13, 14, 15]

  switch (piece) {
    case 'pawn':
      if (
        (startedRow.includes(startId) && startId + width.value * 2 === targetId) || // se está na posição inicial dele, pode mover duas posições
        startId + width.value === targetId || // pode mover uma posição
        (startId + width.value - 1 === targetId &&
          document.querySelector(`[square-id="${startId + width.value - 1}"]`).firstElementChild) || // pode mover uma posição diagonal para a esquerda
        (startId + width.value + 1 === targetId &&
          document.querySelector(`[square-id="${startId + width.value + 1}"]`).firstElementChild) // pode mover uma posição diagonal para a direta
      ) {
        return true
      }

      break

    case 'knight':
      if (
        startId + width.value * 2 + 1 === targetId ||
        startId + width.value * 2 - 1 === targetId ||
        startId + width.value - 2 === targetId ||
        startId + width.value + 2 === targetId ||
        startId - width.value * 2 + 1 === targetId ||
        startId - width.value * 2 - 1 === targetId ||
        startId - width.value - 2 === targetId ||
        startId - width.value + 2 === targetId
      ) {
        return true
      }

      break

    case 'bishop':
      if (
        startId + width.value + 1 === targetId ||
        (startId + width.value * 2 + 2 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value + 1}"]`)
            .firstElementChild) ||
        (startId + width.value * 3 + 3 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value + 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 2 + 2}"]`)
            .firstElementChild) ||
        (startId + width.value * 4 + 4 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value + 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 2 + 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 3 + 3}"]`)
            .firstElementChild) ||
        (startId + width.value * 5 + 5 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value + 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 2 + 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 3 + 3}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 4 + 4}"]`)
            .firstElementChild) ||
        (startId + width.value * 6 + 6 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value + 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 2 + 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 3 + 3}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 4 + 4}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 5 + 5}"]`)
            .firstElementChild) ||
        (startId + width.value * 7 + 7 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value + 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 2 + 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 3 + 3}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 4 + 4}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 5 + 5}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 6 + 6}"]`)
            .firstElementChild) ||
        ///////////////////////////////////////////////////////////
        startId - width.value - 1 === targetId ||
        (startId - width.value * 2 - 2 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value - 1}"]`)
            .firstElementChild) ||
        (startId - width.value * 3 - 3 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value - 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2 - 2}"]`)
            .firstElementChild) ||
        (startId - width.value * 4 - 4 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value - 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2 - 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 3 - 3}"]`)
            .firstElementChild) ||
        (startId - width.value * 5 - 5 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value - 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2 - 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 3 - 3}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 4 - 4}"]`)
            .firstElementChild) ||
        (startId - width.value * 6 - 6 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value - 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2 - 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 3 - 3}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 4 - 4}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 5 - 5}"]`)
            .firstElementChild) ||
        (startId - width.value * 7 - 7 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value - 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2 - 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 3 - 3}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 4 - 4}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 5 - 5}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 6 - 6}"]`)
            .firstElementChild) ||
        ///////////////////////////////////////////////////////////
        startId - width.value + 1 === targetId ||
        (startId - width.value * 2 + 2 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value + 1}"]`)
            .firstElementChild) ||
        (startId - width.value * 3 + 3 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value + 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2 + 2}"]`)
            .firstElementChild) ||
        (startId - width.value * 4 + 4 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value + 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2 + 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 3 + 3}"]`)
            .firstElementChild) ||
        (startId - width.value * 5 + 5 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value + 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2 + 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 3 + 3}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 4 + 4}"]`)
            .firstElementChild) ||
        (startId - width.value * 6 + 6 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value + 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2 + 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 3 + 3}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 4 + 4}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 5 + 5}"]`)
            .firstElementChild) ||
        (startId - width.value * 7 + 7 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value + 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2 + 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 3 + 3}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 4 + 4}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 5 + 5}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 6 + 6}"]`)
            .firstElementChild) ||
        ///////////////////////////////////////////////////////////
        startId + width.value - 1 === targetId ||
        (startId + width.value * 2 - 2 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value - 1}"]`)
            .firstElementChild) ||
        (startId + width.value * 3 - 3 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value - 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2 - 2}"]`)
            .firstElementChild) ||
        (startId + width.value * 4 - 4 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value - 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 2 - 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 3 - 3}"]`)
            .firstElementChild) ||
        (startId + width.value * 5 - 5 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value - 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 2 - 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 3 - 3}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 4 - 4}"]`)
            .firstElementChild) ||
        (startId + width.value * 6 - 6 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value - 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 2 - 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 3 - 3}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 4 - 4}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 5 - 5}"]`)
            .firstElementChild) ||
        (startId + width.value * 7 - 7 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value - 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 2 - 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 3 - 3}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 4 - 4}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 5 - 5}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 6 - 6}"]`)
            .firstElementChild)
      ) {
        return true
      }
      break

    case 'rook':
      if (
        startId + width.value === targetId ||
        (startId + width.value * 2 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value}"]`).firstElementChild) ||
        (startId + width.value * 3 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 2}"]`)
            .firstElementChild) ||
        (startId + width.value * 4 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 3}"]`)
            .firstElementChild) ||
        (startId + width.value * 5 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 3}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 4}"]`)
            .firstElementChild) ||
        (startId + width.value * 6 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 3}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 4}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 5}"]`)
            .firstElementChild) ||
        (startId + width.value * 7 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 3}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 4}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 5}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 6}"]`)
            .firstElementChild) ||
        ///////////////////////////////////////////////////////////
        startId - width.value === targetId ||
        (startId + width.value * 2 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value}"]`).firstElementChild) ||
        (startId - width.value * 3 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2}"]`)
            .firstElementChild) ||
        (startId - width.value * 4 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 3}"]`)
            .firstElementChild) ||
        (startId - width.value * 5 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 3}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 4}"]`)
            .firstElementChild) ||
        (startId - width.value * 6 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 3}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 4}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 5}"]`)
            .firstElementChild) ||
        (startId - width.value * 7 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 3}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 4}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 5}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 6}"]`)
            .firstElementChild) ||
        ///////////////////////////
        startId + 1 === targetId ||
        (startId + 2 === targetId &&
          !document.querySelector(`[square-id="${startId + 1}"]`).firstElementChild) ||
        (startId + 3 === targetId &&
          !document.querySelector(`[square-id="${startId + 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + 2}"]`).firstElementChild) ||
        (startId + 4 === targetId &&
          !document.querySelector(`[square-id="${startId + 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + 3}"]`).firstElementChild) ||
        (startId + 5 === targetId &&
          !document.querySelector(`[square-id="${startId + 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + 3}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + 4}"]`).firstElementChild) ||
        (startId + 6 === targetId &&
          !document.querySelector(`[square-id="${startId + 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + 3}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + 4}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + 5}"]`).firstElementChild) ||
        (startId + 7 === targetId &&
          !document.querySelector(`[square-id="${startId + 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + 3}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + 4}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + 5}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + 6}"]`).firstElementChild) ||
        ///////////////////////////
        startId - 1 === targetId ||
        (startId - 2 === targetId &&
          !document.querySelector(`[square-id="${startId - 1}"]`).firstElementChild) ||
        (startId - 3 === targetId &&
          !document.querySelector(`[square-id="${startId - 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - 2}"]`).firstElementChild) ||
        (startId - 4 === targetId &&
          !document.querySelector(`[square-id="${startId - 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - 3}"]`).firstElementChild) ||
        (startId - 5 === targetId &&
          !document.querySelector(`[square-id="${startId - 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - 3}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - 4}"]`).firstElementChild) ||
        (startId - 6 === targetId &&
          !document.querySelector(`[square-id="${startId - 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - 3}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - 4}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - 5}"]`).firstElementChild) ||
        (startId - 7 === targetId &&
          !document.querySelector(`[square-id="${startId - 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - 3}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - 4}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - 5}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - 6}"]`).firstElementChild)
      ) {
        return true
      }

      break

    case 'queen':
      if (
        startId + width.value + 1 === targetId ||
        (startId + width.value * 2 + 2 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value + 1}"]`)
            .firstElementChild) ||
        (startId + width.value * 3 + 3 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value + 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 2 + 2}"]`)
            .firstElementChild) ||
        (startId + width.value * 4 + 4 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value + 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 2 + 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 3 + 3}"]`)
            .firstElementChild) ||
        (startId + width.value * 5 + 5 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value + 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 2 + 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 3 + 3}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 4 + 4}"]`)
            .firstElementChild) ||
        (startId + width.value * 6 + 6 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value + 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 2 + 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 3 + 3}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 4 + 4}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 5 + 5}"]`)
            .firstElementChild) ||
        (startId + width.value * 7 + 7 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value + 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 2 + 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 3 + 3}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 4 + 4}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 5 + 5}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 6 + 6}"]`)
            .firstElementChild) ||
        ///////////////////////////////////////////////////////////
        startId - width.value - 1 === targetId ||
        (startId - width.value * 2 - 2 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value - 1}"]`)
            .firstElementChild) ||
        (startId - width.value * 3 - 3 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value - 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2 - 2}"]`)
            .firstElementChild) ||
        (startId - width.value * 4 - 4 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value - 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2 - 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 3 - 3}"]`)
            .firstElementChild) ||
        (startId - width.value * 5 - 5 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value - 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2 - 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 3 - 3}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 4 - 4}"]`)
            .firstElementChild) ||
        (startId - width.value * 6 - 6 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value - 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2 - 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 3 - 3}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 4 - 4}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 5 - 5}"]`)
            .firstElementChild) ||
        (startId - width.value * 7 - 7 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value - 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2 - 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 3 - 3}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 4 - 4}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 5 - 5}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 6 - 6}"]`)
            .firstElementChild) ||
        ///////////////////////////////////////////////////////////
        startId - width.value + 1 === targetId ||
        (startId - width.value * 2 + 2 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value + 1}"]`)
            .firstElementChild) ||
        (startId - width.value * 3 + 3 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value + 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2 + 2}"]`)
            .firstElementChild) ||
        (startId - width.value * 4 + 4 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value + 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2 + 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 3 + 3}"]`)
            .firstElementChild) ||
        (startId - width.value * 5 + 5 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value + 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2 + 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 3 + 3}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 4 + 4}"]`)
            .firstElementChild) ||
        (startId - width.value * 6 + 6 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value + 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2 + 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 3 + 3}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 4 + 4}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 5 + 5}"]`)
            .firstElementChild) ||
        (startId - width.value * 7 + 7 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value + 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2 + 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 3 + 3}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 4 + 4}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 5 + 5}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 6 + 6}"]`)
            .firstElementChild) ||
        ///////////////////////////////////////////////////////////
        startId + width.value - 1 === targetId ||
        (startId + width.value * 2 - 2 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value - 1}"]`)
            .firstElementChild) ||
        (startId + width.value * 3 - 3 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value - 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2 - 2}"]`)
            .firstElementChild) ||
        (startId + width.value * 4 - 4 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value - 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 2 - 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 3 - 3}"]`)
            .firstElementChild) ||
        (startId + width.value * 5 - 5 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value - 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 2 - 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 3 - 3}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 4 - 4}"]`)
            .firstElementChild) ||
        (startId + width.value * 6 - 6 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value - 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 2 - 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 3 - 3}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 4 - 4}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 5 - 5}"]`)
            .firstElementChild) ||
        (startId + width.value * 7 - 7 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value - 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 2 - 2}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 3 - 3}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 4 - 4}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 5 - 5}"]`)
            .firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 6 - 6}"]`)
            .firstElementChild) ||
        //////////////////////////////////
        startId + width.value === targetId ||
        (startId + width.value * 2 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value}"]`).firstElementChild) ||
        (startId + width.value * 3 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 2}"]`)
            .firstElementChild) ||
        (startId + width.value * 4 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 3}"]`)
            .firstElementChild) ||
        (startId + width.value * 5 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 3}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 4}"]`)
            .firstElementChild) ||
        (startId + width.value * 6 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 3}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 4}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 5}"]`)
            .firstElementChild) ||
        (startId + width.value * 7 === targetId &&
          !document.querySelector(`[square-id="${startId + width.value}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 3}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 4}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 5}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + width.value * 6}"]`)
            .firstElementChild) ||
        ///////////////////////////////////////////////////////////
        startId - width.value === targetId ||
        (startId + width.value * 2 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value}"]`).firstElementChild) ||
        (startId - width.value * 3 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2}"]`)
            .firstElementChild) ||
        (startId - width.value * 4 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 3}"]`)
            .firstElementChild) ||
        (startId - width.value * 5 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 3}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 4}"]`)
            .firstElementChild) ||
        (startId - width.value * 6 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 3}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 4}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 5}"]`)
            .firstElementChild) ||
        (startId - width.value * 7 === targetId &&
          !document.querySelector(`[square-id="${startId - width.value}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 3}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 4}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 5}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - width.value * 6}"]`)
            .firstElementChild) ||
        ///////////////////////////
        startId + 1 === targetId ||
        (startId + 2 === targetId &&
          !document.querySelector(`[square-id="${startId + 1}"]`).firstElementChild) ||
        (startId + 3 === targetId &&
          !document.querySelector(`[square-id="${startId + 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + 2}"]`).firstElementChild) ||
        (startId + 4 === targetId &&
          !document.querySelector(`[square-id="${startId + 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + 3}"]`).firstElementChild) ||
        (startId + 5 === targetId &&
          !document.querySelector(`[square-id="${startId + 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + 3}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + 4}"]`).firstElementChild) ||
        (startId + 6 === targetId &&
          !document.querySelector(`[square-id="${startId + 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + 3}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + 4}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + 5}"]`).firstElementChild) ||
        (startId + 7 === targetId &&
          !document.querySelector(`[square-id="${startId + 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + 3}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + 4}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + 5}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId + 6}"]`).firstElementChild) ||
        ///////////////////////////
        startId - 1 === targetId ||
        (startId - 2 === targetId &&
          !document.querySelector(`[square-id="${startId - 1}"]`).firstElementChild) ||
        (startId - 3 === targetId &&
          !document.querySelector(`[square-id="${startId - 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - 2}"]`).firstElementChild) ||
        (startId - 4 === targetId &&
          !document.querySelector(`[square-id="${startId - 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - 3}"]`).firstElementChild) ||
        (startId - 5 === targetId &&
          !document.querySelector(`[square-id="${startId - 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - 3}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - 4}"]`).firstElementChild) ||
        (startId - 6 === targetId &&
          !document.querySelector(`[square-id="${startId - 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - 3}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - 4}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - 5}"]`).firstElementChild) ||
        (startId - 7 === targetId &&
          !document.querySelector(`[square-id="${startId - 1}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - 2}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - 3}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - 4}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - 5}"]`).firstElementChild &&
          !document.querySelector(`[square-id="${startId - 6}"]`).firstElementChild)
      ) {
        return true
      }

      break

    case 'king':
      if (
        startId + 1 === targetId ||
        startId - 1 === targetId ||
        startId + width.value === targetId ||
        startId - width.value === targetId ||
        startId + width.value - 1 === targetId ||
        startId + width.value + 1 === targetId ||
        startId - width.value - 1 === targetId ||
        startId - width.value + 1 === targetId
      ) {
        return true
      }

      break
  }
}

const checkForWin = () => {
  const kings = Array.from(document.querySelectorAll('#king'))

  if (!kings.some((king) => king.className.includes('white'))) {
    warnings.value = 'Jogador preto ganhou'

    const allSquares = document.querySelectorAll('.square')

    return allSquares.forEach((square) =>
      square.firstElementChild?.setAttribute('draggable', false)
    )
  }

  if (!kings.some((king) => king.className.includes('black'))) {
    warnings.value = 'Jogador branco ganhou'

    const allSquares = document.querySelectorAll('.square')

    return allSquares.forEach((square) =>
      square.firstElementChild?.setAttribute('draggable', false)
    )
  }
}

const changePlayer = () => {
  if (playerGo.value === 'black') {
    playerGo.value = 'white'

    reverseIds()
  } else {
    playerGo.value = 'black'

    revertIds()
  }
}

const reverseIds = () => {
  const allSquares = document.querySelectorAll('.square')
  allSquares.forEach((square, i) => {
    square.setAttribute('square-id', width.value * width.value - 1 - i)
  })
}

const revertIds = () => {
  const allSquares = document.querySelectorAll('.square')
  allSquares.forEach((square, i) => {
    square.setAttribute('square-id', i)
  })
}
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
  position: relative;
  z-index: 8;
}

.beige {
  background-color: beige;
}

.brown {
  background-color: brown;
}
.piece {
  position: relative;
  z-index: 10;
}

.piece-img {
  height: 40px;
  width: 40px;
  position: relative;
  z-index: -10;
}

@keyframes blink {
  85% {
    background-color: rgb(250, 14, 14);
  }
}

.blinking {
  animation: blink 300ms;
}
</style>
