<template>
  <div style="height:65%;">
    <div style="display: flex;height: 100%;background:white;margin:10px;">
      <!-- 显示类别 -->
      <div style="box-sizing:border-box;height: 100%;width: 30%;overflow-x: auto;padding-top:5px;border-right: 2px solid rgb(177,207,243);">
        <ul v-for="item in eventChildData">
          <li class="hoverStyle" style="cursor:pointer;" @click="getTypeDetail(item)">
            <div style="margin-left: 20px;">{{item.typeName}}</div>
          </li>
        </ul>
      </div>
      <!-- 显示详细内容 -->
      <div style="width: 70%;height:100%;margin-top:5px;position: relative;">
        <div ref="headScroll" style="width: 100%;position: absolute;left:0;top:0;z-index: 10;overflow: hidden; ">
          <div style="display: flex;margin-left: 10px;width:2592px;">
            <div class="topList" v-for="cell in contentConfig">{{cell.text}}</div>
          </div>
        </div>
        <div ref="contentScroll" style="height:calc(100% - 23px);width: 100%;overflow-y: auto;position: absolute;left:0;top:23px;">
          <div style="width:2592px;">
            <div v-for="list in commonTypeList" style="display: flex;margin-left: 10px;" @click="getItem(list)">
              <div v-for="cl in contentConfig" style="width: 160px;border:1px solid rgb(177,207,243);">
                <div style="height:100%;width:160px;" v-if="cl.status=='inable'">
                  <input v-if="list.writeAble" type="text" v-model="list[cl.value]" @blur="inputBlur(list)" @keyup="chan(list)" style="display:block;width:100%;border:0;height:100%;outline:none;">
                  <input v-if="!list.writeAble" type="text" v-model="list[cl.value]" @click="valueWriteAble(list)" style="display:block;width:100%;border:0;height:100%;outline:none;">
                </div>
                <div style="width:160px;" v-if="cl.status!='inable'">
                  {{list[cl.value]}}
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div style="text-align: right;margin-right: 30px;">
      <button style="width: 100px;height: 30px;" @click="addMedAnesthesiaInputDict" :disabled="isAdd">新增</button>
      <button style="width: 100px;height: 30px;" :disabled="isSave" @click="saveValue">保存</button>
      <button style="width: 100px;height: 30px;" @click="cancleEdit" :disabled="isCancle">取消</button>
      <button style="width: 100px;height: 30px;" :disabled="isDelete" @click="deleteMedAnesthesiaEventOpen">删除</button>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      comTypeList: [],
      tempTypeItem: "",
      isAdd: true,
      obj: "",
      isSave: true,
      isCancle: true,
      isDelete: true,
      contentConfig: [{
          text: "序号",
          value: "itemNo"

        },
        {
          text: "事件分类",
          value: "itemClass"
        },
        {
          text: "事件名称",
          value: "itemName",
          status: "inable"
        },
        {
          text: "药品规格",
          value: "itemSpec",
          status: "inable"
        },
        {
          text: "收费分类",
          value: "relBill",
          status: "inable"
        },
        {
          text: "剂量",
          value: "dosage",
          status: "inable"
        },
        {
          text: "单位",
          value: "dosageUnits",
          status: "inable"
        },
        {
          text: "浓度",
          value: "concentration",
          status: "inable"
        },
        {
          text: "单位",
          value: "concentrationUnit",
          status: "inable"
        },
        {
          text: "用药速度",
          value: "performSpeed",
          status: "inable"
        },
        {
          text: "流速单位",
          value: "speedUnit",
          status: "inable"
        },
        {
          text: "途径",
          value: "administrator",
          status: "inable"
        },
        {
          text: "持续",
          value: "durativeIndicator",
          status: "inable"
        },
        {
          text: "属性",
          value: "eventAttr",
          status: "inable"
        },
        {
          text: "药品属性",
          value: "eventAttr2",
          status: "inable"
        },
        {
          text: "医嘱分类",
          value: "operClass",
          status: "inable"
        },
      ],
      commonTypeList: [],
      changeData: [],
    }
  },
  props: [
    'eventChildData'
  ],
  methods: {
    chan(list) {
      this.isSave = false;
      this.isCancle = false;

    },
    inputBlur(list) {
      list.writeAble = false;
      if (list.arrayStats == 0) {

      } else {
        this.changeData.push(list)

      }
    },
    valueWriteAble(list) {
      this.obj = list;
      this.isSave = true;
      list.writeAble = true;
    },
    getTypeDetail(item) {
      this.tempTypeItem = item;
      this.isAdd = false;
      let params = {
        itemClass: item.typeId
      }
      this.api.medAnesthesiaEventOpenByItemClass(params)
        .then(
          res => {
            this.commonTypeList = res.list;
          });
    },
    addMedAnesthesiaInputDict() {
      this.commonTypeList.push({
        arrayStats: 0, //0为新增
        itemNo: this.commonTypeList.length + 1,
        itemClass: this.tempTypeItem.typeId,
        itemName: '',
        itemSpec: "",
        itemSpec: "",
        relBill: "",
        dosage: "",
        dosageUnits: "",
        concentration: "",
        concentrationUnit: "",
        performSpeed: "",
        speedUnit: "",
        administrator: ""
      });
      this.isAdd = true;
      this.isCancle = false;
    },
    cancleEdit() {
      this.changeData = [];
      this.getTypeDetail(this.tempTypeItem);
      this.isCancle = false;
      this.isAdd = false;
      this.isSave = true;
      this.isDelete = true;
    },
    saveValue() {
      var li = this.commonTypeList;
      var k = li.length - 1;
      let params = {};

      if (li[k].arrayStats == 0) {
        params = {
          itemNo: li[k].itemNo,
          itemClass: li[k].itemClass,
          itemName: li[k].itemName,
          itemSpec: li[k].itemSpec,
          relBill: li[k].relBill,
          dosage: li[k].dosage,
          dosageUnits: li[k].dosageUnits,
          concentration: li[k].concentration,
          concentrationUnit: li[k].concentrationUnit,
          performSpeed: li[k].performSpeed,
          speedUnit: li[k].speedUnit,
          administrator: li[k].administrator
        };
        console.log(params)
        this.api.insertMedAnesthesiaEventOpen(params)
          .then(
            res => {
              this.getTypeDetail(this.tempTypeItem);
            });
      } else {
        this.api.updateMedAnesthesiaEventOpenBatch(this.changeData)
          .then(
            res => {
              this.changeData = [];
              this.getTypeDetail(this.tempTypeItem);
              if (res.success) {
                alert("保存成功")
              } else {
                alert("保存失败")
              }
            });
      }
      this.isCancle = true;
      this.isAdd = false;
      this.isSave = true;
      this.isDelete = true;
    },
    deleteMedAnesthesiaEventOpen() {

      console.log(this.obj.itemNo);
      let params = {
        itemNo: this.obj.itemNo,
        itemClass: this.obj.itemClass,
      }
      this.api.deleteMedAnesthesiaEventOpen(params)
        .then(
          res => {
            this.changeData = [];
            this.getTypeDetail(this.tempTypeItem);
            this.isDelete = true;
          });
    },
    getItem(item) {
      this.obj = item;
      this.isDelete = false;
    },

  },
  mounted() {
    //滚动条监听
    let conDom = this.$refs.contentScroll;
    let headDom = this.$refs.headScroll;
    conDom.onscroll = () => {
      headDom.scrollLeft = conDom.scrollLeft

    }
  },
}

</script>
<style scoped>
.topList {
  width: 160px;
  border: 1px solid #b1f3c2;
  color: #4cbb5c;
  background: url('../../assets/contentTitleBack.jpg')no-repeat;
  background-size: cover;
  padding-left: 5px;
}

.hoverStyle:hover {
  background-color: #51bb4c;
  color: #fff;
}

</style>
