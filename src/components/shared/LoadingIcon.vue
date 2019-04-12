<template>
    <div
        class="Loader"
        title="4"
        :style="{
            height: loaderHeight,
            margin: loaderMargin,
        }"
    >
        <svg
            id="Layer_1"
            version="1.1"
            xmlns="http://www.w3.org/2000/svg"
            xmlns:xlink="http://www.w3.org/1999/xlink"
            x="0px"
            y="0px"
            width="24px"
            height="30px"
            viewBox="0 0 24 30"
            style="enable-background:new 0 0 50 50;"
            xml:space="preserve"
        >
            <rect
                x="0"
                y="0"
                :width="rectangleWidth"
                :height="rectangleHeight"
                :fill="getRectangleFill(0)"
            >
                <animateTransform
                    attributeType="xml"
                    attributeName="transform"
                    type="translate"
                    values="0 0; 0 20; 0 0"
                    begin="0"
                    dur="0.6s"
                    repeatCount="indefinite"
                />
            </rect>
            <rect
                x="10"
                y="0"
                :width="rectangleWidth"
                :height="rectangleHeight"
                :fill="getRectangleFill(1)"
            >
                <animateTransform
                    attributeType="xml"
                    attributeName="transform"
                    type="translate"
                    values="0 0; 0 20; 0 0"
                    begin="0.2s"
                    dur="0.6s"
                    repeatCount="indefinite"
                />
            </rect>
            <rect
                x="20"
                y="0"
                :width="rectangleWidth"
                :height="rectangleHeight"
                :fill="getRectangleFill(2)"
            >
                <animateTransform
                    attributeType="xml"
                    attributeName="transform"
                    type="translate"
                    values="0 0; 0 20; 0 0"
                    begin="0.4s"
                    dur="0.6s"
                    repeatCount="indefinite"
                />
            </rect>
        </svg>
    </div>
</template>

<script>
import get from 'lodash';

export default {
    name: 'LoadingIcon',

    props: {
        size: {
            type: String,
            required: false,
            default: 'small',
            validator: val => ['small', 'large'].includes(val),
        },
        theme: {
            type: String,
            required: false,
            default: 'mine',
            validator: val => ['mine', 'grey', 'white'].includes(val),
        },
        layout: {
            type: String,
            required: false,
            default: 'regular',
            validator: val => ['regular', 'center'].includes(val),
        },
    },

    data() {
        return {
            sizes: {
                small: {
                    loaderHeight: '25px',
                    rectangleHeight: '10',
                    rectangleWidth: '4',
                },
                large: {
                    loaderHeight: '150px',
                    rectangleHeight: '100',
                    rectangleWidth: '5',
                },
            },
            colors: {
                mine: ['#ff6347', '#ff0000', '#ff6347'],
                grey: ['#333'],
                white: ['#fff'],
            },
        };
    },

    computed: {
        layoutCentered() {
            return this.layout === 'center';
        },

        loaderHeight() {
            return this.sizes[this.size].loaderHeight;
        },

        loaderMargin() {
            let margin = '0';

            if (this.layoutCentered) {
                margin = '0 auto';
            }

            return margin;
        },

        rectangleWidth() {
            return this.sizes[this.size].rectangleWidth;
        },

        rectangleHeight() {
            return this.sizes[this.size].rectangleHeight;
        },
    },

    methods: {
        getRectangleFill(index) {
            return get(this.colors[this.theme], index, this.colors[this.theme][0]);
        },
    },
};
</script>
<style lang="scss">
.Loader {
    width: 20%;
    text-align: center;
    display: flex;
    justify-content: center;
    align-items: center;
}
</style>
