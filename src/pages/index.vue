<script setup lang="ts">
import type { Ref } from 'vue'

interface Block {
  x: number
  y: number
  num: number
  input?: number
  hide: boolean
  error?: boolean
}
const ax = ref<number>(-1)
const ay = ref<number>(-1)
const level = ref<number>(0.5)

const mask = shuffle([2, 5, 4, 9, 7, 1, 3, 6, 8])

const base = [
  [1, 4, 7, 2, 5, 8, 3, 6, 9],
  [2, 5, 8, 3, 6, 9, 4, 7, 1],
  [3, 6, 9, 4, 7, 1, 5, 8, 2],
  [4, 7, 1, 5, 8, 2, 6, 9, 3],
  [5, 8, 2, 6, 9, 3, 7, 1, 4],
  [6, 9, 3, 7, 1, 4, 8, 2, 5],
  [7, 1, 4, 8, 2, 5, 9, 3, 6],
  [8, 2, 5, 9, 3, 6, 1, 4, 7],
  [9, 3, 6, 1, 4, 7, 2, 5, 8],
]
const newlist: Ref<Block[][]> = ref([])

init()

function init() {
  newlist.value = []
  base.forEach((row, y) => {
    const newRow: Block[] = []
    row.forEach((unit, x) => {
      const idx = base[y][x]
      const num = mask[mask[idx - 1] - 1]
      newRow.push({
        x,
        y,
        num,
        hide: Math.random() > level.value,
      } as Block)
    })
    newlist.value.push(newRow)
  })
}

const activeBlock = (block: Block) => {
  if (!block.hide)
    return
  ax.value = block.y
  ay.value = block.x
}

const getBolckClass = (x: number, y: number) => {
  if ((x >= 3 && x < 6) || (y >= 3 && y < 6)) {
    if ((x >= 3 && x < 6) && (y >= 3 && y < 6))
      return 'block unit-odd'
    return 'block unit-env'
  }
  return 'block unit-odd'
}

const doWork = (key: number) => {
  if (ax.value === -1 || ay.value === -1)
    return
  const selectedY = ax.value
  const selectedX = ay.value

  const selectedBlock = newlist.value[selectedY][selectedX]
  selectedBlock.input = key
  selectedBlock.error = false
}

const changeLevel = (l: number) => {
  level.value = l
  init()
}

const submitHandle = () => {
  newlist.value.flat().forEach((b) => {
    if (!b.hide)
      return

    if (!b.input || b.num !== b.input)
      b.error = true
  })
}

function shuffle(arr: number[]) {
  let l = arr.length
  let index, temp
  while (l > 0) {
    index = Math.floor(Math.random() * l)
    temp = arr[l - 1]
    arr[l - 1] = arr[index]
    arr[index] = temp
    l--
  }
  return arr
}
</script>

<template>
  <div class="plant">
    <span btn m-6 @click="submitHandle">提交</span>
    <span btn m-1 @click="changeLevel(0.8)">初级</span>
    <span btn m-1 @click="changeLevel(0.5)">中级</span>
    <span btn m-1 @click="changeLevel(0.4)">高级</span>
    <span btn m-1 @click="changeLevel(0.2)">大师级</span>
  </div>
  <div class="play">
    <div>
      <div v-for="(row, x) in newlist" :key="x" class="board">
        <div v-for="(block, y) in row" :key="y" :class="[getBolckClass(x, y), x === ax && y === ay ? 'selected':'', block.input? 'input': '', block.error? 'error': '']" @click="activeBlock(block)">
          {{ block.hide ? block.input: block.num }}
        </div>
      </div>
    </div>
    <div class="action-box">
      <div v-for="i in 9" :key="i" class="block btn" @click="doWork(i)">
        {{ i }}
      </div>
    </div>
  </div>
</template>

<style scoped>

.plant {
  margin-bottom: 20px;
}
.play{
  display: flex;
  justify-content: center;
  align-items: center;
}

.action-box{
  margin-left: 32px;
}

.board {
  display: flex;
  justify-content: center;
  align-items: center;
}
.block{
  height: 80px;
  width: 80px;
  display: flex;
  justify-content: center;
  align-items: center;
  border: solid 1px #ecf0f1;
  color: aliceblue;
  font-weight: bold;
  font-size: 32px;
  border-radius: 8px;
  box-sizing: border-box;
  transition: all 0.1s ease-in-out;
}
.block:hover {
  background: #7f8c8d;
}

.input{
  color: #34495e;
}

.error {
  color: red;
  border: 4px solid red !important;
}

.selected {
  border: 4px solid #3498db !important;
}

.unit-odd{
  background: #bdc3c7;
}

.unit-env{
  background: #a8adb0f0;
}

</style>
