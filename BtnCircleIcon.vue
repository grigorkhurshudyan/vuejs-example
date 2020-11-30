<template>
    <div
            :class="['wow-app-bar-btn-icon', 'mx-1', {'active': on}]"
            v-bind:title="titleStr">
        <i :class="[`${iconClass} wow-app-bar-btn-round wow-background-hover cursor-pointer`,{'bgray' : disabled}]"></i>
        <span class="text">{{text}}</span>
    </div>
</template>

<script>
    export default {
        name: "BtnCircleIcon",

        props: {
            disabled:{
                type: Boolean,
                default: false
            },
            iconClass: {
                type: String,
                default: "fa fa-smile"
            },
            titleStr: {
                type: String,
                default: ""
            },
            text: {
                type: String,
                default: ""
            },
        },

        data() {
            return {
                on: false
            }
        },

        methods: {
            toggle() {
                this.on = !this.on;
            },

            setOn(on) {
                this.on = on;
            }
        }
    }
</script>

<style scoped lang="less">
    @import (reference) "~@/less/main/style.less";

    .wow-app-bar-btn-icon {
        position: relative;
        display: flex;
        justify-content: center;
        align-items: center;

        .wow-app-bar-btn-round {
            position: relative;
            width: @btn-height;
            height: @btn-width;
            border-radius: 50%;
            font-size: @btn-font-size-contain;

            color: #606367;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .text {
            position: absolute;
            font-weight: bold;
            pointer-events: none;
            color: white;
            -webkit-text-stroke: 1px black;
        }

    }

    .sm {
        font-size: 15px !important;
        width: 1.3em !important;
        height: 1.3em !important;
    }
</style>