<template>
    <div class="game-container">
        <div
            class="modal"
            v-if="showModal"
        >
            <div>
                <img
                    v-if="!isDraw"
                    :src="playerTurn === 'X' ? iconX : iconO"
                >
                <p
                    v-if="!isDraw"
                    class="modal-heading-takes"
                    :style="{ color: playerTurn === 'X' ? '#31C3BD' : '#F2B137' }"
                > TAKES THE ROUND</p>
                <p
                    v-else
                    class="modal-heading-takes"
                    :style="{ color: '#A8BFC9' }"
                >
                    ROUND TIED
                </p>
            </div>
            <div class="modal-buttons">
                <button
                    class="quit"
                    @click="reloadPage"
                ><span>Quit</span></button>
                <button
                    class="next-round"
                    @click="reset"
                ><span>Next Round</span></button>
            </div>

        </div>
        <div
            class="wrapper"
            :style="{ filter: gameOver ? 'brightness(0.5)' : 'none' }"
        >
            <div class="top">
                <div class="images">
                    <img :src="iconX">
                    <img :src="iconO">
                </div>
                <div class="turn">
                    <div>
                        <img :src="playerTurn === 'X' ? iconXOutline : iconOOutline">
                        <p class="turn-text"> TURN </p>
                    </div>
                </div>
                <div class="undo">
                    <div class="restart">
                        <img :src="iconRestart">
                    </div>

                </div>
            </div>
            <div class="middle">
                <button
                    v-for="cell in gridCells"
                    :key="cell"
                    :class="cell"
                    @click="markPlayerChoice($event)"
                    :disabled="isMarked(cell) || showModal"
                    :style="styles(cell)"
                >
                    <img
                        v-if="isMarked(cell)"
                        :src="renderImg(cell)"
                        :style="imgStyles(cell)"
                    >
                </button>
            </div>
            <div class="bottom">
                <div class="you">
                    <p>X (YOU)</p>
                    <p>{{ xScore }}</p>
                </div>
                <div class="ties">
                    <p>TIES</p>
                    <p>{{ drawScore }}</p>
                </div>
                <div class="cpu">
                    <p>0 (CPU)</p>
                    <p>{{ oScore }}</p>
                </div>
            </div>
        </div>

    </div>
</template>

<script>
export default {
    data() {
        return {
            stack: [],
            playerTurn: "X",
            gridCells: [
                "r-1 c-1", "r-1 c-2", "r-1 c-3",
                "r-2 c-1", "r-2 c-2", "r-2 c-3",
                "r-3 c-1", "r-3 c-2", "r-3 c-3"
            ],
            iconX: require('../assets/icon-x.svg'),
            iconO: require('../assets/icon-o.svg'),
            iconXOutline: require('../assets/icon-x-outline.svg'),
            iconOOutline: require('../assets/icon-o-outline.svg'),
            iconRestart: require('../assets/icon-restart.svg'),
            winningRowsCombination: [
                ["r-1 c-1", "r-1 c-2", "r-1 c-3"],
                ["r-2 c-1", "r-2 c-2", "r-2 c-3"],
                ["r-3 c-1", "r-3 c-2", "r-3 c-3"]
            ],
            winningColumnsCombination: [
                ["r-1 c-1", "r-2 c-1", "r-3 c-1"],
                ["r-1 c-2", "r-2 c-2", "r-3 c-2"],
                ["r-1 c-3", "r-2 c-3", "r-3 c-3"]
            ],
            winningDiagonalCombination: [
                ["r-1 c-1", "r-2 c-2", "r-3 c-3"],
                ["r-1 c-3", "r-2 c-2", "r-3 c-1"]
            ],
            xScore: 0,
            oScore: 0,
            drawScore: 0,
            totalNoOfMoves: 0,
        };
    },
    computed: {
        isPlayerWon() {
            const currentPlayerMoves = this.stack
                .filter(item => item.playerChoice === this.playerTurn)
                .map(item => item.gridCell);

            return this.checkWinningCombination(currentPlayerMoves);
        },
        isDraw() {
            return this.totalNoOfMoves == 9 && !this.isPlayerWon;
        },
        showModal() {
            return this.gameOver;
        },
        gameOver() {
            return this.isPlayerWon || this.isDraw;
        }
    },
    methods: {
        markPlayerChoice(event) {
            if (this.gameOver) return; // Prevent marking if game is over

            this.totalNoOfMoves++;
            const gridCell = event.target.className;
            this.stack.push({ gridCell, playerChoice: this.playerTurn });

            if (this.gameOver) {
                this.updateScore();
                return;
            }

            this.playerTurn = this.playerTurn === "X" ? "O" : "X";
        },
        isMarked(cell) {
            return this.stack.some(item => item.gridCell === cell);
        },
        styles(cell) {
            let styles = {};
            styles.cursor = this.isMarked(cell) || this.showModal ? 'not-allowed' : 'pointer';

            if (this.isPlayerWon) {
                const winningCombination = this.getWinningCombination(this.playerTurn);
                if (winningCombination.includes(cell)) {
                    styles.backgroundColor = this.playerTurn === "X" ? '#31C3BD' : '#F2B137';
                }
            }
            return styles;
        },
        imgStyles(cell) {
            let styles = {};
            if (this.isPlayerWon) {
                const winningCombination = this.getWinningCombination(this.playerTurn);
                if (winningCombination.includes(cell)) {
                    styles.filter = 'brightness(0)';
                }
            }
            return styles;
        },
        renderImg(cell) {
            const playerChoice = this.stack.find(item => item.gridCell === cell).playerChoice;
            return playerChoice === "X" ? this.iconX : this.iconO;
        },
        checkWinningCombination(playerMoves) {
            const allWinningCombinations = [
                ...this.winningRowsCombination,
                ...this.winningColumnsCombination,
                ...this.winningDiagonalCombination
            ];
            return allWinningCombinations.some(combination =>
                combination.every(cell => playerMoves.includes(cell))
            );
        },
        getWinningCombination(playerTurn) {
            const playerChoices = new Set(
                this.stack
                    .filter(item => item.playerChoice === playerTurn)
                    .map(item => item.gridCell)
            );

            const winningCombinations = [
                ...this.winningRowsCombination,
                ...this.winningColumnsCombination,
                ...this.winningDiagonalCombination
            ];

            return winningCombinations.find(combination =>
                combination.every(cell => playerChoices.has(cell))
            ) || [];
        },
        updateScore() {
            if (this.isPlayerWon) {
                this.playerTurn === 'X' ? this.xScore++ : this.oScore++;
            }
            else {
                this.drawScore += 1;
            }
        },
        reloadPage() {
            window.location.reload();
        },
        reset() {
            this.stack = [];
            this.playerTurn = "X";
            this.totalNoOfMoves = 0; // Reset the total moves as well
        },
        updateDrawScore() {
            this.drawScore++;
        }
    }
};
</script>
<style scoped>
button:hover {
    cursor: pointer;
}

.modal {
    justify-content: center;
    align-items: center;
    display: flex;
    flex-direction: column;
    position: absolute;
    top: 50%;
    left: 0%;
    transform: translateY(-50%);
    width: 100%;
    background-color: var(--black-secondary);
    gap: 20px;
    padding: 20px;
    z-index: 1;
}

.wrapper {
    display: grid;
    row-gap: 20px;
}

.modal-heading-takes {
    font-size: var(--heading-large);
}

.modal div {
    justify-content: center;
    align-items: center;
    flex-grow: 1;
    display: flex;
    gap: 20px;
}

.quit {
    background-color: var(--gray-primary);
}

.next-round {
    background-color: var(--yellow-primary);
}

.modal-buttons button {
    padding: 15px;
}

.modal-buttons button span {
    font-family: 'Outfit';
    text-transform: uppercase;
    font-weight: 500;
}

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
    display: flex;
    align-items: center;
    justify-content: center;
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

@media (max-width: 768px) {
    .middle {
        height: 41.2rem;
    }

    .middle button img {
        height: 64px;
        width: 64px;
    }
}
</style>