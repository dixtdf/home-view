<template>
    <div class="time-capsule">
        <div class="title">
            <hourglass-full
                :fill="['#efefef', '#00000020']"
                size="24"
                theme="two-tone"
            />
            <span>时光胶囊</span>
        </div>
        <span class="text"
        >今日已经度过了&nbsp;{{ timeData.day.elapsed }}&nbsp;小时</span
        >
        <el-progress
            :percentage="timeData.day.pass"
            :stroke-width="20"
            :text-inside="true"
        />
        <span class="text"
        >本周已经度过了&nbsp;{{ timeData.week.elapsed }}&nbsp;天</span
        >
        <el-progress
            :percentage="timeData.week.pass"
            :stroke-width="20"
            :text-inside="true"
        />
        <span class="text"
        >本月已经度过了&nbsp;{{ timeData.month.elapsed }}&nbsp;天</span
        >
        <el-progress
            :percentage="timeData.month.pass"
            :stroke-width="20"
            :text-inside="true"
        />
        <span class="text"
        >今年已经度过了&nbsp;{{ timeData.year.elapsed }}&nbsp;个月</span
        >
        <el-progress
            :percentage="timeData.year.pass"
            :stroke-width="20"
            :text-inside="true"
        />
        <div v-if="startDateText && store.siteStartShow">
            <span class="text" v-html="startDateText"/>
            <!-- <el-progress
              :show-text="false"
              :indeterminate="true"
              :stroke-width="6"
              :percentage="80"
              :duration="2"
            /> -->
        </div>
    </div>
</template>

<script setup>
import {onBeforeUnmount, onMounted, ref} from "vue";
import {HourglassFull} from "@icon-park/vue-next";
import {getTimeCapsule, siteDateStatistics} from "@/utils/getTime.js";
import {mainStore} from "@/store";

const store = mainStore();

// 进度条数据
let timeData = ref(getTimeCapsule());
let startDate = ref(import.meta.env.VITE_SITE_START);
let startDateText = ref(null);
let timeInterval = null;

onMounted(() => {
    timeInterval = setInterval(() => {
        timeData.value = getTimeCapsule();
        if (startDate.value)
            startDateText.value = siteDateStatistics(new Date(startDate.value));
    }, 1000);
});

onBeforeUnmount(() => {
    clearInterval(timeInterval);
});
</script>

<style lang="scss" scoped>
.time-capsule {
    width: 100%;

    .title {
        display: flex;
        flex-direction: row;
        align-items: center;
        margin: 0.2rem 0 1.5rem;
        font-size: 1.1rem;

        .i-icon {
            display: flex;
            justify-content: center;
            align-items: center;
            margin-right: 6px;
        }
    }

    .text {
        display: block;
        margin: 1rem 0rem 0.5rem 0rem;
        font-size: 0.95rem;
    }
}
</style>
