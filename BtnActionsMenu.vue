<template>
    <div class="wow-app-bar-btn-icon mx-2" title="Actions">
        <i class="fas fa-ellipsis-v actions-menu-button wow-app-bar-btn-round-border wow-border-hover wow-color-hover cursor-pointer"></i>
    </div>
</template>

<script>
    export default {
        name: "BtnActionsMenu",

        props: {
            iconClass: {
                type: String,
                default: "fa fa-smile"
            },
            onclick: {
                type: Function,
                default: function () {
                    // eslint-disable-next-line no-console
                    console.log("BtnCircleIcon click happened");
                }
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

    .actions-menu-button {
        @animation-duration: 0.5s;

        -webkit-animation: spin-off @animation-duration ease-in-out;
        -moz-animation: spin-off @animation-duration ease-in-out;
        animation: spin-off @animation-duration ease-in-out;

        @-moz-keyframes spin-off {
            100% {
                -moz-transform: rotate(-360deg);
            }
        }
        @-webkit-keyframes spin-off {
            100% {
                -webkit-transform: rotate(-360deg);
            }
        }
        @keyframes spin-off {
            100% {
                -webkit-transform: rotate(-360deg);
                transform: rotate(-360deg);
            }
        }

        @-moz-keyframes spin-on {
            100% {
                -moz-transform: rotate(360deg);
            }
        }
        @-webkit-keyframes spin-on {
            100% {
                -webkit-transform: rotate(360deg);
            }
        }
        @keyframes spin-on {
            100% {
                -webkit-transform: rotate(360deg);
                transform: rotate(360deg);
            }
        }

        &:hover {
            -webkit-animation: spin-on @animation-duration ease-in-out;
            -moz-animation: spin-on @animation-duration ease-in-out;
            animation: spin-on @animation-duration ease-in-out;
        }
    }
</style>