<template>
  <div style="position: relative;margin:2px;">
    <div v-if="!page">
      <div v-if="changeEventView" style="width:100%;height:100%;position:fixed;z-index:14;top:0;left:0;">
        <div class="addData" style="height:400px;width: 600px;background-color: #E3EFFF;">
          <div class="load_top">
            <div style="line-height: 30px;font-size: 18px;color:white;">术中事件</div>
            <div @click="closeChangeEvent" class="top_active">X</div>
          </div>
          <div style="padding: 15px;display:flex;flex-wrap:wrap;height:230px;">
            <div style="display: flex;height:21px;width:255px;">
              <div style="width: 80px;">类型</div>
              <input type="text" style="width:170px;" v-model="eventObj.TYPE_NAME">
            </div>
            <div style="display: flex;height:21px;width:255px;">
              <div style="width: 80px;text-align: right">事件名称</div>
              <input type="text" style="width:170px;" v-model="eventObj.ITEM_NAME">
            </div>
            <div style="display: flex;height:21px;width:255px;">
              <div style="width: 80px;">途径</div>
              <select v-model="eventObj.ADMINISTRATOR" style="display:inline-block;height:100%;width:174px;height:22px;">
                <option value=""></option>
                <option style="background-color: white;" v-for="(item,index) in roadList" :value="item.itemName">{{ item.itemName }}</option>
              </select>
            </div>
            <div style="display: flex;height:21px;width:255px;">
              <div style="width: 80px;text-align: right">浓度</div>
              <input type="text" style="width:170px;" v-model="eventObj.CONCENTRATION">
            </div>
            <div style="display: flex;height:21px;width:255px;">
              <div style="width: 80px;">单位</div>
              <select v-model="eventObj.CONCENTRATION_UNIT" style="display:inline-block;height:100%;width:174px;height:22px;">
                <option value=""></option>
                <option style="background-color: white;" v-for="(item,index) in concentrationList" :value="item.itemName">{{ item.itemName }}</option>
              </select>
            </div>
            <div style="display: flex;height:21px;width:255px;">
              <div style="width: 80px;text-align: right;">速度</div>
              <input style="width:170px;" type="text" v-model="eventObj.PERFORM_SPEED">
            </div>
            <div style="display: flex;height:21px;width:255px;">
              <div style="width: 80px;">单位</div>
              <select v-model="eventObj.SPEED_UNIT" style="display:inline-block;height:100%;width:174px;height:22px;">
                <option value=""></option>
                <option style="background-color: white;" v-for="(item,index) in speedUnitList" :value="item.itemName">{{ item.itemName }}</option>
              </select>
            </div>
            <div style="display: flex;height:21px;width:255px;">
              <div style="width: 80px;text-align: right">剂量</div>
              <input style="width: 170px;" type="text" v-model="eventObj.DOSAGE">
            </div>
            <div style="display: flex;height:21px;width:255px;">
              <div style="width: 80px;">单位</div>
              <select v-model="eventObj.DOSAGE_UNITS" style="display:inline-block;height:100%;width:174px;height:22px;">
                <option value=""></option>
                <option style="background-color: white;" v-for="(item,index) in dosageUnitsList" :value="item.itemName">{{ item.itemName }}</option>
              </select>
            </div>
            <div style="display: flex;height:21px;width:255px;">
              <div style="width: 80px;text-align: right">开始时间</div>
              <dateTime width="174" style="display:block;font-size:12px;" v-model="eventObj.START_TIME"></dateTime>
            </div>
            <div style="display: flex;height:21px;width:255px;">
              <div style="width: 80px;">是否持续</div>
              <select style="width: 174px;height:22px;display:block;font-size:12px;" v-model="eventObj.DURATIVE_INDICATOR">
                <option style="background-color: white;" v-bind:value="0">
                  不持续
                </option>
                <option style="background-color: white;" v-bind:value="1">
                  持续
                </option>
              </select>
            </div>
            <div style="display: flex;height:21px;width:255px;">
              <div style="width: 80px;text-align: right;">结束时间</div>
              <dateTime width="174" style="display:block;font-size:12px;" v-model="eventObj.ENDDATE"></dateTime>
            </div>
          </div>
          <div style="margin-right: 100px;text-align: right;">
            <button @click="saveEvent">保存</button>
            <button @click="cancleEditeEvent">取消</button>
          </div>
        </div>
      </div>
      <div style="max-height: 20px;">
        <div v-for="(item,index) in xTimeArray" v-if="index%6==0" style="width: 30px;margin-left: -8px;font-size: 12px;display: inline-block;" :title="item">{{item}}</div>
        <div v-else style="width: 12px;display: inline-block;"></div>
      </div>
      <div>
        <div v-for="(item,index) in dataArray" v-if="index < rows-1" :style="{top:svgHeight/rows*index+20+'px',height:svgHeight/rows-2.7+'px',lineHeight:svgHeight/rows+'px',}" style="line-height: 14px;width: 160px;border-bottom: 1px solid #8391a2;  font-size: 14px;position: absolute;left: -165px;padding-left: 5px;white-space:nowrap;word-break: keep-all;">
          <span v-if="item.ITEM_NAME"> {{item.ITEM_NAME}}({{item.DOSAGE_UNITS}},{{item.ADMINISTRATOR}})</span>
          <span v-if="item.ADMINISTRATOR"></span>
        </div>
        <div v-for="(item,index2) in dataArray" v-if="index2==rows-1" :style="{top:svgHeight/rows*index2+20+'px',height:svgHeight/rows-2.7+'px',lineHeight:svgHeight/rows+'px',}" style="width: 160px; font-size: 14px;position: absolute;left: -165px;padding-left: 5px;white-space:nowrap;word-break: keep-all;">
          <span v-if="item.ITEM_NAME"> {{item.ITEM_NAME}}({{item.DOSAGE_UNITS}})</span>
        </div>
      </div>
      <div style="position: relative;">
        <svg :width="svgWidth" :height="svgHeight" id="tableSvg">
          <g v-for="(item,index2) in lineArray">
            <line v-if="index2%6==0" :x1="item.x.x1" :x2="item.x.x1" y1="0" :y2="svgHeight" style="stroke:rgba(0,0,0,0.6);stroke-width:2px;"></line>
            <line v-else :x1="item.x.x1" :x2="item.x.x1" y1="0" :y2="svgHeight" style="stroke:#8391a2;stroke-width:1px;"></line>
          </g>
          <g v-for="(item,index) in lineArray" v-if="index < rows">
            <line x1="0" x2="700" :y1="item.y.y1" :y2="item.y.y1" style="stroke:#8391a2;stroke-width:1px;"></line>
          </g>
        </svg>
        <div @mousemove.stop="mouseMoveInfo(item,$event)" @mouseenter="showTipInfo(item,$event)" @mouseleave="hideTipInfo()" v-if="item.obj.DURATIVE_INDICATOR=='0'||!item.obj.DURATIVE_INDICATOR" style="csursor: pointer;position: absolute;font-size: 8pt;color: blue;" :style="{top:item.top+'px',left:item.x1-1+'px',height:svgHeight/rows-3+'px',lineHeight:svgHeight/rows+'px'}" v-for="(item,index) in xArray">
          <span v-if="item.obj.ITEM_NAME=='七氟烷1'" style="padding: 0 2px 0 0px;">{{item.obj.CONCENTRATION}}{{item.obj.CONCENTRATION_UNIT}}</span>
          <span v-else style="padding: 0 2px 0 0px;">{{item.obj.DOSAGE}}</span>
        </div>
        <div v-if="item.obj.DURATIVE_INDICATOR=='1'" style="position: absolute;font-size: 8pt;color: blue;" :style="{top:item.top+2+'px',left:item.x1+item.w/2-15+'px',height:svgHeight/rows-3+'px',lineHeight:svgHeight/rows+'px'}" v-for="(item,index) in xArray">
          <span v-if="item.obj.ITEM_NAME=='七氟烷1'" style="padding: 0 2px 0 0px;display: block;width: 16px;text-align: center;">{{item.obj.CONCENTRATION}}{{item.obj.CONCENTRATION_UNIT}}</span>
          <span v-else style="padding: 0 2px 0 0px;">{{item.obj.DOSAGE}}
            <span v-if="item.obj.PERFORM_SPEED">
              ({{item.obj.CONCENTRATION}}{{item.obj.CONCENTRATION_UNIT}} {{item.obj.PERFORM_SPEED}}{{item.obj.SPEED_UNIT}})
            </span>
          </span>
        </div>
        <div v-if="tipView">
          <div style="position: absolute;max-width:300px;min-width:220px;width:auto;background-color: white;border: 0.5px solid;padding: 3px;font-size: 12px;z-index: 15" :style="{ top:tipTop+'px',left:tipLeft+'px'}">
            <div>
              {{lineObj.ITEM_NAME}}({{lineObj.DOSAGE_UNITS}})
            </div>
            <div>
              =========
            </div>
            <div>
              开始时间：{{lineObj.START_TIME}}
            </div>
            <div v-if="lineObj.DURATIVE_INDICATOR=='1'&&lineObj.ENDDATE">
              结束时间：{{lineObj.ENDDATE}}
            </div>
            <div v-if="lineObj.DURATIVE_INDICATOR=='1'&&lineObj.PERFORM_SPEED">
              流速：{{lineObj.PERFORM_SPEED}}
            </div>
            <div v-if="lineObj.DOSAGE">
              总量：{{lineObj.DOSAGE}}
            </div>
            <div v-if="lineObj.nowTime">
              鼠标当前时间：{{lineObj.nowTime}}
            </div>
          </div>
        </div>
        <!-- 持续用药 -->
        <div v-if="item.obj.DURATIVE_INDICATOR=='1'" style="position: absolute;z-index: 5;" :style="{top:item.y1-svgHeight/rows/8+'px',left:item.x1+'px',width:item.w+'px',height:svgHeight/rows/4+'px'}" @mouseenter="showTipInfo(item,$event)" @mouseleave="hideTipInfo()" v-for="item in xArray" @mousemove.stop="mouseMoveInfo(item,$event)" @dblclick="openChangeEvent(item)">
        </div>
      </div>
    </div>
    <div v-else>
      <div style="max-height: 20px;">
        <div v-for="(item,index) in xTimeArray" v-if="index%3==0" style="width: 28px;margin-left: -10px;font-size: 12px;display: inline-block;">{{item}}</div>
        <div v-else style="width: 12px;display: inline-block;"></div>
      </div>
      <div>
        <div v-for="(item,index) in dataArray" :style="{top:svgHeight/rows*index+20+'px'}" style="height: 14px;line-height: 12px;width: 165px;border-bottom: 1px solid #8391a2;  font-size: 12px;position: absolute;left: -165px;">
        </div>
      </div>
      <div style="position: relative;">
        <svg :width="svgWidth" :height="svgHeight" id="tableSvg">
          <g v-for="item in lineArray">
            <line :x1="item.x.x1" :x2="item.x.x1" y1="0" :y2="svgHeight" style="stroke:#8391a2;stroke-width:0.5px;"></line>
          </g>
          <g v-for="item in lineArray">
            <line x1="0" x2="700" :y1="item.y.y1" :y2="item.y.y1" style="stroke:#8391a2;stroke-width:0.5px;"></line>
          </g>
        </svg>
      </div>
    </div>
  </div>
</template>
<script type="text/javascript">
import * as d3 from 'd3';
import Bus from '@/bus.js';
import dateTime from '@/components/plugins/dateTime.vue';
export default {
  data() {
    return {
      data: [],
      line: '',
      rows: 8,
      columns: 50,
      handleItem: {},
      wd: 0,
      ht: 0,
      xTimeArray: [],
      svgWidth: 700,
      svgHeight: 150,
      svgPadding: 0,
      //每个格子代表时间(分钟)
      tbMin: 5,
      dataArray: [],
      //数据提示窗口宽度
      tipWidth: 200,
      tipHeight: 150,
      tipTop: 0,
      tipLeft: 0,
      tipView: false,
      lineObj: {},
      xArray: [],
      lineArray: [],
      percentPageData: [],
      setTimeId: '', //定时器返回的一个ID
      svgObj: '',
      roadList: [], //途径列表
      concentrationList: [], //用药浓度列表,
      speedUnitList: [], //速度单位列表
      dosageUnitsList: [], //用药单位列表
      eventObj: {},
      changeEventView: false,
      tempObj: {},
      tempObj1: {},

    }
  },
  methods: {
    getLineXy() {
      var array = [];
      for (var i = 0; i < this.columns; i++) {
        array.push({
          x: {
            x1: i * (this.svgWidth / this.columns),
            x2: i * (this.svgWidth / this.columns),
          },
          y: {
            y1: i * (this.svgHeight / this.rows),
            y2: i * (this.svgHeight / this.rows)
          }
        })
      }
      this.lineArray = array;

    },

    //对时间进行计算操作
    timeControl(startTime) {
      var m = this.tbMin; //加几分钟
      var timeDate = new Date(startTime);
      var toMin = timeDate.getTime() + 1000 * 60 * m;
      var timeArray = [];
      let startMinTime = this.config.startMinTime
      let defaultTime = new Date().Format("yyyy-MM-dd") + " 08:00"
      let pageOper = this.config.pageOper

      if (pageOper == 0 && startMinTime) {
        for (var i = 0; i <= this.columns; i++) {

          timeArray.push(new Date(new Date(this.config.initTime).getTime() + 1000 * 60 * m * i).Format("hh:mm"));
        }
      } else if (!startMinTime && pageOper == 0) {
        for (var i = 0; i <= this.columns; i++) {
          timeArray.push(new Date(new Date(defaultTime).getTime() + 1000 * 60 * m * i).Format("hh:mm"));
        }
      } else {
        for (var i = 0; i <= this.columns; i++) {

          timeArray.push(new Date(this.config.initTime.getTime() + 1000 * 60 * m * i).Format("hh:mm"));
        }
      }

      // this.config.initTime = new Date(timeDate.getTime());
      // this.config.maxTime = new Date(timeDate.getTime() + 1000 * 60 * m * this.columns);
      this.xTimeArray = timeArray;
      this.$nextTick(function() {
        this.getLineXy();
        if (this.page == false) {
          if (this.setTimeId) {
            this.selectMedAnesthesiaEventListNoTime();
          } else {
            this.selectMedAnesthesiaEventList();
          }

        }
      })

    },
    timeControlNoTime(startTime) {
      var m = this.tbMin; //加几分钟
      var timeArray = [];
      let startMinTime = this.config.startMinTime
      let defaultTime = new Date().Format("yyyy-MM-dd") + " 08:00"
      if (this.config.pageOper == 0 && startMinTime) {
        for (var i = 0; i <= this.columns; i++) {

          timeArray.push(new Date(new Date(this.config.initTime).getTime() + 1000 * 60 * m * i).Format("hh:mm"));
        }
      } else if (!startMinTime && this.config.pageOper == 0) {
        for (var i = 0; i <= this.columns; i++) {
          timeArray.push(new Date(new Date(defaultTime).getTime() + 1000 * 60 * m * i).Format("hh:mm"));
        }
      } else {
        for (var i = 0; i <= this.columns; i++) {

          timeArray.push(new Date(this.config.initTime.getTime() + 1000 * 60 * m * i).Format("hh:mm"));
        }
      }
      this.xTimeArray = timeArray;
      this.$nextTick(function() {
        this.getLineXy();
        if (this.page == false) {
          this.selectMedAnesthesiaEventListNoTime();
        }
      })

    },
    //时间初始化显示
    xTimeInit() {
      if (!this.page) {
        this.timeControl(this.config.startMinTime);
      } else {
        this.timeControl(new Date().Format("yyyy-MM-dd") + " 08:00");
      }

    },
    //加载病人麻醉事件里面麻醉用药数据
    selectMedAnesthesiaEventList() {
      if (this.setTimeId) {
        clearTimeout(this.setTimeId)
      }
      var w = this.svgWidth,
        lMin = this.tbMin,
        h = this.svgHeight;
      let params = {
        patientId: this.config.userInfo.patientId,
        operId: this.config.userInfo.operId,
        visitId: this.config.userInfo.visitId,
        itemClass: '2C4',
        eventNo: this.config.eventNo
      }
      for (var i = 0; i < this.rows; i++) {
        this.dataArray.push(i);
      }
      if (this.page) {
        return;
      }

      this.api.selectMedAnesthesiaEventList(params)
        .then(res => {
          if (res.list.length > 0) {

            this.dataOperChange(res.list);
          }
          // this.setTimeId = setTimeout(_ => this.selectMedAnesthesiaEventList(), this.config.timeSet)

        });
    },
    //不加定时器的方法
    selectMedAnesthesiaEventListNoTime() {

      this.dataArray = [];
      this.xArray = [];
      var w = this.svgWidth,
        lMin = this.tbMin,
        h = this.svgHeight;
      let params = {
        patientId: this.config.userInfo.patientId,
        operId: this.config.userInfo.operId,
        visitId: this.config.userInfo.visitId,
        itemClass: '2C4',
        eventNo: this.config.eventNo
      }
      for (var i = 0; i < this.rows; i++) {
        this.dataArray.push(i);
      }
      if (this.page) {
        return;
      }

      this.api.selectMedAnesthesiaEventList(params)
        .then(res => {
          var list = res.list;
          this.dataOperChange(list);
        });
    },
    //计算时间差分钟
    getMinuteDif(startTime, endTime) {
      let sTime = new Date(startTime).getTime()
      let enTime = new Date(endTime).getTime()
      var min = '';
      min = (enTime - sTime) / 1000 / 60;
      return Math.round(min)

    },
    createLine(x1, x2, y1, y2, obj) {
      var _this = this;
      if (obj.DURATIVE_INDICATOR == 1 && (obj.ENDDATE == null || obj.ENDDATE == "")) {
        // setTimeout(() => {
        this.svgObj.append("line")
          .attr('stroke-width', 1)
          .attr("stroke", "blue")
          .attr("class", "test")
          .attr("y1", y1 - 4)
          .attr("y2", y2 + 4)
          .attr("x1", x1)
          .attr("x2", x1)
        if (x2 - x1 > 0) {
          this.svgObj.append("path")
            .attr('d', this.drawLineArrow(x1, y1, x2, y2))
            .attr('stroke-width', 1)
            .attr("stroke", "blue")
            .attr("class", "test")
        }
        // }, 500)

      }
      if (obj.DURATIVE_INDICATOR == 1 && obj.ENDDATE != null && obj.ENDDATE != "") {
        // setTimeout(() => {
        _this.svgObj.append("line")
          .attr('stroke-width', 1)
          .attr("stroke", "blue")
          .attr("class", "test")
          .attr("y1", y1 - 4)
          .attr("y2", y2 + 4)
          .attr("x1", x1)
          .attr("x2", x1)
        _this.svgObj.append("line")
          .attr("stroke", "blue")
          .attr("stroke-width", 1)
          .attr("class", "test")
          .attr("y1", y1)
          .attr("y2", y2)
          .attr("x1", x1)
          .attr("x2", x2)
        _this.svgObj.append("line")
          .attr('stroke-width', 1)
          .attr("stroke", "blue")
          .attr("class", "test")
          .attr("y1", y1 - 4)
          .attr("y2", y2 + 4)
          .attr("x1", x2)
          .attr("x2", x2)
        // }, 500)

      }

    },
    drawLineArrow(x1, y1, x2, y2) {
      var path;
      var slopy, cosy, siny;
      var Par = 6.0;
      var x3, y3;
      slopy = Math.atan2((y1 - y2), (x1 - x2));
      cosy = Math.cos(slopy);
      siny = Math.sin(slopy);

      path = "M" + x1 + "," + y1 + " L" + x2 + "," + y2;

      x3 = (Number(x1) + Number(x2)) / 2;
      y3 = (Number(y1) + Number(y2)) / 2;

      path += " M" + x2 + "," + y2;

      path += " L" + (Math.round(x2) + Math.round(Par * cosy - (Par / 2.0 * siny))) + "," + (Math.round(y2) + Math.round(Par * siny + (Par / 2.0 * cosy)));

      path += " M" + (Math.round(x2) + Math.round(Par * cosy + Par / 2.0 * siny) + "," + (Math.round(y2) - Math.round(Par / 2.0 * cosy - Par * siny)));
      path += " L" + x2 + "," + y2;

      return path;
    },
    getTime() {
      return new Date().Format("yyyy-MM-dd hh:mm:ss")
    },
    showTipInfo(item, ev) {
      this.tipView = true;
      this.tipLeft = ev.offsetX + item.x1;
      this.tipTop = item.top + 15;
      if (item.obj.ENDDATE == null || item.obj.ENDDATE == "") {
        item.obj.ENDDATE = this.config.patientMaxTime;
        item.obj.endTime = ""
      }
      this.lineObj = item.obj;
    },
    hideTipInfo() {
      this.tipView = false;
      this.lineObj = '';
    },
    mouseMoveInfo(item, ev) {

      item.obj.nowTime = this.getTime();
      var gWidth = this.svgWidth / this.columns;
      this.$set(this.lineObj, "nowTime", this.getTime());
      //var ev = ev || event;
      var offX = ev.offsetX + item.x1; //横坐标值
      var m = Math.round(offX / gWidth * 5);

      var time = new Date(this.config.userInfo.inDateTime);
      var time = this.config.initTime;
      var time1 = time.getTime() + m * 60 * 1000;
      var time2 = new Date(time1).Format("yyyy-MM-dd hh:mm");
      item.obj.nowTime = time2;
      this.tipLeft = ev.offsetX + item.x1;
      this.lineObj = item.obj;
    },

    //翻页
    pageChange() {
      var svg = d3.selectAll(".test")

      if (svg) {
        svg.remove();
      }
      this.xArray = [];
      this.$nextTick(function() {
        if (this.config.pageOper == 0) {
          this.config.pageNum = 1;
          this.xTimeInit();
        }
        if (this.config.pageOper == -1) {
          this.timeControl(this.config.initTime)
          return

          let m = this.config.initTime.getTime() - 250 * 60 * 1000;
          // this.timeControl(new Date(m));
          var list = [];
          list = this.percentPageData;

          for (var i = 0; i < list.length; i++) {
            if (this.config.pagePercentNum != 1 && list[i].PATIENT_ID) {
              list[i].vStartTime = this.config.initTime.Format("yyyy-MM-dd hh:mm:ss");
            }
          }
        }
        if (this.config.pageOper == 1) {
          this.timeControl(this.config.maxTime)
          return
          let arrList = this.dataArray;
          this.percentPageData = arrList;
          var arrayList = [];
          var list = this.dataArray;
          for (var i = 0; i < list.length; i++) {
            if (list[i].PATIENT_ID) {
              if (list[i].ENDDATE == null || list[i].ENDDATE == "") {

                if (new Date(this.config.patientMaxTime) > new Date(this.config.initTime)) {
                  list[i].vStartTime = new Date(this.config.initTime).Format("yyyy-MM-dd hh:mm:ss");
                  arrayList.push(list[i]);
                } else {}
              } else {
                if (new Date(list[i].ENDDATE) > new Date(this.config.initTime)) {
                  list[i].vStartTime = new Date(this.config.initTime).Format("yyyy-MM-dd hh:mm:ss");
                  arrayList.push(list[i]);
                } else {

                }
              }
            }
          }

        }
        if (this.config.pageOper == 2) {
          this.timeControl(this.config.maxTime)
        }
      })


    },
    //处理数据进行划线
    dataOperChange(list) {
      let svg = d3.selectAll(".test")
      svg.remove();
      this.xArray = [];
      this.dataArray = [];
      let dataArr = []
      var w = this.svgWidth,
        lMin = this.tbMin,
        h = this.svgHeight,
        m = 0;
      for (var i = 0; i < list.length; i++) {
        if (list[i].START_TIME) {
          if (i == -1)
            break;
          else {
            if (list[i].DURATIVE_INDICATOR == 1) {
              //开始时间间隔
              let sMin = ''
              //结束时间间隔
              let eMin = ''
              let maxPatTime = this.config.patientMaxTime
              //判断是否在当前时间内
              if (new Date(list[i].START_TIME) > new Date(this.config.maxTime)) {
                continue;
              }
              if (list[i].DURATIVE_INDICATOR == 0) {
                if (new Date(list[i].START_TIME) < new Date(this.config.initTime)) {
                  continue;
                }
              }
              //如果是持续用药
              if (list[i].DURATIVE_INDICATOR == 1) {
                //判断是否有结束时间
                if (list[i].ENDDATE) {
                  if (new Date(list[i].ENDDATE) < new Date(this.config.initTime)) {
                    continue;
                  }
                } else {
                  if (new Date(this.config.patientMaxTime) < new Date(this.config.initTime)) {
                    continue;
                  }
                }
              }
              sMin = this.getMinuteDif(this.config.initTime, list[i].START_TIME);
              if (sMin < 0) {
                sMin = 0;
              }

              //如果病人这个用药没有结束时间那么默认使用过程中最大的时间
              if (list[i].ENDDATE == null || list[i].ENDDATE == "") {
                if (new Date(this.config.patientMaxTime) > this.config.maxTime) {
                  eMin = this.getMinuteDif(this.config.initTime, this.config.maxTime);
                } else {
                  eMin = this.getMinuteDif(this.config.initTime, new Date(this.config.patientMaxTime));
                }
              } else {

                if (new Date(list[i].ENDDATE) >= this.config.maxTime) {
                  eMin = this.getMinuteDif(this.config.initTime, this.config.maxTime);
                } else if (this.config.initTime < new Date(list[i].ENDDATE) < this.config.maxTime) {
                  eMin = this.getMinuteDif(this.config.initTime, new Date(list[i].ENDDATE));
                } else {
                  // list[i].DURATIVE_INDICATOR = 0;
                  eMin = 0;
                }
              }
              let x1 = Math.round(sMin / lMin * (w / this.columns))
              let x2 = Math.round(eMin / lMin * (w / this.columns))
              let y1
              let y2
              let flag = true;
              let topi
              y1 = Math.round(h / this.rows / 2 * (m + 1) + h / this.rows * m / 2)
              y2 = y1
              //判断是否同一种药品
              if (this.dataArray.length > 0) {
                for (var j = 0; j < this.dataArray.length; j++) {
                  if (list[i].ITEM_NAME == this.dataArray[j].ITEM_NAME && list[i].ITEM_CLASS == this.dataArray[j].ITEM_CLASS) {
                    y1 = Math.round(h / this.rows / 2 * (j + 1) + h / this.rows * j / 2)
                    y2 = y1
                    flag = false;
                    topi = j;
                    break;
                  }
                }
              }
              list[i].vStartTime = '';
              list[i].nowTime = '';
              let _this = this
              // list[i].PERFORM_SPEED = list[i].PERFORM_SPEED*(eMin-sMin)
              if (list[i].DURATIVE_INDICATOR == 1 && x2 >= 0 && m < this.rows) {
                this.createLine(x1, x2, y1, y2, list[i]);


              }
              if (flag) {
                if (m < this.rows) {
                  this.xArray.push({
                    x1: x1,
                    y1: y1,
                    x2: x2,
                    y2: y2,
                    w: x2 - x1,
                    obj: list[i],
                    top: m * (this.svgHeight / this.rows)

                  })
                  this.dataArray.push(list[i]);
                  m++;
                }


              } else {
                this.xArray.push({
                  x1: x1,
                  y1: y1,
                  x2: x2,
                  y2: y2,
                  w: x2 - x1,
                  obj: list[i],
                  top: topi * (this.svgHeight / this.rows)
                })

              }
            }
          }
        }
      }

      for (var k = 0; k < this.rows - m; k++) {
        this.dataArray.push(m)
      }
    },
    openChangeEvent(item) {
      this.tempObj = item.obj
      let params = {
        patientId: item.obj.PATIENT_ID,
        operId: item.obj.OPER_ID,
        visitId: item.obj.VISIT_ID,
        itemNo: item.obj.ITEM_NO,
        eventNo: item.obj.EVENT_NO,
        itemName: item.obj.ITEM_NAME
      }
      this.api.selectMapByKey(params)
        .then(res => {
          this.eventObj = res
          this.tempObj1 = res
        })
      // this.eventObj = item.obj
      this.changeEventView = true
    },
    //取消编辑事件
    cancleEditeEvent() {
      debugger
      let params = {
        patientId: this.tempObj1.PATIENT_ID,
        operId: this.tempObj1.OPER_ID,
        visitId: this.tempObj1.VISIT_ID,
        itemNo: this.tempObj1.ITEM_NO,
        eventNo: this.tempObj1.EVENT_NO
      }
      this.api.selectMapByKey(params)
        .then(res => {
          this.eventObj = res
          this.tempObj1 = res
        })
      // this.eventObj = this.tempObj1

    },
    //关闭修改事件界面
    closeChangeEvent() {
      this.eventObj = {}
      this.tempObj1 = {}
      this.tempObj = {}
      this.changeEventView = false
    },
    //获取途径列表
    getRoadList() {
      this.api.getMedAnesthesiaCommDictByItemClass({
          itemClass: '用药途径'
        })
        .then(
          res => {
            this.roadList = res.list;
          });
      this.api.getMedAnesthesiaCommDictByItemClass({
          itemClass: '用药浓度单位'
        })
        .then(
          rest => {
            this.concentrationList = rest.list
          });
      this.api.getMedAnesthesiaCommDictByItemClass({
          itemClass: '用药速度单位'
        })
        .then(
          rs => {
            this.speedUnitList = rs.list
          });
      this.api.getMedAnesthesiaCommDictByItemClass({
          itemClass: '用药单位'
        })
        .then(
          rt => {
            this.dosageUnitsList = rt.list
          });
    },
    //保存事件修改
    saveEvent() {
      // console.log(this.tempObj)
      // console.log(this.eventObj)
      // return
      // console.log(new Date(this.tempObj.START_TIME))
      // console.log(new Date(this.eventObj.START_TIME))
      if (this.tempObj.START_TIME != null && new Date(this.tempObj.START_TIME) < new Date(this.eventObj.START_TIME)) {
        //插入一条术中登记记录
        let arr1 = []
        let arr2 = []
        let insertparams = {
          patientId: this.eventObj.PATIENT_ID,
          operId: this.eventObj.OPER_ID,
          visitId: this.eventObj.VISIT_ID,
          eventNo: this.eventObj.EVENT_NO,
          itemName: this.eventObj.ITEM_NAME,
          administrator: this.eventObj.ADMINISTRATOR,
          concentration: this.eventObj.CONCENTRATION,
          concentrationUnit: this.eventObj.CONCENTRATION_UNIT,
          performSpeed: this.eventObj.PERFORM_SPEED,
          speedUnit: this.eventObj.SPEED_UNIT,
          dosage: this.eventObj.DOSAGE,
          itemClass: this.eventObj.ITEM_CLASS,
          dosageUnits: this.eventObj.DOSAGE_UNITS,
          durativeIndicator: this.eventObj.DURATIVE_INDICATOR,
          startTime: new Date(this.eventObj.START_TIME),
          endDate: this.eventObj.ENDDATE ? new Date(this.eventObj.ENDDATE) : '',
        }
        if (this.eventObj.ENDDATE == this.tempObj.ENDDATE) {
          insertparams.endDate = ''
        }
        arr1.push(insertparams)
        this.api.insertMedAnesthesiaEventBatch(arr1)
          .then(res => {
            let updateparms = {
              patientId: this.tempObj.PATIENT_ID,
              operId: this.tempObj.OPER_ID,
              visitId: this.tempObj.VISIT_ID,
              itemNo: this.tempObj.ITEM_NO,
              eventNo: this.tempObj.EVENT_NO,
              itemName: this.tempObj.ITEM_NAME,
              administrator: this.tempObj.ADMINISTRATOR,
              concentration: this.tempObj.CONCENTRATION,
              concentrationUnit: this.tempObj.CONCENTRATION_UNIT,
              performSpeed: this.tempObj.PERFORM_SPEED,
              speedUnit: this.tempObj.SPEED_UNIT,
              dosage: this.tempObj.DOSAGE,
              itemClass: this.tempObj.ITEM_CLASS,
              dosageUnits: this.tempObj.DOSAGE_UNITS,
              durativeIndicator: this.tempObj.DURATIVE_INDICATOR,
              startTime: new Date(this.tempObj.START_TIME),
              endDate: new Date(this.eventObj.START_TIME),
            }
            if (this.tempObj1.ENDDATE == null || this.tempObj1.ENDDATE) {
              updateparms.endDate = new Date(this.eventObj.START_TIME)
            } else {
              updateparms.endDate = new Date(this.tempObj1.ENDDATE)
            }
            arr2.push(updateparms)
            this.api.updateMedAnesthesiaEventBatch(arr2)
              .then(rest => {
                if (rest.success) {
                  this.xTimeInit();
                  alert("保存成功")
                } else {
                  alert("保存失败")
                }
              })
          })



      }
      //否则更新
      else {
        let arr = []
        let params = {
          patientId: this.eventObj.PATIENT_ID,
          operId: this.eventObj.OPER_ID,
          visitId: this.eventObj.VISIT_ID,
          itemNo: this.eventObj.ITEM_NO,
          eventNo: this.eventObj.EVENT_NO,
          itemName: this.eventObj.ITEM_NAME,
          administrator: this.eventObj.ADMINISTRATOR,
          concentration: this.eventObj.CONCENTRATION,
          concentrationUnit: this.eventObj.CONCENTRATION_UNIT,
          performSpeed: this.eventObj.PERFORM_SPEED,
          speedUnit: this.eventObj.SPEED_UNIT,
          dosage: this.eventObj.DOSAGE,
          itemClass: this.eventObj.ITEM_CLASS,
          dosageUnits: this.eventObj.DOSAGE_UNITS,
          durativeIndicator: this.eventObj.DURATIVE_INDICATOR,
          startTime: new Date(this.eventObj.START_TIME),
          endDate: this.eventObj.ENDDATE ? new Date(this.eventObj.ENDDATE) : '',
        }

        arr.push(params)
        this.api.updateMedAnesthesiaEventBatch(arr)
          .then(res => {
            if (res.success) {
              this.xTimeInit();
              alert("保存成功")
            } else {
              alert("保存失败")
            }
          })
      }

    }
  },
  mounted() {
    this.getRoadList();
    this.svgObj = d3.select("#tableSvg")
    if (this.setTimeId) {
      clearTimeout(this.setTimeId);
    }
    this.xTimeInit();

  },
  created() {
    Bus.$on('test', this.pageChange);
    Bus.$on('timeSetChange', this.timeControlNoTime)

  },
  beforeDestroy() {
    Bus.$off('test', this.pageChange);
    Bus.$off('timeSetChange', this.timeControlNoTime)
    clearTimeout(this.setTimeId);

  },
  components: {
    dateTime
  },
  props: ['page'],
  computed: {

  }
}

</script>
<style scoped>
svg {
  /*margin: 25px;*/
}

path {
  fill: none;
  stroke: red;
  stroke-width: 1px;
}

.addData {
  width: 700px;
  min-height: 340px;
  background-color: white;
  z-index: 3;
  position: absolute;
  top: calc(50% - 250px);
  left: calc(50% - 350px);
  border: 2px solid rgb(61, 164, 206);
  box-shadow: 1px 1px 20px #AAA;
  background-color: #E3EFFF;
}

</style>
