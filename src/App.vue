<template>
    <div id="app">
        <!-- <loading-overlay v-if="loading" /> -->

        <div class="PageContent">
            <page-header />

            <div
                v-if="!boardCreated"
                class="GameOptionsContainer"
            >
                <h2>Game Options</h2>

                <div class="SettingContainer">
                    <h3>Board Size:</h3>
                    <input
                        v-model.number="options.columns"
                        type="number"
                        min="2"
                        max="16"
                        step="1"
                        :disabled="loading"
                    />
                    &nbsp;x&nbsp;
                    <input
                        v-model.number="options.rows"
                        type="number"
                        min="2"
                        max="16"
                        step="1"
                        :disabled="loading"
                    />
                    &nbsp;(Total Squares: {{ totalBoardSquares }})
                </div>

                <div class="SettingContainer">
                    <h3>Number of Mines:</h3>
                    <input
                        v-model.number="options.mines"
                        type="number"
                        min="1"
                        :max="totalBoardSquares - 1"
                        step="1"
                        :disabled="loading"
                    />
                    &nbsp;(Min: 1 | Max: {{ totalBoardSquares - 1 }})
                </div>
                <m-button
                    :loading="loading"
                    @click="handleStartGame"
                >
                    Start
                </m-button>
            </div>

            <div v-if="submitted && !loading && gameOver">
                <div :class="[gameOverClass, 'Result']">
                    <template v-if="gameWon">
                        🎉&nbsp;Congratulations, you beat MineSweepr!&nbsp;🎉
                    </template>
                    <template v-else>
                        Bomber...sorry, you lost! 💣
                    </template>
                </div>
                <m-button
                    class="PlayAgainButton"
                    :loading="loadingPlayAgain"
                    @click="handlePlayAgain"
                >
                    Play Again
                </m-button>
            </div>

            <board
                v-if="submitted"
                v-show="!loading"
                :rows="options.rows"
                :columns="options.columns"
                :mines="options.mines"
                @board:created="handleBoardCreated"
            />
            <page-footer />
        </div>
    </div>
</template>

<script>
import { EventBus } from './EventBus';
import PageHeader from './components/shared/PageHeader';
import PageFooter from './components/shared/PageFooter';
import MButton from './components/shared/MButton';

import Board from './components/Board';

/**
 * Cleanup List:
 * - Write Unit Tests & 100% Coverage with Jest
 * - Migrate to PostCSS
 * - Move settings into its own component
 */

const initialState = () => ({
    loading: false,
    submitted: false,
    boardCreated: false,
    gameOver: false,
    gameWon: false,
    loadingPlayAgain: false,
    options: {
        rows: 8,
        columns: 8,
        mines: 8,
    },
});

export default {
    name: 'App',

    components: {
        PageHeader,
        PageFooter,
        MButton,
        Board,
    },

    data() {
        return initialState();
    },

    computed: {
        totalBoardSquares() {
            return this.options.columns * this.options.rows;
        },

        gameOverClass() {
            return `Result${this.gameWon ? 'Won' : 'Failure'}`;
        },
    },

    watch: {
        options: {
            handler() {
                if (this.options.mines >= this.totalBoardSquares) {
                    this.options.mines = this.totalBoardSquares - 1;
                }
            },
            deep: true,
        },
    },

    mounted() {
        EventBus.$on('game:over', value => {
            this.gameOver = value;
        });

        EventBus.$on('game:won', value => {
            this.gameWon = value;
        });
    },

    methods: {
        handleStartGame() {
            this.loading = true;
            this.submitted = true;
        },

        handleBoardCreated() {
            this.loading = false;
            this.boardCreated = true;
        },

        handlePlayAgain() {
            this.loadingPlayAgain = true;
            setTimeout(() => {
                Object.assign(this.$data, initialState());
            }, 1000);
        },
    },
};
</script>

<style lang="scss">
* {
    box-sizing: border-box;
}

.PageContent {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    width: 100%;
    height: 100%;
    margin: 20px 0 0 0;
    padding: 0;
}

input {
    padding: 2px;
    font-size: 15px;

    &:focus {
        outline: none;
    }
}

.GameOptionsContainer {
    width: 50%;
    margin: 0 auto;
    border: 1px solid #ccc;
    border-radius: 15px;
    background-color: #eee;
    padding: 20px;

    h2 {
        margin-top: 0;
    }
}

.SettingContainer {
    display: flex;
    justify-content: center;
    align-items: center;
    margin-top: -20px;

    h3 {
        margin-right: 10px;
    }

    input {
        width: 50px;
        text-align: center;
    }
}

.Result {
    width: 600px;
    margin: -25px auto 0 auto;
    padding: 10px;
    font-size: 24px;
    border-radius: 10px;
    border: 1px solid #ccc;
}

.PlayAgainButton {
    margin: 20px 0;
}
</style>
