<template>
 <div>
  <div class="row">
    <div class="col s12">
        <temp-var-chart :tempVar="tempVar"></temp-var-chart>
    </div>
    </div>
        <today-highlights :highlights="highlights"></today-highlights>
  </div>
</template>

<script>
import TempVarChart from './TempVarChart.vue';
import Highlights from './Highlights.vue';

export default {
 props: ['highlights', 'tempVar'],
 components: {
   'temp-var-chart': TempVarChart,
   'today-highlights': Highlights
 },
}
</script>

<style>

</style>
