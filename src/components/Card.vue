<script lang="ts" setup>
    type CardStatus = ''|'x'|'o'

    const props = defineProps<{
        status: CardStatus,
        index: number
    }>()

    const emit = defineEmits<{
        (event: 'click', index: number): void
    }>()

    const clickHandle = () => {
        if (props.status !== '') {
            return
        }
        emit('click', props.index)
    }
</script>

<template>

    <span class="card" :class="{['card-'+props.status]: props.status}"  @click="clickHandle"></span>

</template>

<style scoped>
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