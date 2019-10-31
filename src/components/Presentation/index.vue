<template>
  <div class="home">
    <div class="titleQ">监控报告</div>
    <!-- <el-tabs v-model="activeName" @tab-click="handleClick"> -->
    <div class="search">
      <el-form :inline="true" class="demo-form-inline" :model="formData">
        <span class="demonstration">资源类型</span>
        <el-select v-model="formData.typeName" filterable placeholder="请选择" class="right" clearable>
          <el-option
            v-for="item in options"
            :key="item.value"
            :label="item.label"
            :value="item.label"
          ></el-option>
        </el-select>

        <span class="demonstration">资源状态</span>
        <el-select v-model="formData.status" filterable placeholder="请选择" class="right" clearable>
          <el-option
            v-for="item in optionss"
            :key="item.label"
            :label="item.label"
            :value="item.value"
          ></el-option>
        </el-select>
        <span class="demonstration">时间选择</span>
        <el-date-picker
          v-model="formData.selectTime"
          type="daterange"
          align="right"
          class="right"
          unlink-panels
          range-separator="至"
          start-placeholder="开始日期"
          end-placeholder="结束日期"
        ></el-date-picker>
        <el-form-item>
          <el-button type="primary" @click="onSubmit" class="right">搜索</el-button>
          <el-button type="primary" @click="dialogVisible = true" class="right">生成报告</el-button>
          <!-- 0 -->
          <el-dialog title="提示" :visible.sync="dialogVisible" class="onace" width="30%">
            <el-row>
              
              <span class="demonstration">资源类型</span>
              <el-select
                v-model="edfrom_.name"
                filterable
                placeholder="请选择"
                class="right"
                clearable
              >
                <el-option
                  v-for="item in options"
                  :key="item.value"
                  :label="item.label"
                  :value="item.label"
                ></el-option>
              </el-select>
            </el-row>
            <el-row>
              报告开始时间
              <el-date-picker v-model="edfrom_.createtime" type="date" placeholder="选择日期"></el-date-picker>
            </el-row>
            <el-row style="margin-top:20px;">
              报告结束时间
              <el-date-picker v-model="edfrom_.endtime" type="date" placeholder="选择日期"></el-date-picker>
            </el-row>
            <div slot="footer" class="dialog-footer">
              <el-button @click="dialogVisible = false">取 消</el-button>
              <el-button type="primary" @click="sou">确 定</el-button>
            </div>
          </el-dialog>
          <!-- 0 -->
        </el-form-item>
      </el-form>
    </div>
    <el-table :data="tableData" style="width: 100%;" class="tabP">
      <el-table-column prop="id" label="报告ID" width="180"></el-table-column>
      <el-table-column prop="typeName" label="资源类型" width="180"></el-table-column>
      <el-table-column prop="reportStartTime" label="报告开始时间"></el-table-column>
      <el-table-column prop="reportEndTime" label="报告结束时间"></el-table-column>
      <el-table-column show-overflow-tooltip prop="fileName" label="报告文件名"></el-table-column>
      <el-table-column prop="createTime" label="报告创建时间"></el-table-column>
      <el-table-column prop="status" label="报告状态"></el-table-column>
      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-button type="text" @click="open(scope.row.fileName)">
            <i class="icon iconfont icon-xiazai" style="font-size:18px; font-weight:bold;"></i>
          </el-button>
        </template>
      </el-table-column>
    </el-table>
    <div class="block">
      <el-pagination
        @current-change="handleCurrentChange"
        :current-page.sync="pagination.start"
        :page-size="pagination.pageSize"
        layout="total, pager"
        :total="pagination.total"
      ></el-pagination>
    </div>
  </div>
</template>

 <script>
export default {
  data() {
    return {
      formData: {
        typeName: "",
        ststus: "",
        selectTime: []
      },
      currentPage: 1, //初始页
      pagesize: 10, //每页的数据
      dialogVisible: false,
      edfrom_: {
        name: "",
        createtime: "",
        endtime: ""
      },
      pagination: {
        start: 1, //从第一页开始
        pageSize: 10, //每一页展示条数据
        total: 0
      }, // 分页配置
      value1: "",
      value2: [],
      // formInline: {
      //   user: "",
      //   name: "",
      //   region: "",
      //   values:''
      // },
      addForm: {
        name: "",
        sort: 99
      },
      editForm: {
        name: "",
        sort: 99
      },
      title: "",
      title1: "",
      total: 0,
      dialogAddgsVisible: false,
      dialogEditgsVisible: false,
      dialogEditgsVisible1: false,
      dialogDetailsVisible: false,
      detailForm: [],
      optionss: [
        {
          value: "0",
          label: "成功"
        },
        {
          value: "1",
          label: "失败"
        }
      ],
      options: [
        {
          value: "选项1",
          label: "主机"
        },
        {
          value: "选项2",
          label: "组件"
        },
        {
          value: "选项3",
          label: "服务"
        }
      ],
      value: "",
      values: "",

      rules: {
        name: [
          { required: true, message: "请输入名称" },
          { min: 2, max: 10, message: "SSSSSSS", trigger: "blur" }
        ],
        sort: [{ type: "number", message: "11233552", trigger: "blur" }]
      },
      tableData: [],
      ta: [],
      listmy: [],
      start_: "",
      end_: ""
    };
  },
  methods: {
    //数据展示
    getss(arr) {
       
        this.$axios
        .post("/oms-basic/report!list.json", this.$qs.stringify(arr))
        .then(res => {
         
          console.log(res.data.list, "返回的数据");
          res.data.list.forEach((item, index) => {
            res.data.list[index].status = item.status === 0 ? "成功" : "失败";
          });
          this.tableData = res.data.list;
          this.pagination.total = res.data.count;
          
        });
    },
    //////
    open(index) {
      this.$confirm("此操作将下载该数据, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
        center: true
      })
        .then(() => {
          window.location.href =
            "/oms-basic/downloadSystemReportFile.json?fileName=" + index;
          this.$message({
            type: "success",
            message: "下载成功"
          });
        })
        .catch(() => {
          console.log("11111111");
          this.$message({
            type: "info",
            message: "已取消下载"
          });
        });
    },
    currentChangePage(list) {
      let from = (this.currentPage - 1) * this.pageSize;
      let to = this.currentPage * this.pageSize;

      for (; from < to; from++) {
        if (list[from]) {
          this.tableData.push(list[from]);
        }
      }
    },
    // 初始页currentPage、初始每页数据数pagesize和数据data
    handleSizeChange: function(size) {
      this.pagesize = size;
      console.log(this.pagesize); //每页下拉显示数据
    },
    handleCurrentChange: function(currentPage) {
      let params = {
        start: currentPage,
        pageSize: this.pagination.pageSize,
        typeName: this.formData.typeName,
        status: this.formData.status
      };
      if (this.formData.selectTime.length) {
        params.reportStartTime = this.dateTransfer(this.formData.selectTime[0]),
        params.reportEndTime = this.dateTransfer(this.formData.selectTime[1])}
      this.getss(params);
    },
    deleteRow1(index) {
      this.tableData.forEach((item, index) => {});

      this.dialogEditgsVisible1 = false;
    },

    editgsForm(val) {
      this.$router.push("/LogDetails");
    },
    saveEditForm(aaa) {
      this.$refs[aaa].validate(valid => {
        console.log(this.$refs[aaa]);
        if (valid) {
          // this.$axios.put(`http://localhost:3000/admin/categories/${this.editForm.id}`,this.editForm).then( res =>{
          //   alert('更新成功');
          this.dialogEditgsVisible = false;
          this.init();
          console.log(valid);
          // })
        }
      });
    },
    dateTransfer(date) {
      var y = date.getFullYear();
      var m = date.getMonth() + 1;
      m = m < 10 ? "0" + m : m;
      var d = date.getDate();
      d = d < 10 ? "0" + d : d;
      var h = date.getHours();
      var minute = date.getMinutes();
      minute = minute < 10 ? "0" + minute : minute;
      return y + "-" + m + "-" + d + " " + "00:00:00";
    },
    onSubmit() {
      var formData = {};
      if (this.formData.typeName) {
        formData.typeName = this.formData.typeName;
      }
      if (this.formData.status) {
        formData.status = this.formData.status;
      }
      // console.log(this.value2, "this.value2");
      if (
        this.formData.selectTime != "" &&
        this.formData.selectTime != undefined
      ) {
        formData.reportStartTime = this.dateTransfer(
          this.formData.selectTime[0]
        );
        formData.reportEndTime = this.dateTransfer(this.formData.selectTime[1]);
      }
      console.log(formData, "传递的值");
      this.getss(formData);
    },
    viewdetail(index, row) {
      console.log(index, "index");
      this.dialogDetailsVisible = true;
      this.detailForm = row;
    },
    sou() {
      var that = this;
      this.dialogVisible = false;
      var dataList = {
        reportStartTime: this.edfrom_.createtime,
        reportEndTime: this.edfrom_.endtime,
        typeName: this.edfrom_.name
      };
      console.log(dataList);
      this.$axios
        .post(
          "/oms-basic/report!createReportExcel.json",
          this.$qs.stringify(dataList)
        )
        .then(res => {
          console.log(res, "生成报告");
          this.getss();
        });
    }
  },
  mounted() {
    this.getss({
      start: this.pagination.start,
      pageSize: this.pagination.pageSize
    });
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
.home {
  height: 870px;
  background: #fff;
  overflow: hidden;
  position: relative;
  .titleQ {
    width: 100%;
    height: 50px;
    border-bottom: 1px solid #ccc;
    line-height: 50px;
    text-indent: 1em;
    border-left: 8px solid #3f67e1;
    box-sizing: border-box;
  }
  .search {
    padding: 12px 0 0 12px;
  }
  .block {
    position: absolute;
    left: 30%;
    bottom: 5px;
  }
}
.right {
  margin-right: 24px;
}
#Detailedy {
  width: 100%;
  height: 50px;
  border-bottom: 1px solid #ccc;
  border-top: 1px solid #ccc;
  line-height: 50px;
  padding-left: 10px;

  box-sizing: border-box;
}
#bd {
  color: #3584f3;
  background: #d5e6ff;
  font-weight: bold;
  border-radius: 50%;
}
</style>
