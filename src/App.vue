<template>
  <div id="app" ref="wde">
    <el-container>
      <el-menu
        default-active="1-4-1"
        class="el-menu-vertical-demo"
        style="background:#3584f3;"
        @open="handleOpen"
        @close="handleClose"
        :collapse="isCollapse"
      >
        <div class="header_title">能力开发系统管理</div>
        <el-submenu index="1" style="background:#3584f3;">
          <template slot="title">
            <i class="icon iconfont icon-zuhuguanli" style="color:#fff; font-size:16px;"></i>
            <span slot="title" ref="bgcolor" style="color:#fff;">租户管理</span>
          </template>
          <el-menu-item-group>
            <router-link to="/"><el-menu-item index="1-1">租户列表</el-menu-item></router-link> 
            <router-link to="/Administration"><el-menu-item index="1-2">授权管理</el-menu-item></router-link> 
          </el-menu-item-group>
        </el-submenu>
        <el-submenu index="2" style="background:#3584f3;">
          <template slot="title">
            <i class="icon iconfont icon-rizhiguanli" style="color:#fff; font-size:16px;"></i>
            <span slot="title" class="bg" style="color:#fff;">日志管理</span>
          </template>
          <el-menu-item-group>
            <router-link to="/Journal"><el-menu-item index="2-1">日志列表</el-menu-item></router-link> 
            <router-link to="/Abnormallog"><el-menu-item index="2-2">异常日志列表</el-menu-item></router-link> 
          </el-menu-item-group>
        </el-submenu>
        <el-submenu index="3" style="background:#3584f3;">
          <template slot="title">
            <i class="icon iconfont icon-xitongjiankong" style="color:#fff; font-size:16px;"></i>
            <span slot="title" style="color:#fff;">系统监控</span>
          </template>
          <el-menu-item-group>
            <router-link to="/Monitoring"><el-menu-item index="3-1">监控管理</el-menu-item></router-link>
           <router-link to="/alem"><el-menu-item index="3-2">告警管理</el-menu-item></router-link> 
           <router-link to="/Presentation"><el-menu-item index="3-3">监控报告</el-menu-item></router-link> 
          </el-menu-item-group>
        </el-submenu>
      </el-menu>

      <el-container>
        <el-header style="text-align: right; font-size: 12px" class="el_header">
          <el-radio-group v-model="isCollapse" class="but">
            <el-radio-button :label="false">展开</el-radio-button>
            <el-radio-button :label="true">收起</el-radio-button>
          </el-radio-group>
          <el-dropdown>
            <!-- <span>通知</span>
            <span>帮助</span>-->
            <i class="el-icon-setting" style="margin-right:15px"></i>
            <el-dropdown-menu slot="dropdown">
              <el-dropdown-item>查看</el-dropdown-item>
              <el-dropdown-item>新增</el-dropdown-item>
              <el-dropdown-item>退出</el-dropdown-item>
            </el-dropdown-menu>
          </el-dropdown>
          <span>王小虎</span>
          <!-- <span>退出</span> -->
        </el-header>
        
        <el-main class="el_main">
          <router-view />
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<script>
export default {
  data() {
    const item = {
      date: "2016-05-02",
      name: "王小虎",
      address: "上海市普陀区金沙江路 1518 弄"
    };
    return {
      tableData: Array(20).fill(item),
      isCollapse: false
    };

  },
  methods: {
    handleOpen(key, keyPath) {
      console.log(key, keyPath);
    },
    handleClose(key, keyPath) {
      console.log(key, keyPath);
    }
  },
  created(){

  }
};
</script>
<style lang="less">
  * {
  margin: 0;
  padding: 0;
}
a{
  text-decoration: none;
  color:#fff;
}
</style>
<style lang="less" scoped>

#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  background: #f4f3f3;
}
.el-menu-vertical-demo:not(.el-menu--collapse) {
  width: 200px;
  min-height: 400px;
}
.el_main {
  padding: 0;
  margin: 20px;
}

.but {
  position: absolute;
  left: 10px;
  top: 10px;
}
.header_title {
  width: 100%;
  height: 60px;
  line-height: 60px;
  text-align: center;
  overflow: hidden;
  padding: 0 10px;
  box-sizing: border-box;
  font-size: 18px;
  font-weight: bold;
  background: #3584f3;
  color: #ffffff;
}

.tet {
  width: 100%;
  height: 60px;
  font-size: 20px;
  font-family: "微软雅黑";
  line-height: 60px;
  text-align: center;
}
.el-header {
  background-color: #b3c0d1;
  color: #fff;
  line-height: 60px;
}

.el-aside {
  color: #fff;
  height: 968px;
}
.el_header {
  background: #fff;
  border-bottom: 1px solid #ccc;
  color: #000;
  position: relative;
  span {
    padding: 0 10px;
  }
}
/deep/ .el-dialog__header{
  background: #3584f3;
  color:#fff;
  /deep/ .el-dialog__title{
    color:#fff;
  }
  /deep/ .el-dialog__close.el-icon.el-icon-close::before{
    color:#fff;
  }
}
/deep/ .el-menu-item-group{
  background:#386dd9 !important;
  .el-menu-item{
    background:#386dd9;
    
  }
}
/deep/.el-submenu__title:hover{
  background: #386dd9;
}
/deep/.el-submenu__icon-arrow{
  color:#fff;
} /deep/.el-icon-arrow-down{
  color:#fff;
}
/deep/ .el-menu li .el-menu-item{
  color:#fff;
  .is-active{
    color:red;
  }
}


</style>
