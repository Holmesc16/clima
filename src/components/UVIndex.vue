<template>
  <div id="uvindex">
    <fusioncharts 
       :type="type"
       :width="width"
       :height="height"
<<<<<<< HEAD
       :containerbackgroundopacity="containerbackgroundopacity"
       :dataformat="dataformat"
=======
       :containerbackgroundopacity="0"
       :dataformat="dataFormat"
>>>>>>> f86c26b5d7b536718a7afeb0c0a388a20a33aa89
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
<<<<<<< HEAD
        height: '200',
=======
        height: '275',
>>>>>>> f86c26b5d7b536718a7afeb0c0a388a20a33aa89
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
<<<<<<< HEAD
          showValue: "1",
=======
>>>>>>> f86c26b5d7b536718a7afeb0c0a388a20a33aa89
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
<<<<<<< HEAD
              code: "#7DA9E0"
=======
              code: "#ffcc22"
>>>>>>> f86c26b5d7b536718a7afeb0c0a388a20a33aa89
            },
            {
              minvalue: this.highlights.uvIndex.toString(),
              maxvalue: "15",
<<<<<<< HEAD
              code: "#D8EDFF"
=======
              code: "#fff022"
>>>>>>> f86c26b5d7b536718a7afeb0c0a388a20a33aa89
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
<<<<<<< HEAD
                  text: this.highlights.uvIndex.toString(),
                  fontSize: "20",
                  font: "Source Sans Pro",
=======
                   text: this.highlights.uvIndex.toString(),
                  fontSize: "20",
                  font: "'Josefin Sans', Helvetica, Arial",
>>>>>>> f86c26b5d7b536718a7afeb0c0a388a20a33aa89
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
