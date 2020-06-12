<template>
  <div id="tempdiv" style="opacity: 0;width:100%;height:100%;display:flex;flex-direction:column;flex-wrap:wrap;-webkit-flex-wrap:wrap;position:relative;overflow:hidden;">
    <div v-if="page == false" :title="item.titleWord" v-for="(item,index) in dataBody" :id="item.idName" style="opacity: 1;width:auto;max-width:201px;min-width:201px;font-size:13px;padding:0 20px 0px 0;display:flex;cursor:default;border-left:1px solid rgba(22,22,22,0);">
      <span v-if="item.itemNom" style="padding-right:0px;width:20px;display:block;">{{item.itemNom}}.</span>
      <span v-if="item.ADDTIME" style="padding-right:5px;">{{item.ADDTIME}}</span>
      {{item.ITEM_NAME}} {{item.DOSAGE}} {{item.DOSAGE_UNITS}} {{item.ADMINISTRATOR}}
    </div>
    <div v-else>
    </div>
    <div v-if="firstShow" style="opacity: 0;position:absolute;top:0px;left:0;width:100%;height:100%;display:flex;flex-direction:column;flex-wrap:wrap;-webkit-flex-wrap:wrap;">
      <div v-if="page == false" v-for="(abc,index) in dataOfEveryTime" :id="abc.idName" style="width:auto;max-width:201px;min-width:201px;font-size:13px;padding:0 20px 0px 0;display:flex;cursor:default;border-left:1px solid rgba(22,22,22,0);">
        <span v-if="abc.itemNom" style="padding-right:0px;width:20px;display:block;">{{abc.itemNom}}.</span>
        <span v-if="abc.ADDTIME" style="padding-right:5px;">{{abc.ADDTIME}}</span>
        {{abc.ITEM_NAME}} {{abc.DOSAGE}} {{abc.DOSAGE_UNITS}} {{abc.ADMINISTRATOR}}
      </div>
    </div>
  </div>
</template>
<script>
import Bus from "@/bus.js";
export default {
  data() {
    return {
      dataBody: [],
      tipTop: "",
      tipLeft: "",
      tipView: false,
      // showDataMore:'',
      maxTimeInPage: "",
      startTimeInPage: "",
      setTimeId: "", // 定时器执行
      dataOfEveryTime: [],
      everyMaxTime: "", // 每一个时间段的结束时间
      everyStartTime: "", // 每一个时间段的开始时间
      allPageNum: 1,
      readClear: false,
      firstShow: true,
      opacityData: false
    };
  },
  methods: {
    selectMedAnesthesiaEventList() {
      // this.opacityData = false;
      let masterData = [];
      let masterDataSz = [];
      this.maxTimeInPage = new Date(this.config.maxTime).getTime();
      this.startTimeInPage = new Date(this.config.initTime).getTime();
      // console.log(this.startTimeInPage)
      let params = {
        patientId: this.config.userInfo.patientId,
        operId: this.config.userInfo.operId,
        visitId: this.config.userInfo.visitId,
        itemClass: "yp",
        eventNo: this.config.eventNo
      };
      if (this.setTimeId) {
        clearTimeout(this.setTimeId);
      }
      this.api.selectMedAnesthesiaEventList(params).then(res => {
        // console.log(res)
        let title = "";
        this.dataBody = [];
        masterData.push({
          ITEM_NAME: "《术中用药》"
        });
        for (var i = 0; i < res.list.length; i++) {
          if (
            res.list[i].ITEM_CLASS == "C" ||
            res.list[i].ITEM_CLASS == "2" ||
            res.list[i].ITEM_CLASS == "3" ||
            res.list[i].ITEM_CLASS == "B" ||
            res.list[i].ITEM_CLASS == "4"
          ) {
          } else {
            var time = new Date(res.list[i].START_TIME).getTime();
            if (this.startTimeInPage <= time && time <= this.maxTimeInPage) {
              this.$set(res.list[i], "sort", time);
              if (res.list[i].START_TIME) {
                var nameDate = res.list[i].START_TIME.split(" ");
                var nextTime = nameDate[1].split(":");
                // console.log(nextTime)
              } else {
                var nextTime = ["", ""];
              }
              if (res.list[i].ENDDATE) {
                var nameDateFir = res.list[i].ENDDATE.split(" ");
                var nextTimeFir = nameDateFir[1].split(":");
              } else {
                var nameDateFir = this.config.patientMaxTime.split(" ");
                var nextTimeFir = nameDateFir[1].split(":");
              }

              if (res.list[i].DOSAGE !== null) {
                if (res.list[i].DURATIVE_INDICATOR == 1) {
                  if (res.list[i].ADMINISTRATOR !== null) {
                    var titleData = [
                      res.list[i].ITEM_NAME,
                      "================",
                      "开始时间：" + res.list[i].START_TIME,
                      "结束时间：" + res.list[i].ENDDATE,
                      "途径：" + res.list[i].ADMINISTRATOR,
                      "量：" + res.list[i].DOSAGE,
                      "单位：" + res.list[i].DOSAGE_UNITS
                    ];
                  } else {
                    var titleData = [
                      res.list[i].ITEM_NAME,
                      "================",
                      "开始时间：" + res.list[i].START_TIME,
                      "结束时间：" + res.list[i].ENDDATE,
                      "量：" + res.list[i].DOSAGE,
                      "单位：" + res.list[i].DOSAGE_UNITS
                    ];
                  }
                } else {
                  if (res.list[i].ADMINISTRATOR !== null) {
                    var titleData = [
                      res.list[i].ITEM_NAME,
                      "================",
                      "开始时间：" + res.list[i].START_TIME,
                      "途径：" + res.list[i].ADMINISTRATOR,
                      "量：" + res.list[i].DOSAGE,
                      "单位：" + res.list[i].DOSAGE_UNITS
                    ];
                  } else {
                    var titleData = [
                      res.list[i].ITEM_NAME,
                      "================",
                      "开始时间：" + res.list[i].START_TIME,
                      "量：" + res.list[i].DOSAGE,
                      "单位：" + res.list[i].DOSAGE_UNITS
                    ];
                  }
                }
                title = titleData.join("\n");
                this.$set(res.list[i], "titleWord", title);
              } else {
                var titleData = [
                  res.list[i].ITEM_NAME,
                  "================",
                  "开始时间：" + res.list[i].START_TIME
                ];
                title = titleData.join("\n");
                this.$set(res.list[i], "titleWord", title);
              }
              masterData.push({
                ADDTIME:
                nextTime[0] +
                ":" +
                nextTime[1] +
                "→" +
                nextTimeFir[0] +
                ":" +
                nextTimeFir[1],
                ITEM_NAME: res.list[i].ITEM_NAME,
                DOSAGE: res.list[i].DOSAGE,
                DOSAGE_UNITS: res.list[i].DOSAGE_UNITS,
                titleWord: res.list[i].titleWord,
                ADMINISTRATOR: res.list[i].ADMINISTRATOR,
                sort: time
              });
            }
          }
        }
        // 入量
        let paramsTwo = {
          patientId: this.config.userInfo.patientId,
          operId: this.config.userInfo.operId,
          visitId: this.config.userInfo.visitId,
          itemClass: "3B",
          eventNo: this.config.eventNo
        };
        this.api.selectMedAnesthesiaEventList(paramsTwo).then(zze => {
          // console.log(zze.list)

          if (zze.list.length > 6) {
            let newList = [];
            for (var ahd = 0; ahd < zze.list.length; ahd++) {
              var time3B = new Date(zze.list[ahd].START_TIME).getTime();
              if (
                this.startTimeInPage <= time3B &&
                time3B <= this.maxTimeInPage
              ) {
                newList.push(zze.list[ahd]);
              }
            }
            // debugger
            if (newList.length > 6) {
              for (var sameO = 0; sameO < 6; sameO++) {
                for (var nextOne = 0; nextOne < zze.list.length; nextOne++) {
                  if (
                    zze.list[sameO].ITEM_NAME == zze.list[nextOne].ITEM_NAME &&
                    sameO !== nextOne
                  ) {
                    newList.splice(sameO - 6, 1);
                  }
                }
              }
              // console.log(newList)

              // for (var ti = 0; ti < 6; ti++) {
              for (var t = 6; t < newList.length; t++) {
                // if (newList[ti].ITEM_NAME !== newList[t].ITEM_NAME && newList[ti].ITEM_CLASS !== newList[t].ITEM_CLASS) {
                if (newList[t].START_TIME) {
                  var nameDate1 = newList[t].START_TIME.split(" ");
                  var nextTime1 = nameDate1[1].split(":");
                } else {
                  var nextTime1 = ["", ""];
                }
                if (newList[t].ENDDATE) {
                  var nameDate12 = newList[t].ENDDATE.split(" ");
                  var nextTime12 = nameDate12[1].split(":");
                } else {
                  var nameDate12 = this.config.patientMaxTime.split(" ");
                  var nextTime12 = nameDate12[1].split(":");
                }
                var timeMoreOne = new Date(newList[t].START_TIME).getTime();
                if (
                  this.startTimeInPage <= timeMoreOne &&
                  timeMoreOne <= this.maxTimeInPage
                ) {
                  // if (newList[ti].ITEM_NAME == newList[t].ITEM_NAME && ti !== t) {
                  // newList.splice(ti, 1)
                  // } else {
                  var titleDataTwo = [
                    newList[t].ITEM_NAME,
                    "================",
                    "时间：" + newList[t].START_TIME
                  ];
                  var titleTwo = titleDataTwo.join("\n");
                  masterData.push({
                    ADDTIME:
                    nextTime1[0] +
                    ":" +
                    nextTime1[1] +
                    "→" +
                    nextTime12[0] +
                    ":" +
                    nextTime12[1],
                    ITEM_NAME: newList[t].ITEM_NAME,
                    START_TIME: newList[t].START_TIME,
                    titleWord: titleTwo,
                    DOSAGE: newList[t].DOSAGE,
                    DOSAGE_UNITS: newList[t].DOSAGE_UNITS,
                    ADMINISTRATOR: newList[t].ADMINISTRATOR,
                    sort: timeMoreOne
                  });
                  // }
                }
                // } else {

                // }
              }
            }
            // break;
            // }
          } else {
          }
          // 出量
          let paramsTwo = {
            patientId: this.config.userInfo.patientId,
            operId: this.config.userInfo.operId,
            visitId: this.config.userInfo.visitId,
            itemClass: "D",
            eventNo: this.config.eventNo
          };
          this.api.selectMedAnesthesiaEventList(paramsTwo).then(cld => {
            // console.log(cld)
            let newList = [];
            if (cld.length > 3) {
              for (var ahd = 0; ahd < cld.list.length; ahd++) {
                var timeCl = new Date(cld.list[ahd].START_TIME).getTime();
                if (
                  this.startTimeInPage <= timeCl &&
                  timeCl <= this.maxTimeInPage
                ) {
                  newList.push(cld.list[ahd]);
                }
              }
              for (var clNo1 = 0; clNo1 < 3; clNo1++) {
                for (var clNo2 = 0; clNo2 < newList.length; clNo2++) {
                  if (
                    newList[clNo1].ITEM_NAME == newList[clNo2].ITEM_NAME &&
                    clNo1 !== clNo2
                  ) {
                    newList.splice(clNo1, 1);
                  }
                }
              }
              for (var cd = 3; cd < newList.length; cd++) {
                var timeClDate = new Date(newList[cd].START_TIME).getTime();

                if (
                  this.startTimeInPage <= timeClDate &&
                  timeClDate <= this.maxTimeInPage
                ) {
                  // if (newList[ti].ITEM_NAME == newList[cd].ITEM_NAME && ti !== t) {
                  // newList.splice(ti, 1)
                  // } else {
                  if (newList[cd].START_TIME) {
                    var nameDate21 = newList[d].START_TIME.split(" ");
                    var nextTime21 = nameDate21[1].split(":");
                  } else {
                    var nameDate21 = ["", ""];
                  }
                  if (newList[cd].ENDDATE) {
                    var nameDate22 = newList[cd].ENDDATE.split(" ");
                    var nextTime22 = nameDate22[1].split(":");
                  } else {
                    var nameDate22 = this.config.patientMaxTime.split(" ");
                    var nextTime22 = nameDate22[1].split(":");
                  }
                  var titleDataClTwo = [
                    newList[cd].ITEM_NAME,
                    "================",
                    "时间：" + newList[cd].START_TIME
                  ];
                  var titleCl = titleDataClTwo.join("\n");
                  masterData.push({
                    ADDTIME:
                    nextTime21[0] +
                    ":" +
                    nextTime21[1] +
                    "→" +
                    nextTime22[0] +
                    ":" +
                    nextTime22[1],
                    ITEM_NAME: newList[cd].ITEM_NAME,
                    START_TIME: newList[cd].START_TIME,
                    titleWord: titleCl,
                    DOSAGE: newList[cd].DOSAGE,
                    DOSAGE_UNITS: newList[cd].DOSAGE_UNITS,
                    ADMINISTRATOR: newList[cd].ADMINISTRATOR,
                    sort: timeClDate
                  });
                  // }
                }
              }
            }

            // 麻醉用药
            let paramsTwo = {
              patientId: this.config.userInfo.patientId,
              operId: this.config.userInfo.operId,
              visitId: this.config.userInfo.visitId,
              itemClass: "2C4",
              eventNo: this.config.eventNo
            };
            this.api.selectMedAnesthesiaEventList(paramsTwo).then(aff => {
              let pushCXData = [];
              // console.log(aff)
              // debugger
              for (var h = 0; h < aff.list.length; h++) {
                if (
                  aff.list[h].DURATIVE_INDICATOR == 0 ||
                  aff.list[h].DURATIVE_INDICATOR == ""
                ) {
                  if (aff.list[h].START_TIME) {
                    var nameDate2 = aff.list[h].START_TIME.split(" ");
                    var nextTime2 = nameDate2[1].split(":");
                  } else {
                    var nextTime2 = ["", ""];
                  }
                  // var timeMoreNEXT = new Date(aff.list[h].START_TIME).getTime();
                  // pushCXData.push(aff.list[h])
                  var timeMoreOne = new Date(aff.list[h].START_TIME).getTime();
                  if (
                    this.startTimeInPage <= timeMoreOne &&
                    timeMoreOne <= this.maxTimeInPage
                  ) {
                    // var titleDataTwo = [aff.list[h].ITEM_NAME, '================', '开始时间：' + aff.list[h].START_TIME];
                    var titleDataTwo = [
                      aff.list[h].ITEM_NAME,
                      "================",
                      "开始时间：" + aff.list[h].START_TIME,
                      "途径：" + aff.list[h].ADMINISTRATOR,
                      "量：" + aff.list[h].DOSAGE,
                      "单位：" + aff.list[h].DOSAGE_UNITS
                    ];
                    var titleTwo = titleDataTwo.join("\n");
                    masterData.push({
                      ADDTIME: nextTime2[0] + ":" + nextTime2[1],
                      ADMINISTRATOR: aff.list[h].ADMINISTRATOR,
                      ITEM_NAME: aff.list[h].ITEM_NAME,
                      START_TIME: aff.list[h].START_TIME,
                      titleWord: titleTwo,
                      sort: timeMoreOne,
                      DOSAGE: aff.list[h].DOSAGE,
                      DOSAGE_UNITS: aff.list[h].DOSAGE_UNITS
                    });
                  }
                } else if (aff.list[h].DURATIVE_INDICATOR == 1) {
                  var timeMoreNEXT = new Date(aff.list[h].START_TIME).getTime();
                  if (
                    this.startTimeInPage <= timeMoreNEXT &&
                    timeMoreNEXT <= this.maxTimeInPage
                  ) {
                    pushCXData.push(aff.list[h]);
                  }
                }
              }
              if (pushCXData.length > 8) {
                // 判断是否拥有同一种用药
                for (var qe = 0; qe < 8; qe++) {
                  for (var ugr = 8; ugr < pushCXData.length; ugr++) {
                    if (pushCXData[qe].ITEM_NAME == pushCXData[ugr].ITEM_NAME && qe !== ugr
                    ) {
                      pushCXData.splice(qe, 1);
                    }
                  }
                }
                for (var d = 8; d < pushCXData.length; d++) {
                  // if (pushCXData[d].DURATIVE_INDICATOR == 1) {
                  if (pushCXData[d].START_TIME) {
                    var nameDate3 = pushCXData[d].START_TIME.split(" ");
                    var nextTime3 = nameDate3[1].split(":");
                  } else {
                    var nameDate3 = ["", ""];
                  }
                  if (pushCXData[d].ENDDATE) {
                    var nameDate4 = pushCXData[d].ENDDATE.split(" ");
                    var nextTime4 = nameDate4[1].split(":");
                  } else {
                    var nameDate4 = this.config.patientMaxTime.split(" ");
                    var nextTime4 = nameDate4[1].split(":");
                  }
                  var timeMoreOne = new Date(
                    pushCXData[d].START_TIME
                  ).getTime();
                  if (
                    this.startTimeInPage <= timeMoreOne &&
                    timeMoreOne <= this.maxTimeInPage
                  ) {
                    // var titleDataTwo = [pushCXData[d].ITEM_NAME, '================', '开始时间：' + pushCXData[d].START_TIME];
                    if (!pushCXData[d].ENDDATE) {
                      var titleDataTwo = [
                        pushCXData[d].ITEM_NAME,
                        "================",
                        "开始时间：" + pushCXData[d].START_TIME,
                        "最大时间：" + pushCXData[d].MAX_TIME,
                        "途径：" + pushCXData[d].ADMINISTRATOR,
                        "量：" + pushCXData[d].DOSAGE,
                        "单位：" + pushCXData[d].DOSAGE_UNITS
                      ];
                      var titleTwo = titleDataTwo.join("\n");
                      masterData.push({
                        ADDTIME:
                        nextTime3[0] +
                        ":" +
                        nextTime3[1] +
                        "→" +
                        nextTime4[0] +
                        ":" +
                        nextTime4[1],
                        ADMINISTRATOR: pushCXData[d].ADMINISTRATOR,
                        ITEM_NAME: pushCXData[d].ITEM_NAME,
                        START_TIME: pushCXData[d].START_TIME,
                        titleWord: titleTwo,
                        sort: timeMoreOne,
                        DOSAGE: pushCXData[d].DOSAGE,
                        DOSAGE_UNITS: pushCXData[d].DOSAGE_UNITS
                      });
                    } else {
                      var titleDataTwo = [
                        pushCXData[d].ITEM_NAME,
                        "================",
                        "开始时间：" + pushCXData[d].START_TIME,
                        "结束时间：" + pushCXData[d].ENDDATE,
                        "途径：" + pushCXData[d].ADMINISTRATOR,
                        "量：" + pushCXData[d].DOSAGE,
                        "单位：" + pushCXData[d].DOSAGE_UNITS
                      ];
                      var titleTwo = titleDataTwo.join("\n");
                      masterData.push({
                        ADDTIME:
                        nextTime3[0] +
                        ":" +
                        nextTime3[1] +
                        "→" +
                        nextTime4[0] +
                        ":" +
                        nextTime4[1],
                        ADMINISTRATOR: pushCXData[d].ADMINISTRATOR,
                        ITEM_NAME: pushCXData[d].ITEM_NAME,
                        START_TIME: pushCXData[d].START_TIME,
                        titleWord: titleTwo,
                        sort: timeMoreOne,
                        DOSAGE: pushCXData[d].DOSAGE,
                        DOSAGE_UNITS: pushCXData[d].DOSAGE_UNITS
                      });
                    }
                  }
                  // } else {

                  // }
                }
              }
              masterData.sort(this.sortFun);
              masterData.push({
                ITEM_NAME: "《术中事件》"
              });
              var startOperation = this.config.userInfo.startDateTime;
              var endOperation = this.config.userInfo.endDateTime;
              if (startOperation) {
                var time1 = new Date(startOperation).getTime();
                var titleDataOne = [
                  "手术开始",
                  "================",
                  "时间：" + startOperation
                ];
                var titleOne = titleDataOne.join("\n");
                if (
                  this.startTimeInPage <= time1 &&
                  time1 <= this.maxTimeInPage
                ) {
                  var newDateOfFun = startOperation.split(" ");
                  masterDataSz.push({
                    ITEM_NAME: "手术开始",
                    ADDTIME: newDateOfFun[1],
                    START_TIME: startOperation,
                    titleWord: titleOne,
                    sort: time1
                  });
                }
              }
              if (endOperation) {
                var time1 = new Date(endOperation).getTime();
                var titleDataOne = [
                  "手术结束",
                  "================",
                  "时间：" + endOperation
                ];
                var titleOne = titleDataOne.join("\n");
                if (
                  this.startTimeInPage <= time1 &&
                  time1 <= this.maxTimeInPage
                ) {
                  var newDateOfFun = endOperation.split(" ");
                  masterDataSz.push({
                    ITEM_NAME: "手术结束",
                    ADDTIME: newDateOfFun[1],
                    START_TIME: endOperation,
                    titleWord: titleOne,
                    sort: time1
                  });
                }
              }
              if (
                this.config.userInfo.inDateTime &&
                this.config.listChangeData == false
              ) {
                var time1 = new Date(this.config.userInfo.inDateTime).getTime();
                var titleDataOne = [
                  "入手术室",
                  "================",
                  "时间：" + this.config.userInfo.inDateTime
                ];
                var titleOne = titleDataOne.join("\n");
                if (
                  this.startTimeInPage <= time1 &&
                  time1 <= this.maxTimeInPage
                ) {
                  var newDateOfFun = this.config.userInfo.inDateTime.split(" ");
                  masterDataSz.push({
                    ITEM_NAME: "入手术室",
                    ADDTIME: newDateOfFun[1],
                    START_TIME: this.config.userInfo.inDateTime,
                    titleWord: titleOne,
                    sort: time1
                  });
                }
              } else {
                var time1 = new Date(
                  this.config.userInfo.pacuStartTime
                ).getTime();
                var titleDataOne = [
                  "入复苏室",
                  "================",
                  "时间：" + this.config.userInfo.pacuStartTime
                ];
                var titleOne = titleDataOne.join("\n");
                if (
                  this.startTimeInPage <= time1 &&
                  time1 <= this.maxTimeInPage
                ) {
                  var newDateOfFun = this.config.userInfo.pacuStartTime.split(" ");
                  masterDataSz.push({
                    ITEM_NAME: "入复苏室",
                    ADDTIME: newDateOfFun[1],
                    START_TIME: this.config.userInfo.pacuStartTime,
                    titleWord: titleOne,
                    sort: time1
                  });
                }
              }
              if (
                this.config.userInfo.outDateTime &&
                this.config.listChangeData == false
              ) {
                var time2 = new Date(
                  this.config.userInfo.outDateTime
                ).getTime();
                var titleDataTwo = [
                  "出手术室",
                  "================",
                  "时间：" + this.config.userInfo.outDateTime
                ];
                var titleTwo = titleDataTwo.join("\n");
                if (
                  this.startTimeInPage <= time2 &&
                  time2 <= this.maxTimeInPage
                ) {
                  var newDateOfFun = this.config.userInfo.outDateTime.split(" ");
                  masterDataSz.push({
                    itemNom: "",
                    ADDTIME: newDateOfFun[1],
                    ITEM_NAME: "出手术室",
                    START_TIME: this.config.userInfo.outDateTime,
                    titleWord: titleTwo,
                    sort: time2
                  });
                }
              } else {
                var time2 = new Date(
                  this.config.userInfo.pacuEndTime
                ).getTime();
                var titleDataTwo = [
                  "出复苏室",
                  "================",
                  "时间：" + this.config.userInfo.pacuEndTime
                ];
                var titleTwo = titleDataTwo.join("\n");
                if (
                  this.startTimeInPage <= time2 &&
                  time2 <= this.maxTimeInPage
                ) {
                  var newDateOfFun = this.config.userInfo.pacuEndTime.split(" ");
                  masterDataSz.push({
                    itemNom: "",
                    ITEM_NAME: "出复苏室",
                    ADDTIME: newDateOfFun[1],
                    START_TIME: this.config.userInfo.pacuEndTime,
                    titleWord: titleTwo,
                    sort: time2
                  });
                }
              }
              let paramsSzAll = {
                itemNom: "",
                patientId: this.config.userInfo.patientId,
                operId: this.config.userInfo.operId,
                visitId: this.config.userInfo.visitId,
                itemClass: "sz",
                eventNo: this.config.eventNo
              };
              this.api.selectMedAnesthesiaEventList(paramsSzAll).then(waa => {
                console.log(waa)
                for (var B = 0; B < waa.list.length; B++) {
                  if (waa.list[B].ITEM_CLASS == "Y") {
                  } else {
                    var timesz = new Date(waa.list[B].START_TIME).getTime();
                    this.$set(waa.list[B], "sort", timesz);
                    // 事件鼠标上移内容是否有结束时间
                    if (waa.list[B].ENDDATE) {
                      var titleDataOneSz = [
                        waa.list[B].ITEM_NAME,
                        "================",
                        "开始时间：" + waa.list[B].START_TIME,
                        "结束时间：" + waa.list[B].ENDDATE
                      ];
                      var titleTwoSz = titleDataOneSz.join("\n");
                    } else {
                      var titleDataOneSz = [
                        waa.list[B].ITEM_NAME,
                        "================",
                        "开始时间：" + waa.list[B].START_TIME
                      ];
                      var titleTwoSz = titleDataOneSz.join("\n");
                    }

                    if (
                      this.startTimeInPage <= timesz &&
                      timesz <= this.maxTimeInPage
                    ) {
                      // 若有结束时间则加在事件名称后面
                      var newDateOfFun = waa.list[B].START_TIME.split(" ");
                      var fDate = newDateOfFun[1].split(":");
                      if (waa.list[B].ENDDATE) {
                        var newEndDate = waa.list[B].ENDDATE.split(" ");
                        var eDate = newEndDate[1].split(":")
                        masterDataSz.push({
                          itemNom: "",
                          ITEM_NAME: fDate[0] + ':' + fDate[1] + "→" + eDate[0] + ':' + eDate[1] + waa.list[B].ITEM_NAME,
                          DOSAGE: waa.list[B].DOSAGE,
                          DOSAGE_UNITS: waa.list[B].DOSAGE_UNITS,
                          titleWord: titleTwoSz,
                          ADMINISTRATOR: waa.list[B].ADMINISTRATOR,
                          sort: timesz
                        });
                      } else {
                        masterDataSz.push({
                          itemNom: "",
                          ITEM_NAME: fDate[0] + ':' + fDate[1] + waa.list[B].ITEM_NAME,
                          DOSAGE: waa.list[B].DOSAGE,
                          DOSAGE_UNITS: waa.list[B].DOSAGE_UNITS,
                          titleWord: titleTwoSz,
                          ADMINISTRATOR: waa.list[B].ADMINISTRATOR,
                          sort: timesz
                        });
                      }
                    }
                  }
                }
                masterDataSz.sort(this.sortFun);
                for (var s = 0; s < masterDataSz.length; s++) {
                  masterDataSz[s].itemNom = s + 1;
                  masterData.push(masterDataSz[s]);
                }
                for (var mas = 0; mas < masterData.length; mas++) {
                  this.$set(masterData[mas], "idName", "boxIdN" + mas);
                }
                this.dataBody = masterData;
                console.log(this.dataBody);
                // 获取当前时间段的所有页
                console.log("显示" + this.config.pageDataOfGradNow);
                let _this = this;
                setTimeout(function() {
                  var allpage = "";
                  var lastOne = "";
                  var firstOne = "";
                  var thisPageData = [];
                  var lastNum = "";
                  var firstNum = "";
                  // var length = masterData.length
                  for (var otk = 0; otk < masterData.length; otk++) {
                    // console.log(otk)
                    var lastLeft = document.getElementById(
                      masterData[otk].idName
                    ).offsetLeft;
                    if (otk == masterData.length - 1) {
                      allpage = Math.ceil(lastLeft / 666);
                      // console.log('总页数' + allpage)
                    }
                  }
                  // debugger
                  if (allpage > _this.config.pageDataOfGradNow) {
                    _this.config.pageNowData = false;
                  } else if (allpage == _this.config.pageDataOfGradNow) {
                    _this.config.pageNowData = true;
                  }

                  for (var tar = 0; tar < masterData.length; tar++) {
                    // console.log(allpage)
                    // console.log('当前' + _this.config.pageDataOfGradNow)
                    // console.log(masterData[tar].idName)
                    var lema = document.getElementById(masterData[tar].idName)
                      .offsetHeight;
                    var lastLeft = document.getElementById(
                      masterData[tar].idName
                    ).offsetLeft;
                    var lastTop = document.getElementById(
                      masterData[tar].idName
                    ).offsetTop;
                    var firstTop = document.getElementById(
                      masterData[tar].idName
                    ).offsetTop;

                    // 当前没在第一页获取当前页的第一条数据和最后一条数据
                    if (_this.config.pageDataOfGradNow > 1) {
                      // console.log('现在不是第一页')
                      // console.log(lastLeft)
                      if (
                        allpage > _this.config.pageDataOfGradNow &&
                        lastLeft ==
                        (_this.config.pageDataOfGradNow - 1) * 888 &&
                        lastTop == 0
                      ) {
                        // 如果当前页不是最后一页获取到当前页的最后一条数据和最后一页的第一条
                        lastOne = masterData[tar - 1];
                        lastNum = tar - 1;

                        // console.log('最后一个')
                        // console.log('不是最后一页' + lastOne)
                        // _this.config.pageNowData = false;
                      } else if (
                        allpage == _this.config.pageDataOfGradNow &&
                        tar == masterData.length - 1
                      ) {
                        // 如果当前页是最后一页获取最后一条数据
                        lastOne = masterData[tar];
                        lastNum = tar;
                        // console.log('最后一页' + lastOne)
                      }
                      if (
                        _this.config.pageDataOfGradNow > 1 &&
                        lastLeft ==
                        (_this.config.pageDataOfGradNow - 1) * 888 &&
                        lastTop == 0
                      ) {
                        // 如果当前页不是第一页获取到当前页的第一条数据
                        firstOne = masterData[tar];
                        // console.log('第一个')
                        firstNum = tar;
                      } else if (_this.config.pageDataOfGradNow == 1) {
                        // 如果当前页是第一页
                        firstOne = masterData[0];
                        firstNum = 0;
                      } else if (
                        _this.config.pageDataOfGradNow == allpage &&
                        lastLeft ==
                        (_this.config.pageDataOfGradNow - 1) * 888 &&
                        lastTop == 0
                      ) {
                        // 如果当前是最后一页那么获取到此页的第一条数据
                        // console.log('最后一页的第一个')
                        firstNum = tar;
                        // console.log(firstNum)
                      }
                    } else if (_this.config.pageDataOfGradNow == 1) {
                      // console.log('现在是第一页' + lastLeft)
                      if (lastLeft == 888 && lastTop == 0) {
                        firstNum = 0;
                        lastNum = tar;
                      } else if (
                        tar == masterData.length - 1 &&
                        lastLeft < 888
                      ) {
                        firstNum = 0;
                        lastNum = tar + 1;
                      }
                      // console.log(lastNum)
                    }
                    // console.log(lastLeft)
                    // console.log(lastTop)
                  }
                  // 获取当前页的数据并显示出来
                  // setTimeout(function() {
                  // console.log(firstNum)
                  // console.log(lastNum)
                  console.log(_this.config.initTime);
                  console.log(thisPageData);
                  if (_this.config.pageDataOfGradNow == 1) {
                    for (var libo = 0; libo < lastNum; libo++) {
                      thisPageData.push(masterData[libo]);
                    }
                  } else if (_this.config.pageDataOfGradNow > 1) {
                    for (var libo = firstNum; libo <= lastNum; libo++) {
                      thisPageData.push(masterData[libo]);
                    }
                  }
                  _this.dataBody = thisPageData;
                  sessionStorage.setItem('thisPageData', JSON.stringify(_this.dataBody));
                  Bus.$emit('dataOfGridPrint')
                  // localStorage.setItem('thisPageData', _this.dataBody);
                  document.getElementById("tempdiv").style.opacity = "1";
                  // console.log(thisPageData)
                  // }, 500);
                }, 200);
              });
              // this.setTimeId = setTimeout(_ => this.selectMedAnesthesiaEventList(), this.config.timeSet)
            });
          });
        });
      });
    },
    getIdNum(index) {
      return "boxId" + index;
    },
    listenFun() {
      this.opacityData = false;
      this.selectMedAnesthesiaEventList();
      console.log("监听调用....当前页：" + this.config.pagePercentNum);
      console.log("监听调用....总页数：" + this.config.pageTotal);
      if (this.config.pagePercentNum == 1) {
        this.config.pageDataOfGrad = 0;
        this.config.pageDataOfGradNow = 1;
        this.firstShow = true;
        // 计算每个时间段有多少页然后全部加起来就是总页数
        this.selectMedAnesthesiaEventListTwo(1);
      } else {
        this.firstShow = false;
      }
    },
    selectMedAnesthesiaEventListTwo(ce) {
      let masterData = [];
      let masterDataSz = [];
      if (ce == 1) {
        this.everyMaxTime = new Date(this.config.maxTime).getTime();
        this.everyStartTime = new Date(this.config.initTime).getTime();
      } else if (ce > 1) {
        this.everyMaxTime =
          new Date(this.config.maxTime).getTime() + 15000000 * (ce - 1);
        this.everyStartTime =
          new Date(this.config.initTime).getTime() + 15000000 * (ce - 1);
      }
      // console.log( this.everyMaxTime)
      // console.log(this.everyStartTime)
      // 15000000   250分钟
      let params = {
        patientId: this.config.userInfo.patientId,
        operId: this.config.userInfo.operId,
        visitId: this.config.userInfo.visitId,
        itemClass: "yp",
        eventNo: this.config.eventNo
      };
      if (this.setTimeId) {
        clearTimeout(this.setTimeId);
      }
      this.api.selectMedAnesthesiaEventList(params).then(res => {
        this.dataOfEveryTime = [];
        let title = "";
        masterData.push({
          ITEM_NAME: "《术中用药》"
        });
        for (var i = 0; i < res.list.length; i++) {
          if (
            res.list[i].ITEM_CLASS == "C" ||
            res.list[i].ITEM_CLASS == "2" ||
            res.list[i].ITEM_CLASS == "3" ||
            res.list[i].ITEM_CLASS == "B" ||
            res.list[i].ITEM_CLASS == "4"
          ) {
          } else {
            var time = new Date(res.list[i].START_TIME).getTime();
            if (this.everyStartTime <= time && time <= this.everyMaxTime) {
              this.$set(res.list[i], "sort", time);
              if (res.list[i].START_TIME) {
                var nameDate = res.list[i].START_TIME.split(" ");
                var nextTime = nameDate[1].split(":");
                // console.log(nextTime)
              } else {
                var nextTime = ["", ""];
              }

              if (res.list[i].DOSAGE !== null) {
                if (res.list[i].DURATIVE_INDICATOR == 1) {
                  if (res.list[i].ADMINISTRATOR !== null) {
                    // var titleData = [res.list[i].ITEM_NAME, '================', '开始时间：' + res.list[i].START_TIME, '结束时间：' + res.list[i].ENDDATE, '途径：' + res.list[i].ADMINISTRATOR, '量：' + res.list[i].DOSAGE, '单位：' + res.list[i].DOSAGE_UNITS]
                  } else {
                    // var titleData = [res.list[i].ITEM_NAME, '================', '开始时间：' + res.list[i].START_TIME, '结束时间：' + res.list[i].ENDDATE, '量：' + res.list[i].DOSAGE, '单位：' + res.list[i].DOSAGE_UNITS]
                  }
                } else {
                  if (res.list[i].ADMINISTRATOR !== null) {
                    // var titleData = [res.list[i].ITEM_NAME, '================', '开始时间：' + res.list[i].START_TIME, '途径：' + res.list[i].ADMINISTRATOR, '量：' + res.list[i].DOSAGE, '单位：' + res.list[i].DOSAGE_UNITS]
                  } else {
                    // var titleData = [res.list[i].ITEM_NAME, '================', '开始时间：' + res.list[i].START_TIME, '量：' + res.list[i].DOSAGE, '单位：' + res.list[i].DOSAGE_UNITS]
                  }
                }
                title = titleData.join("\n");
                // this.$set(res.list[i], 'titleWord', title)
              } else {
                // var titleData = [res.list[i].ITEM_NAME, '================', '开始时间：' + res.list[i].START_TIME]
                // title = titleData.join('\n')
                // this.$set(res.list[i], 'titleWord', title)
              }
              masterData.push({
                ADDTIME: nextTime[0] + ":" + nextTime[1],
                ITEM_NAME: res.list[i].ITEM_NAME,
                DOSAGE: res.list[i].DOSAGE,
                DOSAGE_UNITS: res.list[i].DOSAGE_UNITS,
                // titleWord: res.list[i].titleWord,
                ADMINISTRATOR: res.list[i].ADMINISTRATOR,
                sort: time
              });
            }
          }
        }
        // 入量
        let paramsTwo = {
          patientId: this.config.userInfo.patientId,
          operId: this.config.userInfo.operId,
          visitId: this.config.userInfo.visitId,
          itemClass: "3B",
          eventNo: this.config.eventNo
        };
        this.api.selectMedAnesthesiaEventList(paramsTwo).then(zze => {
          if (zze.list.length > 6) {
            let newList = [];
            for (var ahd = 0; ahd < zze.list.length; ahd++) {
              var time3B = new Date(zze.list[ahd].START_TIME).getTime();
              if (
                this.everyStartTime <= time3B &&
                time3B <= this.everyMaxTime
              ) {
                newList.push(zze.list[ahd]);
              }
            }
            if (newList.length > 6) {
              for (var sameO = 0; sameO < 6; sameO++) {
                for (var nextOne = 0; nextOne < newList.length; nextOne++) {
                  if (
                    newList[sameO].ITEM_NAME == newList[nextOne].ITEM_NAME &&
                    sameO !== nextOne
                  ) {
                    newList.splice(sameO, 1);
                  }
                }
              }
              // for (var ti = 0; ti < 6; ti++) {
              for (var t = 6; t < newList.length; t++) {
                // if (newList[ti].ITEM_NAME !== newList[t].ITEM_NAME && newList[ti].ITEM_CLASS !== newList[t].ITEM_CLASS) {
                if (newList[t].START_TIME) {
                  var nameDate1 = newList[t].START_TIME.split(" ");
                  var nextTime1 = nameDate1[1].split(":");
                } else {
                  var nextTime1 = ["", ""];
                }
                if (newList[t].ENDDATE) {
                  var nameDate12 = newList[t].ENDDATE.split(" ");
                  var nextTime12 = nameDate12[1].split(":");
                } else {
                  var nameDate12 = this.config.patientMaxTime.split(" ");
                  var nextTime12 = nameDate12[1].split(":");
                }
                var timeMoreOne = new Date(newList[t].START_TIME).getTime();
                if (
                  this.everyStartTime <= timeMoreOne &&
                  timeMoreOne <= this.everyMaxTime
                ) {
                  // if (newList[ti].ITEM_NAME == newList[t].ITEM_NAME && ti !== t) {
                  // newList.splice(ti, 1)
                  // } else {
                  // var titleDataTwo = [newList[t].ITEM_NAME, '================', '时间：' + newList[t].START_TIME]
                  // var titleTwo = titleDataTwo.join('\n')
                  masterData.push({
                    ADDTIME:
                    nextTime1[0] +
                    ":" +
                    nextTime1[1] +
                    "→" +
                    nextTime12[0] +
                    ":" +
                    nextTime12[1],
                    ITEM_NAME: newList[t].ITEM_NAME,
                    START_TIME: newList[t].START_TIME,
                    // titleWord: titleTwo,
                    DOSAGE: newList[t].DOSAGE,
                    DOSAGE_UNITS: newList[t].DOSAGE_UNITS,
                    ADMINISTRATOR: newList[t].ADMINISTRATOR,
                    sort: timeMoreOne
                  });
                  // }
                }
                // } else {

                // }
              }
              // break;
              // }
            }

          } else {
          }
          // 出量
          let paramsTwo = {
            patientId: this.config.userInfo.patientId,
            operId: this.config.userInfo.operId,
            visitId: this.config.userInfo.visitId,
            itemClass: "D",
            eventNo: this.config.eventNo
          };
          this.api.selectMedAnesthesiaEventList(paramsTwo).then(cld => {
            let newList = [];
            if (cld.length > 3) {
              for (var ahd = 0; ahd < cld.list.length; ahd++) {
                var timeCl = new Date(cld.list[ahd].START_TIME).getTime();
                if (
                  this.everyStartTime <= timeCl &&
                  timeCl <= this.everyMaxTime
                ) {
                  newList.push(cld.list[ahd]);
                }
              }
              for (var clNo1 = 0; clNo1 < 3; clNo1++) {
                for (var clNo2 = 0; clNo2 < newList.length; clNo2++) {
                  if (
                    newList[clNo1].ITEM_NAME == newList[clNo2].ITEM_NAME &&
                    clNo1 !== clNo2
                  ) {
                    newList.splice(clNo1, 1);
                  }
                }
              }
              for (var cd = 3; cd < newList.length; cd++) {
                var timeClDate = new Date(newList[cd].START_TIME).getTime();
                if (
                  this.everyStartTime <= timeClDate &&
                  timeClDate <= this.everyMaxTime
                ) {
                  if (newList[cd].START_TIME) {
                    var nameDate21 = newList[d].START_TIME.split(" ");
                    var nextTime21 = nameDate21[1].split(":");
                  } else {
                    var nameDate21 = ["", ""];
                  }
                  if (newList[cd].ENDDATE) {
                    var nameDate22 = newList[cd].ENDDATE.split(" ");
                    var nextTime22 = nameDate22[1].split(":");
                  } else {
                    var nameDate22 = this.config.patientMaxTime.split(" ");
                    var nextTime22 = nameDate22[1].split(":");
                  }
                  // if (newList[ti].ITEM_NAME == newList[cd].ITEM_NAME && ti !== t) {
                  // newList.splice(ti, 1)
                  // } else {
                  // var titleDataClTwo = [newList[cd].ITEM_NAME, '================', '时间：' + newList[cd].START_TIME]
                  // var titleCl = titleDataClTwo.join('\n')
                  masterData.push({
                    ADDTIME:
                    nextTime21[0] +
                    ":" +
                    nextTime21[1] +
                    "→" +
                    nextTime22[0] +
                    ":" +
                    nextTime22[1],
                    ITEM_NAME: newList[cd].ITEM_NAME,
                    START_TIME: newList[cd].START_TIME,
                    // titleWord: titleCl,
                    DOSAGE: newList[cd].DOSAGE,
                    DOSAGE_UNITS: newList[cd].DOSAGE_UNITS,
                    ADMINISTRATOR: newList[cd].ADMINISTRATOR,
                    sort: timeClDate
                  });
                  // }
                }
              }
            }

            // 麻醉用药
            let paramsTwo = {
              patientId: this.config.userInfo.patientId,
              operId: this.config.userInfo.operId,
              visitId: this.config.userInfo.visitId,
              itemClass: "2C4",
              eventNo: this.config.eventNo
            };
            this.api.selectMedAnesthesiaEventList(paramsTwo).then(aff => {
              let pushCXData = [];
              // console.log(aff)
              // debugger
              for (var h = 0; h < aff.list.length; h++) {
                if (
                  aff.list[h].DURATIVE_INDICATOR == 0 ||
                  aff.list[h].DURATIVE_INDICATOR == ""
                ) {
                  if (aff.list[h].START_TIME) {
                    var nameDate2 = aff.list[h].START_TIME.split(" ");
                    var nextTime2 = nameDate2[1].split(":");
                  } else {
                    var nextTime2 = ["", ""];
                  }
                  // var timeMoreNEXT = new Date(aff.list[h].START_TIME).getTime();
                  // pushCXData.push(aff.list[h])
                  var timeMoreOne = new Date(aff.list[h].START_TIME).getTime();
                  if (
                    this.everyStartTime <= timeMoreOne &&
                    timeMoreOne <= this.everyMaxTime
                  ) {
                    // var titleDataTwo = [aff.list[h].ITEM_NAME, '================', '开始时间：' + aff.list[h].START_TIME];
                    // var titleDataTwo = [aff.list[h].ITEM_NAME, '================', '开始时间：' + aff.list[h].START_TIME, '途径：' + aff.list[h].ADMINISTRATOR, '量：' + aff.list[h].DOSAGE, '单位：' + aff.list[h].DOSAGE_UNITS]
                    // var titleTwo = titleDataTwo.join('\n')
                    masterData.push({
                      ADDTIME: nextTime2[0] + ":" + nextTime2[1],
                      ADMINISTRATOR: aff.list[h].ADMINISTRATOR,
                      ITEM_NAME: aff.list[h].ITEM_NAME,
                      START_TIME: aff.list[h].START_TIME,
                      // titleWord: titleTwo,
                      sort: timeMoreOne,
                      DOSAGE: aff.list[h].DOSAGE,
                      DOSAGE_UNITS: aff.list[h].DOSAGE_UNITS
                    });
                  }
                } else if (aff.list[h].DURATIVE_INDICATOR == 1) {
                  var timeMoreNEXT = new Date(aff.list[h].START_TIME).getTime();
                  if (
                    this.everyStartTime <= timeMoreNEXT &&
                    timeMoreNEXT <= this.everyMaxTime
                  ) {
                    pushCXData.push(aff.list[h]);
                  }
                }
              }
              if (pushCXData.length > 8) {
                // 判断是否拥有同一种用药
                for (var qe = 0; qe < 8; qe++) {
                  for (var ugr = 8; ugr < pushCXData.length; ugr++) {
                    if (
                      pushCXData[qe].ITEM_NAME == pushCXData[ugr].ITEM_NAME &&
                      qe !== ugr
                    ) {
                      pushCXData.splice(qe, 1);
                    }
                  }
                }
                for (var d = 8; d < pushCXData.length; d++) {
                  // if (pushCXData[d].DURATIVE_INDICATOR == 1) {
                  if (pushCXData[d].START_TIME) {
                    var nameDate3 = pushCXData[d].START_TIME.split(" ");
                    var nextTime3 = nameDate3[1].split(":");
                  } else {
                    var nameDate3 = ["", ""];
                  }
                  if (pushCXData[d].ENDDATE) {
                    var nameDate4 = pushCXData[d].ENDDATE.split(" ");
                    var nextTime4 = nameDate4[1].split(":");
                  } else {
                    var nameDate4 = this.config.patientMaxTime.split(" ");
                    var nextTime4 = nameDate4[1].split(":");
                  }
                  var timeMoreOne = new Date(
                    pushCXData[d].START_TIME
                  ).getTime();
                  if (
                    this.everyStartTime <= timeMoreOne &&
                    timeMoreOne <= this.everyMaxTime
                  ) {
                    // var titleDataTwo = [pushCXData[d].ITEM_NAME, '================', '开始时间：' + pushCXData[d].START_TIME];
                    if (!pushCXData[d].ENDDATE) {
                      // var titleDataTwo = [pushCXData[d].ITEM_NAME, '================', '开始时间：' + pushCXData[d].START_TIME, '最大时间：' + pushCXData[d].MAX_TIME, '途径：' + pushCXData[d].ADMINISTRATOR, '量：' + pushCXData[d].DOSAGE, '单位：' + pushCXData[d].DOSAGE_UNITS]
                      // var titleTwo = titleDataTwo.join('\n')
                      masterData.push({
                        ADDTIME:
                        nextTime3[0] +
                        ":" +
                        nextTime3[1] +
                        "→" +
                        nextTime4[0] +
                        ":" +
                        nextTime4[1],
                        ADMINISTRATOR: pushCXData[d].ADMINISTRATOR,
                        ITEM_NAME: pushCXData[d].ITEM_NAME,
                        START_TIME: pushCXData[d].START_TIME,
                        // titleWord: titleTwo,
                        sort: timeMoreOne,
                        DOSAGE: pushCXData[d].DOSAGE,
                        DOSAGE_UNITS: pushCXData[d].DOSAGE_UNITS
                      });
                    } else {
                      // var titleDataTwo = [pushCXData[d].ITEM_NAME, '================', '开始时间：' + pushCXData[d].START_TIME, '结束时间：' + pushCXData[d].ENDDATE, '途径：' + pushCXData[d].ADMINISTRATOR, '量：' + pushCXData[d].DOSAGE, '单位：' + pushCXData[d].DOSAGE_UNITS]
                      // var titleTwo = titleDataTwo.join('\n')
                      masterData.push({
                        ADDTIME:
                        nextTime3[0] +
                        ":" +
                        nextTime3[1] +
                        "→" +
                        nextTime4[0] +
                        ":" +
                        nextTime4[1],
                        ADMINISTRATOR: pushCXData[d].ADMINISTRATOR,
                        ITEM_NAME: pushCXData[d].ITEM_NAME,
                        START_TIME: pushCXData[d].START_TIME,
                        // titleWord: titleTwo,
                        sort: timeMoreOne,
                        DOSAGE: pushCXData[d].DOSAGE,
                        DOSAGE_UNITS: pushCXData[d].DOSAGE_UNITS
                      });
                    }
                  }
                  // } else {

                  // }
                }
              }
              masterData.sort(this.sortFun);
              masterData.push({
                ITEM_NAME: "《术中事件》"
              });
              var startOperation = this.config.userInfo.startDateTime;
              var endOperation = this.config.userInfo.endDateTime;
              if (startOperation) {
                var time1 = new Date(startOperation).getTime();
                var titleDataOne = [
                  "手术开始",
                  "================",
                  "时间：" + startOperation
                ];
                var titleOne = titleDataOne.join("\n");
                if (
                  this.startTimeInPage <= time1 &&
                  time1 <= this.maxTimeInPage
                ) {
                  var newDateOfFun = startOperation.split(" ");
                  masterDataSz.push({
                    ITEM_NAME: "手术开始",
                    ADDTIME: newDateOfFun[1],
                    START_TIME: startOperation,
                    titleWord: titleOne,
                    sort: time1
                  });
                }
              }
              if (endOperation) {
                var time1 = new Date(endOperation).getTime();
                var titleDataOne = [
                  "手术结束",
                  "================",
                  "时间：" + endOperation
                ];
                var titleOne = titleDataOne.join("\n");
                if (
                  this.startTimeInPage <= time1 &&
                  time1 <= this.maxTimeInPage
                ) {
                  var newDateOfFun = endOperation.split(" ");
                  masterDataSz.push({
                    ITEM_NAME: "手术结束",
                    ADDTIME: newDateOfFun[1],
                    START_TIME: endOperation,
                    titleWord: titleOne,
                    sort: time1
                  });
                }
              }
              if (
                this.config.userInfo.inDateTime &&
                this.config.listChangeData == false
              ) {
                var time1 = new Date(this.config.userInfo.inDateTime).getTime();
                // var titleDataOne = ['入手术室', '================', '时间：' + this.config.userInfo.inDateTime]
                // var titleOne = titleDataOne.join('\n')
                if (
                  this.everyStartTime <= time1 &&
                  time1 <= this.everyMaxTime
                ) {
                  masterDataSz.push({
                    ITEM_NAME: "入手术室",
                    START_TIME: this.config.userInfo.inDateTime,
                    // titleWord: titleOne,
                    sort: time1
                  });
                }
              } else {
                var time1 = new Date(
                  this.config.userInfo.pacuStartTime
                ).getTime();
                // var titleDataOne = ['入手复苏室', '================', '时间：' + this.config.userInfo.pacuStartTime]
                // var titleOne = titleDataOne.join('\n')
                if (
                  this.everyStartTime <= time1 &&
                  time1 <= this.everyMaxTime
                ) {
                  masterDataSz.push({
                    ITEM_NAME: "入手复苏室",
                    // START_TIME: this.config.userInfo.pacuStartTime,
                    // titleWord: titleOne,
                    sort: time1
                  });
                }
              }
              if (
                this.config.userInfo.outDateTime &&
                this.config.listChangeData == false
              ) {
                var time2 = new Date(
                  this.config.userInfo.outDateTime
                ).getTime();
                // var titleDataTwo = ['出手术室', '================', '时间：' + this.config.userInfo.outDateTime]
                // var titleTwo = titleDataTwo.join('\n')
                if (
                  this.everyStartTime <= time2 &&
                  time2 <= this.everyMaxTime
                ) {
                  masterDataSz.push({
                    itemNom: "",
                    ITEM_NAME: "出手术室",
                    START_TIME: this.config.userInfo.outDateTime,
                    // titleWord: titleTwo,
                    sort: time2
                  });
                }
              } else {
                var time2 = new Date(
                  this.config.userInfo.pacuEndTime
                ).getTime();
                // var titleDataTwo = ['出复苏室', '================', '时间：' + this.config.userInfo.pacuEndTime]
                // var titleTwo = titleDataTwo.join('\n')
                if (
                  this.everyStartTime <= time2 &&
                  time2 <= this.everyMaxTime
                ) {
                  masterDataSz.push({
                    itemNom: "",
                    ITEM_NAME: "出复苏室",
                    START_TIME: this.config.userInfo.pacuEndTime,
                    // titleWord: titleTwo,
                    sort: time2
                  });
                }
              }
              let paramsSzAll = {
                itemNom: "",
                patientId: this.config.userInfo.patientId,
                operId: this.config.userInfo.operId,
                visitId: this.config.userInfo.visitId,
                itemClass: "sz",
                eventNo: this.config.eventNo
              };
              this.api.selectMedAnesthesiaEventList(paramsSzAll).then(waa => {
                for (var B = 0; B < waa.list.length; B++) {
                  if (waa.list[B].ITEM_CLASS == "Y") {
                  } else {
                    var timesz = new Date(waa.list[B].START_TIME).getTime();
                    this.$set(waa.list[B], "sort", timesz);
                    // 事件鼠标上移内容是否有结束时间
                    if (waa.list[B].ENDDATE) {
                      // var titleDataOneSz = [waa.list[B].ITEM_NAME, '================', '开始时间：' + waa.list[B].START_TIME, '结束时间：' + waa.list[B].ENDTIME]
                      // var titleTwoSz = titleDataOneSz.join('\n')
                    } else {
                      // var titleDataOneSz = [waa.list[B].ITEM_NAME, '================', '开始时间：' + waa.list[B].START_TIME]
                      // var titleTwoSz = titleDataOneSz.join('\n')
                    }
                    if (
                      this.everyStartTime <= timesz &&
                      timesz <= this.everyMaxTime
                    ) {
                      // 若有结束时间则加在事件名称后面
                      if (waa.list[B].ENDDATE) {
                        masterDataSz.push({
                          itemNom: "",
                          ITEM_NAME:
                          waa.list[B].ITEM_NAME +
                          " " +
                          waa.list[B].START_TIME +
                          "→" +
                          waa.list[B].ENDDATE,
                          DOSAGE: waa.list[B].DOSAGE,
                          DOSAGE_UNITS: waa.list[B].DOSAGE_UNITS,
                          // titleWord: titleTwoSz,
                          ADMINISTRATOR: waa.list[B].ADMINISTRATOR,
                          sort: timesz
                        });
                      } else {
                        masterDataSz.push({
                          itemNom: "",
                          ITEM_NAME: waa.list[B].ITEM_NAME,
                          DOSAGE: waa.list[B].DOSAGE,
                          DOSAGE_UNITS: waa.list[B].DOSAGE_UNITS,
                          // titleWord: titleTwoSz,
                          ADMINISTRATOR: waa.list[B].ADMINISTRATOR,
                          sort: timesz
                        });
                      }
                    }
                  }
                }
                masterDataSz.sort(this.sortFun);
                console.log(masterDataSz);
                for (var s = 0; s < masterDataSz.length; s++) {
                  masterDataSz[s].itemNom = s + 1;
                  masterData.push(masterDataSz[s]);
                }

                for (var mas = 0; mas < masterData.length; mas++) {
                  this.$set(masterData[mas], "idName", "boxId" + mas);
                }

                this.dataOfEveryTime = masterData;

                // 获取当前时间段的所有页
                let _this = this;
                setTimeout(function() {
                  var allpage = "";
                  var lastOne = "";
                  // var length = masterData.length

                  for (var tar = 0; tar < masterData.length; tar++) {
                    // debugger
                    // console.log(masterData[tar].idName)
                    var lema = document.getElementById(masterData[tar].idName)
                      .offsetHeight;
                    var lastLeft = document.getElementById(
                      masterData[tar].idName
                    ).offsetLeft;
                    var lastTop = document.getElementById(
                      masterData[tar].idName
                    ).offsetTop;
                    if (lastLeft == 888 && lastTop == 0) {
                      lastOne = masterData[tar - 1];
                      // console.log('最后一个')
                    }
                    if (tar == masterData.length - 1) {
                      // console.log(lastLeft)
                      allpage = Math.ceil(lastLeft / 666);
                      console.log("总页数" + allpage);
                    }
                  }
                  // 上面算出此时间段的总页数。然后这儿加在公共文件里面
                  var nersa = _this.config.tempAddPae;
                  var ticeNum = ce + 1;
                  if (allpage > 1) {
                    _this.config.pageDataOfGrad =
                      _this.config.pageDataOfGrad - 1 + allpage;
                    // console.log('加了几页：' + _this.config.pageDataOfGrad)
                  } else if (allpage == 0) {
                    // console.log('没有多的分页')
                  }
                  // console.log(_this.config.pageDataOfGrad)
                  // console.log(nersa)
                  if (nersa > 1) {
                    if (ce == nersa - 1) {
                      // alert(allpage)
                      // if (allpage == 0) {
                      // _this.config.lastBeforOne = 1
                      // } else {
                      _this.config.lastBeforOne = allpage;
                      // }

                      // console.log(_this.config.lastBeforOne)
                    }
                  } else if (nersa == 1) {
                    _this.config.lastBeforOne = 1;
                  }
                  if (ce >= 1 && nersa > 1 && ce < nersa) {
                    _this.selectMedAnesthesiaEventListTwo(ticeNum);
                    // if (ce == nersa - 1) {
                    //   console.log('倒数第二页' + allpage)
                    //   _this.config.lastPage = allpage;
                    // } else if (ce == 1 && nersa == 1) {
                    //   _this.config.lastPage = allpage;
                    // }
                    // break
                  } else {
                    if (ce == nersa) {
                      console.log(
                        "计算完毕增加页数：" + _this.config.pageDataOfGrad
                      );
                      Bus.$emit("changePageTotal");
                      console.log("计算完毕总页数：" + _this.config.pageTotal);
                      if (allpage == 0) {
                        _this.config.lastPage = 1;
                      } else {
                        _this.config.lastPage = allpage;
                      }

                      _this.firstShow = false;
                    }
                  }
                  console.log("末页" + _this.config.lastPage);
                }, 300);
              });
            });
          });
        });
      });
    },
    listFun() {
      for (var a = 0; a < this.dataBody.length; a++) {
        var idName = "#boxId" + a;
        // console.log(idName)
        var lema = $(idName);
        // console.log(lema)
      }
    },
    sortFun(a, b) {
      return a.sort - b.sort;
    }
  },
  props: ["dataOfPeo", "page"],
  mounted() {
    if (this.setTimeId) {
      clearTimeout(this.setTimeId);
    }
    if (this.page == false) {
      // console.log(this.config.pageDataOfGrad)
      // this.selectMedAnesthesiaEventList()
      // console.log(this.config.pagePercentNum)
      console.log("创建执行------" + this.config.pagePercentNum);
      this.opacityData = false;
      this.listenFun();
    }
  },
  created() {
    this.dataBody = [];
    // Bus.$on('test', this.selectMedAnesthesiaEventList)
    Bus.$on("test", this.listenFun);
    Bus.$on("timeSetChange", this.selectMedAnesthesiaEventList);
  },
  beforeDestroy() {
    this.dataBody = [];
    // Bus.$off('test', this.selectMedAnesthesiaEventList)
    Bus.$off("test", this.listenFun);
    Bus.$off("timeSetChange", this.selectMedAnesthesiaEventList);
    clearTimeout(this.setTimeId);
  }
};
</script>
<style scoped>
.opClass {
  opacity: 1 !important;
}
</style>  