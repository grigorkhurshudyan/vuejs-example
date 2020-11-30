<template>
  <v-container>

    <v-row>

      <v-col cols="12" sm="12">

        <template>
                    <span @click="ChangeHeadingListHide(heading)" class="cursor-pointer">
                        <v-icon right v-if="!heading.collapsed">fas fa-caret-right</v-icon>
                        <v-icon down v-else>fas fa-caret-down</v-icon>
                        <span>  {{ heading.name }} ({{ filterCards.length }} cards)</span>
                    </span>
        </template>
        <template v-if="heading.collapsed">
          <v-container fluid class=" wow-cards-container d-flex flex-wrap">
             <draggable class="draggable"
                       v-model="filterCards"
                       group="order"
                       @change="movement">
                  <btn-card
              v-for="card in filterCards"
              :key="card.id"
              :card="card"
          >
            <template v-slot:phies>
              <p-h-i-e-s-icon
                  :value="card.phies_sum"
              ></p-h-i-e-s-icon>
            </template>
          </btn-card>
                </draggable>
          </v-container>
        </template>

      </v-col>
    </v-row>

  </v-container>
</template>

<script>
import draggable from 'vuedraggable'
import BtnCard from "@/components/Cards/BtnCard";
import BtnAction from "@/components/Cards/BtnAction";
import PHIESIcon from "@/components/Cards/PHIESIcon";
import {mapState, mapActions} from "vuex";
import {BoardType, TimeFrames} from "@/helpers/BoardType";
import moment from "moment";
import _ from 'lodash';

export default {
  name: "Headings",
  props: [
    'heading',
    'index'
  ],
  data() {
    return {
      ideationBoardTabs: [BoardType.CAPTURE, BoardType.RATE],
    }
  },
  components: {
    BtnCard,
    draggable,
    BtnAction,
    PHIESIcon
  },
  computed: {
    ...mapState('folder', ['cards']),
    ...mapState('board', ['board']),


    filterCards() {
      let sortedCards = _.sortBy(this.cards, 'due_date')
      return sortedCards.filter(card => {
        let now = moment(new Date());
        let end = moment(card.due_date);
        let duration = moment.duration(end.diff(now));
        let diffDays = Math.round(duration.asDays())
        let diffWeeks = Math.round(duration.asWeeks())


        if (this.heading.name === TimeFrames.TODAY) {
          if (diffDays === 0 && diffWeeks === 0) {
            return card
          }
        }

          if (this.heading.name === TimeFrames.TOMORROW) {
          if (diffDays === 1 && diffWeeks === 0 ) {
            return card
          }
        }

        if (this.heading.name === TimeFrames.THIS_WEEK) {
          if (diffWeeks === 0 && diffDays >= 2) {
            return card
          }
        }

        if (this.heading.name === TimeFrames.NEXT_WEEK) {
          if (diffWeeks === 1) {
            return card
          }
        }

        if (this.heading.type === 'idea') {
          return card.type !== this.heading.type && this.ideationBoardTabs.includes(card.board)
        } else {
          return card.type === this.heading.type && !this.ideationBoardTabs.includes(card.board)
        }
      })
    }
  },
  methods: {
    ...mapActions('card', ['updateCard']),
    ...mapActions('folder', ['changeHeadingCollapsed']),

    ChangeHeadingListHide(heading) {
      if (heading.name) return heading.collapsed = !heading.collapsed

      this.changeHeadingCollapsed(this.index)
    },

    movement(value) {
      if (value.moved) {
        this.updateCard({
          card_id: value.moved.element.id,
          order: value.moved.newIndex
        })
      }
    },
  },
}
</script>

<style scoped lang="less">
    @import (reference) "~@/less/main/style.less";
    .draggable {
        display: flex;
        width: 100%;
        flex-wrap: wrap;
    }
</style>