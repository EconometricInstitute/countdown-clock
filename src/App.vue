<template>
  <v-app>
    <v-main>
      <CountdownClock v-if="deadline" :deadline="deadline" />
      <DeadlinePicker v-else />
    </v-main>
  </v-app>
</template>

<script lang="ts">
  import { defineComponent } from 'vue';
  import CountdownClock from '@/components/CountdownClock.vue'
  import DeadlinePicker from '@/components/DeadlinePicker.vue'

  const params = new URLSearchParams(window.location.search);
  const deadline = params.get('deadline');
  
  export default defineComponent({
    components: {
      CountdownClock,
      DeadlinePicker
    },
    data: () => ({
      deadline
    })
  });
</script>
