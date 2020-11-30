<template>
    <v-container fluid class="d-flex px-0 justify-end">
        <div class="col-4 d-flex px-2 align-center">
            <label>{{labelText}}</label>
        </div>

        <div class="col-8 d-flex">
            <input ref="textInput" type="text"
                   class="flex-grow-1 wow-input"
                   :disabled="inputDisabled">
            <v-btn text dense depressed
                   class="flex-grow-0 wow-input-btn"
                   @click="inputDisabled = !inputDisabled">{{inputDisabled ? 'Edit' : 'Save'}}
            </v-btn>
        </div>
    </v-container>
</template>

<script>
    export default {
        name: "FieldBtnEdit",

        props: {
            labelText: String
        },

        data() {
            return {
                inputDisabled: true
            }
        }
    }
</script>

<style scoped lang="less">
    @wow-border: 1px #101010 solid;

    .wow-input {
        background-color: white;
        border: @wow-border;
        border-radius: 0;
        height: 36px;
    }

    .wow-input-btn {
        border-right: @wow-border;
        border-bottom: @wow-border;
        border-top: @wow-border;
        border-radius: 0;
    }
</style>