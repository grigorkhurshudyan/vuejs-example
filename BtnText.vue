<template>
    <v-btn text class="px-2 text-transform-uppercase cursor-pointer wow-color-hover">
        <slot/>
    </v-btn>
</template>

<script>
    export default {
        name: "BtnText",
    }
</script>

<style scoped lang="less">
    @import (reference) "~@/less/main/style.less";

    .v-btn:before {
        transition: none;
    }

    .v-btn:hover:before, .v-btn:focus:before {
        background: transparent !important;
    }

    .v-btn {
        font-weight: bold;
    }
</style>