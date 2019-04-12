<template>
    <div class="LoadingOverlay">
        <loading-icon size="large" />
    </div>
</template>

<script>
import LoadingIcon from './LoadingIcon';

export default {
    name: 'LoadingOverlay',

    components: {
        LoadingIcon,
    },
};
</script>

<style lang="scss">
.LoadingOverlay {
    width: 100%;
    height: 100%;
    position: absolute;
    z-index: 9999;
    background-color: rgba(255, 255, 255, 0.8);
    display: flex;
    justify-content: center;
    align-items: center;
}
</style>
