<template>
  <div style="position: relative;">
    <div style="width: 100%;height: 100%;display: flex;flex-wrap: wrap;width: 100%;height: 100%;display: flex;flex-flow: column wrap;">
      <div
        v-for="data in anesDate"
        style="width:auto;max-width:300px;min-width:220px;font-size:14px;"
      >
        <span>{{data.ITEM_NAME}}</span>
        <span>{{data.ADMINISTRATOR}}</span>
        <span>{{data.DOSAGE}}</span>
        <span>{{data.DOSAGE_UNITS}}</span>
      </div>
    </div>

  </div>
</template>
<script>
import Bus from "@/bus.js";
export default {
  data() {
    return {
      anesDate: "",
      weight: ""
    };
  },
  methods: {
    getDate() {
      // 麻醉用药
      let paramsTwo = {
        patientId: this.config.userInfo.patientId,
        operId: this.config.userInfo.operId,
        visitId: this.config.userInfo.visitId,
        itemClass: "2",
        eventNo: this.config.eventNo
      };
      this.api.selectMedAnesthesiaEventList(paramsTwo).then(aff => {
        var sTime;
        var enTime;
        // console.log(aff)
        for (var a = 0; a < aff.list.length; a++) {
          debugger;
          // console.log(num)
          if (aff.list[a].ADMINISTRATOR == "泵注") {
            // console.log(a)
            var num = aff.list[a].SPEED_UNIT.toUpperCase();
            // console.log(num.indexOf("KG"))
            if (num.indexOf("KG") == -1) {
              // console.log(a+"@没有KG")
              // 没有KG
              if (num.indexOf("MIN") == -1) {
                // 单位是小时
                // console.log(a+"@单位小时")
                // this.config.patientMaxTime 最长时间
                if (aff.list[a].ENDDATE) {
                  sTime = new Date(aff.list[a].START_TIME).getTime();
                  enTime = new Date(aff.list[a].ENDDATE).getTime();
                  var min = "";
                  min = (enTime - sTime) / 1000 / 60 / 60;
                  // return Math.round(min)
                  // console.log(min)
                  var timeOF =
                    aff.list[a].PERFORM_SPEED * aff.list[a].PERFORM_SPEED * min;
                  this.$set(
                    aff.list[a],
                    "DOSAGE",
                    Math.floor(timeOF * 100) / 100
                  );
                } else {
                  sTime = new Date(aff.list[a].START_TIME).getTime();
                  enTime = new Date(this.config.patientMaxTime).getTime();
                  var min = "";
                  min = (enTime - sTime) / 1000 / 60 / 60;
                  // return Math.round(min)
                  // console.log(min)
                  var timeOF =
                    aff.list[a].PERFORM_SPEED * aff.list[a].PERFORM_SPEED * min;
                  this.$set(
                    aff.list[a],
                    "DOSAGE",
                    Math.floor(timeOF * 100) / 100
                  );
                }
              } else {
                // 单位是分钟
                // console.log(a+"@单位分钟..")
                if (aff.list[a].ENDDATE) {
                  // console.log("有结束时间")
                  sTime = new Date(aff.list[a].START_TIME).getTime();
                  enTime = new Date(aff.list[a].ENDDATE).getTime();
                  debugger;
                  var min = "";
                  min = (enTime - sTime) / 1000 / 60;
                  // return Math.round(min)
                  // console.log(min)
                  var timeOF =
                    aff.list[a].PERFORM_SPEED * aff.list[a].PERFORM_SPEED * min;
                  this.$set(
                    aff.list[a],
                    "DOSAGE",
                    Math.floor(timeOF * 100) / 100
                  );
                } else {
                  // console.log("没有结束时间")
                  sTime = new Date(aff.list[a].START_TIME).getTime();
                  enTime = new Date(this.config.patientMaxTime).getTime();
                  var min = "";
                  min = (enTime - sTime) / 1000 / 60;
                  // return Math.round(min)
                  // console.log(min)
                  // console.log(aff.list[a].PERFORM_SPEED,aff.list[a].PERFORM_SPEED,min)
                  var timeOF =
                    aff.list[a].PERFORM_SPEED * aff.list[a].PERFORM_SPEED * min;
                  this.$set(
                    aff.list[a],
                    "DOSAGE",
                    Math.floor(timeOF * 100) / 100
                  );
                }
              }
            } else {
              // console.log(a+"@有KG")
              // 有KG
              if (num.indexOf("MIN") == -1) {
                // 单位是小时
                // this.config.patientMaxTime 最长时间
                if (aff.list[a].ENDDATE) {
                  sTime = new Date(aff.list[a].START_TIME).getTime();
                  enTime = new Date(aff.list[a].ENDDATE).getTime();
                  var min = "";
                  min = (enTime - sTime) / 1000 / 60 / 60;
                  // return Math.round(min)
                  // console.log(min)
                  var timeOF =
                    aff.list[a].PERFORM_SPEED * aff.list[a].PERFORM_SPEED * min;
                  this.$set(
                    aff.list[a],
                    "DOSAGE",
                    Math.floor(timeOF * 100) / 100
                  );
                } else {
                  sTime = new Date(aff.list[a].START_TIME).getTime();
                  enTime = new Date(this.config.patientMaxTime).getTime();
                  var min = "";
                  min = (enTime - sTime) / 1000 / 60 / 60;
                  // return Math.round(min)
                  // console.log(min)
                  var timeOF =
                    aff.list[a].PERFORM_SPEED * aff.list[a].PERFORM_SPEED * min;
                  this.$set(
                    aff.list[a],
                    "DOSAGE",
                    Math.floor(timeOF * 100) / 100
                  );
                }
              } else {
                // 单位是分钟
                if (aff.list[a].ENDDATE) {
                  sTime = new Date(aff.list[a].START_TIME).getTime();
                  enTime = new Date(aff.list[a].ENDDATE).getTime();
                  var min = "";
                  min = (enTime - sTime) / 1000 / 60;
                  // return Math.round(min)
                  // console.log(min)
                  var timeOF =
                    aff.list[a].PERFORM_SPEED *
                    aff.list[a].PERFORM_SPEED *
                    min *
                    this.weight;
                  this.$set(
                    aff.list[a],
                    "DOSAGE",
                    Math.floor(timeOF * 100) / 100
                  );
                } else {
                  sTime = new Date(aff.list[a].START_TIME).getTime();
                  enTime = new Date(this.config.patientMaxTime).getTime();
                  var min = "";
                  min = (enTime - sTime) / 1000 / 60;
                  // return Math.round(min)
                  // console.log(min)
                  var timeOF =
                    aff.list[a].PERFORM_SPEED *
                    aff.list[a].PERFORM_SPEED *
                    min *
                    this.weight;
                  this.$set(
                    aff.list[a],
                    "DOSAGE",
                    Math.floor(timeOF * 100) / 100
                  );
                }
              }
            }
          }
        }
        // console.log(aff.list)
        this.anesDate = aff.list;
      });
    },
    getPation() {
      let params = {
        patientId: this.config.userInfo.patientId,
        operId: this.config.userInfo.operId,
        visitId: this.config.userInfo.visitId
      };
      this.api.getPatienDetailInfo(params).then(res => {
        this.weight = res.WEIGHT;
      });
    }
  },
  props: ["value", "page"],
  mounted() {
    this.getDate();
    this.getPation();
  }
};
</script>
<style scoped>
</style>
