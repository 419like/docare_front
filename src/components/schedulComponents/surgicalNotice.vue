<template>
  <div style="margin: 10px 30px;width: 1300px;padding-top:100px;">
    <div>
      <input type="date" v-model="dateTimeValue">
      <button @click="getView">查询</button>
      <button @click="print">打印</button>
    </div>
    <div ref="tableView" style="">
      <div style="display:flex;top:-25px;left:50%;margin-left:-400px;justify-content:space-between;width:800px;margin:auto;width:950px;">
        <div></div>
        <div style="text-align:center;font-size:20px;font-weight:blod;">毕节市中医医院麻醉科手术通知单</div>
        <div style="font-size:20px;font-weight:blod;">{{'共计'+allNo+'台'}}</div>
      </div>
      <table style="border-collapse:collapse;width: 100%;font-size: 10.5pt;background: white;text-align: center;" border="1" cellspacing="0" cellpadding="0">
        <thead>
          <th v-for="item in tableTitleList" style="font-weight: normal;padding: 5px" :style="{width:item.width+'px'}">
            {{item.title}}
          </th>
        </thead>
        <tbody>
          <tr v-for="tr in viewList">
            <td v-for="td in tableTitleList" style="word-wrap:break-word;word-break:break-all;">
              {{tr[td.field]}}
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>
<script type="text/javascript">
import { getLodop } from '@/assets/js/LodopFuncs';
let LODOP
export default {
  data() {
    return {
      viewList: [],
      allNo: '',
      dateTimeValue: '',
      tableTitleList: [{
        title: '手术间',
        field: 'OPERATING_ROOM_NO',
        width: 60,
      }, {
        title: '台次',
        field: 'SEQUENCE',
        width: 40,
      }, {
        title: '科室',
        field: 'OPERATING_DEPT_NAME',
        // width:50,
      }, {
        title: '床号',
        field: 'BED_NO',
        width: 50,
      }, {
        title: '姓名',
        field: 'PAT_NAME'
        // ,width:50,
      }, {
        title: '性别',
        field: 'SEX',
        width: 40,
      }, {
        title: '年龄',
        field: 'PAT_AGE',
        width: 40,
      }, {
        title: '住院号',
        field: 'INP_NO',
        // width:50,
      }, {
        title: '术前诊断',
        field: 'DIAG_BEFORE_OPERATION',
        // width:50,
      }, {
        title: '手术名称',
        field: 'OPERATION_NAME',
        // width:50,
      }, {
        title: '主刀医生',
        field: 'SURGEON_NAME',
        // width:50,
      }, {
        title: '手术助手',
        field: 'FIRST_ASSISTANT_NAME',
        // width:50,
      }, {
        title: '麻醉方式',
        field: 'ANESTHESIA_METHOD',
        // width:50,
      }, {
        title: '主麻',
        field: 'ANESTHESIA_DOCTOR_NAME',
        // width:50,
      }, {
        title: '助手',
        field: 'ANESTHESIA_ASSISTANT_NAME',
        // width:50,
      }, {
        title: '洗手',
        field: 'FIRST_OPERATION_NURSE_NAME',
        // width:50,
      }, {
        title: '巡回',
        field: 'FIRST_SUPPLY_NURSE_NAME',
        // width:50,
      }, {
        title: '备注',
        field: 'SPECIAL_EQUIPMENT',
        width: 110
      },
      ]
    }
  },
  methods: {
    getView() {
      let params = {
        dateTime: this.dateTimeValue
      }
      this.api.getScheduleReportView(params)
        .then(res => {
          if (res.list.length == 0) {
            alert("所选日期没有手术")
          }
          this.viewList = res.list;
          console.log(this.viewList)
          this.allNo = res.list.length;
        })
    },
    print() {
      LODOP = getLodop();
      // LODOP.ADD_PRINT_TABLE(100, 1, "99.8%", 250, this.$refs.tableView.innerHTML);
      // console.log(this.$refs.tableView.innerHTML)
      LODOP.ADD_PRINT_HTM(10, 5, "99%", "BottomMargin:1mm", this.$refs.tableView.innerHTML);
      LODOP.PREVIEW();
    }
  },
  mounted() {
    this.dateTimeValue = new Date().Format("yyyy-MM-dd");
    this.getView()

  }
}

</script>
