<template>
  <div class="note">
    <div class="listBox">
      <div class="topName">
        DoCare 围术期管理决策平台
      </div>
      <div class="urlButtonBox">
        <div class="urlBox" @click="goUrl('/workLoad')">
          <img src="../../../assets/tj/icon_1.png" alt="">
          <span>工作量统计</span>
        </div>
        <div class="urlBox" @click="goUrl('/department')">
          <img src="../../../assets/tj/icon_2.png" alt="">
          <span>科室报表</span>
        </div>
        <div class="urlBox" @click="goUrl('/grade')">
          <img src="../../../assets/tj/icon_3.png" alt="">
          <span>等级评审</span>
        </div>
        <div class="urlBox" @click="goUrl('/control')">
          <img src="../../../assets/tj/icon_4.jpg" alt="">
          <span>麻醉质控2015</span>
        </div>
        <div class="urlBox" @click="goUrl('/business')">
          <img src="../../../assets/tj/icon_5.jpg" alt="">
          <span>业务查询</span>
        </div>
        <div class="urlBox" @click="goUrl('/system')">
          <img src="../../../assets/tj/icon_6.png" alt="">
          <span>系统设置</span>
        </div>
      </div>
    </div>
    <div class="turnOff" @click="exit()">
      <img src="../../../assets/tj/turnOff.png" alt="">
    </div>
  </div>
</template>
<script>
export default {
  name: 'TJlogin',
  data() {
    return {
      note: {
        backgroundRepeat: "no-repeat",
        backgroundSize: 'cover',
        height: "100%",
        backgroundPosition: "center",
      },
    }
  },
  methods: {
    goUrl(url) {
      this.$router.push({
        path: url
      })
    },
    exit() {
      if (confirm("是否要退出系统？")) {
        this.$router.push({
          path: 'menu'
        })
      } else {

      }
    }
  },

  mounted() {

  },
  components: {

  },
}

</script>
<style type="text/css" scoped>
.note {
  background: url("../../../assets/tj/tLogin.jpg") no-repeat;
  background-size: cover;
  height: 100%;
  position: relative;
}

.listBox {
  position: absolute;
  left: 50%;
  top: 50%;
  width: 820px;
  height: 500px;
  margin-left: -410px;
  margin-top: -250px;
  background-color: #fff;
}

.topName {
  color: #545557;
  font-size: 24px;
  width: 90%;
  margin: auto;
  padding: 20px 0;
  border-bottom: 1px solid #545557;
}

.urlButtonBox {
  width: 90%;
  height: auto;
  margin: auto;
  display: flex;
  flex-wrap: wrap;
  padding-top: 10px;
}

.urlBox {
  height: 175px;
  width: 150px;
  margin: 10px 16px;
  border: 2px solid #83afe6;
  border-radius: 5px;
  box-shadow: 1px 2px 5px #c6cacf;
  display: flex;
  flex-direction: column;
  align-items: center;
  box-sizing: border-box;
  padding-top: 15px;
  color: #656565;
  font-size: 18px;
  font-weight: bold;
  cursor: pointer;
}

.urlBox:hover {
  border: 2px solid #71b4ff;
  box-shadow: 4px 4px 5px #c6cacf;
}

.urlBox img {
  width: 90px;
  height: 90px;
}

.urlBox span {
  padding-top: 20px;
}

.urlBox:hover span {
  color: #71b4ff;
}

.turnOff {
  width: 60px;
  height: 60px;
  border-radius: 50px;
  background-color: #022e6f;
  border: 1px solid #0f4a88;
  position: absolute;
  bottom: 30px;
  right: 30px;
}

.turnOff img {
  display: block;
  margin: 15px auto 0;
  width: 30px;
  height: 30px;
}

.turnOff:hover {
  background-color: #1f5cb4;
}
</style>
