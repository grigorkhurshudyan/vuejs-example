<template>
    <button :class="`wow-btn-border-dashed ${classStr}`" :disabled="disabled">
        <slot></slot>
    </button>
</template>

<script>
    export default {
        name: "BtnBorderDashed",

        props: {
            disabled:Boolean,
            classStr: {
                type: String,
                default: ""
            }
        },

        data() {
            return {}
        },
    }
</script>

<style scoped lang="less">
    @wow-btn-border-dashed-background-color: #fff;
    @wow-btn-border-dashed-color-idle: #ccc;
    @wow-btn-border-dashed-color-hover: #000;

    .wow-btn-border-dashed {
        outline: none;
        border-color: @wow-btn-border-dashed-color-idle;
        border-style: dashed;
        border-width: 0.08em;
        background: @wow-btn-border-dashed-background-color;
        position: relative;
        color: @wow-btn-border-dashed-color-idle;
        transition: .4s;
        cursor: pointer;
        font-weight: normal;
        font-size: 1em;
        line-height: 1em;
        border-radius: 1em;
        width: auto;
        text-align: center;
        display: flex;
        align-items: center;
        justify-content: center;
        height: 3em;
        margin: 0 0.6em 0 0.6em;
        padding: 0 1em 0 1em;
    }

    .wow-btn-border-dashed:hover {
        border-color: @wow-btn-border-dashed-color-hover;
        color: @wow-btn-border-dashed-color-hover;
    }
</style>