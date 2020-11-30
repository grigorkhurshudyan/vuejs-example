<template>
    <div>
        <v-menu
                ref="menu"
                v-model="showMenu"
                :close-on-content-click="false"
                :return-value.sync="inputValue"
                transition="scale-transition"
                offset-y
                min-width="290px"
        >
            <template v-slot:activator="{ on }">
                <v-text-field
                        color="#82256c"
                        v-model="inputValue"
                        outlined
                        readonly
                        hide-details
                        dense
                        v-on="on"
                        :disabled="disabled"
                >
                    <span slot="label"><span class="fa fa-calendar-day mr-1"></span>{{label}}</span>
                </v-text-field>
            </template>
            <v-date-picker
                    ref="picker"
                    color="#82256c"
                    v-model="inputValue"
                    no-title
                    scrollable
            >
                <v-spacer></v-spacer>
                <v-btn text color="#82256c" @click="handleCancel">Cancel</v-btn>
                <v-btn text color="#82256c" @click="handleOk">OK</v-btn>
            </v-date-picker>
        </v-menu>
    </div>
</template>

<script>
    export default {
        name: "DatePicker",
        props: {
            value: String,
            label: String,
            onChange: Function,
            disabled: {
                type: Boolean,
                default: false
            }
        },
        data() {
            return {
                inputValue: this.value,
                showMenu: false
            }
        },
        watch: {
            indeterminate(val) {
                this.$nextTick(() => (this.inputValue = val));
            },
            inputValue(val) {
                this.$emit('update:indeterminate', val);
            }
        },
        methods: {
            handleOk() {
                this.$refs.menu.save(this.inputValue);
                if (this.onChange) {
                    this.onChange(this.inputValue);
                }
            },
            handleCancel() {
                this.inputValue = this.value;
                this.showMenu = false;
            },
            setValue(value) {
                this.inputValue = value;
            }
        }
    }
</script>

<style scoped>

</style>