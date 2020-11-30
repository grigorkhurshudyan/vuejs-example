<template>
    <v-container class="main-container" fluid>
        <div class="container-title" v-if="tooltipText">
             <popper trigger="hover" :options="{placement: 'top'}" :delay-on-mouse-out="200">
                 <div class="popper">
                    {{tooltipText}}
                     <span v-if="tooltipLink"><a target="_blank" :href="tooltipLink"><br/>Learn More >>></a></span>
                 </div>
                 <span slot="reference">
                     <span :class="`container-header-icon pr-1 ${iconClass}`"></span> {{title}}
                 </span>
            </popper>
        </div>
        <div v-else class="container-title">
            <span :class="`container-header-icon pr-1 ${iconClass}`"></span>{{title}}
        </div>
        <div>
            <slot name="content"></slot>
        </div>
    </v-container>
</template>

<script>
    import Popper from 'vue-popperjs';
    import 'vue-popperjs/dist/vue-popper.css';

    export default {
        name: "ContainerHeaderIcon",

        components: {
      'popper': Popper
    },

        props: {
            title: {
                type: String,
                default: ''
            },
            iconClass: {
                type: String,
                default: ''
            },
            tooltipText: {
                type: String,
                default: ''
            },
            tooltipLink: {
                type: String,
                default: ''
            }
        },
    }
</script>

<style scoped lang="less">
    @container-header-icon-color: #fff;
    @container-border-color: #dadce0;

    /*.main-container {
        border: 0.1em solid @container-border-color;
        border: none;
        background: @container-header-icon-color;
    }*/

    .container-title{
        /*pointer-events: none;*/
        font-size: 1em;
    }

    .container-header-icon {
        font-size: 1em;
        line-height: unset;
    }
</style>