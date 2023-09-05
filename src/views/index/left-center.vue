<script setup lang="ts">
import { ref, reactive } from "vue";
import { graphic } from "echarts/core";
import { POST,type Params } from "@/utils/axios";
import dayjs from 'dayjs';
let colors = ["#0BFC7F", "#A0A0A0", "#F48C02", "#F4023C"];
const option = ref({});
const state = reactive({
  through:0,// 直接访问
  link:0,// 外部链接
  search:0,// 搜索引擎
  totalNum:0
});
const echartsGraphic = (colors: string[]) => {
  return new graphic.LinearGradient(1, 0, 0, 0, [
    { offset: 0, color: colors[0] },
    { offset: 1, color: colors[1] },
  ]);
};
const getData = (param:Params) => {
  POST('/api/getSourceAll', param || {}).then(res => {
    if (res.code == 200) {
      let data = res.data.items[1].map((item:[number,number,number])=>item[0])
      state.through = data[0];
      state.link = data[1];
      state.search = data[2];
      state.totalNum  = data.reduce((pre:number,cur:number)=>{return pre + cur},0)
      setOption();
    }
  });
};
getData({
  start_data: dayjs().format("YYYYMMDD"),
  // start_data: dayjs().subtract(1, 'day').format("YYYYMMDD"),
  end_date: dayjs().format("YYYYMMDD")
});
const setOption = () => {
  option.value = {
    title: {
      top: "center",
      left: "center",
      text: [`{value|${state.totalNum}}`, "{name|总数}"].join("\n"),
      textStyle: {
        rich: {
          value: {
            color: "#ffffff",
            fontSize: 24,
            fontWeight: "bold",
            lineHeight: 20,
            padding:[4,0,4,0]
          },
          name: {
            color: "#ffffff",
            lineHeight: 20,
          },
        },
      },
    },
    tooltip: {
      trigger: "item",
      backgroundColor: "rgba(0,0,0,.6)",
      borderColor: "rgba(147, 235, 248, .8)",
      textStyle: {
        color: "#FFF",
      },
    },
    series: [
      {
        name: "浏览量(PV)",
        type: "pie",
        radius: ["40%", "70%"],
        // avoidLabelOverlap: false,
        itemStyle: {
          borderRadius: 6,
          borderColor: "rgba(255,255,255,0)",
          borderWidth: 2,
        },
        color: colors,
        label: {
          show: true,
          formatter: "   {b|{b}}   \n   {c|{c}}   {per|{d}%}  ",
          //   position: "outside",
          rich: {
            b: {
              color: "#fff",
              fontSize: 12,
              lineHeight: 26,
            },
            c: {
              color: "#31ABE3",
              fontSize: 14,
            },
            per: {
              color: "#31ABE3",
              fontSize: 14,
            },
          },
        },
        emphasis: {
          show: false,
        },
        legend: {
          show: false,
        },
        tooltip: { show: true },

        labelLine: {
          show: true,
          length: 20, // 第一段线 长度
          length2: 36, // 第二段线 长度
          smooth: 0.2,
          lineStyle: {},
        },
        data: [
          {
            value: state.through,
            name: "直接访问",
            itemStyle: {
              color: echartsGraphic(["#0BFC7F", "#A3FDE0"]),
            },
          },
          {
            value: state.link,
            name: "外部链接",
            itemStyle: {
              color: echartsGraphic(["#A0A0A0", "#DBDFDD"]),
            },
          },
          {
            value: state.search,
            name: "搜索引擎",
            itemStyle: {
              color: echartsGraphic(["#F48C02", "#FDDB7D"]),
            },
          },
          // {
          //   value: state.alarmNum,
          //   name: "异常",
          //   itemStyle: {
          //     color: echartsGraphic(["#F4023C", "#FB6CB7"]),
          //   },
          // },
        ],
      },
    ],
  };
};
</script>

<template>
  <v-chart class="chart" :option="option" />
</template>

<style scoped lang="scss"></style>
