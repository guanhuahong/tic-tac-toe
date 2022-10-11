<script lang="ts" setup>
import { nextTick, ref } from 'vue';
import Card from './Piece.vue'
import { sleep } from '../utils/sleep';

type CardStatus = '' | 'x' | 'o'

const cards = ref([] as Array<CardStatus>)
const gameStart = ref(false)
const stopGame = () => gameStart.value = false
const startGame = () => gameStart.value = true
const curStatus = ref(true)
const step = ref(0)
const winLine = ref('')
const init = () => {
    startGame()
    cards.value = [
        '', '', '',
        '', '', '',
        '', '', ''
    ] as Array<CardStatus>
    curStatus.value = true
    step.value = 0
}

init()

const clickHandle = (index: number) => {
    if (gameStart.value !== true) return;
    if (step.value > 8) {
        return;
    }
    // 防止重复按同一个位置
    if (cards.value[index] !== '') return;
    cards.value = [
        ...cards.value.slice(0, index),
        curStatus.value ? 'x' : 'o',
        ...cards.value.slice(index + 1, cards.value.length)]
    curStatus.value = !curStatus.value
    // 从第5步开始检查输赢
    step.value >= 4 && check()
    step.value = step.value + 1
}

const winMap = [
    [0, 1, 2], // - 1
    [0, 4, 8],
    [0, 3, 6], // I 3
    [1, 4, 7], // I 4
    [2, 4, 6],
    [2, 5, 8], // I 6
    [3, 4, 5], // - 7
    [6, 7, 8] // - 8
]

const check = () => {
    for (let i = 0; i < winMap.length; i++) {
        const win = winMap[i];
        const [l, c, r] = [cards.value[win[0]], cards.value[win[1]], cards.value[win[2]]]

        if (l !== '' && l === c && l === r) {
            stopGame()
            showWin(l, i)
        }
    }
}

const showWin = async (t: CardStatus, winIndex: number) => {
    if (t === '') return;
    winLine.value = 'win-line-' + (winIndex + 1)
    await sleep(500)
    alert("Winner is " + (t === 'x' ? '1P' : '2P'))

}

</script>

<template>
    <div class="box">
        <template v-for="(item,index) in cards" v-key="index">
            <Card :status="item" :index="index" @click="clickHandle" />
        </template>
        <div class="win-line" :class="winLine"></div>
    </div>
    
</template>

<style scoped>
.box {
    position: relative;
    display: flex;
    background-color: #fff;
    width: 300px;
    height: 300px;
    flex-wrap: wrap;
    border: 1px solid #999;
}

.win-line {
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    pointer-events: none;
    z-index: 888;
}

.win-line::before {
    content: "";
    position: absolute;
    display: block;
    line-height: 0;
    font-size: 0;
    z-index: 9999;
    animation: drawline 2s ease-out forwards;
    animation-play-state: paused;
}

.win-line-1::before,
.win-line-7::before,
.win-line-8::before,
.win-line-3::before,
.win-line-4::before,
.win-line-6::before,
.win-line-2::before,
.win-line-5::before {
    width: 100%;
    height: 0;
    border-top: 1px solid green;
    top: 50px;
    animation-play-state: running;
}

.win-line-7::before {
    top: 150px;
}

.win-line-8::before {
    top: 250px;
}

.win-line-3::before,
.win-line-4::before,
.win-line-6::before {
    top: 0;
    left: 50px;
    transform: rotate(90deg);
    transform-origin: left;
}

.win-line-4::before {
    left: 150px;
}

.win-line-6::before {
    left: 250px;
}

.win-line-2::before,
.win-line-5::before {
    width: 424.264px;
    left: 0;
    top: 0;
    transform: rotate(45deg);
    transform-origin: left;
    animation-name: drawline2;
}

.win-line-5::before {
    left: 300px;
    transform: rotate(-45deg);
    transform-origin: left;
}

@keyframes drawline {
    from { width: 0; }
    to { width: 100%; }
}

@keyframes drawline2 {
    from { width: 0; }
    to { width: 424.264px; }
}
</style>