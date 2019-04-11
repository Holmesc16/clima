<template>
  <div id="uvindex">
    <fusioncharts 
            :type="gaugeChartData.chart.type" 
            :dataEmptyMessage="gaugeChartData.chart.empty"
            :width="gaugeChartData.chart.width"
            :dataFormat="gaugeChartData.chart.dataFormat"
            :dataSource="gaugeChartData"
          ></fusioncharts>
  </div>
</template>

<script>

export default {
  props: ["highlights"],
  data () {
    return {
      uvIndex: this.highlights.uvIndex,
      gaugeChartData:{
        data: '',
        chart: {
          type: 'angulargauge',
          renderAt: 'chart-container',
          width: '350',
          height: '200',
          dataFormat: 'json',
          caption: "UV Index",
         captionFontBold: "0",
         captionFontColor: "#000000",
         lowerLimit: "0",
         upperLimit: "15",
         lowerLimitDisplay: "0",
         upperLimitDisplay: "15",
         showValue: "1",
         theme: "fusion",
         baseFont: "'Avenir', Arial, Sans-Serif",
         bgAlpha: "0",
         canvasbgAlpha: "0",
         gaugeInnerRadius: "75",
         gaugeOuterRadius: "110",
         pivotRadius: "0",
         pivotFillAlpha: "0",
         valueFontSize: "20",
         valueFontColor: "#000000",
         valueFontBold: "1",
         tickValueDistance: "3",
         autoAlignTickValues: "1",
         majorTMAlpha: "20",
         chartTopMargin: "30",
         chartBottomMargin: "40"
        },
        
      }
    }
  },
  methods: {
    getUVIndex: function() {
     this.gaugeChartData.data = this.uvIndex
    }
  },
  created: function(){
    console.log('uv index', this.uvIndex)
    this.getUVIndex()
  },
  watch: {
    uvIndex: {
      handler: function() {
        console.log('uv index', this.uvIndex)
        this.getUVIndex()
      }
    }
  }
}
</script>

<style>

</style>
