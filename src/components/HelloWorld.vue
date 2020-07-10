<template>
  <div class="hello">
    <div class="layout-grids">
      <div class="layout-grid">
        <div ref="chartdiv"></div>
        <grid-layout
          :layout.sync="layout"
          :col-num="12"
          :row-height="30"
          :is-draggable="true"
          :is-resizable="true"
          :is-mirrored="false"
          :vertical-compact="true"
          :margin="[10, 10]"
          :use-css-transforms="true"
        >
          <grid-item
            v-for="item in layout"
            :x="item.x"
            :y="item.y"
            :w="item.w"
            :h="item.h"
            :i="item.i"
            :key="item.i"
          ></grid-item>
        </grid-layout>
      </div>
      <div class="layout-grid">
        <grid-layout
          :layout.sync="layout"
          :col-num="12"
          :row-height="30"
          :is-draggable="true"
          :is-resizable="true"
          :is-mirrored="false"
          :vertical-compact="true"
          :margin="[10, 10]"
          :use-css-transforms="true"
        >
          <grid-item
            v-for="item in layout"
            :x="item.x"
            :y="item.y"
            :w="item.w"
            :h="item.h"
            :i="item.i"
            :key="item.i"
          >{{item.i}}</grid-item>
        </grid-layout>
        <div class="layout-grid">
          <grid-layout
            :layout.sync="layout"
            :col-num="12"
            :row-height="30"
            :is-draggable="true"
            :is-resizable="true"
            :is-mirrored="false"
            :vertical-compact="true"
            :margin="[10, 10]"
            :use-css-transforms="true"
          >
            <grid-item
              v-for="item in chartLayout"
              :key="item.i"
              :static="item.static"
              :x="item.x"
              :y="item.y"
              :w="item.w"
              :h="item.h"
              :i="item.i"
              @resize="resize"
              @move="move"
              @resized="resized"
              @container-resized="containerResized"
              @moved="moved"
            >
              <custom-drag-element :text="item.i"></custom-drag-element>
            </grid-item>
          </grid-layout>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import VueGridLayout from "vue-grid-layout";
import CustomDragElement from "../components/CustomDragElement";
import * as am4core from "@amcharts/amcharts4/core";
import * as am4charts from "@amcharts/amcharts4/charts";
import am4themes_animated from "@amcharts/amcharts4/themes/animated";

am4core.useTheme(am4themes_animated);
var testLayout = [
  { x: 0, y: 0, w: 6, h: 6, i: "1" },
  { x: 0, y: 9, w: 6, h: 6, i: "2" },
  { x: 6, y: 0, w: 6, h: 6, i: "3" },
  { x: 6, y: 9, w: 6, h: 6, i: "4" }
];
var chartLayout = [{ x: 0, y: 0, w: 6, h: 6, i: "5" }];

export default {
  name: "HelloWorld",

  components: {
    GridLayout: VueGridLayout.GridLayout,
    GridItem: VueGridLayout.GridItem,
    CustomDragElement
  },
  data() {
    return {
      layout: testLayout,
      chartLayout: chartLayout
    };
  },
  mounted() {
    let chart = am4core.create(this.$refs.chartdiv, am4charts.XYChart);

    chart.paddingRight = 20;

    let data = [];
    let visits = 10;
    for (let i = 1; i < 366; i++) {
      visits += Math.round((Math.random() < 0.5 ? 1 : -1) * Math.random() * 10);
      data.push({
        date: new Date(2018, 0, i),
        name: "name" + i,
        value: visits
      });
    }

    chart.data = data;

    let dateAxis = chart.xAxes.push(new am4charts.DateAxis());
    dateAxis.renderer.grid.template.location = 0;

    let valueAxis = chart.yAxes.push(new am4charts.ValueAxis());
    valueAxis.tooltip.disabled = true;
    valueAxis.renderer.minWidth = 35;

    let series = chart.series.push(new am4charts.LineSeries());
    series.dataFields.dateX = "date";
    series.dataFields.valueY = "value";

    series.tooltipText = "{valueY.value}";
    chart.cursor = new am4charts.XYCursor();

    let scrollbarX = new am4charts.XYChartScrollbar();
    scrollbarX.series.push(series);
    chart.scrollbarX = scrollbarX;

    this.chart = chart;
  },

  beforeDestroy() {
    if (this.chart) {
      console.log("destoryed");
      this.chart.dispose();
    }
  },
  methods: {
    increaseWidth: function() {
      let width = document.getElementById("content").offsetWidth;
      width += 20;
      document.getElementById("content").style.width = width + "px";
    },
    decreaseWidth: function() {
      let width = document.getElementById("content").offsetWidth;
      width -= 20;
      document.getElementById("content").style.width = width + "px";
    },
    removeItem: function(item) {
      //console.log("### REMOVE " + item.i);
      this.layout.splice(this.layout.indexOf(item), 1);
    },
    addItem: function() {
      // let self = this;
      //console.log("### LENGTH: " + this.layout.length);
      let item = {
        x: 0,
        y: 0,
        w: 2,
        h: 2,
        i: this.index + "",
        whatever: "bbb"
      };
      this.index++;
      this.layout.push(item);
    },
    move: function(i, newX, newY) {
      console.log("MOVE i=" + i + ", X=" + newX + ", Y=" + newY);
    },
    resize: function(i, newH, newW, newHPx, newWPx) {
      console.log(
        "RESIZE i=" +
          i +
          ", H=" +
          newH +
          ", W=" +
          newW +
          ", H(px)=" +
          newHPx +
          ", W(px)=" +
          newWPx
      );
    },
    moved: function(i, newX, newY) {
      console.log("### MOVED i=" + i + ", X=" + newX + ", Y=" + newY);
    },
    resized: function(i, newH, newW, newHPx, newWPx) {
      console.log(
        "### RESIZED i=" +
          i +
          ", H=" +
          newH +
          ", W=" +
          newW +
          ", H(px)=" +
          newHPx +
          ", W(px)=" +
          newWPx
      );
    },
    containerResized: function(i, newH, newW, newHPx, newWPx) {
      console.log(
        "### CONTAINER RESIZED i=" +
          i +
          ", H=" +
          newH +
          ", W=" +
          newW +
          ", H(px)=" +
          newHPx +
          ", W(px)=" +
          newWPx
      );
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.layout-grids {
  display: grid;
  grid-template-columns: 1fr 3fr;
}
.vue-grid-item {
  background: #ccc;
}
</style>
