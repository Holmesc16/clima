<template>
  <div id="uvindex">
    <fusioncharts 
       :type="type"
       :width="width"
       :height="height"
       :containerbackgroundopacity="containerbackgroundopacity"
       :dataformat="dataformat"
       :datasource="datasource"
          ></fusioncharts>
  </div>
</template>

<script>
export default {
 props: ["highlights"],
 components: {},
 data() {
   return {
        type: 'angulargauge',
        renderAt: 'chart-container',
        width: '350',
        height: '200',
        dataFormat: 'json',
        datasource: {
          chart: {
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
          chartBottomMargin: "40",
          containerBackgroundCapacity: 0
          },
          colorrange: {
          color: [
            {
              minvalue: "0",
              maxvalue: this.highlights.uvIndex.toString(),
              code: "#7DA9E0"
            },
            {
              minvalue: this.highlights.uvIndex.toString(),
              maxvalue: "15",
              code: "#D8EDFF"
            }
          ]
        },
        annotations: {
          groups: [
            {
              items: [
                {
                  id: "val-label",
                  type: "text",
                  text: this.highlights.uvIndex.toString(),
                  fontSize: "20",
                  font: "Source Sans Pro",
                  fontBold: "1",
                  fillcolor: "#212529",
                  x: "$gaugeCenterX",
                  y: "$gaugeCenterY"
                }
              ]
            }
          ]
        },
        dials: {
          dial: [
            {
              value: this.highlights.uvIndex.toString(),
              baseWidth: "0",
              radius: "0",
              borderThickness: "0",
              baseRadius: "0"
            }
          ]
        }
     }
    }
 },
 methods: {},
 computed: {},
 watch: {
   highlights: {
     handler: function() {
       this.datasource.colorrange.color[0].maxvalue = this.highlights.uvIndex.toString();
       this.datasource.colorrange.color[1].minvalue = this.highlights.uvIndex.toString();
       this.datasource.annotations.groups[0].items[0].text = this.highlights.uvIndex.toString();
     },
     deep: true
   }
 }
};
</script>

<style>

</style>
