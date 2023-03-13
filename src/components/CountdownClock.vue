<template>
  <v-container class="fill-height">
    <v-responsive class="d-flex align-center text-center fill-height">
      <v-row class="d-flex align-center justify-center">
        <v-col cols="auto">
          <h2 :class="currentClass">{{ label }}</h2>
        </v-col>
      </v-row>
      <v-row class="d-flex align-center justify-center">
        <v-col cols="auto">
          <div class="timer" :class="currentClass">
            <div class="time-block">
              <div>{{ timeLeftHours }}</div>
              <div class="label">hours</div>
            </div>
            <div class="time-block">
              <div>:</div>
            </div>
            <div class="time-block">
              <div>{{ timeLeftMinutes }}</div>
              <div class="label">minutes</div>
            </div>
            <template v-if="showSeconds">
              <div class="time-block">
                <div>:</div>
              </div>
              <div class="time-block">
                <div>{{ timeLeftSeconds }}</div>
                <div class="label">seconds</div>
              </div>
            </template>
          </div>
        </v-col>
      </v-row>
    </v-responsive>
  </v-container>
</template>

<script lang="ts">
  import { defineComponent } from 'vue';

  export default defineComponent({
    props: {
      deadline: { type: Number, required: true},
      labelBefore: { type: String},
      labelAfter: { type: String},
      minutesRemainingToShowSeconds: { type: Number, default: 5 },
      minutesRemainingToShowWarning: {type: Number, default: 15},
      minutesRemainingToShowError: {type: Number, default: 0}
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
      timeLeft(): number {
        return this.deadline - this.currentTime;
      },
      minutesLeft(): number {
        return this.timeLeft / 60000;
      },
      timeLeftHours(): String {
        const hours = Math.abs(Math.floor(this.timeLeft / 3600000));
        return hours.toString();
      },
      timeLeftMinutes(): String {
        const minutes = Math.abs(Math.floor((this.timeLeft % 3600000) / 60000));
        return minutes.toString().padStart(2, '0');
      },
      timeLeftSeconds(): String {
        const seconds = Math.abs(Math.floor((this.timeLeft % 60000) / 1000));
        return seconds.toString().padStart(2, '0');
      },
      showSeconds(): boolean {
        return this.minutesLeft <= this.minutesRemainingToShowSeconds;
      },
      currentClass() {
        if (this.minutesLeft <= this.minutesRemainingToShowError) {
          return { 'text-error': true };
        }
        else if (this.minutesLeft <= this.minutesRemainingToShowWarning) {
          return { 'text-warning': true };
        }
        return {};
      },
      label(): String {
        if (this.timeLeft > 0) {
          return this.labelBefore ?? 'Time left';
        }
        return this.labelAfter ?? 'Time past deadline';
      }
    }
  })
</script>

<style scoped>
  .timer {
    text-align: center;
    font-size: 18vw;
    line-height: 19vw;
    display: flex;
  }
  .time-block {
    display: flex;
    flex-direction: column;
  }
  .label {
    font-size: 10pt;
    line-height: 12pt;
    font-variant: small-caps;
  }
</style>