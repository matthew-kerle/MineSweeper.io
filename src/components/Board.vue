<template>
    <div
        class="GameBoard"
        :style="{
            width: boardWidth,
        }"
    >
        <div
            v-for="(row, rowIndex) in board.rows"
            :key="rowIndex"
            :ref="`row_${rowIndex}`"
            class="BoardRow"
        >
            <div
                v-for="(column, columnIndex) in board.columns"
                :key="columnIndex"
                :ref="`column_${columnIndex}`"
                :style="{
                    width: `${boardBoxSize}px`,
                    height: `${boardBoxSize}px`,
                }"
                class="BoardColumn"
            >
                &nbsp;
            </div>
        </div>
    </div>
</template>

<script>
// Board Box Size in Pixels
const BOARD_BOX_SIZE = 75;

const ARTIFICIAL_LOADING_TIME = 2000;

export default {
    name: 'Board',

    components: {
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
                rows: [],
                columns: [],
                mine_coordinates: [],
            },
        };
    },

    computed: {
        boardWidth() {
            return `${this.columns * BOARD_BOX_SIZE}px`;
        },

        boardBoxSize() {
            return BOARD_BOX_SIZE;
        },
    },

    watch: {
        created(newValue) {
            setTimeout(() => {
                this.$emit('board:created', newValue);
            });
        },
    },

    mounted() {
        this.__fillArray(this.board.rows, this.rows);
        this.__fillArray(this.board.columns, this.columns);

        setTimeout(() => {
            this.created = true;
        }, ARTIFICIAL_LOADING_TIME);
    },

    methods: {
        __fillArray(array, itemCount, base = 1) {
            for (let i = base; i <= itemCount; i++) {
                array.push('');
            }
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

        .BoardColumn {
            display: inline-flex;
            justify-content: center;
            align-items: center;
            border-right: 1px solid #ccc;
            font-size: xx-small;

            &:first-of-type {
                border-left: 1px solid #ccc;
            }

            // TODO: Don't show hover state if already uncovered.
            &:hover {
                background-color: #eee;
                cursor: pointer;
            }
        }
    }
}
</style>
