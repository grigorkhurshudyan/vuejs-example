<template>
    <v-overlay
          :absolute="true"
          :opacity="0.6"
          :value="showing"
          :z-index="100"
        >
        <v-img
                src="@/assets/spinner.svg"
                class="spinning"
        ></v-img>
    </v-overlay>
</template>

<script>
    export default {
        name: "Spinner",
        props: ['showing']
    }
</script>

<style scoped>
    .spinning {
        animation-name: spin;
        animation-duration: 3000ms;
        animation-iteration-count: infinite;
        animation-timing-function: linear;
    }

    @keyframes spin {
        from {
            transform:rotate(0deg);
        }
        to {
            transform:rotate(360deg);
        }
    }
</style>