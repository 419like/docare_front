<template>
  <div :style="note" style="background-color: rgb(0,0,0);position: relative;">
    <div class="loginBox">
      <div style="height:calc(100% - 100px);">
        <div style="color:#545557;font-size:26px;text-align:center;height:80px;line-height:80px;">麻醉 围术期管理决策平台</div>
        <div style="width:300px;margin:auto;">
          <div style="border-bottom:1px solid #545557;width:100%;padding-top:20px;">
            <span style="display:inline-block;width:65px;height:100%;color:#545557;">用户名：</span>
            <input type="" name="" style="width: 180px;height:22px;box-sizing:border-box;border:0;">
          </div>
          <div style="border-bottom:1px solid #545557;width:100%;padding-top:20px;">
            <span style="display:inline-block;width:65px;height:100%;color:#545557;"> 密 码：</span>
            <input type="password" name="" style="width: 180px;height:22px;box-sizing:border-box;border:0;">
          </div>
        </div>
        <div class="titleNotice">用户名，密码不能为空。</div>
      </div>
      <div style="background-color:#e0e0e0;height:100px;width:100%;padding-top:30px;box-sizing:border-box;">
        <button @click="login('/statisticsList')">登陆</button>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: 'TJlogin',
  data() {
    return {
      msg: '欢迎登陆！',
      loginName: '',
      loginPwd: '',
      note: {
        backgroundImage: "url(" + require("../../../assets/tj/tLogin.jpg") + ")",
        backgroundRepeat: "no-repeat",
        backgroundSize: 'cover',
        height: "100%",
        backgroundPosition: "center",
      },
    }
  },
  methods: {
    login(url) {
      this.$router.push({
        path: url
      })
    }
  },

  mounted() {

  },
  components: {

  },
}

</script>
<style type="text/css" scoped>
button {
  width: 300px;
  /* background-color:rgb(198, 229, 253); */
  background: linear-gradient(#2c84f2, #2c84f2, #2c84f2);
  line-height: 24px;
  border: none;
  color: #fff;
  height: 45px;
  margin: auto;
  display: block;
  border-radius: 3px;
  font-size: 15px;
  box-sizing: border-box;
  border: 1px solid rgb(121, 169, 228);
}

button:hover {
  cursor: pointer;
  background: linear-gradient(#4d95ef, #4d95ef, #4d95ef);
}

button:active {
  /* background: linear-gradient(#adc7cb, #99c4ec, #b4d4f2); */
}

.loginBox {
  position: absolute;
  left: 50%;
  top: 50%;
  width: 450px;
  height: 340px;
  margin-left: -225px;
  margin-top: -170px;
  background-color: #fff;
}

.timePicker::-webkit-inner-spin-button {
  display: none;
}

.titleNotice {
  color: red;
  font-size: 12px;
  padding-top: 30px;
  width: 300px;
  margin: auto;
}
</style>
