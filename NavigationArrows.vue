<template>
    <div>
        <v-btn class="board-nav-arrow arrow-left" @click="panelArrowNavigateLeft" :disabled="prevDisabled">
            <i class="fa fa-angle-left"></i>
        </v-btn>
        <v-btn class="board-nav-arrow arrow-right" @click="panelArrowNavigateRight" :disabled="nextDisabled">
            <i class="fa fa-angle-right"></i>
        </v-btn>
    </div>
</template>

<script>
    import {mapState} from 'vuex'
    import {PanelType} from "@/helpers/PanelType";
    import {updateRouterParams} from "@/helpers/utils";

    export default {
        data() {
            return {
                prevDisabled: false,
                nextDisabled: false
            }
        },

        computed: {
            ...mapState('workspace', ['workspace']),
            ...mapState('folder', ['ROOT_FOLDER']),

            getPanelTypeFromUrl() {
                return this.$route.params.panelType;
            },

            allPanelNames() {
                let panelNames = Object.values(PanelType);
                let currentPanel = this.getPanelTypeFromUrl;
                let prevNextPanel = {
                    prevPanel: undefined,
                    nextPanel: undefined
                };

                for (let i = 0; i < panelNames.length; i++) {
                    if (currentPanel === panelNames[i]) {
                        prevNextPanel.prevPanel = panelNames[i - 1];
                        prevNextPanel.nextPanel = panelNames[i + 1];
                    }
                }

                return prevNextPanel;
            },

            firstPanel() {
                const allPanels = Object.values(PanelType);
                return allPanels[0];
            },

            lastPanel() {
                const allPanels = Object.values(PanelType);
                return allPanels.pop();
            },
        },

        methods: {
            arrowNav(panel) {
                panel === this.firstPanel ? this.prevDisabled = true : this.prevDisabled = false;
                panel === this.lastPanel ? this.nextDisabled = true : this.nextDisabled = false;
            },

            panelArrowNavigateLeft() {
                if (this.allPanelNames.prevPanel !== undefined) {
                    updateRouterParams(
                        this.$router,
                        {
                            panelType: this.allPanelNames.prevPanel,
                            folderId: this.ROOT_FOLDER,
                        }
                    );
                }

                this.$nextTick(() => {
                    this.arrowNav(this.getPanelTypeFromUrl);

                    this.$root.$emit('filter-nav', this.getPanelTypeFromUrl);

                    if (this.nextDisabled !== false) {
                        this.nextDisabled = false;
                    }
                });
            },

            panelArrowNavigateRight() {
                if (this.allPanelNames.nextPanel !== undefined) {
                    updateRouterParams(
                        this.$router,
                        {
                            panelType: this.allPanelNames.nextPanel,
                            folderId: this.ROOT_FOLDER,
                        }
                    );
                }

                this.$nextTick(() => {
                    this.arrowNav(this.getPanelTypeFromUrl);

                    // Emit value to use for selected tab in FilterPanel
                    this.$root.$emit('filter-nav', this.getPanelTypeFromUrl);

                    if (this.prevDisabled !== false) {
                        this.prevDisabled = false;
                    }
                });
            }
        },

        created() {
            this.arrowNav(this.getPanelTypeFromUrl);

            this.$root.$on('panel-selected', (board) => {
                this.arrowNav(board);
            });
        }
    }
</script>

<style scoped lang="less">
    @import (reference) "~@/less/main/style.less";

    .board-nav-arrow {
        position: absolute;
        top: 50px;
        min-width: 60px !important;
        max-width: 60px !important;
        height: 60px !important;
        display: flex !important;
        align-items: center;
        justify-content: center;
        text-align: center;
        font-size: 2em;
        border-radius: 50%;

        &.arrow-left {
            left: -90px;
        }

        &.arrow-right {
            right: -90px;
        }
    }

    @media screen and (min-width: 1920px) {
        .board-nav-arrow {
            &.arrow-left {
                left: -68px;
            }

            &.arrow-right {
                right: -68px;
            }
        }
    }
</style>