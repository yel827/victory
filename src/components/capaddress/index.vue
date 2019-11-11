<template>
  <div>
    <el-tabs v-model="activeName" @tab-click="handleClick">
      <el-tab-pane label="能力管理" name="first">
        <div class="tabone">
          <el-form ref="form" :model="form" label-width="70px" style="display:flex;">
            <el-form-item label="活动名称">
              <el-input v-model="form.name" label-width="80px"></el-input>
            </el-form-item>
            <el-form-item>
              <el-button type="primary" @click="onSubmit">搜索</el-button>
              <el-button>+ 添加文件</el-button>
            </el-form-item>
          </el-form>
          <el-table :data="tableData" stripe style="width: 100%">
            <el-table-column prop="date" label="ID" width="200"></el-table-column>
            <el-table-column prop="date" label="能力名称" width="200"></el-table-column>
            <el-table-column prop="date" label="能力编号" width="200"></el-table-column>
            <el-table-column prop="name" label="创建时间" width="300"></el-table-column>
            <el-table-column prop="address" label="更新时间" width="300"></el-table-column>
            <el-table-column label="操作" width="300">
              <!-- 能力监控操作详情 -->
              <template slot-scope="scope">
                <el-button type="text">
                  <i class="icon iconfont icon-chakan" style="font-size:18px; font-weight:bold;"></i>
                </el-button>
                <el-button type="text">
                  <i class="el-icon-refresh" style="font-size:24px; font-weight:bold;"></i>
                </el-button>
                <!-- <el-dialog 
                width='54%' 
                title="能力监控详情" 
                :visible.sync="dialogTableVisible3">
                <el-table :data="gridData3">
                  <el-table-column property="hostIp" label="能力IP" width="100"></el-table-column>
                  <el-table-column property="port" label="能力端口" width="100"></el-table-column>
                  <el-table-column property="status" label="能力状态" width="100"></el-table-column>
                  <el-table-column property="totolRunTime" label="运行时长" width="100"></el-table-column>
                  <el-table-column property="normalRunTimeStr" label="正常运行时长" width="105"></el-table-column>
                  <el-table-column property="errorRunTimeStr" label="异常运行时长" width="105"></el-table-column>
                  <el-table-column property="componentRatio" label="组件可用比" width="100"></el-table-column>
                  <el-table-column property="updateTime" label="更新时间" width="100"></el-table-column>
                  <el-table-column  label="操作" width="100">
                    <el-button type="text">刷新</el-button>
                  </el-table-column>
                </el-table>
                </el-dialog>-->
              </template>
            </el-table-column>
          </el-table>
          <el-pagination background layout="prev, pager, next" :total="1000"></el-pagination>
        </div>
      </el-tab-pane>
      <el-tab-pane label="能力地址管理" name="second">能力地址管理</el-tab-pane>
    </el-tabs>
  </div>
</template>
<script>
export default {
  data() {
    return {
      activeName: "first",
      form: {
        name: ""
      },
      tableData: [
        {
          date: "2016-05-02",
          name: "王小虎",
          address: "上海市普陀区金沙江路 1518 弄"
        },
        {
          date: "2016-05-04",
          name: "王小虎",
          address: "上海市普陀区金沙江路 1517 弄"
        },
        {
          date: "2016-05-01",
          name: "王小虎",
          address: "上海市普陀区金沙江路 1519 弄"
        },
        {
          date: "2016-05-03",
          name: "王小虎",
          address: "上海市普陀区金沙江路 1516 弄"
        },
        {
          date: "2016-05-01",
          name: "王小虎",
          address: "上海市普陀区金沙江路 1519 弄"
        },
        {
          date: "2016-05-01",
          name: "王小虎",
          address: "上海市普陀区金沙江路 1519 弄"
        }
      ]
    };
  },
  methods: {
    handleClick(tab, event) {
      // console.log(tab,'tab---tab');
      // console.log(event,'event---event')
    },
    onSubmit() {}
  }
};
</script>
<style scoped lang="less">
.tabone {
  // width: 100%;
  // display: flex;

  // /deep/ .el-input__inner {
  //   width: 150px;
  // }
  height: 870px;
  background: #fff;
  overflow: hidden;
  position: relative;
  // /deep/ .el-tabs__item is-top {}  el-tabs__nav-scroll
  /deep/ .el-tabs__nav.is-top {
    width: 100%;
    height: 50px;
    display: flex;
    flex-direction: rows;
    /deep/ .el-tabs__item.is-top {
      flex: 1;
      text-align: center;
    }
  }
}
</style>

