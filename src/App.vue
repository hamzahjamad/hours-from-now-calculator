<template>
  <div class="container main">
    <ClockCalculator/>
  </div>
</template>

<script>
import ClockCalculator from './components/ClockCalculator.vue'

export default {
  name: 'app',
  components: {
    ClockCalculator
  }
}
</script>

<style>
 .main {
   padding-top: 30px;
 }
</style>
