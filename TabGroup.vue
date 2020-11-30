<template>
    <div>
        <label
                v-for="item in items"
                :key="item.handle"
                :class="['wow-radio-btn',item.handle, {'selected': picked === item.handle}]">
            <input
                    type="radio"
                    :value="item.handle"
                    :name="name"
                    v-model="picked">
            <span>{{ item.name }}</span>
        </label>
    </div>

</template>

<script>
    export default {
        name: "TabGroup",
        props: [
            'items',
            'value'
        ],
        data() {
            return {
                name: 'value_' + (new Date()).getDate(),
            }
        },
        computed: {
            picked: {
                get() {
                    return this.value
                },
                set(val) {
                    this.$emit('input', val);
                    this.$root.$emit('panel-selected', val);
                }
            }
        },
    }
</script>

<style scoped lang="less">
    @import (reference) "~@/less/main/style.less";

    .wow-radio-btn {
        border-top-right-radius: @card-btn-border-radius;
        border-top-left-radius: @card-btn-border-radius;
        border-bottom-right-radius: 0;
        border-bottom-left-radius: 0;
        width: auto;
        font-weight: bolder;
        padding: 1em 2em;
        margin-right: 0.5em;

        color: #606367;

        -moz-user-select: none;
        -khtml-user-select: none;
        -webkit-user-select: none;
        -ms-user-select: none;
        user-select: none;

        background: @card-btn-background;
        transition: all 0.5s;
        box-shadow: 6px 6px 12px @shadow-color-outer, -6px -6px 12px @shadow-color-inner;

        &:hover, &.selected {
            transition: all 0.2s;
            background: @card-btn-background-selected;
            box-shadow: inset 3px 3px 7px @shadow-color-outer-hover, inset -3px -3px 7px @shadow-color-inner-hover;
        }

        input {
            display: none;
        }
    }
</style>