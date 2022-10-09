<script lang="ts" setup>
import { nextTick, ref } from 'vue';
import Card from '../components/Card.vue'
import { sleep } from '../utils/sleep';

type CardStatus = ''|'x'|'o'

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
        ...cards.value.slice(index + 1, cards.value.length) ]
    curStatus.value = !curStatus.value
    // 从第5步开始检查输赢
    step.value >= 4 && check()
    step.value = step.value + 1
}

const winMap = [
    [0,1,2], // -
    [0,4,8],
    [0,3,6], // I
    [1,4,7], // I
    [2,4,6],
    [2,5,8], // I
    [3,4,5], // -
    [6,7,8] // -
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
    <div>
        <div class="box" :class="winLine">
            <template v-for="(item,index) in cards" v-key="index">
                <Card :status="item" :index="index" @click="clickHandle" />
            </template>
            
        </div>
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

    .win-line-1::before,
    .win-line-2::before,
    .win-line-3::before,
    .win-line-4::before,
    .win-line-5::before,
    .win-line-6::before,
    .win-line-7::before,
    .win-line-8::before {
        content: "";
        position: absolute;
        display: block;
        line-height: 0;
        font-size: 0;
        z-index: 9999;
    }

    .win-line-1::before,
    .win-line-7::before,
    .win-line-8::before {
        width: 100%;
        height: 0;
        border-top: 1px solid green;
        top: 50px;
    }

    .win-line-7::before {
        top: 150px;
    }

    .win-line-8::before {
        top: 250px;
    }

    .win-line-2::before,
    .win-line-3::before,
    .win-line-5::before {
        width: 0;
        height: 300px;
        top: 0;
        left: 50px;
        border-left: 1px solid green;
    }

    .win-line-3::before {
        left: 150px;
    }

    .win-line-5::before {
        left: 250px;
    }

    .win-line-2::before,
    .win-line-5::before {
        width: 424.264px;
        height: 0;
        left: 150px;
        top: 150px;
        border-top: 1px solid green;
        transform: translate3d(-50%, 0, 0) rotate(45deg);
    }

    .win-line-5::before {
        transform: translate3d(-50%, 0, 0) rotate(-45deg);
    }
</style>