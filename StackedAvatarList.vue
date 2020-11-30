<template>
    <div class="wow-avatars-container">
        <NavDropdownMenu
                v-for="(user, index) in users"
                :key="index"
                :style="calcMargin(index)"
        >
            <template v-slot:dropDownButton>
                <IconAvatar :account="user"/>
            </template>

            <template v-slot:dropDownMenu>
                <v-list v-if="!info" dense>
                    <v-list-item>
                        <div class="d-flex align-center flex-column">
                            <span class="inv-user-icon">
                                <IconAvatar :account="user"/>
                                <span class="inv-user-info">
                                    <span>{{getFirstName(user)}} {{getLastName(user)}}</span>
                                    <span>{{getUsername(user)}}</span>
                                </span>
                            </span>
                            <slot name="extension" :user="user"/>
                            <v-btn
                                    :disabled="disabled"
                                    v-if="getUsername(user) !== username"
                                    v-show="showRemove"
                                    small
                                    class="inv-user-remove"
                                    @click.native="handleRemove(user)">
                                Remove
                            </v-btn>
                        </div>
                    </v-list-item>
                </v-list>
            </template>
        </NavDropdownMenu>
    </div>
</template>

<script>
    import NavDropdownMenu from "@/components/NavBar/NavDropdownMenu";
    import IconAvatar from "@/components/IconAvatar";
    import {mapState} from "vuex";

    export default {
        name: "StackedAvatarList",

        components: {
            IconAvatar,
            NavDropdownMenu
        },

        props: {
            disabled:Boolean,
            users: Array,
            showRemove: {
                type: Boolean,
                default: false
            },
            info: {
                type: Boolean,
                default: false
            }
        },

        computed: {
            ...mapState('workspace', ['workspace']),
            ...mapState('account', ['username']),
        },

        methods: {
            getFirstName(user){
                return user ? user.firstName : null;
            },
            getUsername(user){
                return user ? user.username : null;
            },
            getLastName(user){
                return user ? user.lastName : null;
            },

            calcMargin(index) {
                let zIndex = this.users.length - index;
                return `z-index: ${zIndex};`;
            },

            handleRemove(user) {
                this.$emit('removeUser', user);
            }
        }
    }
</script>

<style scoped lang="less">
    @import (reference) "~@/less/main/style.less";

    .wow-avatars-container {
        position: relative;
        display: flex;
        align-items: center;

        .wow-round-btn {
            display: block;
            border: 1px solid #fff;
            border-radius: 50%;
        }

        .text-center:not(:first-child) {
            margin-left: -7px;
        }
    }

    .inv-user-icon {
        display: flex;
        align-items: center;
        padding-bottom: 1em;

        .wow-round-btn {
            display: block;
            margin-right: 7px;
        }

        .inv-user-info {
            text-align: center;

            span {
                display: block;
            }
        }
    }

    .inv-user-remove {
        margin-top: 12px;
        text-transform: initial !important;
    }
</style>