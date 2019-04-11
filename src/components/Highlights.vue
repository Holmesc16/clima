<template>
<div class="row">
  <div class="col s12 m4 l4">
    <uv-index :highlights="highlights"></uv-index>
  </div>
  <div class="col s12 m4 l4">  
    <visibility :highlights="highlights"></visibility>
  </div>
  <div class="col s12 m4 l4">
    <wind-status :highlights="highlights"></wind-status>
  </div>
</div>
</template>

<script>
import UVIndex from './UVIndex.vue';
import Visibility from './Visibility.vue';
import WindStatus from './WindStatus.vue';

export default {
  props: ["highlights"],
  components: {
    'uv-index': UVIndex,
    'visibility': Visibility,
    'wind-status': WindStatus,
  },
  data () {
    return {

    }
  },
  methods: {

  },
  computed: {

  },
}
</script>

<style>

</style>
