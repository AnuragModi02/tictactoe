<template>
    <div class="game-container">
        <div class="top">
            <div class="images">
                <img :src="require('../assets/icon-x.svg')">
                <img :src="require('../assets/icon-o.svg')">
            </div>
            <div class="turn">
                <div>
                    <img :src="require('../assets/icon-x-outline.svg')">
                    <p class="turn-text"> TURN </p>
                </div>
            </div>
            <div class="undo">
                <div class="restart">
                    <img :src="require('../assets/icon-restart.svg')">
                </div>

            </div>
        </div>
        <div class="middle">
            <button class="r-1 c-1"></button>
            <button class="r-1 c-2"></button>
            <button class="r-1 c-3"></button>
            <button class="r-2 c-1"></button>
            <button class="r-2 c-2"></button>
            <button class="r-2 c-3"></button>
            <button class="r-3 c-1"></button>
            <button class="r-3 c-2"></button>
            <button class="r-3 c-3"></button>
        </div>
        <div class="bottom">
            <div class="you">
                <p>X (YOU)</p>
                <p>0</p>
            </div>
            <div class="ties">
                <p>TIES</p>
                <p>0</p>
            </div>
            <div class="cpu">
                <p>0 (CPU)</p>
                <p>0</p>
            </div>
        </div>
    </div>
</template>

<script>
export default {

}
</script>

<style scoped>
.game-container {
    display: grid;
    height: 62.3rem;
    width: 46rem;
    grid-template-rows: 5.2rem 1fr auto;
    row-gap: 25px;
}

.top {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    column-gap: 2rem;
}

.middle {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    grid-template-rows: repeat(3, 1fr);
    gap: 2rem;
    grid-row: 2/2;
    height: 46.1rem;
}

button {
    background-color: var(--black-secondary);
    border-radius: 10px;
    border: none;
    /* height: 140px; */
}

.images {
    display: flex;
    align-items: center;
    height: 5.2rem;
    gap: 10px;
}

.images>img {
    height: 100%;
    object-fit: scale-down;
    width: 30px;
}

.restart {
    display: flex;
    align-items: center;
    width: 5.2rem;
    background-color: var(--gray-primary);
    color: var(--black-secondary);
    height: 100%;
    justify-content: center;
    border-radius: 10px;
}

.undo {
    justify-self: flex-end;
}

.turn {
    display: flex;
    justify-content: center;
    align-items: center;
    color: var(--gray-primary);
    background-color: var(--black-secondary);
    border-radius: 10px;
}

.turn>div {
    height: 20px;
    width: 80px;
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 10px;
}

.turn>div>img {
    height: 100%;
    color: var(--gray-primary);
}

.turn-text {
    font-size: var(--heading-extra-small);
    font-weight: 600;
}

.bottom {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    height: 72px;
    column-gap: 2rem;
}

.bottom>* {
    flex-direction: column;
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: var(--heading-extra-small);
    font-weight: 600;
    border-radius: 10px;
}

.you {
    background-color: var(--blue-primary);
}

.ties {
    background-color: var(--gray-primary);
}

.cpu {
    background-color: var(--yellow-primary);
}
</style>