<template>
    <div class="wow-email-card">
        <p class="wow-email">{{email}}</p>
        <p class="wow-details">{{type}}</p>
        <p class="wow-details">Added: {{dateString}}</p>
    </div>
</template>

<script>
    export default {
        name: "EmailCard",

        props: {
            email: {
                type: String,
                default: "Error: unresolved email"
            },
            type: {
                type: String,
                default: "Primary email"
            },
            addedDateTime: {
                type: String,
                default: Date.now()
            }
        },

        computed: {
            dateString() {
                return (new Date(this.addedDateTime)).toLocaleString();
            }
        }
    }
</script>

<style scoped lang="less">
    .wow-email-card {
        background: #edeff0;
        border-radius: 0.2em;
        box-sizing: border-box;
        display: inline-block;
        margin: 0.5em 0 0.5em 0;
        overflow: hidden;
        padding: 0.5em 1em;
        position: relative;
        width: auto;
        word-wrap: break-word;
        vertical-align: top;

        p {
            margin: 0.1em 0;
        }
    }

    .wow-email {
        color: black;
    }

    .wow-details {
        color: #8c8c8c
    }
</style>