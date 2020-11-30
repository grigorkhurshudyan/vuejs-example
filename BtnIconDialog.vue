<template>
    <div :class="`${classStr}`" v-bind:title="titleStr" >
        <i :class="`${iconClass} wow-app-bar-btn-round wow-color-hover cursor-pointer`"></i>
    </div>
</template>

<script>
    export default {
        name: "BtnIconDialog",

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

    .wow-color-hover {
        color: rgba(128, 128, 128);

        &:hover {
            color: #000;
        }
    }
</style>