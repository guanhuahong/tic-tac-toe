<script lang="ts" setup>
import { nextTick, ref } from 'vue';

type CardStatus = ''|'x'|'o'

const cards = ref([] as Array<CardStatus>)
const gameStart = ref(false)
const stopGame = () => gameStart.value = false
const startGame = () => gameStart.value = true
const curStatus = ref(true)
const step = ref(0)
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

const wins = [
    [
        1,1,1,
        0,0,0,
        0,0,0
    ],
    [
        1,0,0,
        0,1,0,
        0,0,1
    ],
    [
        1,0,0,
        1,0,0,
        1,0,0,
    ],
    [
        0,1,0,
        0,1,0,
        0,1,0
    ],
    [
        0,0,1,
        0,1,0,
        1,0,0
    ],
    [
        0,0,1,
        0,0,1,
        0,0,1
    ],
    [
        0,0,0,
        1,1,1,
        0,0,0
    ],
    [
        0,0,0,
        0,0,0,
        1,1,1
    ]
]

const check = () => {
    console.log(cards.value.slice(0,3), '\n', cards.value.slice(3,6), '\n', cards.value.slice(6,9))
    let winCards: string[] = []
    for (let i = 0; i < wins.length; i++) {
        const win = wins[i];
        for (let j = 0; j < win.length; j++) {
            const target = win[j];
            if (target === 1) {
                const value: CardStatus = cards.value[j]
                if (value === '') {
                    winCards = []    
                    break;
                }
                if (winCards.length === 0) {
                    winCards = [value]
                    continue;
                }
                if (value != winCards[0]) {
                    winCards = []
                    break;
                }
                winCards = [...winCards, value]
                if (winCards.length ===  3) {
                    console.log(j, winCards, value)
                    stopGame()
                    setTimeout(() => showWin(value), 500)
                    return
                }
            }
            
        }
        winCards = []
    }
}

const showWin = (t: CardStatus) => {
    switch(t) {
        case '': return;
        case 'x':
            alert("Winner is 1P")
            break
        case 'o':
            alert("Winner is 2P")
            break
    }
    init()
}

</script>

<template>
    <div>
        <div class="box">
            <template v-for="(item,index) in cards" v-key="index">
                <span class="card" :class="{['card-'+item]: item}"  @click="clickHandle(index)"></span>
            </template>
            
        </div>
    </div>
</template>

<style scoped>
    .box {
        display: flex;
        background-color: #fff;
        width: 300px;
        height: 300px;
        flex-wrap: wrap;
    }

    .card {
        display: block;
        width: 100px;
        height: 100px;
        box-sizing: border-box;
        font-size: 30px;
        text-align: center;
        position: relative;
    }

    .card-o::before,
    .card-x::before,
    .card-x::after {
        content: "";
        position: absolute;
        display: block;
        top: 50%;
        left: 50%;
        transform: translate3d(-50%, 0, 0) rotate(45deg);
        height: 0;
        font-size: 0;
        line-height: 0;
        width: 60px;
        border-top: 1px solid red;
    }
    .card-x::before {
        transform: translate3d(-50%, 0, 0) rotate(-45deg);
    }

    .card-o::before {
        width: 60px;
        height: 60px;
        border-radius: 30px;
        border: 1px solid blue;
        transform: translate3d(-50%, -50%, 0);
    }

    .card:nth-child(3n+2),
    .card:nth-child(3n+1) {
        border-right: 1px solid #999;
    }

    .card:nth-child(1),
    .card:nth-child(2),
    .card:nth-child(3),
    .card:nth-child(4),
    .card:nth-child(5),
    .card:nth-child(6) {
        border-bottom: 1px solid #999;
    }
</style>