<template>
  <div style="margin: 10px 30px;width: 1300px;padding-top:100px;">
    <div>
      <span>同步开始日期:</span>
      <input type="date" v-model="startTimeValue">
    </div>
    <div style="margin-top: 15px;">
      <span>同步结束日期:</span>
      <input type="date" v-model="endTimeValue">
    </div>
    <div style="margin-top: 10px;">
      <button @click="synchroSchedule">同步预约</button>
      <button @click="synchroUserAndDept">同步其他</button>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      startTimeValue: '',
      endTimeValue: '',
    }
  },
  methods: {
    synchroSchedule() {
      let params = {
        startDate: this.startTimeValue,
        endDate: this.endTimeValue
      }
      this.api.synchroSchedule(params)
        .then(res => {
          if(res.success){
             alert("同步完成");
          }
          else
          {
             alert("同步失败");
          }
         
        })

    },
    synchroUserAndDept() {
      let params = {

      }
      this.api.synchroUserAndDept(params)
        .then(res => {
          alert("同步完成");
        })
    },
    getDate() {
      let count = 3
      let day = new Date(this.startTimeValue)
      let targetday_milliseconds = day.getTime() + 1000 * 60 * 60 * 24 * count;
      day.setTime(targetday_milliseconds);
      let tYear = day.getFullYear();
      let tMonth = day.getMonth();
      let tDate = day.getDate();
      tMonth = this.doHandleMonth(tMonth + 1);
      tDate = this.doHandleMonth(tDate);
      this.endTimeValue = tYear + "-" + tMonth + "-" + tDate;
    },
    doHandleMonth(month) {
      var m = month;
      if (month.toString().length == 1) { m = "0" + month; }
      return m;
    },
  },
  mounted() {
    let curDate = new Date();
    this.startTimeValue = new Date(curDate.getTime() + 24*60*60*1000).Format("yyyy-MM-dd");
    this.getDate();
  }
}

</script>
<style scoped>


</style>
