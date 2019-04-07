<template>
  <div>
    <div id="chart-container" v-if="tempVar">
            <fusioncharts 
            :type="tempChartData.chart.type" 
            :dataEmptyMessage="tempChartData.chart.empty"
            :width="tempChartData.chart.width"
            :dataFormat="tempChartData.chart.dataFormat"
            :dataSource="tempChartData"
            ></fusioncharts>
  </div>
  </div>
</template>

<script>
export default {
  props: ["tempVar"],
  components: {},
  data() {
    return {   
      tempChartData: {
        data: [],
        chart: {
        type: 'spline',
        width: '100%',
        dataFormat: 'json',
        empty:"i-https://i.postimg.cc/R0QCk9vV/Rolling-0-9s-99px.gif",
        caption: 'Hourly Temperature',
        captionFoldbold: '0',
        captionFontColor: '#5474fb',
        captionFontSize: '18',
        captionPadding: '30',
        baseFont: "'Avenir', Arial, Sans-serif",
        chartTopMargin: '20',
        showHoverEffect: '1',
        showAxisLines: '1',
        numberSuffix: '°F',
        anchorBigColor: '#5474fb',
        paletteColors: '#5474fb',
        drawCrossLine: '1',
        plotToolText: '$label<br><hr><b>$dataValue</b>',
        showYAxisValues: '1',
        yAxisMaxValue: '100',
        yAxisMinValue: '0',
        anchorRadius: '4',
        divLineAlpha: '0',
        labelFontSize: '13',
        labelAlpha: '65',
        labelFontBold: '0',
        rotateLabels: '1',
        slantLabels: '1',
        canvasPadding: '20',
        bgColor: '#ffffff',
        showBorder: '0',
        alternateHGridColor: '#dedede',
        xAxisLineColor: '#ffffff',
        yAxisLineColor: '#ffffff'
      }
      }
    }
  },
  methods: {
    setChartData: function() {
     var data = [];
     for (var i = 0; i < this.tempVar.tempToday.length; i++) {
       var dataObject = {
         label: this.tempVar.tempToday[i].hour,
         value: this.tempVar.tempToday[i].temp
       };
       data.push(dataObject);
     }
     this.tempChartData.data = data;
   },
  },
 mounted: function() {
   this.setChartData()
 },
 beforeUpdate: function() {
   this.setChartData()
 },
 updated: function() {
   this.setChartData()
 },
 watch: {
   tempVar: {
     handler: function() {
       this.setChartData();                                 
     },
     deep: true
   },
 }
};
</script>

