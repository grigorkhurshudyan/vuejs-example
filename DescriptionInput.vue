<template>
        <textarea class="wow-textarea-description px-4 py-2" :value="value"
                  @input="$emit('input', $event.target.value)"></textarea>
</template>

<script>
    export default {
        name: "DescriptionInput",
        props: ['value'],
    }
</script>

<style scoped lang="less">
    @wow-textarea-font-size: 0.9em;
    @wow-textarea-line-height: 150%;
    @wow-textarea-line-height-em: @wow-textarea-font-size * @wow-textarea-line-height / 100;
    @wow-textarea-height: @wow-textarea-font-size * 4 + @wow-textarea-line-height-em * 2;

    .wow-textarea-description {
        font-size: @wow-textarea-font-size;
        line-height: @wow-textarea-line-height;

        height: @wow-textarea-height;
        width: 100%;

        resize: none;
        outline: none;
        box-shadow: none;
        overflow: hidden;
    }
</style>