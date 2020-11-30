<template>
    <textarea class="wow-title-textarea px-4 py-2" v-model="title">
    </textarea>
</template>

<script>
    export default {
        name: "TitleInput",

        props: {
            title: Object
        }
    }
</script>

<style scoped lang="less">
    @wow-textarea-font-size: 1em;
    @wow-textarea-line-height: 150%;
    @wow-textarea-line-height-em: @wow-textarea-font-size * @wow-textarea-line-height / 100;
    @wow-textarea-height: @wow-textarea-font-size * 2 + @wow-textarea-line-height-em;

    .wow-title-textarea {
        background-color: transparent;

        width: 100%;
        height: @wow-textarea-height;

        resize: none;
        outline: none;
        border: none;
        box-shadow: none;
        overflow: hidden;

        font-size: @wow-textarea-font-size;
        line-height: @wow-textarea-line-height;
        color: black;
        font-weight: bold;

        &:focus {
            background-color: white;
        }
    }
</style>