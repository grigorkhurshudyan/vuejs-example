<template>
    <v-badge
            dot
            bordered
            bottom
            color="deep-purple accent-4"
            offset-x="10"
            offset-y="10"
    >
        <v-avatar size="40" :style="{background: statusAvatarBackground}">
            <v-img :src="statusAvatarSrc" :title="statusAvatarTitle"></v-img>
        </v-avatar>
    </v-badge>
</template>

<script>
    export default {
        name: "StatusAvatar",

        computed: {
            statusColor() {
                return status === "online" ? "green" : "gray";
            }
        },

        props: {
            statusAvatarSrc: {
                type: String,
                required: true
            },
            statusAvatarTitle: {
                type: String,
                required: true,
            },
            statusAvatarBackground: {
                type: String,
                required: true,
            }
        },

        data() {
            return {
                status: "online",
            }
        },
    }
</script>

<style scoped>

</style>