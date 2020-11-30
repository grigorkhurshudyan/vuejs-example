<template>
    <div class="d-flex">
        <div
                :class="first"
                class="wow-app-bar-btn-icon "
        >
            <i @click="$emit('onclickFirst')"
               :class="`${iconFirst} wow-app-bar-btn-left wow-background-hover cursor-pointer`"></i>
        </div>
        <div
                :class="second"
                class="wow-app-bar-btn-icon "
        >
            <i @click="$emit('onclickSecond')"
               class="wow-app-bar-btn-right wow-background-hover cursor-pointer">
                <img style="height: 20px;" src="../assets/images/duck.svg">
            </i>

        </div>
    </div>

</template>

<script>
    import {CardType} from "@/helpers/CardDataTypes";

    export default {
        name: "BtnOvalIcon",
        props: {
            onclickFirst: {
                type: Function
            },
            onclickSecond: {
                type: Function
            },
            iconFirst: {
                type: String,
                default: "fa fa-smile"
            },

            cardType: {
                type: String,
            },
        },

        computed: {
            first: function () {
                return {
                    'active': this.cardType === CardType.EXPERIMENT
                }
            },
             second: function () {
                return {
                    'active': this.cardType === CardType.DUCKSOUP
                }
            }
        },

    }
</script>

<style scoped lang="less">
    @import (reference) "~@/less/main/style.less";

    .wow-app-bar-btn-icon {
        position: relative;
        display: flex;
        justify-content: center;
        align-items: center;

        .wow-app-bar-btn-right {
            position: relative;
            width: 4rem;
            height: @btn-width;
            border-radius: 0 100% 100% 0;
            font-size: @btn-font-size-contain;
            padding: 5px;

            color: #606367;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .wow-app-bar-btn-left {
            position: relative;
            width: 4rem;
            height: @btn-width;
            border-radius: 100% 0 0 100%;
            font-size: @btn-font-size-contain;
            padding: 5px;

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
</style>