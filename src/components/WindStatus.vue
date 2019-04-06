<template>
  <div>
  </div>
</template>

<script>

export default {
  props: ["highlights"],
  data () {
    return {
      windSpeed: this.highlights.windStatus.windSpeed,
      derivedWindDirection: this.highlights.windStatus.derivedWindDirection,
      windDirection: this.highlights.windStatus.windDirection
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
