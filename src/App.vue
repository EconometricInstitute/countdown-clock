<template>
  <v-app>
    <v-main>
      <CountdownClock :deadline="Date.now()+3600000" />
    </v-main>
  </v-app>
</template>

<script setup lang="ts">
  import CountdownClock from '@/components/CountdownClock.vue'
</script>
