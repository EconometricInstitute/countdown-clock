<template>
  <v-container class="fill-height">
    <v-responsive class="d-flex align-center text-center fill-height">
      <v-row class="d-flex align-center justify-center">
        <v-col cols="auto">
          <h2>Time Left</h2>
        </v-col>
      </v-row>
      <v-row class="d-flex align-center justify-center">
        <v-col cols="auto">
          <div class="timer">{{ timeLeft }}</div>
        </v-col>
      </v-row>
    </v-responsive>
  </v-container>
</template>

<script lang="ts">
  import { defineComponent } from 'vue'

  export default defineComponent({
    props: {
      deadline: { type: Number, required: true}
    },
    data: () => ({
      currentTime: Date.now(),
      updateInterval: undefined as number|undefined
    }),
    methods: {
      updateCurrentTime() {
        this.currentTime = Date.now();
      }
    },
    mounted() {
      this.updateInterval = window.setInterval(this.updateCurrentTime, 1000);
    },
    unmounted() {
      if (this.updateInterval) {
        window.clearInterval(this.updateInterval);
        this.updateInterval = undefined;
      }
    },
    computed: {
      timeLeft() {
        const timeLeft:number = this.deadline - this.currentTime;
        const hours = Math.floor(timeLeft / 3600000);
        const minutes = Math.floor((timeLeft % 3600000) / 60000);
        const seconds = Math.floor((timeLeft % 60000) / 1000);
        return `${hours}:${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`;
      }
    }
  })
</script>

<style scoped>
  .timer {
    text-align: center;
    font-size: 18vw;
  }
</style>