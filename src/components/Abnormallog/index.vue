<template>
  <div class="home">
    <div class="titleQ">异常日志列表</div>
    <div class="search">
      <el-form :inline="true" :model="formInline" class="demo-form-inline">
        <el-form-item label="租户名称" class="right">
          <el-input v-model="formInline.same" placeholder="租户名称"  clearable></el-input>
        </el-form-item>

        <span class="demonstration">日志等级</span>
        <el-select v-model="value" filterable value="warn" class="right" clearable>
          <el-option
            v-for="item in options"
            :key="item.label"
            :label="item.label"
            :value="item.label"
          ></el-option>
        </el-select>

        <el-form-item label="来源URL" class="right">
          <el-input v-model="formInline.name" placeholder="来源URL"  clearable></el-input>
        </el-form-item>
        <span class="demonstration">时间选择</span>
        <el-date-picker
          v-model="value2"
          type="daterange"
          align="right"
          class="right"
          unlink-panels
          range-separator="至"
          start-placeholder="开始日期"
          end-placeholder="结束日期"
          :picker-options="pickerOptions"
        ></el-date-picker>
        <el-form-item>
          <el-button type="primary" @click="onSubmit" class="right">搜索</el-button>
          <el-button type="primary" @click="dao" class="right">导出当前</el-button>
        </el-form-item>
      </el-form>
    </div>
    <el-table
      :data="tableData"
      style="width: 100%;"
      class="tabP"
      id="out-table"
    >
      <template v-for="(item, index) in tableLabel">
        <el-table-column show-overflow-tooltip :key="index" :prop="item.prop" :label="item.label" width></el-table-column>
      </template>
      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-button type="text" @click="viewdetail(scope.$index, scope.row)">
            <i class="icon iconfont icon-chakan" style="font-size:18px; font-weight:bold;"></i>
          </el-button>
        </template>
      </el-table-column>
    </el-table>
    <div class="block">
      <el-pagination
        @current-change="handleCurrentChange"
        :current-page.sync="pagination.start"
        :page-size="pagination.pageSize"
        layout="total, prev, pager, next"
        :total="pagination.total"
      ></el-pagination>
    </div>
    <el-dialog title="详情" :visible.sync="dialogDetailsVisible" width="50%" style="text-align:left">
      <ul class="detailBox">
        <li>
          <div class="bg_cyan">日志 ID</div>
          <div class="msgBox">{{detailForm.logID}}</div>
        </li>
        <li>
          <div class="bg_cyan">租户名称</div>
          <div class="msgBox">{{detailForm.tenantName}}</div>
        </li>
        <li>
          <div class="bg_cyan">日志等级</div>
          <div class="msgBox">{{detailForm.level}}</div>
        </li>
        <li>
          <div class="bg_cyan">来源URL</div>
          <div class="msgBox">{{detailForm.source}}</div>
        </li>
        <li>
          <div class="bg_cyan">调用时间</div>
          <div class="msgBox">{{detailForm.updateTime}}</div>
        </li>
        <li style="border-bottom: 1px solid #000;">
          <div class="bg_cyan" style=" height:80px; line-height:80px;">日志信息</div>
          <div class="msgBox" style=" height:80px; line-height:80px;">{{detailForm.msg}}</div>
        </li>
      </ul>
    </el-dialog>
  </div>
</template>

 <script>
export default {
  data() {
    return {
      currentPage: 1, //初始页
      pagesize: 10, //每页的数据
      pickerOptions: {
        shortcuts: [
          {
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 7);
              picker.$emit("pick", [start, end]);
            }
          },
          {
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 30);
              picker.$emit("pick", [start, end]);
            }
          },
          {
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 90);
              picker.$emit("pick", [start, end]);
            }
          }
        ]
      },
      value1: "",
      value2: [],
      formInline: {
        user: "",
        name: "",
        same:"",
        region: ""
      },
      addForm: {
        name: "",
        sort: 99
      },
      editForm: {
        name: "",
        sort: 99
      },
       pagination: {
        start: 1, //从第一页开始
        pageSize:10, //每一页展示6条数据
        total: 0
      }, // 分页配置
      title: "",
      title1: "",
      total: 0,
      dialogAddgsVisible: false,
      dialogEditgsVisible: false,
      dialogEditgsVisible1: false,
      dialogDetailsVisible: false,
      detailForm: [],
      options: [
        {
          value: "选项2",
          label: "WARN"
        }
      ],
      optionss: [
        {
          value: "选项1",
          label: "移动"
        },
        {
          value: "选项2",
          label: "电信"
        },
        {
          value: "选项3",
          label: "联通"
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
      tableLabel: [
        { label: "日志ID", prop: "logID" },
        { label: "租户名", prop: "tenantName" },
        { label: "日志等级", prop: "level" },
        { label: "来源URL", prop: "source" },
        { label: "调用时间", prop: "createTime" },
        { label: "日志类型", prop: "logType" },
        { label: "日志内容", prop: "msg" }
      ]
    };
  },
  methods: {
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
      let formData = {
        start:1,
        pageSize:this.pagination.pageSize
      
      };

      // if (this.formInline.level) {
      //   formData.level = this.formInline.level;
      // }
      if (this.formInline.name) {
        formData.source = this.formInline.name;
      }
      if (this.formInline.same) {
        formData.tenantName = this.formInline.same;
      }
      console.log(this.value2, "this.value2");

      if (this.value2 != "" && this.value2 != undefined) {
        formData.startTime = this.dateTransfer(this.value2[0]);
        formData.endTime = this.dateTransfer(this.value2[1]);
      }
      if (this.value) {
        formData.level = 'WARN';
      }
      console.log(formData, "传递的值");
      this.$axios
        .post(
          "/oms-basic/abilityLog!selectLog.json",
          this.$qs.stringify(formData)
        )
        .then(res => {
          this.tableData = res.data.data;
          this.pagination.total = res.data.count
          // console.log(res, "search");
        })
        .catch(err => {
          console.log(err,'err')
        });
    },
    viewdetail(index, row) {
      console.log(index, "index");
      this.dialogDetailsVisible = true;
      this.detailForm = row;
    },
    //////
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
      // this.pagination.start = currentPage;
      let params = {
        start: currentPage,
        pageSize: this.pagination.pageSize,
        level: this.value,
        // ...this.formData
        tenantName:this.formInline.same,
        source:this.formInline.name,
      };
      if(this.value2.length) {
        params.startTime=this.dateTransfer(this.value2[0]),
        params.endTime=this.dateTransfer(this.value2[1])
      }
      this.listInt(params);
    },
    deleteRow1(index) {
      this.tableData.forEach((item, index) => {});

      this.dialogEditgsVisible1 = false;
    },

    editgsForm(logID) {
      // console.log(logID,"zunbei")
      // this.$router.push("/LogDetails",logID);
    },
    listInt(arr) {
      arr.level = "WARN"
      this.$axios
        .post("/oms-basic/abilityLog!selectLog.json",
          this.$qs.stringify(arr)
        )
        .then(res => {
          this.tableData = res.data.data;
          this.pagination.total = res.data.count;
          // console.log(res.data.data);
          // console.log(res, ".data.data");
        });
    },
    onexport: function(evt) {
      var wb = XLSX.utils.table_to_book(document.getElementById('out-table'));
      var wbout = XLSX.write(wb, { bookType: 'xlsx', type: 'binary' });
      saveAs(new Blob([this.s2ab(wbout)], { type: 'application/octet-stream'}),evt+"日志列表.xlsx");
    },

    s2ab: function(s) {
      if(typeof ArrayBuffer != 'undefined') {
        var buf = new ArrayBuffer(s.length);
        var view = new Uint8Array(buf);
        for (var i=0; i!=s.length; ++i) view[i] = s.charCodeAt(i) & 0xFF;
        return buf;
      }  
      else {
      var buf = new Array(s.length);
      for (var i=0; i!=s.length; ++i) buf[i] = s.charCodeAt(i) & 0xFF;
      return buf;
      }
    },
    dao() {
      if(this.formInline.same){
        this.onexport(this.formInline.same+'第一页')
      }else{
        this.onexport('第一页')
      }
      // this.$axios
      //   .post("/oms-basic/abilityLog!exportLogMessage.json")
      //   .then(res => {
      //     const urlb = window.location.host+res.data.address
      //     const eleLink = document.createElement('a')
      //     eleLink.download = urlb
      //     eleLink.style.display = 'none'
      //     eleLink.href = urlb
      //     // // 触发点击
      //     document.body.appendChild(eleLink)
      //     eleLink.click()
      //     // // 然后移除
      //     document.body.removeChild(eleLink)
      //   });
    },
    goDownload(_url){
      window.location.href="http://192.168.1.203:28082"+_url
    }
  },
  
  mounted() {
    this.listInt({
      start:this.pagination.start,
      pageSize:this.pagination.pageSize
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
/deep/.el-dialog__title {
  display: inline-block;
  width: 100%;
  text-align: center;
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
.detailBox {
  li {
    border-top: 1px solid #000;
    border-left: 1px solid #000;
    display: flex;
    align-items: center;
    justify-content: space-between;
    .bg_cyan {
      line-height: 40px;
      background-color: #f9fbfd;
      width: 100px;
      font: 14px/40px "";
      text-align: center;
      border-right: 1px solid #000;
    }
    .msgBox {
      flex: 1;
      line-height: 40px;
      text-indent: 30px !important;
      border-right: 1px solid #000;
    }
  }
}
</style>
