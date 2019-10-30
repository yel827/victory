<template>
  <div class="hello">
    <div class="bg">
      <div class="bg1">
        <div class="left">
          <div>
            <img src="../../assets/log_03.png" class="tu_wen" alt />
            <span>能力开发管理系统</span>
          </div>
          <div></div>
        </div>
        <div class="right">
          <div class="right_project">
            <p>登 录</p>
            <div class="input_A">
                <img src="../../assets/uese.png" alt="">
              <input type="text" placeholder="请输入用户名" class="tet">
            </div>
            <div class="input_A">
                <img src="../../assets/pass.png" alt="">
              <input type="password" placeholder="请输入密码" class="tet">
            </div>
            <div class="input_A">
                <img src="../../assets/us.png" alt="">
              <input type="password" placeholder="请输入验证码" class="tet">
            </div>
            <router-link><button class="btn"> 登 录 </button></router-link> 
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  data() {
    return {
      msg: "Welcome to Your Vue.js App"
    };
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
.bg {
  width: 100%;
  height: 964px;
  background: url(../../assets/bg.png) no-repeat left;
  background-size: 100%;
  position: relative;
  .bg1 {
    width: 80%;
    height: 80%;
    background: url(../../assets/bg1.png) no-repeat center center;
    background-size: 99%;
    position: absolute;
    left: 0;
    top: 0;
    right: 0;
    bottom: 0;
    margin: auto;
    .left {
      width: 50%;
      height: 100%;
      padding-left: 10%;
      padding-top: 2%;
      padding-bottom: 4%;
      box-sizing: border-box;
      float: left;
      display: flex;
      flex-direction: column;
      div:nth-child(1) {
        width: 100%;
        height: 60px;
        line-height: 60px;

        .tu_wen {
          height: 60px;
          width: 220px;
          float: left;
        }
        span {
          display: inline-block;
          width: auto;
          height: 60px;
          line-height: 60px;
          float: left;
          font-size: 32px;
        }
      }
      div:nth-child(2) {
        flex: 1;
        line-height: 60px;
        background: url(../../assets/canvas1.png) no-repeat center center;
        background-size: 100%;
      }
    }
    .right {
      width: 50%;
      height: 100%;
      padding-right: 10%;
      padding-top: 2%;
      float: left;
      text-align: right;
      box-sizing: border-box;
      position: relative;
      .right_project {
        width: 60%;
        height: 60%;
        position: absolute;
        left: 0;
        top: 0;
        right: 0;
        bottom: 0;
        margin: auto;
        .btn{
            
            width:80%;
            height:60px;
            margin-right:10%;
            margin-top:16%;
            background: #3285f2;
            outline: none;
            border:none;
            border-radius: 6px;
            color:#fff;
            font-size:26px;
        }
        .input_A{
            width:100%;
            height:50px;
            margin-top:40px;
            img{
                float: left;
                width:40px;
                height:50px;
            }
            .tet{
                float: left;
                width:90%;
                height:100%;
                border-top:none;
                border-left:none;
                border-right:none;
                outline: none;
                text-align: left;
                padding:10px;
                box-sizing:border-box;


            }
        }
        p {
          width: 100%;
          height: 50px;
          text-align: center;
          font-size: 32px;
          color: #3285f2;
          font-weight: bold;
        }
      }
    }
  }
}
</style>
