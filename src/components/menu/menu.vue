<template>
  <div class="back">
    <!-- <img style="width:200px;margin:10px;" src="../../assets/logo.png" alt=""> -->
    <h1 class="title">麻醉临床信息系统</h1>
    <div class="buttonBox">
      <div class="surround">
        <div @click="goUrl('/scheduleList')" class="systemButton" style="left:100px;top:0px;">
          <span>排班系统</span>
        </div>
        <div @click="goUrl('/operationMaster')" class="systemButton" style="left:280px;bottom:0px;">
          <span>麻醉手术系统</span>
        </div>
        <!-- <div @click="runCountExe" class="systemButton" style="left:360px;top:0;"> -->
        <!-- <div @click="goUrl('/statistics')" class="systemButton" style="left:360px;top:0;">
              <span>工作量统计</span>
            </div> -->
        <div @click="goUrl('/management')" class="systemButton" style="left:460px;top:0px;">
          <span>用户管理</span>
        </div>
        <div @click="runCountExe" class="systemButton" style="left:640px;bottom:0px;">
          <span>统计系统</span>
        </div>
        <div @click="exitSystem()" class="systemButton" style="left:820px;top:0px;">
          <span>退出系统</span>
        </div>
      </div>
    </div>
    <div class="copyRight">版权信息</div>
  </div>
</template>
<script>
export default {
  name: 'login',
  data() {
    return {

    }
  },
  methods: {
    goUrl(url) {
      if (url == '/statistics') {
        alert('暂无权限。')
      } else if (url == '/management') {
        if (this.config.userId == 'admin') {
          this.$router.push({
            path: url
          })
        } else {
          alert('暂无权限。')
        }
      } else if (url == '/statsFilesLogin') {
        this.$router.push({
          path: url
        })
      } else {
        this.$router.push({
          path: url
        })
      }

    },
    //统计客户端调用
    runCountExe(){
      window.ipc.send('runCountExe', 'test');
    },
    // 退出系统
    exitSystem() {
      if (confirm("是否要退出系统？")) {
        this.$router.push({
          path: 'login'
        })
      } else { }
    },
  },
  mounted() {

  },
  components: {}
}

</script>
<style scoped>
.back {
  min-width: 1366px;
  position: relative;
  width: 100%;
  height: 100%;
  background: url('../../assets/loginBack.jpg') no-repeat;
  background-size: cover;
  background-position: center;
}

.title {
  color: white;
  position: absolute;
  display: block;
  width: 100%;
  text-align: center;
  top: calc(50% - 160px);
}

.buttonBox {
  position: absolute;
  /* background-color: lightblue; */
  width: 100%;
  height: 200px;
  top: calc(50% - 80px);
  left: 0;
}

.surround {
  width: 1050px;
  height: 100%;
  margin: 0 auto;
  position: relative;
}

.systemButton {
  position: absolute;
  color: #666;
  height: 100px;
  padding: 0 10px;
  border: 1px solid rgba(249, 249, 249, 0.8);
  box-sizing: border-box;
  width: 150px;
  text-align: center;
  line-height: 100px;
  cursor: pointer;
  text-align: center;
  background-color: rgba(249, 249, 249, 0.8);
  box-shadow: 1px 2px 10px rgba(249, 249, 249, 0.8);
}

.copyRight {
  color: #fff;
  font-size: 12px;
  text-align: center;
  width: 100%;
  position: absolute;
  bottom: 0;
  padding-bottom: 5px;
}
</style>
