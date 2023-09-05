<script setup lang="ts">
import { reactive, ref } from "vue";
import { POST,type Params } from "@/utils/axios";
import dayjs from 'dayjs';
import CountUp from "@/components/count-up";
const duration = ref(2);
const state = reactive({
  pv_count: 0,
  visitor_count: 0,
  ip_count: 0
});
const getData = (param: Params) => {
  POST('/api/getTimeTrendRpt', param || {}).then(res => {
    if (res.code == 200) {
      const todayData = res.data.items[1][1]
      state.pv_count = todayData[0]
      state.visitor_count = todayData[1]
      state.ip_count = todayData[2]
    }
  });

};
getData({
  start_data: dayjs().subtract(1, 'day').format("YYYYMMDD"),
  end_date: dayjs().format("YYYYMMDD")
});
</script>

<template>
  <ul class="user_Overview flex">
    <li class="user_Overview-item" style="color: #00fdfa">
      <div class="user_Overview_nums allnum">
        <CountUp :endVal="state.pv_count" :duration="duration" />
      </div>
      <p>浏览量(PV)</p>
    </li>
    <li class="user_Overview-item" style="color: #07f7a8">
      <div class="user_Overview_nums online">
        <CountUp :endVal="state.visitor_count" :duration="duration" />
      </div>
      <p>访客数(UV)</p>
    </li>
    <li class="user_Overview-item" style="color: #e3b337">
      <div class="user_Overview_nums offline">
        <CountUp :endVal="state.ip_count" :duration="duration" />
      </div>
      <p>IP数</p>
    </li>
  </ul>
</template>

<style scoped lang="scss">
.left-top {
  width: 100%;
  height: 100%;
}

.user_Overview {
  li {
    flex: 1;

    p {
      text-align: center;
      height: 16px;
      font-size: 16px;
    }

    .user_Overview_nums {
      width: 100px;
      height: 100px;
      text-align: center;
      line-height: 100px;
      font-size: 22px;
      margin: 50px auto 30px;
      background-size: cover;
      background-position: center center;
      position: relative;

      &::before {
        content: "";
        position: absolute;
        width: 100%;
        height: 100%;
        top: 0;
        left: 0;
      }

      &.bgdonghua::before {
        animation: rotating 14s linear infinite;
      }
    }

    .allnum {
      &::before {
        background-image: url("@/assets/img/left_top_lan.png");
      }
    }

    .online {
      &::before {
        background-image: url("@/assets/img/left_top_lv.png");
      }
    }

    .offline {
      &::before {
        background-image: url("@/assets/img/left_top_huang.png");
      }
    }

    .laramnum {
      &::before {
        background-image: url("@/assets/img/left_top_hong.png");
      }
    }
  }
}
</style>
