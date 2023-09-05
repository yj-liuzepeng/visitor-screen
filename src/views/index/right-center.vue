<script setup lang="ts">
import { ref } from "vue";
import CapsuleChart from "@/components/datav/capsule-chart";
import { POST, type Params } from "@/utils/axios";
import dayjs from 'dayjs';
const config = ref({
  showValue: true,
  unit: "次",
});
const data = ref([])
const getData = (param: Params) => {
  POST('/api/getVisitToppage', param || {}).then(res => {
    if (res.code == 200) {
      let pages = res.data.items[0].map((item: any) => item[0].name)
      let nums = res.data.items[1].map((item: any) => item[0])
      let pagedata = pages.map((item: any, idx: number) => {
        return {
          name: item.length>25 ? item.slice(0,25)+'...' :item,
          value: nums[idx]
        }
      })
      if(pagedata.length>8) {
        pagedata = pagedata.slice(0,8)
      }
      data.value = pagedata;
    } else {
      window["$message"]({
        text: res.msg,
        type: "warning",
      });
    }
  });
};
getData({
  start_data: dayjs().subtract(6, 'day').format("YYYYMMDD"),
  end_date: dayjs().format("YYYYMMDD")
});
</script>

<template>
  <div class="right_bottom">
    <CapsuleChart :config="config" style="width: 100%; height: 260px" :data="data" />
  </div>
</template>

<style scoped lang="scss">
.right_bottom {
  box-sizing: border-box;
  padding: 0 16px;
}
</style>
