<template>
    <v-btn icon class="wow-btn-drop">
        <v-icon small><slot></slot>
        </v-icon>
    </v-btn>
</template>

<script>
    export default {
        name: "BtnHoverDrop"
    }
</script>

<style scoped lang="less">
    @import (reference) "~@/less/main/style.less";

    @keyframes drop {
        0% {
            transform: scale(1);
            opacity: 1;
        }
        25% {
            transform: scale(1.1);
            opacity: 1;
        }
        50% {
            transform: scale(1.15);
            opacity: 1;
        }
        75% {
            transform: scale(1.2);
            opacity: 0.6;
        }
        100% {
            transform: scale(1.2);
            opacity: 0;
        }
    }

    .wow-btn-drop {
        background: @wow-color-accent;

            color: #fff!important;
        &:after {
            content: '';
            border-radius: 50%;
            position: absolute;
            display: block;
            width: 100%;
            height: 100%;
            transition: all 0.2s ease-in-out !important;
        }
    }

    .wow-btn-drop:hover {
        &:after {
            opacity: 0;
            border: 0.1px solid @wow-color-accent;
            animation: drop 0.2s ease-in-out;
        }
    }
</style>