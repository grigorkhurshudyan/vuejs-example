<template>
    <a :href="url" class="text-decoration-none wow-color-hover cursor-pointer">
        <span :class="`${iconClass} pr-2 mw`"/>
        <slot/>
    </a>
</template>

<script>
    export default {
        name: "BtnIconTextFlat",
        props: {
            iconClass: {
                type: String,
                default: 'fa fa-long-arrow-alt-left'
            },
            url: {
                type: String,
                default: void(0)
            },
        }
    }
</script>

<style scoped lang="less">
    @import (reference) "~@/less/main/style.less";
</style>