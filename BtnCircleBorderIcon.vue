<template>
    <div :class="`${classStr}`" v-bind:title="titleStr">
        <i :class="`${iconClass} wow-app-bar-btn-round-border wow-border-hover wow-color-hover cursor-pointer`"></i>
    </div>
</template>

<script>
    export default {
        name: "BtnCircleBorderIcon",

        props: {
            iconClass: {
                type: String,
                default: "fa fa-smile"
            },
            classStr: {
                type: String,
                default: ""
            },
            titleStr: {
                type: String,
                default: ""
            }
        },

        methods: {}
    }
</script>

<style scoped lang="less">
    @import (reference) "~@/less/main/style.less";

    .wow-app-bar-btn-round-border {
        width: 100%;
        height: 100%;
        border-radius: 50%;
        font-size: 1em;
        display: flex;
        align-items: center;
        justify-content: center;
        border-width: @wow-border-width;
        border-style: @wow-border-style;
    }
</style>