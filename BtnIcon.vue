<template>
    <div :class="`${classStr} mx-2`" v-bind:title="titleStr">
        <i :class="`${iconClass} wow-app-bar-btn-round wow-color-hover cursor-pointer`" ></i>
    </div>
</template>

<script>
    export default {
        name: "BtnIcon",

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

    .wow-app-bar-btn-round {
        width: @btn-height;
        height: @btn-width;
        font-size: @wow-btn-text-font-size;
        display: flex;
        align-items: center;
        justify-content: center;

        background: linear-gradient(315deg, #d4d4d4, #fbfbfb);
        box-shadow: 3px 3px 5px rgba(0, 0, 0, 0.1), -2px -2px 5px rgba(255, 255, 255, 1);
        border-color: @wow-background;
        color: @wow-color-idle;
        background: @wow-background;
        transition: all 0.5s;
        &:hover {
            color: #000 !important;
            box-shadow: 0px 1px 3px rgba(0, 0, 0, 0.3) inset;
            transform: scale(1.1);
            transition: all 0.2s;
            background: @wow-background !important;
            span {
              transform: scale(0.9);
            }
        }
    }
</style>