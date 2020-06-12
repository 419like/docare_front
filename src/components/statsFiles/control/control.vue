<template>
    <div class="note">
        <div class="topDiv">
            <img @click="exit()" class="turnOff" src="../../../assets/tj/turnOff_1.png">
        </div>
        <div class="listBox">
            <div class="pathBox">
                <div class="goBack" @click="goBack()">返回</div>
                <div class="listContent">
                    <img src="../../../assets/tj/icon_4.jpg" alt="">
                    <div style="font-size:20px;font-weight:bold;color:#545557">麻醉质控 2015</div>
                    <div class="clickUrl">麻醉医生工作量</div>
                    <div class="clickUrl">护士工作量</div>
                    <div class="clickUrl">临床科室工作量（等级）</div>
                    <div class="clickUrl">临床科室手术量（月）</div>
                </div>
            </div>
            <div class="contentBox">
                <div class="defaultBox">
                    <div class="welcomeWord">欢迎来到 麻醉 围术期管理决策平台</div>
                    <img src="../../../assets/tj/welcome.png" style="width:550px;position: absolute;left:50%;top:50%;margin:-138px 0 0 -275px;" alt="">
                </div>
            </div>
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
        workload() {

        },
        goBack() {
            this.$router.push({
                path: 'statisticsList'
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
    width: 1200px;
    height: 500px;
    margin-left: -600px;
    margin-top: -250px;
    background-color: #fff;
    display: flex;
}

.topDiv {
    width: 100%;
    height: 60px;
    position: relative;
}

.turnOff {
    width: 40px;
    height: 40px;
    position: absolute;
    right: 25px;
    top: 15px;
    cursor: pointer;
}

.pathBox {
    width: 260px;
    height: 100%;
    overflow-y: auto;
    display: relative;
}

.goBack {
    font-weight: bold;
    width: 70px;
    height: 24px;
    border: 1px solid #74aef6;
    border-radius: 5px;
    font-size: 12px;
    text-align: center;
    line-height: 24px;
    position: absolute;
    top: 10px;
    left: 10px;
    cursor: pointer;
}

.goBack:hover {
    background-color: #74aef6;
    color: #fff;
}

.listContent {
    display: flex;
    flex-direction: column;
    align-items: center;
    padding-top: 40px;
}

.listContent img {
    width: 50px;
    height: 50px;
    margin-bottom: 10px;
}

.clickUrl {
    width: 200px;
    height: 42px;
    line-height: 42px;
    text-align: center;
    font-size: 14px;
    font-weight: bold;
    margin: 5px 0;
    border-radius: 5px;
    border: 1px solid #74aef6;
    cursor: pointer;
}

.clickUrl:hover {
    color: #74aef6;
}

.contentBox {
    width: calc(100% - 260px);
    height: 100%;
    box-sizing: border-box;
    border-left: 1px solid #e7e7e7;
    box-shadow: -3px 0px 1px #e7e7e7;
}

.defaultBox {
    position: relative;
    width: 100%;
    height: 100%;
}

.welcomeWord {
    position: absolute;
    left: 50%;
    top: 50%;
    margin: -180px 0 0 -275px;
    color: #545557;
    font-size: 26px;
    width: 550px;
    text-align: center;
}
</style>
