<template>
    <div id="app">
        <loading-overlay v-if="loading" />

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
                <button
                    type="submit"
                    :disabled="loading"
                    @click="handleStartGame"
                >
                    Start
                </button>
            </div>

            <board
                v-if="submitted"
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
import PageHeader from './components/shared/PageHeader';
import PageFooter from './components/shared/PageFooter';
import LoadingOverlay from './components/shared/LoadingOverlay';

import Board from './components/Board';

/**
 * TODO List:
 *
 *  - During board creation place mines randomly
 *
 * - After mines are placed, add number of surrounding mines
 * next to each square by evaluating + and - rows and then + and - each column
 *
 * - Highlight all clicked squares
 *
 * - Add Click Handler for Squares:
 *
 * WHEN SQUARE CLICKED:
 * - If mine trigger "GAME OVER STATE" with Restart Button
 * - If not mine AND has surrounding: display the number of surronding mines (immediately, any direction)
 * - If not mine AND no surrounding: trigger same function for each neighboring square (recursion FTW)
 *
 * - Show all mines when you click a mine square
 * - Display time taken to solve in seconds
 *
 * Cover any recursion issues:
 * - Don't allow negative board squares for edges of board
 */

/**
 * Cleanup List:
 * - Write Unit Tests & 100% Coverage with Jest
 * - Migrate to PostCSS
 * - Move settings into its own component
 * - Utilize Vuex for state management
 * - Fix overflow issues with larger grids
 */

export default {
    name: 'App',

    components: {
        PageHeader,
        PageFooter,
        LoadingOverlay,
        Board,
    },

    data() {
        return {
            loading: false,
            submitted: false,
            boardCreated: false,
            options: {
                rows: 8,
                columns: 8,
                mines: 8,
            },
        };
    },

    computed: {
        totalBoardSquares() {
            return this.options.columns * this.options.rows;
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

    methods: {
        handleStartGame() {
            this.loading = true;
            this.submitted = true;
        },

        handleBoardCreated() {
            this.loading = false;
            this.boardCreated = true;
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

button {
    background-color: #007fff;
    color: #fff;
    outline: none;
    border: 0;
    padding: 10px;
    font-size: 20px;
    width: 150px;
    height: 50px;

    &:hover:not([disabled]) {
        cursor: pointer;
        background-color: #0067cd;
    }
}

.GameOptionsContainer {
    width: 50%;
    margin: 0 auto;
    border: 1px solid #ccc;
    border-radius: 15px;
    background-color: #eee;
    padding: 15px;
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
</style>
