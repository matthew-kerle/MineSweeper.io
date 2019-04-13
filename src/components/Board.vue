<template>
    <div
        class="GameBoard"
        :style="{
            width: boardWidth,
        }"
    >
        <div
            v-for="(row, rowIndex) in board.squares"
            :key="rowIndex"
            class="BoardRow"
            :style="{
                height: rowHeight,
            }"
        >
            <square
                v-for="(square, columnIndex) in row"
                :key="`row_${rowIndex}_column_${columnIndex}`"
                :ref="`row_${rowIndex}_column_${columnIndex}`"
                :has-mine="square.hasMine"
                :adjacent-mines="square.adjacentMines"
                :parent-revealed="square.parentRevealed"
                @square:revealed="handleSquareRevealed(rowIndex, columnIndex)"
            />
        </div>
    </div>
</template>

<script>

/*
 * TODO: add row / column to each square so it's aware of where it is
 * so we can just pass "square" instead of row / column everywhere and
 * do repeated lookups.
 */

import Square from './Square';

import { EventBus } from '../EventBus';
import { BOARD_BOX_SIZE } from '../constants';

const ARTIFICIAL_LOADING_TIME = 2000;

export default {
    name: 'Board',

    components: {
        Square,
    },

    props: {
        rows: {
            type: Number,
            required: true,
        },
        columns: {
            type: Number,
            required: true,
        },
        mines: {
            type: Number,
            required: true,
        },
    },

    data() {
        return {
            created: false,
            board: {
                squares: [],
            },
            numberOfSquaresRevealed: 0,
            gameOver: false,
            gameWon: false,
        };
    },

    computed: {
        boardWidth() {
            return `${this.columns * BOARD_BOX_SIZE}px`;
        },

        rowHeight() {
            return `${BOARD_BOX_SIZE}px`;
        },

        totalSquares() {
            return this.columns * this.rows;
        },
    },

    watch: {
        created(newValue) {
            setTimeout(() => {
                this.$emit('board:created', newValue);
            });
        },
        gameOver(newValue) {
            EventBus.$emit('game:over', newValue);
        },
    },

    mounted() {
        this.__fillBoard();
        this.__randomlyPlaceMines();
        this.__fillAdjacentNumbersForSquares();

        setTimeout(() => {
            this.created = true;
        }, ARTIFICIAL_LOADING_TIME);

        EventBus.$on('game:over', value => {
            this.gameOver = value;
            this.__checkCompletionStatus();
        });
    },

    methods: {
        handleSquareRevealed(row, column) {
            const square = this.board.squares[row][column];

            if (square.revealed || square.parentRevealed) {
                return;
            }

            square.revealed = true;
            this.numberOfSquaresRevealed++;

            if (!square.hasMine && square.adjacentMines === 0) {
                this.__revealAdjacentSquares(row, column);
            }

            this.__checkCompletionStatus();
        },

        __checkCompletionStatus() {
            this.gameWon = (this.totalSquares - this.mines) === this.numberOfSquaresRevealed;
            this.gameOver = this.gameWon;
            EventBus.$emit('game:won', this.gameWon);
        },

        __revealAdjacentSquares(row, column) {
            const adjacentSquares = this.__getAdjacentSquares(row, column);
            adjacentSquares.forEach(adjacentSquare => {
                if (!adjacentSquare.revealed && !adjacentSquare.parentRevealed) {
                    this.numberOfSquaresRevealed++;
                }

                if (!adjacentSquare.revealed
                    && !adjacentSquare.parentRevealed
                    && adjacentSquare.adjacentMines === 0
                ) {
                    adjacentSquare.parentRevealed = true;
                    this.__revealAdjacentSquares(adjacentSquare.row, adjacentSquare.column);
                } else if (adjacentSquare.adjacentMines !== 0) {
                    adjacentSquare.parentRevealed = true;
                }
            });
        },

        __getAdjacentSquares(row, column) {
            const adjacentSquares = [];
            const aboveRow = this.board.squares[row - 1] || false;
            const belowRow = this.board.squares[row + 1] || false;

            if (aboveRow) {
                adjacentSquares.push(
                    this.__getSquareByCoords(row - 1, column - 1),
                    this.__getSquareByCoords(row - 1, column),
                    this.__getSquareByCoords(row - 1, column + 1),
                );
            }

            adjacentSquares.push(
                this.__getSquareByCoords(row, column - 1),
                this.__getSquareByCoords(row, column + 1),
            );

            if (belowRow) {
                adjacentSquares.push(
                    this.__getSquareByCoords(row + 1, column - 1),
                    this.__getSquareByCoords(row + 1, column),
                    this.__getSquareByCoords(row + 1, column + 1),
                );
            }

            return adjacentSquares.filter(value => value !== false);
        },


        __fillAdjacentNumbersForSquares() {
            this.board.squares.forEach((row, rowIndex) =>
                row.forEach((square, columnIndex) => this.__setNumberOfAdjacentMinesForSquare(rowIndex, columnIndex))
            );
        },

        __getSquareByCoords(row, column) {
            const squareExists = this.board.squares[row] && this.board.squares[row][column];
            return squareExists ? this.board.squares[row][column] : false;
        },

        __setNumberOfAdjacentMinesForSquare(row, column) {
            let adjacentMinesCount = 0;
            const square = this.board.squares[row][column];
            if (!square.hasMine) {
                const aboveRow = this.board.squares[row - 1] || false;
                const belowRow = this.board.squares[row + 1] || false;

                const adjacentMines = {
                    aboveRow: 0,
                    currentRow: 0,
                    belowRow: 0,
                };

                if (aboveRow) {
                    adjacentMines.aboveRow = this.__getReducedSum([
                        this.__squareHasMine(row - 1, column - 1) ? 1 : 0,
                        this.__squareHasMine(row - 1, column) ? 1 : 0,
                        this.__squareHasMine(row - 1, column + 1) ? 1 : 0,
                    ]);
                }

                adjacentMines.currentRow = this.__getReducedSum([
                    this.__squareHasMine(row, column - 1) ? 1 : 0,
                    this.__squareHasMine(row, column + 1) ? 1 : 0,
                ]);

                if (belowRow) {
                    adjacentMines.belowRow = this.__getReducedSum([
                        this.__squareHasMine(row + 1, column - 1) ? 1 : 0,
                        this.__squareHasMine(row + 1, column) ? 1 : 0,
                        this.__squareHasMine(row + 1, column + 1) ? 1 : 0,
                    ]);
                }

                adjacentMinesCount = this.__getReducedSum(Object.values(adjacentMines));
            }

            square.adjacentMines = adjacentMinesCount;
        },

        __getReducedSum(array) {
            return array.reduce((accumulator, currentValue) => accumulator + currentValue);
        },

        __squareHasMine(row, column) {
            const squareExists = this.board.squares[row] && this.board.squares[row][column];
            return squareExists ? this.board.squares[row][column].hasMine : false;
        },

        __fillBoard() {
            for (let row = 0; row < this.rows; row++) {
                const rowOfColumns = [];

                for (let column = 0; column < this.columns; column++) {
                    rowOfColumns.push({
                        revealed: false,
                        parentRevealed: false,
                        hasMine: false,
                        adjacentMines: 0,
                        row,
                        column,
                    });
                }
                this.board.squares.push(rowOfColumns);
            }
        },

        __randomlyPlaceMines() {
            for (let i = 0; i < this.mines; i++) {
                let minePlaced = false;

                do {
                    const randomRow = this.__getRandomNumber(0, this.rows - 1);
                    const randomColumn = this.__getRandomNumber(0, this.columns - 1);

                    if (!this.__squareHasMine(randomRow, randomColumn)) {
                        this.board.squares[randomRow][randomColumn].hasMine = true;
                        minePlaced = true;
                    }
                } while (!minePlaced);
            }
        },

        /*
         * Reference:
         * https://stackoverflow.com/questions/4959975/generate-random-number-between-two-numbers-in-javascript
         */
        __getRandomNumber(min, max) {
            return Math.floor(Math.random() * (max - min + 1) + min);
        },
    },
};
</script>

<style lang="scss">
.GameBoard {
    margin: 0 auto;
    display: flex;
    flex-wrap: nowrap;
    justify-content: center;
    flex-direction: column;

    .BoardRow {
        border-top: 1px solid #ccc;

        &:last-of-type {
            border-bottom: 1px solid #ccc;
        }
    }
}
</style>
