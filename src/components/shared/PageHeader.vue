<template functional>
    <header class="PageHeader">
        <img
            src="../../assets/bomb.svg"
            class="LogoBomb"
        />
        <h1>MineSweepr.io</h1>
        <img
            src="../../assets/bomb.svg"
            class="LogoBomb"
        />
    </header>
</template>

<script>
export default {
    name: 'PageHeader',
};
</script>

<style lang="scss">
.PageHeader {
    display: flex;
    width: 100%;
    height: 150px;
    justify-content: center;
    align-items: center;

    .LogoBomb {
        margin-top: -20px;
        width: 48px;
    }
}
</style>
