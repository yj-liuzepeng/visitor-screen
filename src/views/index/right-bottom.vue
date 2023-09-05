<script setup lang="ts">
import SeamlessScroll from "@/components/seamless-scroll";
import { computed, onMounted, reactive } from "vue";
import { useSettingStore } from "@/stores";
import { storeToRefs } from "pinia";
import { POST, type Params } from "@/utils/axios";
import dayjs from 'dayjs';
import EmptyCom from "@/components/empty-com";
const settingStore = useSettingStore();
const { defaultOption, indexConfig } = storeToRefs(settingStore);
const state = reactive<any>({
  list: [],
  defaultOption: {
    ...defaultOption.value,
    singleHeight: 252,
    limitScrollNum: 3,
    // step:3
  },
  scroll: true,

});

const getData = (param: Params) => {
  POST('/api/getVisitLandingpage', param || {}).then(res => {
    if (res.code == 200) {
      let pages = res.data.items[0]
      let datas = res.data.items[1]
      let data = pages.map((item: any, idx: number) => {
        return {
          id: item[0].pageId,
          url: item[0].name,
          visit_count: datas[idx][0],
          visitor_count: datas[idx][1],
          new_visitor_count: datas[idx][2],
          avg_visit_time:datas[idx][3]
        }
      })
      state.list = data;
    } else {
      window["$message"]({
        text: res.msg,
        type: "warning",
      });
    }
  });
};

const comName = computed(() => {
  if (indexConfig.value.rightBottomSwiper) {
    return SeamlessScroll;
  } else {
    return EmptyCom;
  }
});

const handleAddress = (item: any) => {
  return `${item.provinceName}/${item.cityName}/${item.countyName}`
}
onMounted(() => {
  getData({
    start_data: dayjs().subtract(6, 'day').format("YYYYMMDD"),
    end_date: dayjs().format("YYYYMMDD")
  });
});
</script>

<template>
  <div class="right_bottom_wrap beautify-scroll-def" :class="{ 'overflow-y-auto': !indexConfig.rightBottomSwiper }">
    <component :is="comName" :list="state.list" v-model="state.scroll" :singleHeight="state.defaultOption.singleHeight"
      :step="state.defaultOption.step" :limitScrollNum="state.defaultOption.limitScrollNum"
      :hover="state.defaultOption.hover" :singleWaitTime="state.defaultOption.singleWaitTime"
      :wheel="state.defaultOption.wheel">
      <ul class="right_bottom">
        <li class="right_center_item" v-for="(item, i) in state.list" :key="i">
          <span class="orderNum">{{ i + 1 }}</span>
          <div class="inner_right">
            <div class="dibu"></div>
            <div class="flex">
              <div class="info">
                <span class="labels">页面ID：</span>
                <span class="text-content zhuyao"> {{ item.id }}</span>
              </div>
              <div class="info">
                <span class="labels">访问次数：</span>
                <span class="text-content"> {{ item.visit_count }}</span>
              </div>
              <div class="info">
                <span class="labels">访客数：</span>
                <span class="text-content">
                  {{ item.visitor_count }}</span>
              </div>
            </div>

            <div class="flex">
              <div class="info">
                <span class="labels shrink-0"> 地址：</span>
                <span class=" ciyao truncate" style="font-size: 12px;width: 220px;" :title="handleAddress(item)">
                  {{ item.url }}</span>
              </div>
              <div class="info time shrink-0">
                <span class="labels">平均时长：</span>
                <span class="text-content" style="font-size: 12px">
                  {{ item.avg_visit_time }}s</span>
              </div>
            </div>
            <div class="flex">
              <div class="info">
                <span class="labels">新访客数量：</span>
                <span class="text-content warning">
                  {{ item.new_visitor_count || 0 }}</span>
              </div>
            </div>
          </div>
        </li>
      </ul>
    </component>
  </div>
</template>

<style scoped lang="scss">
.right_bottom {
  width: 100%;
  height: 100%;

  .right_center_item {
    display: flex;
    align-items: center;
    justify-content: center;
    height: auto;
    padding: 10px;
    font-size: 14px;
    color: #fff;

    .orderNum {
      margin: 0 20px 0 -20px;
    }

    .inner_right {
      position: relative;
      height: 100%;
      width: 400px;
      flex-shrink: 0;
      line-height: 1.5;

      .dibu {
        position: absolute;
        height: 2px;
        width: 104%;
        background-image: url("@/assets/img/zuo_xuxian.png");
        bottom: -12px;
        left: -2%;
        background-size: cover;
      }
    }

    .info {
      margin-right: 10px;
      display: flex;
      align-items: center;

      .labels {
        flex-shrink: 0;
        font-size: 12px;
        color: rgba(255, 255, 255, 0.6);
      }

      .zhuyao {
        color: $primary-color;
        font-size: 15px;
      }

      .ciyao {
        color: rgba(255, 255, 255, 0.8);
      }

      .warning {
        color: #e6a23c;
        font-size: 15px;
      }
    }
  }
}

.right_bottom_wrap {
  overflow: hidden;
  width: 100%;
  height: 252px;
}

.overflow-y-auto {
  overflow-y: auto;
}
</style>
