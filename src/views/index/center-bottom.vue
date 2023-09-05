<script setup lang="ts">
import { ref, onMounted } from "vue";
import {graphic} from "echarts/core"
import { POST, type Params } from "@/utils/axios";
import dayjs from 'dayjs';
const option = ref({});
const getData = (param:Params) => {
  POST('/api/getDistricta', param || {}).then(res => {
    if (res.code == 200) {
      let category = res.data.items[0].map((item:any)=>item[0].name)
      let barData = res.data.items[1].map((item:any)=>item[0])
      let lineData = res.data.items[1].map((item:any)=>item[3])
      let rateData = res.data.items[1].map((item:any)=>item[1])
      let data = {category,barData,lineData,rateData}
      setOption(data);
    } else {
      window["$message"]({
        text: res.msg,
        type: "warning",
      });
    }
  });
};
const setOption =async (newData: any) => {
  option.value = {
    tooltip: {
      trigger: "axis",
      backgroundColor: "rgba(0,0,0,.6)",
      borderColor: "rgba(147, 235, 248, .8)",
      textStyle: {
        color: "#FFF",
      },
      formatter: function (params: any) {
        // 添加单位
        var result = params[0].name + "<br>";
        params.forEach(function (item: any) {
          if (item.value) {
            if (item.seriesName == "浏览量占比") {
              result +=
                item.marker +
                " " +
                item.seriesName +
                " : " +
                item.value +
                "%</br>";
            } else {
              result +=
                item.marker +
                " " +
                item.seriesName +
                " : " +
                item.value +
                "次</br>";
            }
          } else {
            result += item.marker + " " + item.seriesName + " :  - </br>";
          }
        });
        return result;
      },
    },
    legend: {
      data: ["浏览量(PV)", "IP 数", "浏览量占比"],
      textStyle: {
        color: "#B4B4B4",
      },
      top: "0",
    },
    grid: {
      left: "50px",
      right: "40px",
      bottom: "30px",
      top: "20px",
    },
    xAxis: {
      data: newData.category,
      axisLine: {
        lineStyle: {
          color: "#B4B4B4",
        },
      },
      axisTick: {
        show: false,
      },
    },
    yAxis: [
      {
        splitLine: { show: false },
        axisLine: {
          lineStyle: {
            color: "#B4B4B4",
          },
        },

        axisLabel: {
          formatter: "{value}",
        },
      },
      {
        splitLine: { show: false },
        axisLine: {
          lineStyle: {
            color: "#B4B4B4",
          },
        },
        axisLabel: {
          formatter: "{value}% ",
        },
      },
    ],
    series: [
      {
        name: "浏览量(PV)",
        type: "bar",
        barWidth: 10,
        itemStyle: {
          borderRadius: 5,
          color: new graphic.LinearGradient(0, 0, 0, 1, [
            { offset: 0, color: "#956FD4" },
            { offset: 1, color: "#3EACE5" },
          ]),
        },
        data: newData.barData,
      },
      {
        name: "IP 数",
        type: "bar",
        barGap: "-100%",
        barWidth: 10,
        itemStyle: {
          borderRadius: 5,
          color: new graphic.LinearGradient(0, 0, 0, 1, [
            { offset: 0, color: "rgba(156,107,211,0.8)" },
            { offset: 0.2, color: "rgba(156,107,211,0.5)" },
            { offset: 1, color: "rgba(156,107,211,0.2)" },
          ]),
        },
        z: -12,
        data: newData.lineData,
      },
      {
        name: "浏览量占比",
        type: "line",
        smooth: true,
        showAllSymbol: true,
        symbol: "emptyCircle",
        symbolSize: 8,
        yAxisIndex: 1,
        itemStyle: {
          color: "#F02FC2",
        },
        data: newData.rateData,
      },
    ],
  };
};
onMounted(()=>{
  getData({
    start_data: dayjs().subtract(6, 'day').format("YYYYMMDD"),
    end_date: dayjs().format("YYYYMMDD")
  });

})
</script>

<template>
  <v-chart class="chart" :option="option" v-if="JSON.stringify(option)!='{}'"/>
</template>

<style scoped lang="scss"></style>
