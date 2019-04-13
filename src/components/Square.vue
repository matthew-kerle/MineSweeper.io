<template>
    <div
        :style="{
            width: `${boardBoxSize}px`,
            height: `${boardBoxSize}px`,
        }"
        :class="{
            'Square': true,
            Revealed: wasRevealed,
            GameOver: gameOver,
        }"
        @click="handleClick"
    >
        <template v-if="hasMine && wasRevealed">
            &nbsp;<img
                src="../assets/bomb.svg"
                class="Mine"
            />
        </template>
        <template v-else-if="!hasMine && wasRevealed">
            {{ adjacentMines }}
        </template>
        <template v-else>
            &nbsp;
        </template>
    </div>
</template>

<script>
import { EventBus } from '../EventBus';
import { BOARD_BOX_SIZE } from '../constants';

export default {
    name: 'Square',

    props: {
        hasMine: {
            type: Boolean,
            required: true,
        },
        parentRevealed: {
            type: Boolean,
            required: false,
            default: false,
        },
        adjacentMines: {
            type: Number,
            required: true,
        },
    },

    data() {
        return {
            revealed: false,
            gameOver: false,
        };
    },

    computed: {
        boardBoxSize() {
            return BOARD_BOX_SIZE;
        },

        wasRevealed() {
            return this.parentRevealed || this.revealed;
        },
    },

    mounted() {
        EventBus.$on('game:over', () => {
            this.gameOver = true;

            if (this.hasMine) {
                this.revealed = true;
            }
        });
    },

    methods: {
        handleClick(event) {
            if (this.gameOver) {
                return;
            }

            this.revealed = true;

            if (this.hasMine) {
                EventBus.$emit('game:over', true);
            } else {
                this.$emit('square:revealed', event);
            }
        },
    },
};
</script>

<style lang="scss">
.Square {
    display: inline-flex;
    justify-content: center;
    align-items: center;
    border-right: 1px solid #ccc;
    font-size: 16px;

    &:first-of-type {
        border-left: 1px solid #ccc;
    }

    &.Revealed {
        background-color: #eee;
    }

    &:hover:not(.Revealed):not(.GameOver) {
        background-color: #eee;
        cursor: pointer;
    }

    .Mine {
        width: 28px;
    }
}
</style>
