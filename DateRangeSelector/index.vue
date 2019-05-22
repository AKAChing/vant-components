<template>
  <van-popup v-model="groupVisible" position="bottom" class="filter-group">
    <div class="date-range-selector" v-if="selectorType == 'range'">
      <div class="time-title van-hairline--bottom">
        <span class="item cancel" @click="groupVisible = false">取消</span>
        <span class="item title">起始时间</span>
        <span class="item confirm" @click="confirm">确定</span>
      </div>
      <div class="time-pickers">
        <div class="time-picker van-hairline--right">
          <div class="time-picker-title van-hairline--bottom">开始时间</div>
          <van-datetime-picker
            v-model="currentDate1"
            type="date"
            :min-date="minDate1"
            :max-date="maxDate1"
            :show-toolbar="false"
            :item-height="40"
            title="时间选择"
            @change="change1"
          />
        </div>
        <div class="time-picker">
          <div class="time-picker-title van-hairline--bottom">结束时间</div>
          <van-datetime-picker
            v-model="currentDate2"
            type="date"
            :min-date="minDate2"
            :max-date="maxDate2"
            :show-toolbar="false"
            :item-height="40"
            title="时间选择"
          />
        </div>
      </div>
    </div>
    <div class="time-shortcut-selector" v-if="selectorType == 'shortcut'">
      <div
        class="cell-box van-hairline--bottom"
        v-for="(item,index) in shortcuts"
        :key="index"
        @click="selectShorcut(item)"
      >{{ item.name }}</div>
      <div class="cell-box cell-box-cancel van-hairline--top" @click="groupVisible = false">取 消</div>
    </div>
  </van-popup>
</template>

<script>
import { getTimeRange } from "@/utils";
export default {
  name: "date-range-selector",
  data() {
    return {
      currentDate1: new Date(),
      currentDate2: new Date(),
      minDate1: new Date(2019, 0, 1),
      minDate2: new Date(),
      maxDate1: new Date(),
      maxDate2: new Date(),
      groupVisible: false,
      shortcuts: [
        { name: "今 天", num: 0 },
        { name: "最近一周", num: -7 },
        { name: "最近一个月", num: -30 },
        { name: "最近三个月", num: -90 },
        { name: "最近半年", num: -180 },
        { name: "最近一年", num: -365 }
      ]
    };
  },
  props: {
    selectorType: {
      type: String,
      default: "range"
    }
  },
  methods: {
    open() {
      this.groupVisible = true;
    },
    change1(picker) {
      let time = picker.getValues();
      this.minDate2 = new Date(time[0], time[1] - 1, time[2]);
    },
    confirm(date) {
      this.groupVisible = false;
      let start_time =
        Number(this.currentDate1.getFullYear()) +
        "-" +
        Number(this.currentDate1.getMonth() + 1) +
        "-" +
        Number(this.currentDate1.getDate());
      let end_time =
        Number(this.currentDate2.getFullYear()) +
        "-" +
        Number(this.currentDate2.getMonth() + 1) +
        "-" +
        Number(this.currentDate2.getDate());
      this.$emit("get-date-range", [start_time, end_time]);
    },
    selectShorcut(item) {
      this.groupVisible = false;
      this.$emit("get-date-range", getTimeRange(item.num));
    }
  }
};
</script>
<style lang="scss" scoped>
.filter-group {
  .date-range-selector {
    .time-title {
      display: flex;
      line-height: 40px;
      justify-content: center;
      align-items: center;
      .item {
        flex: 1;
        color: #1989fa;
        padding: 0 20px;
      }
      .cancel {
        text-align: left;
      }
      .confirm {
        text-align: right;
      }
      .title {
        text-align: center;
        color: #2c3e50;
      }
    }
    .time-pickers {
      display: flex;
      .time-picker {
        flex: 1;
        .time-picker-title {
          text-align: center;
          line-height: 40px;
        }
      }
    }
  }
  .time-shortcut-selector {
    background-color: #fafafa;
    text-align: center;
    .cell-box {
      line-height: 40px;
      background-color: #fff;
      &:hover {
        background-color: #fafafa;
      }
    }
    .cell-box-cancel {
      margin-top: 10px;
    }
  }
}
</style>