<template>
  <v-dialog
      tag="div"
      v-if="card"
      v-model="isVisible"
      width="100%"
      class="wow-card-dialog"
  >
    <div class="custom-card-wrap" @mousedown="handleWrapperClick">
      <v-card
          tile
          class="wow-dialog"
          max-width="800px">
        <div class="breadcrumbs">
          <span class="fs">Card Type: <i>{{ breadcrumbs }}</i></span>
        </div>
        <v-container class="pa-2 d-flex justify-end">
          <span style="margin-right: 10px;" class="fs">Data modified: <i>{{ formatDate(modifiedDate) }}</i></span>
          <v-btn icon x-small @click="closeCard">
            <v-icon class="fs">fa-times</v-icon>
          </v-btn>
        </v-container> <!--close button-->
        <v-container class="py-0">
          <v-container fluid class="py-0 d-flex justify-space-between align-center">
            <v-textarea
                dense
                v-model="title"
                class=" ma-0 bb"
                @change.native="changeTitle"
                :disabled="disabled"
                :row-height="5"
                auto-grow
                hide-details
                maxlength="150"
                :rules="[rules.titleLength]"
                style="padding-top: 2px">
            </v-textarea>
            <div class="icon-container" v-if="false">
              <i class="fa fa-bell bell-icon"></i>
            </div>

          </v-container> <!--top container-->


          <v-container style="height: 85px" class="d-flex justify-content-around align-center ">
            <v-container
                class="flex-grow-0 d-flex align-center  wow-cards-panels-menu ml">
              <CardMenuScope
                  v-if="!isIdeation"
                  :cardScope="scope"
                  :checkIfDisabled="disabled"
              >
                <v-list-item
                    v-for="s in scopes"
                    :key="s.ordinal"
                >
                  <v-radio
                      :value="s.name"
                      :label="s.name"
                      color="#82256c"
                      @click="handleScopeSelect(s.name)"
                  >
                  </v-radio>
                </v-list-item>
              </CardMenuScope>

              <btn-circle-icon
                  :disabled="disabled"
                  ref="btnUrgent"
                  icon-class="wow-rate-icon wow-icon-cream-pie"
                  @click.native="selectPanel($refs.rateIdeaPanel)"
                  v-if="isPanelRateIdeaVisible"
                  title="Rate"
              ></btn-circle-icon>

              <v-divider vertical class="card-actions-divider" v-if="isPanelRateIdeaVisible"></v-divider>

              <btn-circle-icon
                  ref="btnUrgent"
                  :disabled="disabled"
                  icon-class="fas fa-tags"
                  @click.native="selectPanel($refs.propertiesPanel)"
                  v-if="isPanelPropertiesVisible"
                  title="Properties"
              ></btn-circle-icon>
              <complexity-selector v-if="!isIdeation" :disabled="disabled"/>
              <v-divider vertical class="card-actions-divider"></v-divider>

              <attachment-selector :disabled="disabled"/>
              <btn-circle-icon
                  :disabled="disabled"
                  v-if="!isIdeation"
                  ref="btnUrgent"
                  icon-class="fas fa-cube"
                  @click.native="selectPanel($refs.blocksPanel)"
                  title="Blocks"
              ></btn-circle-icon>
              <v-divider vertical class="card-actions-divider"></v-divider>


              <process-selector v-if="!isIdeation" :disabled="disabled"/>
              <p-h-i-e-s-icon
                  v-if="typeof phiesSum === 'number' && board !== ideationBoardTabs[0]"
                  :value="phiesSum"

              ></p-h-i-e-s-icon>
              <v-menu bottom left class="flex-grow-1">
                <template v-slot:activator="{ on }">

                  <div v-on="on" class="mx-1">
                    <btn-actions></btn-actions>
                  </div>
                </template>
                <v-list :class="{'p0' : disabled}">
                  <v-list-item v-if="(getNextBoard || isAnalysis) && !isCompleted">
                    <btn-icon-text-flat @click.native="moveCardForward"
                                        icon-class="fa fa-long-arrow-alt-right">
                      Move to {{ nextBoardCapitalized }}
                    </btn-icon-text-flat>
                  </v-list-item>
                  <v-list-item v-if="getPreviousBoard && !isCompleted">
                    <btn-icon-text-flat @click.native="moveCardBackward"
                                        icon-class="fa fa-long-arrow-alt-left">
                      Move to {{ previousBoardCapitalized }}
                    </btn-icon-text-flat>
                  </v-list-item>
                  <v-list-item v-if="isCompleted">
                    <btn-icon-text-flat @click.native="Restore" icon-class="fa fa-trash-restore">
                      Restore Card
                    </btn-icon-text-flat>
                  </v-list-item>
                  <v-list-item disabled="disabled" :class="{'bgray' : disabled}">
                    <btn-icon-text-flat @click.native="replicate" icon-class="fa fa-clipboard">
                      Copy Card
                    </btn-icon-text-flat>
                  </v-list-item>
                  <v-list-item v-if="canDeleteCard && card.status !== 'declined'">
                    <btn-icon-text-flat @click.native="declineCard" icon-class="fa fa-archive">
                      Decline Card
                    </btn-icon-text-flat>
                  </v-list-item>
                  <v-list-item v-if="canDeleteCard" disabled="disabled" :class="{'bgray' : disabled}">
                    <delete-card-dialog @delete="deleteCard"></delete-card-dialog>
                  </v-list-item>
                </v-list>
              </v-menu>


            </v-container>
            <v-container style="margin-right: 0 !important;padding-right: 0 !important;" v-if="isNotIdeation"
                         class="picker-container ">

              <v-menu
                  :disabled="disabled"
                  v-model="showDateMenu"
                  :close-on-content-click="false"
                  transition="scale-transition"
                  :left="true"
                  offset-y

              >
                <template class="date-slot" v-slot:activator="{ on }">
                  <v-text-field
                      :class="{'red-due-picker' : dateRed(), 'orange-due-picker': dateOrange() }"
                      color="#82256c"
                      class="due-date-picker "
                      v-model="dateFormatted"
                      placeholder="Due date"
                      append-icon="event"
                      readonly
                      filled
                      dense
                      v-on="on"
                  ></v-text-field>
                </template>
                <v-date-picker @input="updateDueDate(dueDate)" color="#82256c" v-model="dueDate"
                               no-title scrollable>
                  <v-btn text color="#82256c" @click="clearDate()">Reset</v-btn>
                </v-date-picker>
              </v-menu>

            </v-container>
          </v-container>
          <v-container style="padding-top: 0;padding-left: 0;">


            <v-container style="padding-top: 0;"
                         class="flex-grow-0 d-flex justify-start wow-cards-panels-menu">
              <btn-oval-icon
                  class="mr"
                  v-if="!isIdeation"
                  :cardType="card.type"
                  iconFirst="fas fa-flask"
                  @onclickFirst="updateCardType(true)"
                  @onclickSecond="updateCardType(false)"
              ></btn-oval-icon>


              <StackedAvatarList
                  :disabled="disabled"
                  class="flex-grow-0"
                  :users="cardMembers"
                  :showRemove="canRemoveUserFromCard"
                  @removeUser="removeUserFromCard"
              >
                <template v-slot:extension="selectedUser">
                  <v-btn
                      :disabled="disabled"
                      v-show="canInviteUserToCard && !isOwner(selectedUser)"
                      small
                      class="inv-user-remove"
                      @click.native="handleAssign(selectedUser)">
                    <btn-circle-icon
                        ref="btnUrgent"
                        icon-class="fas fa-fire"
                        @click.native="toggleUrgent"
                    ></btn-circle-icon>
                    Assign this card
                  </v-btn>
                </template>
              </StackedAvatarList>
              <add-user-dialog v-if="canAddUserToCard && isNotIdeation" class="flex-grow-0 "
                               :disabled="disabled"></add-user-dialog>

            </v-container>

          </v-container>
          <!--panels-->

          <v-container class="panels-selections"
                       v-if="growthExperimentTitle || aaarrrTitle || experimentTitle || traction20Title"
          >
            <v-chip class="selected-panel-chip"
                    :disabled="disabled"
                    :close="true"
                    @click:close="deleteProperty( type='growthExperimentTitle')"
                    color="orange" label v-if="growthExperimentTitle">
              {{ growthExperimentTitle }}
            </v-chip>
            <v-chip :close="true"
                    :disabled="disabled"
                    @click:close="deleteProperty( type='aaarrrTitle')"
                    class="ml-1 selected-panel-chip" color="grey" label v-if="aaarrrTitle">
              {{ aaarrrTitle }}
            </v-chip>
            <v-chip :close="true"
                    :disabled="disabled"
                    @click:close="deleteProperty( type='experimentTitle')"
                    class="ml-1 mr-1 selected-panel-chip" color="grey" label
                    v-if="experimentTitle">
              {{ experimentTitle }}
            </v-chip>
            <v-chip :close="true"
                    :disabled="disabled"
                    @click:close="deleteProperty( type='traction20Title')"
                    class="ml-1 mr-1 selected-panel-chip" color="grey" label v-if="traction20Title">
              {{ traction20Title }}
            </v-chip>
          </v-container>
          <v-container class="d-flex flex-column py-0">
            <properties-panel
                ref="propertiesPanel"
                v-if="isPanelPropertiesVisible"
            ></properties-panel>
            <blocks-panel
                ref="blocksPanel"
                v-if="isPanelBlocksVisible"
            ></blocks-panel>
            <experiment-panel
                ref="experimentPanel"
                v-if="isPanelExperimentVisible"
            ></experiment-panel>
            <rate-idea-panel
                ref="rateIdeaPanel"
                v-if="isPanelRateIdeaVisible"
                :card="card"
            ></rate-idea-panel>
            <bug-report-panel
                ref="bugReportPanel"
                v-if="isPanelReportBugsVisible"
            >
            </bug-report-panel>
            <bulletins-panel
                ref="bulletinsPanel"
                v-if="isPanelBulletinsVisible"
            >
            </bulletins-panel>
          </v-container>


          <v-container v-if="checkActiveBlock('Description')" fluid>
            <v-card
                class="description-container"
                :class="{'duck-soup': card.type !== 'experiment', 'experiment': card.type === 'experiment'}"
            >
              <div class="container-title-desc">
                <popper trigger="hover" :options="{placement: 'top'}" :delay-on-mouse-out="200">
                  <div class="popper">
                    {{ helpPoints.helpPoints.description.text }}
                  </div>
                  <span slot="reference">
                                        <span class="fas fa-align-right"></span>
                                        Description
                                        <span class="fa fa-question-circle"></span>
                                    </span>
                </popper>
              </div>
              <!--                            <popper trigger="hover" :options="{placement: 'top-start'}" :delay-on-mouse-out="200">-->
              <!--                                <div class="popper">-->
              <!--                                    {{helpPoints.helpPoints.description.text}}-->
              <!--                                </div>-->
              <!--                                <span style="display: block;" slot="reference">-->
              <v-textarea
                  v-model="description"
                  :disabled="disabled"
                  @change.native="updateDescription"
                  class="wow-card-textarea bb"
                  row-height="5"
                  placeholder="Enter a description"
                  auto-grow
                  hide-details
                  color="#82256c">
              </v-textarea>
              <!--                                </span>-->
              <!--                            </popper>-->
              <transition name="slide" v-if="!isIdeation && card.type === 'experiment'">
                <div class="collapsible-section" v-if="!descriptionCollapsed">
                  <popper trigger="hover" :options="{placement: 'top-start'}"
                          :delay-on-mouse-out="200">
                    <div class="popper">
                      {{ helpPoints.helpPoints.problem.text }}
                    </div>
                    <span style="display: block;" slot="reference">
                                            <v-textarea
                                                v-model="problem"
                                                :disabled="disabled"
                                                @change.native="updateProblem"
                                                class="wow-card-textarea"
                                                outlined
                                                auto-grow
                                                hide-details
                                                color="#82256c"
                                            >
                                                <template slot="label">
                                                    <v-icon size="20px"
                                                            class="pr-1">fas fa-exclamation-triangle</v-icon>
                                                    Problem
                                                </template>
                                            </v-textarea>
                                        </span>
                  </popper>
                  <popper trigger="hover" :options="{placement: 'top-start'}"
                          :delay-on-mouse-out="200">
                    <div class="popper">
                      {{ helpPoints.helpPoints.solution.text }}
                    </div>
                    <span style="display: block;" slot="reference">
                                             <v-textarea
                                                 v-model="solution"
                                                 :disabled="disabled"
                                                 @change.native="updateSolution"
                                                 class="wow-card-textarea"
                                                 outlined
                                                 auto-grow
                                                 hide-details
                                                 color="#82256c"
                                             >
                                                <template slot="label">
                                                    <v-icon class="pr-2" size="20px">fas fa-lightbulb</v-icon>
                                                    Solution
                                                </template>
                                            </v-textarea>
                                        </span>
                  </popper>
                </div>
              </transition>
            </v-card>
            <button
                v-if="!isIdeation && card.type === 'experiment'"
                class="field-toggle-btn"
                :class="{rotated: !descriptionCollapsed}"
                @click="collapseButtonClick('description')"
            >
              <i class="fas fa-angle-double-down"></i>
            </button>
          </v-container> <!--description-->


          <!--Announcement-->
          <v-container fluid
                       v-if="!isIdeation && checkActiveBlock('Announcement')">
            <announcement-panel :disabled="disabled"></announcement-panel>
          </v-container>
          <!--Announcement-->


          <!--Question-answers-->
          <v-container fluid
                       v-if="!isIdeation  && checkActiveBlock('Questions Answers')">
            <question-answers-panel :disabled="disabled"></question-answers-panel>
          </v-container>
          <!--Question-answers-->

          <v-container fluid v-if="isNotIdeation && checkActiveBlock('Tasks') ">
            <tasks-panel :disabled="disabled"></tasks-panel>
          </v-container>


          <!--Bug report-->
          <v-container fluid
                       v-if="!isIdeation  && checkActiveBlock('Bug Report')">
            <bug-report-lite-panel :disabled="disabled"></bug-report-lite-panel>
          </v-container>
          <!--Bug report-->


          <div v-if="isNotIdeation"> <!--card type switcher-->

            <div v-if="cardType">


              <v-container v-if="checkActiveBlock('Promise')" fluid>
                <v-card class="pa-3 main-container">
                  <div>
                    <popper trigger="hover" :options="{placement: 'top'}" :delay-on-mouse-out="200">
                      <div class="popper">
                        {{ helpPoints.helpPoints.promise.text }}
                      </div>
                      <span slot="reference">
                      <v-icon class="pr-1" size="20px">fa-handshake</v-icon>
                      Promise
                        <span class="fa fa-question-circle"></span>
                    </span>
                    </popper>
                    <span class="fa fa-times-circle float-right cursor-pointer" @click="closePromise"></span>
                  </div>

                  <div v-if="fieldFormatP && !isFreeTextP">
                    <v-textarea
                        rows="2"
                        auto-grow
                        label="This is..."
                        v-model="userStoryRoleFields.field1"
                        @change.native="updateUserStoryRoleFields"
                        :disabled="disabled"
                    >
                    </v-textarea>

                    <v-textarea
                        rows="2"
                        auto-grow
                        label="As a..."
                        v-model="userStoryRoleFields.field2"
                        @change.native="updateUserStoryRoleFields"
                        :disabled="disabled"
                    >
                    </v-textarea>

                    <v-textarea
                        rows="2"
                        auto-grow
                        label="I want to..."
                        v-model="userStoryRoleFields.field3"
                        @change.native="updateUserStoryRoleFields"
                        :disabled="disabled"
                    >
                    </v-textarea>

                    <v-textarea
                        rows="2"
                        auto-grow
                        label="So that I..."
                        v-model="userStoryRoleFields.field4"
                        @change.native="updateUserStoryRoleFields"
                        :disabled="disabled"
                    >
                    </v-textarea>
                    <v-textarea
                        rows="2"
                        auto-grow
                        label="To solve this I will..."
                        v-model="userStoryRoleFields.field5"
                        @change.native="updateUserStoryRoleFields"
                        :disabled="disabled"
                    >
                    </v-textarea>
                    <v-textarea
                        rows="2"
                        auto-grow
                        label="The persona I will target is..."
                        v-model="userStoryRoleFields.field6"
                        @change.native="updateUserStoryRoleFields"
                        :disabled="disabled"
                    ></v-textarea>
                  </div>
                  <v-textarea
                      rows="2"
                      auto-grow
                      label="Free text"
                      v-model="userStoryIntention"
                      @change.native="updateUserStoryIntention"
                      :disabled="disabled"
                      v-if="isFreeTextP"
                  >
                  </v-textarea>
                  <div v-if="promiseFormatField === 'Story'" class="story">
                    <v-list v-for="(v, i) in fields" :key="i" class="mt-4">
                      <v-list-item>
                        {{ v }}
                      </v-list-item>
                    </v-list>
                  </div>

                  <v-select
                      :disabled="disabled"
                      label="The pirate funnel I will focus on is..."
                      :items="promiseDropList"
                      v-model="userStoryRoleFields.select"
                      @change="updateUserStoryRoleFields"
                      outlined
                      class="selectField"
                  >
                  </v-select>
                  <div class="text-right" style="width: 98%;">
                    <v-btn x-small outlined color="grey" title="Story"
                           @click="changeFormatField('promiseFormatField','Story')" :disabled="disabled"><span
                        class="fas fa-align-left"></span></v-btn>
                    <v-btn x-small color="grey" title="Field" @click="changeFormatField('promiseFormatField','Field')"
                           :disabled="disabled"><span class="fas fa-align-center cw"></span></v-btn>
                    <v-btn x-small color="grey" title="Free text"
                           @click="changeFormatField('promiseFormatField','Free text')" :disabled="disabled"><span
                        class="fa fa-text-height cw"></span></v-btn>
                  </div>

                </v-card>
              </v-container> <!-- User Story -->


              <v-container v-if="checkActiveBlock('Hypothesis')" fluid>
                <v-card class="pa-3 main-container">
                  <div>
                    <popper trigger="hover" :options="{placement: 'top'}" :delay-on-mouse-out="200">
                      <div class="popper">
                        {{ helpPoints.helpPoints.hypothesis.text }}
                        <a target="_blank" :href="helpPoints.helpPoints.hypothesis.link"><br/>Learn
                          More >>></a>
                      </div>
                      <span slot="reference">
                                                <v-icon class="pr-1" size="20px">fas fa-file-contract</v-icon>
                                                Hypothesis
                                            </span>
                    </popper>
                  </div>
                  <v-textarea
                      rows="2"
                      label="We believe that..."
                      v-model="hypothesisBelieve"
                      @change.native="updateHypothesisBelieve"
                      :disabled="disabled"
                  >
                  </v-textarea>
                  <v-textarea
                      rows="2"
                      label="To verify that..."
                      v-model="hypothesisVerify"
                      @change.native="updateHypothesisVerify"
                      :disabled="disabled"
                  >
                  </v-textarea>
                  <v-textarea
                      rows="2"
                      label="And measure..."
                      v-model="hypothesisMeasure"
                      @change.native="updateHypothesisMeasure"
                      :disabled="disabled"
                  >
                  </v-textarea>

                  <transition name="slide">
                    <div class="collapsible-section" v-if="!hypothesisCollapsed">
                      <v-textarea
                          rows="2"
                          label="We are right if..."
                          v-model="hypothesisRight"
                          @change.native="updateHypothesisRight"
                          :disabled="disabled"
                      >
                      </v-textarea>
                      <v-textarea
                          rows="2"
                          label="We expect that..."
                          v-model="hypothesisExpect"
                          @change.native="updateHypothesisExpect"
                          :disabled="disabled"
                      >
                      </v-textarea>
                    </div>
                  </transition>

                </v-card>

                <button
                    class="field-toggle-btn"
                    :class="{rotated: !hypothesisCollapsed}"
                    @click="collapseButtonClick('hypothesis')"
                >
                  <i class="fas fa-angle-double-down"></i>
                </button>

              </v-container> <!-- Hypothesis -->


              <v-container v-if="checkActiveBlock('Success Metric')" fluid> <!--Success Metric -->
                <v-card class="pa-3 main-container">
                  <div>
                    <popper trigger="hover" :options="{placement    : 'top'}"
                            :delay-on-mouse-out="200">
                      <div class="popper">
                        {{ helpPoints.helpPoints.metric.text }}
                        <a target="_blank" :href="helpPoints.helpPoints.metric.link"><br/>Learn
                          More
                          >>></a>
                      </div>
                      <span slot="reference">
                                                <v-icon class="pr-1" size="20px">fas fa-star</v-icon>
                                                Success Metric
                        <span class="fa fa-question-circle"></span>
                                            </span><!--"Success Metric" in Freemium; "Metric that Matters" in others-->
                    </popper>
                    <span class="fa fa-times-circle float-right cursor-pointer" @click="closeSuccessMetric"></span>
                  </div>
                  <v-container @click="collapseButtonClick('metrics')">
                    <v-container
                        v-if="(metricsMode === BlockModeTypes.FIELD || metricsMode === BlockModeTypes.STORY) && metricsCollapsed">
                      <p>The success metric we will use to measure our experiment is <span
                          class="default-description-text"> {{ defaultText('field', mattersMetricObjective.field1) }}</span>
                        . The traction channel we will be focussing on is <span class="default-description-text"> {{
                          defaultText('field', mattersMetricObjective.field2)
                        }}.</span>
                      </p>
                      <p>
                        The baseline metric is <span class="default-description-text pl-1"> {{
                            defaultText('field', mattersMetricObjective.field3)
                          }},</span>
                        recorded between <span class="default-description-text pl-1">{{
                            defaultText('date', mattersMetricObjective.dueDate1)
                          }}</span>
                        and <span
                            class="default-description-text pl-1">{{ defaultText('date', mattersMetricObjective.dueDate2) }} </span>
                      </p>
                      <p>Our target value is <span
                          class="default-description-text pl-1">{{ defaultText('list', mattersMetricObjective.select1) }}. </span>
                         The experiment will begin on <span
                            class="default-description-text pl-1">{{
                            defaultText('date', mattersMetricObjective.dueDate3)
                          }} </span>
                        and end on <span class="default-description-text pl-1">{{defaultText('date', mattersMetricObjective.dueDate4)}} </span> </p>
                      <p>We achieved a value of <span
                          class="default-description-text pl-1">{{ defaultText('list', mattersMetricObjective.select2) }}. </span>
                      </p>
                      <v-row class="mr-0 text-center">
                          <v-col class="colborder">
                            <p>Baseline</p>
                            <p>0</p>
                            <p>Success Metric</p>
                          </v-col>
                          <v-col class="colborder" style="margin: 0 25px">
                            <p>Target</p>
                            <p>0</p>
                            <p>Success Metric</p>
                          </v-col>
                          <v-col class="colborder">
                            <p>Result</p>
                            <p>0</p>
                            <p>Success Metric</p>
                          </v-col>
                        </v-row>
                    </v-container>
                    <v-container v-if="metricsMode === BlockModeTypes.FREE_TEXT && metricsCollapsed">
                      <p>{{ metricMeasure }}</p>
                    </v-container>
                  </v-container>
                  <div v-if="metricsCollapsed" class="d-flex justify-end ">
                    <v-btn-toggle
                        class="align-self-end mr-5"
                        v-model="metricsMode"
                        mandatory
                        dense
                        borderless
                    >
                      <v-btn @click="metricsCollapsed = true" :value="BlockModeTypes.STORY" :disabled="disabled">
                        <v-icon>fas fa-align-left</v-icon>
                      </v-btn>
                      <v-btn :value="BlockModeTypes.FIELD" @click="metricsCollapsed = false" :disabled="disabled">
                        <v-icon>fas fa-align-justify</v-icon>
                      </v-btn>
                      <v-btn :value="BlockModeTypes.FREE_TEXT" :disabled="disabled">
                        <v-icon>fas fa-font</v-icon>
                      </v-btn>
                    </v-btn-toggle>
                  </div>

                  <transition name="slide">
                    <div class="collapsible-section cursor-pointer pb-0 pt-0" v-if="!metricsCollapsed">

                      <v-container class="d-flex flex-column" v-if="metricsMode === BlockModeTypes.FREE_TEXT">
                        <v-textarea
                                rows="5"
                                class="anncm"
                                auto-grow
                                label="Your free text"
                                v-model="metricMeasure"
                                @change.native="updateMetricMeasure"
                                :rules="[rules.cardBlockFreeTextFieldLength]"
                                :maxLength="BLOCK_FREE_TEXT_FIELD_LENGTH_MAX"
                                :disabled="disabled"
                        >
                        </v-textarea>
                        <div class="d-flex justify-end">
                          <v-btn-toggle
                              v-model="metricsMode"
                              mandatory

                              borderless
                          >
                            <v-btn @click="metricsCollapsed = true" :value="BlockModeTypes.STORY">
                              <v-icon>fas fa-align-left</v-icon>
                            </v-btn>
                            <v-btn @click="metricsCollapsed = false" :value="BlockModeTypes.FIELD">
                              <v-icon>fas fa-align-justify</v-icon>
                            </v-btn>
                            <v-btn :value="BlockModeTypes.FREE_TEXT">
                              <v-icon>fas fa-font</v-icon>
                            </v-btn>
                          </v-btn-toggle>
                        </div>
                      </v-container>

                      <v-container class="pt-0 pb-0 "
                                   v-if="metricsMode === BlockModeTypes.FIELD || metricsMode === BlockModeTypes.STORY">
                        <v-row>
                          <v-col cols="6">
                            <v-textarea
                                    auto-grow
                                    rows="1"
                                    flat
                                    label="The success metric is..."
                                    v-model="mattersMetricObjective.field1"
                                    @change.native="updateMattersMetricObjective"
                                    :disabled="disabled"
                            ></v-textarea>
                          </v-col>
                          <v-col cols="6">
                            <v-textarea
                                    auto-grow
                                    rows="1"
                                    flat
                                    label="The traction channel is..."
                                    v-model="mattersMetricObjective.field2"
                                    @change.native="updateMattersMetricObjective"
                                    :disabled="disabled"
                            ></v-textarea>
                          </v-col>
                        </v-row>
                        <v-row class="mr-0">
                          <v-col cols="4">
                            <v-textarea
                                    auto-grow
                                    rows="1"
                                    flat
                                    label="The baseline value is..."
                                    v-model="mattersMetricObjective.field3"
                                    @change.native="updateMattersMetricObjective"
                                    :disabled="disabled"
                            ></v-textarea>
                          </v-col>
                          <v-col cols="4">
                            <v-menu
                                    :disabled="disabled"
                                    :close-on-content-click="false"
                                    transition="scale-transition"
                                    :left="true"
                                    offset-y

                            >
                              <template class="date-slot" v-slot:activator="{ on }">
                                <v-text-field
                                        color="#82256c"
                                        class="due-date-picker bl"
                                        v-model="mattersMetricObjective.dueDate1"
                                        placeholder="It was recorded between..."
                                        append-icon="event"
                                        readonly
                                        filled
                                        dense
                                        v-on="on"
                                ></v-text-field>
                              </template>
                              <v-date-picker @input="updateMattersMetricObjective" color="#82256c"
                                             v-model="mattersMetricObjective.dueDate1"
                                             no-title scrollable>
                                <v-btn text color="#82256c" @click="clearDate()">Reset</v-btn>
                              </v-date-picker>
                            </v-menu>
                          </v-col>
                          <v-col cols="4">
                            <v-menu
                                    :disabled="disabled"
                                    :close-on-content-click="false"
                                    transition="scale-transition"
                                    :left="true"
                                    offset-y

                            >
                              <template class="date-slot" v-slot:activator="{ on }">
                                <v-text-field
                                        color="#82256c"
                                        class="due-date-picker bl"
                                        v-model="mattersMetricObjective.dueDate2"
                                        placeholder="And..."
                                        append-icon="event"
                                        readonly
                                        filled
                                        dense
                                        v-on="on"
                                ></v-text-field>
                              </template>
                              <v-date-picker @input="updateMattersMetricObjective" color="#82256c"
                                             v-model="mattersMetricObjective.dueDate2"
                                             no-title scrollable>
                                <v-btn text color="#82256c" @click="clearDate()">Reset</v-btn>
                              </v-date-picker>
                            </v-menu>
                          </v-col>
                        </v-row>
                        <v-row class="mr-0">
                          <v-col>
                            <v-select
                                    class="pm"
                                    label="The target value is..."
                                    :items="sucMetDromList1"
                                    v-model="mattersMetricObjective.select1"
                                    @change="updateMattersMetricObjective"
                                    :disabled="disabled"
                                    outlined
                            >
                            </v-select>
                          </v-col>
                          <v-col>
                            <v-menu
                                    :disabled="disabled"
                                    :close-on-content-click="false"
                                    transition="scale-transition"
                                    :left="true"
                                    offset-y

                            >
                              <template class="date-slot" v-slot:activator="{ on }">
                                <v-text-field
                                        color="#82256c"
                                        class="due-date-picker bl"
                                        v-model="mattersMetricObjective.dueDate3"
                                        placeholder="The experiment begins on..."
                                        append-icon="event"
                                        readonly
                                        filled
                                        dense
                                        v-on="on"
                                ></v-text-field>
                              </template>
                              <v-date-picker @input="updateMattersMetricObjective" color="#82256c"
                                             v-model="mattersMetricObjective.dueDate3"
                                             no-title scrollable>
                                <v-btn text color="#82256c" @click="clearDate()">Reset</v-btn>
                              </v-date-picker>
                            </v-menu>
                          </v-col>
                          <v-col>
                            <v-menu
                                    :disabled="disabled"
                                    :close-on-content-click="false"
                                    transition="scale-transition"
                                    :left="true"
                                    offset-y

                            >
                              <template class="date-slot" v-slot:activator="{ on }">
                                <v-text-field
                                        color="#82256c"
                                        class="due-date-picker bl"
                                        v-model="mattersMetricObjective.dueDate4"
                                        placeholder="The experiment ends on..."
                                        append-icon="event"
                                        readonly
                                        filled
                                        dense
                                        v-on="on"
                                ></v-text-field>
                              </template>
                              <v-date-picker @input="updateMattersMetricObjective" color="#82256c"
                                             v-model="mattersMetricObjective.dueDate4"
                                             no-title scrollable>
                                <v-btn text color="#82256c" @click="clearDate()">Reset</v-btn>
                              </v-date-picker>
                            </v-menu>
                          </v-col>
                        </v-row>
                        <v-row class="mr-0">
                          <v-col cols="4">
                            <v-select
                                    label="The achieved value was..."
                                    :items="sucMetDromList2"
                                    v-model="mattersMetricObjective.select2"
                                    @change="updateMattersMetricObjective"
                                    :disabled="disabled"
                                    outlined
                                    class="pm"
                            >
                            </v-select>
                          </v-col>
                        </v-row>
                        <div class=" text-right ">
                          <v-btn-toggle
                                  class=" mb-3"
                                  v-model="metricsMode"
                                  mandatory
                                  dense
                                  borderless
                          >
                            <v-btn @click="metricsCollapsed = true" :value="BlockModeTypes.STORY">
                              <v-icon>fas fa-align-left</v-icon>
                            </v-btn>
                            <v-btn @click="metricsCollapsed = false" :value="BlockModeTypes.FIELD">
                              <v-icon>fas fa-align-justify</v-icon>
                            </v-btn>
                            <v-btn :value="BlockModeTypes.FREE_TEXT">
                              <v-icon>fas fa-font</v-icon>
                            </v-btn>
                          </v-btn-toggle>
                        </div>
                      </v-container>
                    </div>
                  </transition>

<!--                  <transition name="slide">-->
<!--                    <div class="collapsible-section" v-if="!metricsCollapsed">-->
<!--                      <v-select-->
<!--                          label="We will measure..."-->
<!--                          :items="metricMeasuresItems"-->
<!--                          v-model="metricMeasure"-->
<!--                          @change="updateMetricMeasure"-->
<!--                          :disabled="disabled"/>-->

<!--                      <v-row>-->
<!--                        <v-col>-->
<!--                          <v-textarea-->
<!--                              rows="1"-->
<!--                              flat-->
<!--                              label="Baseline"-->
<!--                              v-model="mattersMetricBaseline"-->
<!--                              @change.native="updateMattersMetricBaseline"-->
<!--                              :disabled="disabled"/>-->
<!--                          <v-menu-->
<!--                              :disabled="disabled"-->
<!--                              ref="dateMenu"-->
<!--                              v-model="mattersMetricStartDate"-->
<!--                              :close-on-content-click="false"-->
<!--                              :return-value.sync="mattersMetricStartDate"-->
<!--                              transition="scale-transition"-->
<!--                              offset-y-->
<!--                              min-width="290px"-->
<!--                          >-->
<!--                            <template v-slot:activator="{ on }">-->
<!--                              <v-text-field-->
<!--                                  :disabled="disabled"-->
<!--                                  color="#82256c"-->
<!--                                  v-model="mattersMetricStartDate"-->
<!--                                  @change.native="updateMattersMetricStartDate"-->
<!--                                  label="Start date"-->
<!--                                  prepend-icon="event"-->
<!--                                  readonly-->
<!--                                  v-on="on"-->
<!--                              ></v-text-field>-->
<!--                            </template>-->
<!--                            <v-date-picker color="#82256c" v-model="mattersMetricStartDate"-->
<!--                                           no-title-->
<!--                                           scrollable>-->
<!--                              <v-spacer></v-spacer>-->
<!--                              <v-btn text color="#82256c" @click="showDateMenu = false">-->
<!--                                Cancel-->
<!--                              </v-btn>-->
<!--                              <v-btn text color="#82256c"-->
<!--                                     @click="$refs.dateMenu.save(mattersMetricStartDate)">-->
<!--                                OK-->
<!--                              </v-btn>-->
<!--                            </v-date-picker>-->
<!--                          </v-menu>-->
<!--                        </v-col>-->

<!--                        <v-col>-->
<!--                          <v-textarea-->
<!--                              rows="1"-->
<!--                              flat-->
<!--                              label="Expected"-->
<!--                              v-model="mattersMetricExpected"-->
<!--                              @change.native="updateMattersMetricExpected"-->
<!--                              :disabled="disabled"-->
<!--                          ></v-textarea>-->
<!--                          <v-menu-->
<!--                              ref="dateMenu"-->
<!--                              :disabled="disabled"-->
<!--                              v-model="mattersMetricEndDate"-->
<!--                              :close-on-content-click="false"-->
<!--                              :return-value.sync="mattersMetricEndDate"-->
<!--                              transition="scale-transition"-->
<!--                              offset-y-->
<!--                              min-width="290px"-->
<!--                          >-->
<!--                            <template v-slot:activator="{ on }">-->
<!--                              <v-text-field-->
<!--                                  color="#82256c"-->
<!--                                  :disabled="disabled"-->
<!--                                  v-model="mattersMetricEndDate"-->
<!--                                  @change.native="updateMattersMetricEndDate"-->
<!--                                  label="End date"-->
<!--                                  prepend-icon="event"-->
<!--                                  readonly-->
<!--                                  v-on="on"-->
<!--                              ></v-text-field>-->
<!--                            </template>-->
<!--                            <v-date-picker color="#82256c" v-model="mattersMetricEndDate"-->
<!--                                           no-title-->
<!--                                           scrollable>-->
<!--                              <v-spacer></v-spacer>-->
<!--                              <v-btn text color="#82256c" @click="showDateMenu = false">-->
<!--                                Cancel-->
<!--                              </v-btn>-->
<!--                              <v-btn text color="#82256c"-->
<!--                                     @click="$refs.dateMenu.save(mattersMetricEndDate)">OK-->
<!--                              </v-btn>-->
<!--                            </v-date-picker>-->
<!--                          </v-menu>-->
<!--                        </v-col>-->
<!--                      </v-row>-->

<!--                      <v-col v-if="canShowMattersMetricAnalysisFields">-->
<!--                        <v-row>-->
<!--                          <v-textarea-->
<!--                              v-model="mattersMetricAchieved"-->
<!--                              label="Achieved"-->
<!--                              flat-->
<!--                              :rows="1"-->
<!--                              :disabled="disabled"-->
<!--                              @change.native="updateMattersMetricAchieved"/>-->

<!--                          <v-select-->
<!--                              label="What was the result?"-->
<!--                              :items="metricOutcome"-->
<!--                              class="text-capitalize"-->
<!--                              v-model="mattersMetricOutcome"-->
<!--                              @change="updateMattersMetricOutcome"-->
<!--                              :disabled="disabled"/>-->
<!--                        </v-row>-->

<!--                        <v-row>-->
<!--                          <v-textarea-->
<!--                              v-model="mattersMetricObservations"-->
<!--                              label="What did we learn?"-->
<!--                              :rows="1"-->
<!--                              flat-->
<!--                              :disabled="disabled"-->
<!--                              @change.native="updateMattersMetricObservations"/>-->
<!--                        </v-row>-->
<!--                      </v-col>-->
<!--                    </div>-->
<!--                  </transition>-->
                </v-card>
<!--                <button-->
<!--                    class="field-toggle-btn"-->
<!--                    :class="{rotated: !metricsCollapsed}"-->
<!--                    @click="collapseButtonClick('metrics')">-->
<!--                  <i class="fas fa-angle-double-down"/>-->
<!--                </button>-->
              </v-container> <!-- Metric that Matters -->

              <v-container v-if="checkActiveBlock('Result')" fluid>
                <v-card class="pa-3 main-container">
                  <div>
                    <popper trigger="hover" :options="{placement: 'top'}" :delay-on-mouse-out="200">
                      <div class="popper" @click="collapseButtonClick('results')">
                        {{ helpPoints.helpPoints.results.text }}
                      </div>
                      <span slot="reference">
                                                <v-icon class="pr-1" size="20px">fas fa-file-contract</v-icon>
                                                Results
                         <v-icon class="pl-1" size="15px">fas fa-question-circle</v-icon>
                                            </span>
                    </popper>
                    <span class="fa fa-times-circle float-right cursor-pointer" @click="closeResults"></span>
                  </div>
                  <v-container @click="collapseButtonClick('results')">
                    <v-container
                        v-if="(resultMode === BlockModeTypes.FIELD || resultMode === BlockModeTypes.STORY) && resultsCollapsed">
                      <p class="mb-4">Our experiment resulted in <span
                          class="default-description-text"> {{ defaultText('list', resultResulted) }}</span></p>

                      <p>Our findings were <span class="default-description-text"> {{
                          defaultText('field', resultFindings)
                        }}</span>
                        and we learned <span class="default-description-text pl-1"> {{
                            defaultText('field', resultLearned)
                          }}.</span>
                        From this we concluded that we <span class="default-description-text pl-1">{{
                            defaultText('field', resultsConcluded)
                          }}.</span>
                        Consequently, we propose to <span
                            class="default-description-text pl-1">{{ defaultText('list', resultsPropose) }} </span> our
                        experiment.
                      </p>
                      <p>This experiment contributes <span
                          class="default-description-text pl-1">{{ defaultText('field', resultsContribution) }} </span>
                        and
                        therefore we should <span
                            class="default-description-text pl-1">{{
                            defaultText('field', resultsImplications)
                          }} </span>
                        this aspect of our growth strategy. </p>
                    </v-container>
                    <v-container v-if="resultMode === BlockModeTypes.FREE_TEXT && resultsCollapsed">
                      <p>{{ resultFreeText }}</p>
                    </v-container>
                  </v-container>

                  <div v-if="resultsCollapsed" class="d-flex justify-end ">
                    <v-btn-toggle
                        class="align-self-end mr-5"
                        v-model="resultMode"
                        mandatory
                        dense
                        borderless
                    >
                      <v-btn @click="resultsCollapsed = true" :value="BlockModeTypes.STORY" :disabled="disabled">
                        <v-icon>fas fa-align-left</v-icon>
                      </v-btn>
                      <v-btn :value="BlockModeTypes.FIELD" :disabled="disabled">
                        <v-icon>fas fa-align-justify</v-icon>
                      </v-btn>
                      <v-btn :value="BlockModeTypes.FREE_TEXT" :disabled="disabled">
                        <v-icon>fas fa-font</v-icon>
                      </v-btn>
                    </v-btn-toggle>
                  </div>

                  <transition name="slide">
                    <div class="collapsible-section cursor-pointer pb-0 pt-0" v-if="!resultsCollapsed">

                      <v-container class="d-flex flex-column" v-if="resultMode === BlockModeTypes.FREE_TEXT">

                        <v-textarea
                            rows="5"
                            class="anncm"
                            label="Your free text"
                            v-model="resultFreeText"
                            @change.native="updateResulFreeText"
                            :maxLength="BLOCK_FREE_TEXT_FIELD_LENGTH_MAX"
                            :rules="[rules.cardBlockFreeTextFieldLength]"
                            auto-grow
                        >
                        </v-textarea>
                        <div class="d-flex justify-end">
                          <v-btn-toggle
                              v-model="resultMode"
                              mandatory

                              borderless
                          >
                            <v-btn @click="resultsCollapsed = true" :value="BlockModeTypes.STORY">
                              <v-icon>fas fa-align-left</v-icon>
                            </v-btn>
                            <v-btn @click="resultsCollapsed = false" :value="BlockModeTypes.FIELD">
                              <v-icon>fas fa-align-justify</v-icon>
                            </v-btn>
                            <v-btn :value="BlockModeTypes.FREE_TEXT">
                              <v-icon>fas fa-font</v-icon>
                            </v-btn>
                          </v-btn-toggle>
                        </div>
                      </v-container>

                      <v-container class="pt-0 pb-0 "
                                   v-if="resultMode === BlockModeTypes.FIELD || resultMode === BlockModeTypes.STORY">
                        <div class="d-flex justify-space-between align-center">
                          <v-select
                              label="Our experiment resulted in ..."
                              placeholder="experiment resulted in ..."
                              :items="resultSelectResulted"
                              v-model="resultResulted"
                              outlined
                              class="selectField ml-0 "
                              @change="updateResultResulted"
                          >
                          </v-select>
                          <div class=" float-right ">
                            <v-btn-toggle
                                class=" mb-3"
                                v-model="resultMode"
                                mandatory
                                dense
                                borderless
                            >
                              <v-btn @click="resultsCollapsed = true" :value="BlockModeTypes.STORY">
                                <v-icon>fas fa-align-left</v-icon>
                              </v-btn>
                              <v-btn @click="resultsCollapsed = false" :value="BlockModeTypes.FIELD">
                                <v-icon>fas fa-align-justify</v-icon>
                              </v-btn>
                              <v-btn :value="BlockModeTypes.FREE_TEXT">
                                <v-icon>fas fa-font</v-icon>
                              </v-btn>
                            </v-btn-toggle>
                          </div>
                        </div>


<!--                        <v-text-field-->
<!--                            label="Our findings were..."-->
<!--                            v-model="resultFindings"-->
<!--                            :maxLength="BLOCK_FIELD_LENGTH_MAX"-->
<!--                            :rules="[rules.cardBlockFieldLength]"-->
<!--                            @change.native="updateResultFindings"-->
<!--                            placeholder="Our findings"-->
<!--                            outlined-->

<!--                        ></v-text-field>-->

                           <v-textarea
                               class=" mt-0 pt-0 anncm"
                            rows="1"
                            auto-grow
                             label="Our findings were..."
                            v-model="resultFindings"
                            :maxLength="BLOCK_FIELD_LENGTH_MAX"
                            :rules="[rules.cardBlockFieldLength]"
                            @change.native="updateResultFindings"
                            placeholder="Our findings"
                        ></v-textarea>





                        <v-textarea
                            class="pt-0 mt-0 anncm"
                            style="margin-left: 0; padding-left: 0;"
                            rows="1"
                            auto-grow
                            label="We learned..."
                            placeholder="learned"
                            v-model="resultLearned"
                            :maxLength="BLOCK_FIELD_LENGTH_MAX"
                            @change.native="updateResultLearned"
                            :rules="[rules.cardBlockFieldLength]"
                        ></v-textarea>


                        <v-textarea
                            class="area pt-0 mt-0 anncm"
                            rows="1"
                            auto-grow
                            label="We concluded that..."
                            placeholder="concluded"
                            v-model="resultsConcluded"
                            :maxLength="BLOCK_FIELD_LENGTH_MAX"
                            @change.native="updateResultConcluded"
                            :rules="[rules.cardBlockFieldLength]"
                        ></v-textarea>


                        <v-select
                            label="We propose to..."
                            placeholder="propose"
                            :items="resultSelectResulted"
                            v-model="resultsPropose"
                            outlined
                            class="selectField pt-0 mt-0   ml-0"
                            @change.native="updateResultPropose"
                        >
                        </v-select>
                        <div class="d-flex">
                          <v-select
                              class="pt-0 pb-0 mr-3"
                              rows="1"
                              :items="resultSelectResulted"
                              outlined
                              label="Contribution to growth strategy..."
                              placeholder="Contribution..."
                              v-model="resultsContribution"
                              @change.native="updateResultContributions"
                          >
                          </v-select>

                          <v-select
                              rows="1"
                              :items="resultSelectResulted"
                              outlined
                              placeholder="Implications..."
                              label="Implications for growth strategy..."
                              v-model="resultsImplications"
                              @change.native="updateResultImplications"
                          >
                          </v-select>
                        </div>
                      </v-container>
                    </div>
                  </transition>

                </v-card>

              </v-container> <!-- Results -->


            </div>


          </div> <!--duck soup/experiment-->
          <v-container v-if="checkActiveBlock('Attachments')" fluid>
            <attachments-container :disabled="disabled"></attachments-container>
          </v-container> <!--attachments-->
        </v-container>
        <spinner
            :showing="getLoadingCard">
        </spinner>
      </v-card>
    </div>
  </v-dialog>
</template>

<script>
import Spinner from "@/components/Spinner";
import BtnActions from "@/components/BtnActions";
import BtnIconTextFlat from "@/components/BtnIconTextFlat";
import PHIESIcon from "@/components/Cards/PHIESIcon";
import DeleteCardDialog from "@/components/CardsActionsBar/DeleteCardDialog";
import AttachmentsContainer from "@/components/Cards/AttachmentsContainer";
import PropertiesPanel from "@/components/Cards/Panels/PropertiesPanel";
import PromisePanel from "@/components/Cards/Panels/PromisePanel/PromisePanel";
import AnnouncementPanel from "@/components/Cards/Panels/AnnouncementPanel/AnnouncementPanel";
import ExperimentPanel from "@/components/Cards/Panels/ExperimentPanel";
import RateIdeaPanel from "@/components/Cards/Panels/RateIdeaPanel";
import BugReportPanel from "@/components/Cards/Panels/BugReportPanel";
import BtnBorderDashed from "@/components/BtnBorderDashed";
import BtnCircleIcon from "@/components/BtnCircleIcon";
import StackedAvatarList from "@/components/StackedAvatarList";
import AddUserDialog from "@/components/Cards/AddUserDialog";
import ComplexitySelector from "@/components/Cards/ComplexitySelector";
import BulletinsPanel from "@/components/Cards/Panels/BulletinsPanel/BulletinsPanel";
import CardMenuScope from "@/components/Cards/CardMenuScope";
import DatePicker from "@/components/DatePicker";
import {Experiments} from "@/helpers/experimentHelper";
import CardDataTypes from "@/helpers/CardDataTypes";
import HelpPoints from "@/helpers/helpPoints";
import {updateRouterParams, userReadable} from "@/helpers/utils";
import {AAARRR, EXPERIMENT, MetricMeasuresItems, TRACTION20} from "@/helpers/ideaMetricsHelper";
import _ from 'lodash';
import Popper from 'vue-popperjs';
import {mapActions, mapGetters, mapState} from "vuex";
import {sortCardMembers} from "@/helpers/utils";
import {BoardType} from "@/helpers/BoardType";
import {FilterType} from "@/helpers/FilterType";
import TasksPanel from "./Cards/Panels/TasksPanel/TasksPanel";
import {ROOT_FOLDER} from "@/helpers/constants";
import moment from "moment";
import permissionsMixin from "@/mixins/permissionsMixin";
import {OutcomeItems} from "@/helpers/ideaMetricsHelper";
import {CardType} from "@/helpers/CardDataTypes";
import AttachmentSelector from "./Cards/AttachmentSelector";
import ProcessSelector from "./Cards/ProcessSelector";
import AddCardsDialog from "./Cards/AddCardsDialog";
import BtnOvalIcon from "./BtnOvalIcon";
import QuestionAnswersPanel from "./Cards/Panels/QuestionAnswerPanel/QuestionAnswersPanel";
import BugReportLitePanel from "./Cards/Panels/BugReportLitePanel/BugReportLitePanel";
import BlocksPanel from "./Cards/Panels/BlocksPanel";
import {
  BLOCK_FIELD_LENGTH_MAX,
  BLOCK_FREE_TEXT_FIELD_LENGTH_MAX,
  cardBlockFieldLength, cardBlockFreeTextFieldLength,
  titleLength
} from "@/helpers/validations";
import {BlockModeTypes} from "@/helpers/CardDataTypes";

export default {
  name: "CardDialog",

  components: {
    BlocksPanel,
    QuestionAnswersPanel,
    AddCardsDialog,
    ProcessSelector,
    AttachmentSelector,
    TasksPanel,
    BulletinsPanel,
    ComplexitySelector,
    AddUserDialog,
    StackedAvatarList,
    BtnCircleIcon,
    BtnBorderDashed,
    BugReportPanel,
    RateIdeaPanel,
    ExperimentPanel,
    PropertiesPanel,
    AttachmentsContainer,
    DeleteCardDialog,
    PHIESIcon,
    Spinner,
    BtnActions,
    BtnIconTextFlat,
    popper: Popper,
    CardMenuScope,
    DatePicker,
    BtnOvalIcon,
    PromisePanel,
    AnnouncementPanel,
    BugReportLitePanel

  },

  mixins: [permissionsMixin],

  data() {
    return {
      ideationBoardTabs: [BoardType.CAPTURE, BoardType.RATE],
      isVisible: true,
      title: '',
      description: '',
      showActionMenu: false,
      cardType: false,
      complexity: null,
      problem: "",
      solution: "",
      helpPoints: HelpPoints,
      isCardLocked: false,
      selectedPanel: null,
      BlockModeTypes: BlockModeTypes,
      BLOCK_FIELD_LENGTH_MAX: BLOCK_FIELD_LENGTH_MAX,
      BLOCK_FREE_TEXT_FIELD_LENGTH_MAX: BLOCK_FREE_TEXT_FIELD_LENGTH_MAX,

      hypothesisBelieve: null,
      hypothesisVerify: null,
      hypothesisMeasure: null,
      hypothesisRight: null,
      hypothesisExpect: null,

      // mattersMetricObjective: null,
      mattersMetricObjective: {
        field1: null,
        field2: null,
        field3: null,
        dueDate1: null,
        dueDate2: null,
        dueDate3: null,
        dueDate4: null,
        select1: null,
        select2: null
      },
      metricMeasure: null,
      mattersMetricStartDate: null,
      mattersMetricBaseline: null,
      mattersMetricExpected: null,
      mattersMetricEndDate: null,

      mattersMetricAchieved: null,
      mattersMetricOutcome: null,
      mattersMetricObservations: null,


      // userStoryRole: null,
      userStoryRoleFields: {
        field1: '',
        field2: '',
        field3: '',
        field4: '',
        field5: '',
        field6: '',
        select: null
      },
      promiseFormatField: 'Field',
      promiseDropList: [
        'Awareness',
        'Acquisition',
        'Activation',
        'Retention',
        'Referral',
        'Revenue',
        'Reactivation'
      ],
      sucMetDromList2: [
        'Community',
        'Content (inc. blogging)',
        'Customer Service',
        'Email',
        'Engineering',
        'Offline Ads',
        'Offline Events (inc trade shows)',
        'Online Ads (inc. SEM)',
        'Online Events',
        'Partnerships (inc. affiliates)',
        'Platforms',
        'Publicity (inc. Guerilla PR)',
        'Sales',
        'SEO',
        'Viral',
        'Other...'
      ],
      resultSelectPropose: [],
      resultSelectResulted: [
        'Some',
        'Test',
        'Stuff'
      ],
      sucMetDromList1: [
        'Annual Contract Value (ACV)',
        'Customer attrition (churn)',
        'Customer lifetime value (CLTV)',
        'Monthly recurring revenue (MMR)',
        'Order value (OV)',
        'Return on advertising spend (ROAS)',
        'Return on investment (ROI)',
        'Time to recover (Customer acquisition cost)',
        'Virality (K-factor)',
        'Customer satisfaction (CSAT)',
        'Net promoter score (NPS)',
        'Support requests',
        '% resolutions',
        'Time to resolution',
        'Conversions',
        'Marketing qualified leads (MQL)',
        'Sales qualified leads (SQL)',
        'Referrals',
        'Upsells',
        'Active users (DAU)',
        'Lapsed users',
        'Paying customers',
        'Trial users',
        'User dropoff',
        'Cost per action (CPA)',
        'Cost per click (CPC)',
        'Cost per lead (CPL)',
        'Cost per mille (CPM)',
        'Cost per view (CPV)',
        'Clicks',
        'Comments',
        'Forwards',
        'Impressions',
        'Likes ',
        'Mentions',
        'Opens',
        'Shares',
        'Reach',
        'Followers',
        'Views',
        'Backlinks / referring domains',
        'Bounce rate',
        'Domain authority',
        'Page load speed',
        'Pages / sessions',
        'Pageviews',
        'Rankings',
        'Session duration',
        'Time on site',
        'Other...'
      ],
      userStoryIntention: null,
      // userStoryResult: null,

      //Result block
      resultResulted: null,
      resultFindings: null,
      resultLearned: null,
      resultsConcluded: null,
      resultsPropose: null,
      resultsContribution: null,
      resultsImplications: null,
      metricsMode: BlockModeTypes.STORY,
      resultMode: BlockModeTypes.STORY,
      resultFreeText: null,

      dueDate: null,
      dateFormatted: null,
      modifiedDate: null,

      showDateMenu: null,

      scope: null,
      scopes: CardDataTypes.Scope.enumValues,

      descriptionCollapsed: true,
      hypothesisCollapsed: true,
      metricsCollapsed: true,
      resultsCollapsed: true,
      promiseCollapsed: true,

      rules: {
        titleLength: titleLength,
        cardBlockFieldLength: cardBlockFieldLength,
        cardBlockFreeTextFieldLength: cardBlockFreeTextFieldLength,
      }
    }
  },

  computed: {
    ...mapState('workspace', ['workspace']),
    ...mapState('folder', ['folder']),
    ...mapState('filter', ['filter']),
    ...mapState('board', ['board']),
    ...mapState('card', ['card']),
    ...mapGetters('card', ['getLoadingCard']),
    ...mapGetters('board', ['getNextBoard', 'getPreviousBoard']),
    ...mapState('account', ['id']),

    fields() {
      return _.pickBy(this.userStoryRoleFields, (v, k) => k !== 'select' && v?.trim())
    },
    metricCol() {
      return this.mattersMetricObjective.field3 ||
    },

    isFreeTextP() {
      return this.promiseFormatField === 'Free text'
    },

    fieldFormatP() {
      return this.promiseFormatField !== "Story"
    },
    isCompleted() {
      return this.card.status === 'declined' || this.card.status === 'archived'
    },

    phiesSum() {
      return this.card.phies_sum;
    },


    canShowMattersMetricAnalysisFields() {
      return this.board === BoardType.ANALYSIS;
    },

    metricMeasuresItems() {
      return _.zipWith(MetricMeasuresItems, function (c) {
        return c.title
      })
    },

    metricOutcome() {
      return OutcomeItems.map(e => e.value);
    },

    previousBoardCapitalized() {
      if (this.isUnscheduled()) return userReadable(BoardType.CAPTURE)
      return userReadable(this.getPreviousBoard);
    },

    nextBoardCapitalized() {
      if (this.isAnalysis()) return 'Completed'
      return userReadable(this.getNextBoard);
    },

    growthExperimentTitle() {
      let growthExperimentId = this.card.growth_experiment;
      let experiment = Experiments.find(e => e.id === growthExperimentId);
      return experiment?.title;
    },

    aaarrrTitle() {
      let cardAAARRRId = this.card.property_aaarrr;
      return AAARRR.find(a => a.id === cardAAARRRId)?.title;
    },

    experimentTitle() {
      let cardExperimentId = this.card.property_experiment;
      return EXPERIMENT.find(e => e.id === cardExperimentId)?.title;
    },

    traction20Title() {
      let cardTraction20Id = this.card.property_traction20;
      return TRACTION20.find(t => t.id === cardTraction20Id)?.title;
    },

    isIdeation() {
      return this.breadcrumbs === 'Idea';
    },

    cardMembers() {
      if (this.isIdeation) {
        return this.card.members.filter(member => member.id === this.card.owner_id)
      } else {
        return sortCardMembers(this.card);
      }
    },

    isNotIdeation() {
      return !this.ideationBoardTabs.includes(this.board);
    },

    isPanelPropertiesVisible() {
      return true; // should be visible everywhere
    },

    isPanelBlocksVisible() {
      return true; // should be visible everywhere
    },

    isPanelExperimentVisible() {
      return this.board === BoardType.UNSCHEDULED;
    },

    isPanelRateIdeaVisible() {
      return this.card.board !== BoardType.CAPTURE;
    },

    isPanelReportBugsVisible() {
      return this.board === BoardType.DEVELOPMENT || this.board === BoardType.UNSCHEDULED;
    },

    isPanelBulletinsVisible() {
      return this.board === BoardType.DEVELOPMENT || this.board === BoardType.UNSCHEDULED;
    },

    disabled() {
      return this.card && this.card?.locked || this.card?.status !== "active";
    },

    breadcrumbs() {
      if (this.ideationBoardTabs.includes(this.card.board)) {
        return 'Idea'
      } else {
        return this.card.type
      }
    },
    activeBlocks() {
      return this.card.available_blocks
    },
    completedBoard() {
      return this.filter === FilterType.DECLINED
    }
  },

  methods: {
    ...mapActions('card', ['updateCard', 'setCard', 'fetchCard']),
    ...mapActions('folder', ['replicateCard', 'removeCard', 'selectCard']),

    isAnalysis() {
      return BoardType.ANALYSIS === this.board;
    },

    defaultText(mode, value) {
      if (mode === 'list' && !value) return '< select from list >'
      if (mode === 'field' && !value) return '< enter your answer here >'
      if (mode === 'date' && !value) return '< select from date picker >'
      return value
    },

    isUnscheduled() {
      return BoardType.UNSCHEDULED === this.board;
    },
    formatDate(date) {
      if (!date) return null
      return moment(date).format('DD MMM YYYY')
    },
    closePromise() {
      if (!this.disabled) {
        this.updateCard({
          card_id: this.card.id,
          available_blocks: {
            promise_block: false
          }
        })
      }
    },
    closeSuccessMetric() {
      if (!this.disabled) {
        this.updateCard({
          card_id: this.card.id,
          available_blocks: {
            success_metric_block: false
          }
        })
      }
    },
    closeResults() {
      if (!this.disabled) {
        this.updateCard({
          card_id: this.card.id,
          available_blocks: {
            results_block: false
          }
        })
      }
    },

    deleteProperty(type) {
      switch (type) {
        case 'traction20Title':
          this.updateCard({
            card_id: this.card.id,
            property_traction20: null
          })
          break;
        case 'experimentTitle':
          this.updateCard({
            card_id: this.card.id,
            property_experiment: null
          })
          break;
        case 'aaarrrTitle':
          this.updateCard({
            card_id: this.card.id,
            property_aaarrr: null
          })
          break;
        case 'growthExperimentTitle':
          this.updateCard({
            card_id: this.card.id,
            growth_experiment: null
          })
          break
        default:
          return null
      }
    },
    dateRed() {
      if (this.card.status !== 'archive') {
        return _.inRange(this.calcDate(), 1, 200)
      }
    },

    dateOrange() {
      if (this.card.status !== 'archive') {
        return _.inRange(this.calcDate(), 1, -1)
      }
    },

    calcDate() {
      let now = moment(new Date());
      let end = moment(this.dueDate);
      let duration = moment.duration(now.diff(end));
      return Math.round(duration.asDays())
    },
    handleWrapperClick(e) {
      if (e.currentTarget === e.target) {
        this.closeCard();
      }
    },
    checkActiveBlock(block) {
      let status = this.activeBlocks.find(x => x.name === block).status
      return status
    },

    handleAssign({user}) {
      this.updateCard({
        card_id: this.card.id,
        new_owner_id: user?.id,
      }).catch(() => {
        this.setErrorVisibility(true);
      });
    },

    isOwner({user}) {
      return this.card.owner_id === user.id;
    },

    removeUserFromCard(user) {
      this.updateCard({
        card_id: this.card.id,
        remove_card_member: true,
        card_member: user.id
      }).catch(() => {
        this.setErrorVisibility(true);
      });
    },

    closeCard() {
      updateRouterParams(
          this.$router,
          {
            cardId: undefined
          });
      this.metricsMode = BlockModeTypes.STORY
      this.resultMode = BlockModeTypes.STORY
      this.isVisible = false
       this.metricsCollapsed = true
      this.resultsCollapsed = true
      this.userStoryRoleFields = {
        field1: null,
        field2: null,
        field3: null,
        field4: null,
        field5: null,
        field6: null,
        select: null
      };
      this.mattersMetricObjective = {
        field1: null,
        field2: null,
        field3: null,
        dueDate1: null,
        dueDate2: null,
        dueDate3: null,
        dueDate4: null,
        select1: null,
        select2: null
      };

      this.userStoryIntention = null;
      this.promiseFormatField = null;
      this.metricFormatField = null;
      document.querySelector("html").classList.remove('no-scroll')
    },

    async changeTitle() {
      if (this.title.trim().length === 0) {
        this.title = this.card.default_title
      }
      this.updateCard({
        card_id: this.card.id,
        title: this.title
      }).catch(() => {
        this.setErrorVisibility(true);
      });
    },

    toggleUrgent() {
      this.updateCard({
        card_id: this.card.id,
        urgent: !this.card.urgent,
      }).catch(() => {
        this.setErrorVisibility(true);
      }).finally(() => {
        this.$refs.btnUrgent.setOn(this.card.urgent);
      });
    },

    toggleBug() {
      this.updateCard({
        card_id: this.card.id,
        bug: !this.card.bug,
      }).catch(() => {
        this.setErrorVisibility(true);
      }).finally(() => {
        this.$refs.btnBug.setOn(this.card.bug);
      });
    },

    selectPanel(panel) {
      if (this.selectedPanel !== panel && !this.disabled) {
        if (this.selectedPanel) {
          this.selectedPanel.hideCollapsible();
        }
        this.selectedPanel = panel;
        this.selectedPanel.showCollapsible();
      } else {
        this.selectedPanel?.toggleCollapsible();
      }
    },

    lockCard() {
      this.updateCard({
        card_id: this.card.id,
        locked: !this.card.locked,
      }).catch(() => {
        this.setErrorVisibility(true);
      });
    },

    updateDescription() {
      this.updateCard({
        card_id: this.card.id,
        description: this.description
      }).catch(() => {
        this.setErrorVisibility(true);
      });
    },
    clearDate() {
      this.showDateMenu = false
      this.updateDueDate(null)
    },
    async updateDueDate(dueDate) {
      this.updateCard({
        card_id: this.card.id,
        due_date: dueDate
      }).then(() => {
        this.applyStore()
      }).catch(() => {
        this.setErrorVisibility(true);
      });
    },

    deleteCard() {
      this.removeCard([this.card.id]);
      this.closeCard();
    },

    declineCard() {
      this.selectCard([this.card]);
      this.updateCard({
        card_id: this.card.id,
        status: CardDataTypes.Status.Declined.name,
      }).then(() => {
        this.closeCard();

        updateRouterParams(
            this.$router,
            {
              panelType: FilterType.DECLINED,
              folderId: ROOT_FOLDER,
              cardId: undefined
            }
        );
      });
    },

    Restore() {
      this.updateCard({
        card_id: this.card.id,
        status: CardDataTypes.Status.Active.name,
        board_type: this.card.board,
        move_card: true,
      }).then(() => {
        this.closeCard();
        updateRouterParams(
            this.$router,
            {
              panelType: this.card.board,
              folderId: ROOT_FOLDER,
              cardId: undefined
            }
        );
      })
    },

    replicate() {
      this.replicateCard(this.card.id);
      this.closeCard();
    },

    moveCardForward() {
      this.selectCard([this.card]);
      let cardId = this.card.id;
      if (this.isAnalysis()) {
        this.updateCard({
          card_id: this.card.id,
          status: CardDataTypes.Status.Archived.name
        }).then(() => {
          updateRouterParams(
              this.$router,
              {
                panelType: 'archive',
                folderId: ROOT_FOLDER,
                cardId: undefined
              }
          );
        });
        return null
      }

      if (this.getNextBoard === FilterType.ARCHIVE) {
        this.updateCard({
          card_id: this.card.id,
          status: CardDataTypes.Status.Archived.name
        }).then(() => {
          updateRouterParams(
              this.$router,
              {
                panelType: this.getNextBoard,
                folderId: ROOT_FOLDER,
                cardId: undefined
              }
          );
        });
      } else {
        this.updateCard({
          card_id: cardId,
          board_type: this.getNextBoard,
          move_card: true,
        }).then(() => {
          updateRouterParams(
              this.$router,
              {
                panelType: this.getNextBoard,
                folderId: ROOT_FOLDER,
                cardId: cardId
              });
        });
      }

      this.closeCard();
    },

    moveCardBackward() {
      this.selectCard([this.card]);
      let cardId = this.card.id;
      let boardType = this.getPreviousBoard
      if (this.isUnscheduled()) boardType = BoardType.CAPTURE
      this.updateCard({
        card_id: cardId,
        board_type: boardType,
        move_card: true,
      }).then(() => {
        updateRouterParams(
            this.$router,
            {
              panelType: this.getPreviousBoard,
              folderId: ROOT_FOLDER,
              cardId: cardId
            });
      });
      this.closeCard();
    },

    updateCardType(value) {
      if (!this.disabled) {
        let cardType = value ? CardType.EXPERIMENT : CardType.DUCKSOUP;
        let updateCardParams = {
          card_id: this.card.id,
          type: cardType,
        }
        if (cardType === CardType.EXPERIMENT) {
          this.cardType = true
          updateCardParams.experiment_field = true;
        }
        if (cardType === CardType.DUCKSOUP) {
          this.cardType = false
        }

        this.updateCard(updateCardParams);
      }
    },

    updateScope(scope) {
      this.updateCard({
        card_id: this.card.id,
        scope: scope
      });
    },

    handleScopeSelect(name) {
      this.scope = name;
      if (this.card && this.card.scope !== this.scope.toLowerCase()) {
        this.updateScope(name.toLowerCase());
      }
    },


    applyStore() {
      this.isVisible = true;
      this.title = this.card.title;
      this.dueDate = this.card.due_date
      this.modifiedDate = this.card.last_updated
      this.description = this.card.description;
      this.showActionMenu = false;
      this.cardType = this.card.type === 'experiment';
      this.metricMeasure = null;
      this.helpPoints = HelpPoints;
      this.isCardLocked = false;
      this.complexity = this.card.complexity;

      if (this.card.experiment_card) {
        this.userStoryRoleFields = {
          field1: null,
          field2: null,
          field3: null,
          field4: null,
          field5: null,
          field6: null,
        };
        this.userStoryIntentionSelect = {
          select: null,
          text: null
        };
        this.userStoryResultText = {
          select: null,
          text: null
        };
        this.resultResulted = this.card.experiment_card.result_resulted;
        // this.resultMode = this.card.experiment_card.result_mode;
        this.resultFindings = this.card.experiment_card.result_findings;
        this.resultLearned = this.card.experiment_card.result_learned;
        this.resultsConcluded = this.card.experiment_card.result_concluded;
        this.resultsPropose = this.card.experiment_card.result_propose;
        this.resultsContribution = this.card.experiment_card.result_contribution;
        this.resultsImplications = this.card.experiment_card.result_implications;
        this.resultFreeText = this.card.experiment_card.result_free_text;


        this.problem = this.card.experiment_card.problem;
        this.solution = this.card.experiment_card.solution;
        this.hypothesisBelieve = this.card.experiment_card.hypothesis_believe;
        this.hypothesisVerify = this.card.experiment_card.hypothesis_verify;
        this.hypothesisMeasure = this.card.experiment_card.hypothesis_measure;
        this.hypothesisRight = this.card.experiment_card.hypothesis_right;
        this.hypothesisExpect = this.card.experiment_card.hypothesis_expect;
        this.mattersMetricObjective = this.card.experiment_card.matters_metric_objective ? JSON.parse(this.card.experiment_card.matters_metric_objective) : this.mattersMetricObjective;
        this.metricMeasure = this.card.experiment_card.metric_measure;
        this.mattersMetricStartDate = this.card.experiment_card.matters_metric_start_date;
        this.mattersMetricBaseline = this.card.experiment_card.matters_metric_baseline;
        this.mattersMetricExpected = this.card.experiment_card.matters_metric_expected;
        this.mattersMetricEndDate = this.card.experiment_card.matters_metric_end_date;

        this.mattersMetricAchieved = this.card.experiment_card.matters_metric_achieved;
        this.mattersMetricOutcome = this.card.experiment_card.matters_metric_outcome;
        this.mattersMetricObservations = this.card.experiment_card.matters_metric_observations;

        this.userStoryRoleFields = this.card.experiment_card.user_story_role ? JSON.parse(this.card.experiment_card.user_story_role) : this.userStoryRoleFields;
        this.userStoryIntention = this.card.experiment_card.user_story_intention
        this.descriptionCollapsed = this.card.experiment_card.description_collapsed;
        this.hypothesisCollapsed = this.card.experiment_card.hypothesis_collapsed;
        // this.metricsCollapsed = this.card.experiment_card.metrics_collapsed;
      }

      this.dueDate = this.card.due_date ? moment(this.card.due_date).format('YYYY-MM-DD') : this.card.due_date;
      this.modifiedDate = this.card.last_updated ? moment(this.card.last_updated).format('YYYY-MM-DD') : this.card.last_updated;
      this.scope = this.card.scope;

      if (this.$refs.datePicker && this.isNotIdeation)
        this.$refs.datePicker.setValue(this.dueDate);

      if (this.$refs.btnUrgent)
        this.$refs.btnUrgent.setOn(this.card.urgent);

      if (this.$refs.btnBug)
        this.$refs.btnBug.setOn(this.card.bug);
    },

    async applyRoute({cardId}) {
      if (cardId) {
        await this.setCard(cardId);
        this.applyStore();
      }
    },

    updateExperimentField(params) {
      this.updateCard({
        card_id: this.card.id,
        experiment_field: true,
        ...params
      });
    },


    updateResultResulted() {
      this.updateExperimentField({update_result_resulted: this.resultResulted});
    },
    updateResultFindings() {
      this.updateExperimentField({update_result_findings: this.resultFindings});
    },
    updateResultLearned() {
      this.updateExperimentField({update_result_learned: this.resultLearned});
    },
    updateResultConcluded() {
      this.updateExperimentField({update_result_concluded: this.resultsConcluded});
    },
    updateResultPropose() {
      this.updateExperimentField({update_result_propose: this.resultsPropose});
    },
    updateResultContributions() {
      this.updateExperimentField({update_result_contribution: this.resultsContribution});
    },
    updateResultImplications() {
      this.updateExperimentField({update_result_implications: this.resultsImplications});
    },
    updateResultMode() {
      this.updateExperimentField({update_result_mode: this.resultMode});
    },
    updateResulFreeText(value) {
      this.updateExperimentField({update_result_free_text: this.resultFreeText});
    },


    updateProblem() {
      this.updateExperimentField({update_problem: this.problem});
    },
    updateSolution() {
      this.updateExperimentField({update_solution: this.solution});
    },
    updateHypothesisBelieve() {
      this.updateExperimentField({update_hypothesis_believe: this.hypothesisBelieve});
    },
    updateHypothesisVerify() {
      this.updateExperimentField({update_hypothesis_verify: this.hypothesisVerify});
    },
    updateHypothesisMeasure() {
      this.updateExperimentField({update_hypothesis_measure: this.hypothesisMeasure});
    },
    updateHypothesisRight() {
      this.updateExperimentField({update_hypothesis_right: this.hypothesisRight});
    },
    updateHypothesisExpect() {
      this.updateExperimentField({update_hypothesis_expect: this.hypothesisExpect});
    },
    updateMattersMetricObjective() {
      let metric = {
        field1: this.mattersMetricObjective.field1,
        field2: this.mattersMetricObjective.field2,
        field3: this.mattersMetricObjective.field3,
        dueDate1: this.mattersMetricObjective.dueDate1,
        dueDate2: this.mattersMetricObjective.dueDate2,
        dueDate3: this.mattersMetricObjective.dueDate3,
        dueDate4: this.mattersMetricObjective.dueDate4,
        select1: this.mattersMetricObjective.select1,
        select2: this.mattersMetricObjective.select2,
      }
      this.updateExperimentField({update_matters_metric_objective: JSON.stringify(metric)});
    },
    updateMattersMetricBaseline() {
      this.updateExperimentField({update_matters_metric_baseline: this.mattersMetricBaseline});
    },
    updateMattersMetricExpected() {
      this.updateExperimentField({update_matters_metric_expected: this.mattersMetricExpected});
    },

    updateMattersMetricAchieved() {
      this.updateExperimentField({update_matters_metric_achieved: this.mattersMetricAchieved});
    },
    updateMattersMetricOutcome() {
      this.updateExperimentField({update_matters_metric_outcome: this.mattersMetricOutcome});
    },
    updateMattersMetricObservations() {
      this.updateExperimentField({update_matters_metric_observations: this.mattersMetricObservations});
    },

    updateMetricMeasure() {
      this.updateExperimentField({update_metric_measure: this.metricMeasure});
    },
    updateMattersMetricEndDate() {
      this.updateExperimentField({update_matters_metric_end_date: this.mattersMetricEndDate});
    },
    updateMattersMetricStartDate() {
      this.updateExperimentField({update_matters_metric_start_date: this.mattersMetricStartDate});
    },
    updateUserStoryRoleFields() {
      let fields = {
        field1: this.userStoryRoleFields.field1,
        field2: this.userStoryRoleFields.field2,
        field3: this.userStoryRoleFields.field3,
        field4: this.userStoryRoleFields.field4,
        field5: this.userStoryRoleFields.field5,
        field6: this.userStoryRoleFields.field6,
        select: this.userStoryRoleFields.select,
      }
      this.updateExperimentField({update_user_story_role: JSON.stringify(fields)});
    },
    // updateUserStoryRole() {
    //   this.updateExperimentField({update_user_story_role: this.userStoryRole});
    // },
    updateUserStoryIntention() {
      this.updateExperimentField({update_user_story_intention: this.userStoryIntention});
    },
    updateUserStoryResult() {
      this.updateExperimentField({update_user_story_result: this.userStoryResult});
    },
    updateDescriptionCollapsed() {
      this.updateExperimentField({update_description_collapsed: this.descriptionCollapsed});
    },
    updateHypothesisCollapsed() {
      this.updateExperimentField({update_hypothesis_collapsed: this.hypothesisCollapsed});
    },
    // updateMetricsCollapsed() {
    //   this.updateExperimentField({update_metrics_collapsed: this.metricsCollapsed});
    // },

    // Toggle collapsible sections
    collapseButtonClick(collapsibleSection) {
      if (!this.disabled) {
        if (collapsibleSection === 'description') {
          this.descriptionCollapsed = !this.descriptionCollapsed;
          this.updateDescriptionCollapsed();
        } else if (collapsibleSection === 'hypothesis') {
          this.hypothesisCollapsed = !this.hypothesisCollapsed;
          this.updateHypothesisCollapsed();
        } else if (collapsibleSection === 'metrics') {
          this.metricsCollapsed = !this.metricsCollapsed;
          this.metricsMode = BlockModeTypes.FIELD
          // this.updateMetricsCollapsed();
        } else if (collapsibleSection === 'results') {
          this.resultMode = BlockModeTypes.FIELD
          this.resultsCollapsed = !this.resultsCollapsed;
          // this.updateMetricsCollapsed();
        } else {
          console.error('Unknown collapsible!');
        }
      }
    }
  },

  updated() {
    if (this.card?.board === this.ideationBoardTabs[1] && this.isVisible) {
      this.$refs.rateIdeaPanel?.showCollapsible();
    }
  },

  watch: {
    '$route.params.cardId'(val) {
      if (!val) {
        this.isVisible = false;
      } else {
        this.applyRoute({
          cardId: val
        });
      }
    },


    card() {
      if (this.card) {
        this.selectedPanel = null;
        this.isCardLocked = this.card.locked;
        this.scope = this.card.scope;
        this.title = this.card.title;
        this.description = this.card.description;

        document.querySelector("html").classList.add('no-scroll')
      }
    },
    dueDate(val) {
      this.dateFormatted = this.formatDate(val)
    },

    isVisible(val) {
      if (!val) {
        updateRouterParams(
            this.$router,
            {
              cardId: undefined
            }
        );
      }
    }
  }
}
</script>

<style>
html.no-scroll {
  overflow: hidden !important;
}

.ml /deep/ .wow-btn-circle-outlined {
  border-radius: 7px !important;
}

.ml /deep/ .wow-app-bar-btn-round {
  border-radius: 7px !important;
}

.ml /deep/ .btn-actions {
  border-radius: 7px !important;
}

.bl > .v-input__control {
  margin-top: 20px;
}

.bl /deep/ .v-input__slot {
  padding: 9px 12px !important;
}

.pm /deep/ .v-input__control {
  padding: 0 15px;
  margin-top: 20px;
}

.pm /deep/ fieldset {
  height: 100%;
}

.mr {
  margin-right: 25px !important;
}

.fs {
  font-size: 13px !important;
}
</style>

<style scoped lang="less">
@import (reference) "~@/less/main/style.less";
.colborder{
  border: 2px solid;
}
.story {
  width: 95%;
  margin-left: 14px;
}

.p0 {
  padding: 0;
}

.cw {
  color: white;
}

.mx-1 {
  margin-right: 6px !important;
  margin-left: 6px !important;
}

.selectField {
  margin-left: 15px;
  max-width: 48% !important;
  padding-top: 12px;
  margin-top: 4px;
}

.ml {
  margin-left: -15px !important;
}

.default-description-text {
  border-bottom: 1px solid black;
  cursor: pointer;
}

.fl {
  float: right;
  margin-right: -30px;
}

.description-container .wow-card-textarea {
  margin-top: 0 !important;
}

.bb {
  border-bottom: 1px solid;
  border-bottom-left-radius: 0 !important;
  border-bottom-right-radius: 0 !important;
}

.theme--light.v-input {
  color: rgb(150 148 148);
}

@wow-dialog-background-color: #eee;

.title {
  max-width: 100%;
}

.card-actions-divider {
  margin-left: 4px;
  margin-right: 4px;
  background: #606367;
}

.selected-panel-chip {
  color: white;
}

::v-deep .v-textarea.v-text-field--enclosed textarea,
::v-deep .v-textarea.v-text-field--enclosed.v-text-field--single-line textarea {
  margin-top: 0;
}

.wow-actions-container {
  :first-child {
    margin-left: 0;
  }

  :last-child {
    margin-right: 0;
  }
}

.card-type {
  display: flex;
  align-items: center;

  .slider {
    flex: 2;
    display: flex;
    align-items: center;
    justify-content: space-evenly;
  }

  .divider {
    flex: 1;
  }
}

.icon-container .bell-icon {
  font-size: 2em;
  color: grey;
}

.wow-dialog {
  ::v-deep {
    .v-input__slot {
      padding: 5px 15px;
    }

    .v-input__slot:before,
    .v-input__slot:after {
      display: none !important;
    }
  }
}

.due-date-picker {
  border-radius: 10px;
  height: 40px;
}

.date-slot {
  background-color: #0E9A00;
}

.red-due-picker {
  background-color: #ff2c2c !important;
}

.picker-container {
  width: 155px;
  margin-right: -13px;
}

.orange-due-picker {
  background-color: #ff8444 !important;
}

::v-deep .custom-neum-switch {
  .v-input__slot .v-input--switch__track {
    background: transparent !important;
    box-shadow: -8px -4px 8px 0px #ffffff,
    8px 4px 12px 0px @shadow-color-outer,
    4px 4px 4px 0px @shadow-color-outer inset,
      -4px -4px 4px 0px #ffffff inset;
  }

  .v-input--switch__thumb {
    box-shadow: -4px -2px 5px 0px #ffffff,
    5px 2px 6px 0px @shadow-color-outer;
    background: @wow-background;
  }

  .v-input--selection-controls__ripple {
    display: none !important;
  }
}

.wow-dialog {
  margin-left: auto;
  margin-right: auto;
}

::v-deep {
  .v-dialog--active {
    margin: 0;
    padding-top: 0;
    padding-bottom: 0;
    min-height: 100%;
  }
}

.custom-card-wrap {
  position: relative;
  padding-top: 24px;
  padding-bottom: 24px;
  width: 100%;
  height: 100%;
  background: transparent;
  overflow-y: auto;
}


.field-toggle-btn {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  text-align: center;
  margin-left: auto;
  margin-right: auto;
  margin-top: -15px;
  z-index: 10;
  width: 30px;
  height: 30px;
  color: #000;
  border-radius: 50%;
  outline: none !important;

  i {
    transition: all .2s ease-in-out;
  }

  &.rotated {
    i {
      transform: rotate(180deg)
    }
  }
}


.area{
  margin-left: 0 !important;
  padding-left: 0 !important;
}

.slide-enter-active {
  transition-duration: 0.3s;
  transition-timing-function: ease-in;
}

.slide-leave-active {
  transition-duration: 0.3s;
  transition-timing-function: cubic-bezier(0, 1, 0.5, 1);
}

.slide-enter-to,
.slide-leave {
  max-height: 200px;
  overflow: hidden;
}

.slide-enter,
.slide-leave-to {
  max-height: 0;
  overflow: hidden;
}

.breadcrumbs {
  position: absolute;
  left: 24px;
  top: 5px;

  span {

    i {
      text-transform: capitalize;
    }
  }
}
</style>