<template>
    <div
        class="Loader"
        title="4"
        :style="{
            height: loaderHeight,
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
                fill="#ff6347"
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
                fill="#ff0000"
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
                fill="#ff6347"
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
export default {
    name: 'LoadingIcon',

    props: {
        size: {
            type: String,
            default: 'small',
            validator: val => ['small', 'large'].includes(val),
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
        };
    },

    computed: {
        loaderHeight() {
            return this.sizes[this.size].loaderHeight;
        },

        rectangleWidth() {
            return this.sizes[this.size].rectangleWidth;
        },

        rectangleHeight() {
            return this.sizes[this.size].rectangleHeight;
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
