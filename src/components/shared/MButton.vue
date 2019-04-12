<template>
    <button
        type="submit"
        :disabled="isDisabled"
        @click="handleClick"
    >
        <slot v-if="!loading" />
        <loading-icon
            v-else
            theme="white"
            layout="center"
        />
    </button>
</template>

<script>
import LoadingIcon from './LoadingIcon';

export default {
    name: 'MButton',

    components: {
        LoadingIcon,
    },

    props: {
        disabled: {
            type: Boolean,
            default: false,
        },
        loading: {
            type: Boolean,
            default: false,
        },
    },

    computed: {
        isDisabled() {
            return this.loading;
        },
    },

    methods: {
        handleClick(event) {
            this.$emit('click', event);
        },
    },
};
</script>

<style lang="scss">
button {
    background-color: #007fff;
    color: #fff;
    outline: none;
    border: 0;
    padding: 10px;
    font-size: 20px;
    width: 150px;
    height: 50px;
    text-align: center;

    &:hover:not([disabled]) {
        cursor: pointer;
        background-color: #0067cd;
    }
}
</style>
