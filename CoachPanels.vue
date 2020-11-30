<template>
    <v-dialog v-model="isVisible" width="800" persistent no-click-animation>
        <v-card class="wow-background" >
            <v-container class="pa-2 d-flex justify-end">
                <v-btn icon x-small @click="hide">
                    <v-icon>fa-times</v-icon>
                </v-btn>
            </v-container>

            <template>
                <v-carousel
                        v-model="model"
                        class="coach-panel"
                        hide-delimiter-background
                        :show-arrows="false"
                        height="400" interval="1000" :cycle="false"
                >
                    <v-carousel-item
                            v-for="(panel, i) in headingFilter.fields"
                            :key="i"
                    >
                        <v-sheet
                                height="100%"
                        >
                            <v-row
                                    class="panelImg"
                                    align="center"
                                    justify="center"
                            >
                                    <v-img
                                            class="white--text align-end"
                                            height="200px"
                                            src="@/assets/images/workspace_icon.jpg"
                                    >
                                    </v-img>

                            </v-row>
                            <v-row
                                    class="panelTitle"
                                    align="center"
                                    justify="center"
                            >
                                    <div>{{panel.title}}</div>

                            </v-row>
                            <v-row
                                    class="panelText"
                                    align="center"
                                    justify="center"
                            >
                                    <div>{{panel.text}}</div>
                            </v-row>
                        </v-sheet>
                    </v-carousel-item>
                </v-carousel>
                <v-card-actions>
                    <v-spacer/>
                    <BtnText @click.native="hide" class="btn-skip">
                        Skip
                    </BtnText>
                    <BtnText @click.native="next" class="btn-next" v-if="headingFilter.fields.length !== 1">
                        Next
                    </BtnText>
                </v-card-actions>
            </template>
        </v-card>
    </v-dialog>
</template>

<script>
    import BtnText from "@/components/BtnText";

    export default {
        name: "CoachPanels",

        props:['filter'],

        components:{
          BtnText
        },
         data() {
            return {
                isVisible: false,
                model:0,
                panels: [
                    {
                        name: "action",
                        fields: [
                            {
                                title: 'Due Cards & Task',
                                text: "Here you'll find tasks or experiments assigned to your or which you are a member of due within five days. This is an automated process dictated by the card or task due date."
                            },
                            {
                                title: 'Create a "To-Do" List',
                                text: 'Manage your time effectively while keeping track of personal “to-do” items, tasks and cards all in one place. Assign tasks to do today, tomorrow, this week and this month so you’ll never miss a deadline.'
                            },
                        ]
                    },
                    {
                        name: "ideation",
                        fields: [
                            {
                                title: 'Capture Ideas',
                                text: "Ideas are the bedrock on which duck soup (projects) and experiments are built, but serve little value left undocumented and unactioned. Capture ideas by adding idea cards in Ideation."
                            },
                            {
                                title: 'Rate Ideas',
                                text: "To distinguish the great ideas from the also-rans, rate your ideas with PHIEs (Potential, Happiness, Impact, Ease); focussing resources (time, money, effort) on those with the highest score."
                            },
                            {
                                title: 'Prioritise Ideas',
                                text: "PHIE score calculated, prioritise high-scoring ideas by moving them to Unscheduled. Refrain from deleting ideas that don't make the cut. Refine them, combine them with others and re-rate with PHIEs. "
                            }
                        ]
                    },
                    {
                        name: "unscheduled",
                        fields: [
                            {
                                title: 'Experiment or Duck Soup?',
                                text: "Decide whether an idea represents an experiment or duck soup. Experiments test a hypothesis and are measurable. Duck soup represents all other activities which are not measurable, but must be actioned."
                            },
                            {
                                title: 'Promise & Hypothesise',
                                text: "Describe your experiment, target persona, the want or need you seek to fulfill and your assumptions about the experiment by completing the Promise and Hypothesis blocks on your experiment card. "
                            },
                            {
                                title: 'Define Success Metric',
                                text: "This is the metric that you set out to improve when embarking on an experiment. Success or failure of your experiment will be determined by the degree which your experiment impacted the success metric."
                            },
                            {
                                title: 'Plan and document',
                                text: "Break down experiments or duck soups into tasks, assign them to team members and give them a due date. Write briefs, add notes and attach supporting documentation to help your team get started."
                            }
                        ]
                    },
                    {
                        name: "development",
                        fields: [
                            {
                                title: 'Prioritise your Backlog',
                                text: "Assign experiments or ducksoups earmarked for development to your Upcoming Sprint, or leave them Unassigned for future sprints when you have the resources (time, money, effort) to action them."
                            },
                            {
                                title: 'Build > Test > Approve',
                                text: "Put plans into motion by actioning tasks, subtasks and fixing bugs. To avoid scope creep in experiments, pay attention to your Promise and Hypothesis. Thoroughly test and sign-off cards prior to execution."
                            }
                        ]
                    },
                    {
                        name: "execution",
                        fields: [
                            {
                                title: 'Establish your Baseline and Lift',
                                text: "Draw a line in the sand by establishing your baseline (where you are now, expressed as a figure) and defining the lift (the improvement you expect to see should your experiment be successful)."
                            },
                            {
                                title: 'Push Live, Watch and Wait',
                                text: "Tinkering with live experiment risks invalidating the results and compromising learnings, so sit tight and document observations which could yield important insights for this and future experiments."
                            }
                        ]
                    },
                    {
                        name: "analysis",
                        fields: [
                            {
                                title: 'Document Results and Learnings',
                                text: "Experiments concluded, record and interpret the result. Document experiment outcomes, key learnings, next steps and whether you propose to kill, pivot or persevere with your experiment going forward."
                            },
                            {
                                title: 'Accept or Reject Hypothesis',
                                text: "Experiment results documented, accept or reject your hypothesis, noting where your hypothesis deviated from the experiment outcome, why this occurred and what could be done differently next time around."
                            }
                        ]
                    },
                    {
                        name: "completed",
                        fields: [
                            {
                                title: 'Archive or Decline',
                                text: "The Completed board serves as a record of experiments won and lost, duck soups implemented and cards declined. It serves as a reminder of your growth experimentation journey."
                            }
                        ]
                    },

                ],
            }
        },

        computed: {
            headingFilter() {
                return this.panels.find(panel => panel.name === this.filter)
            }
        },
        methods: {
            next(){
                if(this.headingFilter.fields.length-1 === this.model){
                    this.isVisible = false;
                }else{
                    this.model++
                }
            },

            show() {
                this.model = 0
                this.isVisible = true;
            },

            hide() {
                this.isVisible = false;
            }
        },
    }
</script>

<style scoped lang="less">
    @import (reference) "~@/less/main/style.less";


    .btn-next {
        position: absolute;
        right: 25px;
        bottom: 25px;
        z-index: 10001;
        background: darkgray;
    }

    .btn-skip {
        position: absolute;
        bottom: 25px;
        left: 25px;
        z-index: 10001;
    }

    .theme--dark.v-sheet {
        background-color: transparent;
        border-color: transparent;
        color: #000;
    }

    .coach-panel /deep/ .v-carousel__controls .v-btn.v-btn--icon {
        color: #000000;
    }

    .coach-panel /deep/ .v-image__image {
        width: 276px;
        left: 0;
        right: 0;
        margin: auto;

    }

    .panelText {
        padding: 0 155px;
    }

    .panelTitle {
        padding: 10px 0;
        font-size: 20px;
        font-weight: bold;
    }


</style>