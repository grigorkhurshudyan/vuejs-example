<template>
    <div :class="`wow-round-btn cursor-pointer ${classStr}`" v-bind:title="title">
        <v-badge
                dot
                bordered
                bottom
                :value="null"
                :color="statusColor"
                offset-x="0.6rem"
                offset-y="0.6rem"
                class="wow-badge"
        >
            <!--ToDo: put :value for badge, when there will be logic for statuses-->
            <v-avatar :color="color" :size="size">
                <v-img alt="image"
                       v-if="image"
                       :src="image"
                >
                </v-img>
                <span
                        v-if="!image"
                        class="white--text headline"
                        :style="textStyle">
                    {{initials}}
                </span>
            </v-avatar>
        </v-badge>
        <slot></slot>
    </div>
</template>

<script>
    export default {
        name: "IconAvatar",

        props: {
            classStr: String,
            account: Object,
            workspase: Object,
            newColor: String,
            forceInitials: Boolean,
            newImage: String,
            size: {
                type: String,
                default: "2rem"
            },

        },

        computed: {
            textStyle() {
                let matches = this.size.match(/^(\d+(?:\.\d+)?)(.*)$/);
                let number = matches[1] ? matches[1] * 0.5 : 1;
                let units = matches[2] ? matches[2] : "rem";
                return `font-size: ${number}${units}!important;`;
            },
            statusColor() {
                switch (this.account?.status) {
                    case "online":
                        return "green";
                    case "inactive":
                        return "yellow";
                    case "offline":
                        return "gray";
                    default:
                        return '';
                }
            },
            color() {
                if (this.workspase) return 'grey'
                if (!this.newColor)
                return this.account?.profileInitialColor
            else
                return this.newColor;
            },
            image() {
                if (this.workspase)
                    return this.workspase.workspaceAvatar
                if (this.forceInitials)
                    return "";
                if (this.newImage)
                    return this.newImage;
                return this.account?.profileImage;
            },
            initials() {
                if (this.workspase) {
                    return `${this.workspase.title.substring(0, 1).toUpperCase()}`
                }
                if (this.account && this.account.firstName && this.account.lastName) {
                    return `${this.account.firstName.substring(0, 1)}${this.account.lastName.substring(0, 1)}`;
                } else {
                    return '';
                }
            },
            title() {
                if (this.workspase) {
                    return `${this.workspase.title}`
                }
                return `${this.account?.firstName} ${this.account?.lastName}`;
            }
        }
    }
</script>

<style scoped lang="less">
    @import (reference) "~@/less/main/style.less";

    .wow-round-img {
        border-radius: 50%;
        width: 100%;
        height: 100%;
        object-fit: cover;
        vertical-align: top;
        image-rendering: smooth;
    }

    ::v-deep.v-badge--dot .v-badge__badge {
        height: 0.6rem;
        width: 0.6rem;
        border-radius: 50%;
    }
</style>