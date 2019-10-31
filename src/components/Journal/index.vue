<template>
  <div class="home">
    <div class="titleQ">日志列表</div>
    <div class="search">
      <!-- form表单 //form表单里面包含2个select/2个form-item/1个date-picker/-->
      <el-form :inline="true" :model="formData" class="demo-form-inline">
        <!-- 租户名称 -->
        <span class="demonstration">租户名称</span>
        <!-- 下拉框组件一挂载就掉 -->
        <el-select v-model="formData.tenantName" placeholder="租户名称" class="right" clearable>
          <el-option
            v-for="item in tenantNameOptions"
            :key="item.tenantID"
            :value="item.tenantName"
          ></el-option>
        </el-select>
        <!-- 日志等级 -->
        <span class="demonstration">日志等级</span>
        <el-select v-model="formData.level" filterable placeholder="日志等级" class="right" clearable>
          <el-option 
          v-for="item in journalLevel" 
          :key="item.level" 
          :value="item.level">
          </el-option>
        </el-select>
        <!-- 来源IP -->
        <el-form-item label="来源URL" class="right">
          <el-input v-model="formData.source" placeholder="来源URL" clearable></el-input>
        </el-form-item>
        <!-- 时间选择 -->
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
          :picker-options="pickerOptions"
          clearable
        ></el-date-picker>
        <!-- 搜索/导出当前 按钮 -->
        <el-form-item>
          <el-button type="primary" @click="onSubmit" class="right">搜索</el-button>
          <el-button type="primary" class="right" @click="exportCurrent">导出当前</el-button>
        </el-form-item>
      </el-form>
    </div>
    <div ref="barCharts" style="width:100%;height:200px;"></div>
    <div id="Detailedy">
      <i class="el-icon-pie-chart" id="bd"></i> 列表明细
    </div>
    <!-- table布局 -->
    <el-table :data="tableData" id="out-table">
      <el-table-column prop="logID" label="日志ID" width="180"></el-table-column>
      <el-table-column prop="tenantName" label="租户名" width="180"></el-table-column>
      <el-table-column prop="level" label="日志等级"></el-table-column>
      <el-table-column prop="source" show-overflow-tooltip label="来源URL"></el-table-column>
      <el-table-column prop="createTime" label="调用时间"></el-table-column>
      <el-table-column prop="logType" label="日志类型"></el-table-column>
      <el-table-column :show-overflow-tooltip="true" prop="msg" label="日志内容" width="300px"></el-table-column>
      <!-- 造作column -->
      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-button type="text" @click="viewdetail(scope.$index, scope.row)">
            <i class="icon iconfont icon-chakan" style="font-size:18px; font-weight:bold;"></i>
          </el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 分页/分页 -->
    <el-pagination
      style="text-align:center;"
      @current-change="handleCurrentChange"
      :current-page.sync="pagination.start"
      :page-size="pagination.pageSize"
      layout="total, pager"
      :total="pagination.total"
    ></el-pagination>
    <el-dialog title="提示" :visible.sync="dialogDetailsVisible" width="50%" style="text-align:left">
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
 import XLSX from 'xlsx'
import echarts from "echarts";
export default {
  data() {
    return {
      app: null,
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
      //formData是下拉框的数据
      formData: {
        tenantName: "",
        level: "",
        source: "",
        selectTime: []
      }, // 搜索表单
      pagination: {
        start: 1, //从第一页开始
        pageSize: 6, //每一页展示6条数据
        total: 0
      }, // 分页配置
      detailForm: {},
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
      dialogAddgsVisible: false,
      dialogEditgsVisible: false,
      dialogEditgsVisible1: false,
      dialogDetailsVisible: false,
      journalLevel: [{ level: "INFO" }, { level: "WARN" }], // 日志等级死数据
      tenantNameOptions: [
        // {tenantID: 1,tenantName: '移动'},
        // {tenantID: 2, tenantName: '联通'},
        // {tenantID: 3,tenantName: '电信'}
      ], // 租户名死数据
      rules: {
        name: [
          { required: true, message: "请输入名称" },
          { min: 2, max: 10, message: "SSSSSSS", trigger: "blur" }
        ],
        sort: [{ type: "number", message: "11233552", trigger: "blur" }]
      },
      tableData: [
        // {
        //   createTime:"",  //创建时间
        //   logID: 11,  // 日志ID
        //   logType:'',  //日志类型
        //   level:"",  //日志等级
        //              //租户名 ????
        //   source: "http://192.168.0.1:8080",  //来源IP
        //   responseTime: 500 , //调用时间
        //   msg: "日志正常",    //日志内容
        // }
      ],
      loading: true,
      barChartOptions: {
        xAxis: [],
        series: [[], [], []]
      }
    };
  },
  mounted() {
    this.app = echarts.init(this.$refs.barCharts);
    //this.getJournalOverview();
    this.queryTenantName({}); //调取获取租户名称接口
    this.queryLogCallOverview({}); //请求日志调用概况接口
    this.getJournal({
      start: this.pagination.start,
      pageSize: this.pagination.pageSize
    }); // 日志搜索 日志明细接口
  
  },
  methods: {
    // 生成调用概况柱状图
    generatorBarChart() {
      // 初始化echarts实例
      let option = {
        title: {
          text: "调用概况 日志数量:（条）"
        },
        tooltip: {
          trigger: "axis",
          axisPointer: {
            // 坐标轴指示器，坐标轴触发有效
            type: "shadow" // 默认为直线，可选为：'line' | 'shadow'
          }
        },
        grid: {
          left: "3%",
          right: "4%",
          bottom: "3%",
          containLabel: true
        },
        xAxis: [
          {
            type: "category",
            data: this.barChartOptions.xAxis,
            axisTick: {
              alignWithLabel: true
            }
          }
        ],
        yAxis: [
          {
            type: "value"
          }
        ],
        series: [
          {
            name: "0点~8点",
            type: "bar",
            barWidth: 30,
            data: this.barChartOptions.series[0],
            color: ["#3398DB"]
          },
          {
            name: "8点~16点",
            type: "bar",
            barWidth: 30,
            data: this.barChartOptions.series[1],
            color: ["#3398DB"]
          },
          {
            name: "16点~24点",
            type: "bar",
            barWidth: 30,
            data: this.barChartOptions.series[2],
            color: ["#3398DB"]
          }
        ]
      };
      this.app.setOption(option); // 使用配置项显示柱状图
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
    //调用搜索接口
    onSubmit() {
      let formData = {
        start:1,
        pageSize:this.pagination.pageSize
      };
      if(this.formData.tenantName) {
        formData.tenantName = this.formData.tenantName
      };
      if(this.formData.level) {
        formData.level = this.formData.level;
      };
      if(this.formData.source) {
        formData.source = this.formData.source
      };
      if (this.formData.selectTime != "" && this.formData.selectTime != undefined) {
        formData.startTime = this.dateTransfer(this.formData.selectTime[0]);
        formData.endTime = this.dateTransfer(this.formData.selectTime[1]);
      };
      this.getJournal(formData);
      this.queryLogCallOverview(formData);

      
      // let { tenantName, level, IP, selectTime } = this.formData
      // let formData = {};
      // tenantName && (formData.tenantName = tenantName);
      // level && (formData.level = level);
      // IP && (formData.IP = IP);
      // selectTime.length &&
      //   ((formData.startTime = this.dateTransfer(selectTime[0])),
      //   (formData.endTime = this.dateTransfer(selectTime[1])));
      //   console.log(this,'this')
      // this.pagination = {
      //   start: 1,
      //   pageSize: 6,
      //   total: 0
      // };
      // Object.keys(this.pagination).forEach(
      //   item => (formData[item] = this.pagination[item])
      // );
      // console.log("formdata",formData)
    },
    viewdetail(index, row) {
      // console.log(index,'ndex')
      this.dialogDetailsVisible = true;
      this.detailForm = row;
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
    // handleSizeChange: function(size) {
    //   this.pagination.pageSize = size;
    //   // console.log(this.pagesize); //每页下拉显示数据
    // },
    handleCurrentChange: function(currentPage) {
      // this.pagination.start = currentPage;
      console.log(this.formData.selectTime);
      var params={
        start: currentPage,
        pageSize: this.pagination.pageSize,
        // ...this.formData,
        tenantName:this.formData.tenantName,
        level:this.formData.level,
        source:this.formData.source
      }

      if(this.formData.selectTime.length){
        params.startTime=this.dateTransfer(this.formData.selectTime[0])
        params.endTime=this.dateTransfer(this.formData.selectTime[1])
      }
      this.getJournal(params);
      // console.log(this.pagination.start)  //点击第几页
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
        // console.log(this.$refs[aaa]);
        if (valid) {
          // this.$axios.put(`http://localhost:3000/admin/categories/${this.editForm.id}`,this.editForm).then( res =>{
          //   alert('更新成功');
          this.dialogEditgsVisible = false;
          this.init();
          // console.log(valid);
          // })
        }
      });
    },
    // 列表明细接口 table表格
    getJournal(obj) {
      // this.loading = true;
      this.$axios
        .post("/oms-basic/abilityLog!selectLog.json", this.$qs.stringify(obj))
        .then(res => {
          // console.log("表格数据",res.data)
          if (res.data.code === "10000") {
            this.pagination.total = res.data.count;
            this.tableData = res.data.data;
            // this.loading = false;
            // this.resetForm();
          }
        })
        .catch(err => console.log("queryJouralList_error", err));
      //this.getJournalOverview();
    },
    // 调取获取租户名称接口
    queryTenantName(obj) {
      this.$axios
        .post(
          "/oms-basic/tenant!selectTenantList.json",
          this.$qs.stringify(obj)
        )
        .then(res => {
          if (res.data.code === "10000") {
            console.log("res",res.data.data)
            // this.tenantOptions = [...res.data.data];
            this.tenantNameOptions = res.data.data;
          }
        })
        .catch(err => console.log("tenantName_error", err));
    },
    // 获取日志等级接口  用的时候叫实参  定义的时候叫形参
    queryJournalLevel(obj) {
      this.$axios
        .post("", this.$qs.stringify(obj))
        .then(res => {
          if (res.data.code === "100000") {
            console.log("level", res.data.data);
          }
        })
        .catch(err => console.log("journalLevel_error", err));
    },
    // 表单重置函数
    resetForm() {
      this.formData = {
        tenantName: "",
        level: "",
        IP: "",
        selectTime: []
      };
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
    // 导出接口
    exportCurrent() {
      if(this.formData.tenantName){
        this.onexport(this.formData.tenantName+'第一页')
      }else{
        this.onexport('第一页')
      }
      // 
      // let data = {};
      // console.log(this.formData)
      // if(this.formData.tenantName){
      //  data.tenantName = this.formData.tenantName;
      //  console.log(data.tenantName,'data.tenantName')
      // };
      // if(this.formData.level){
      //   data.level = this.formData.level;
      // };
      // if(this.formData.source){
      //   data.source = this.formData.source;
      // };
      // if(this.formData.selectTime.length){
      //   data.startTime = this.dateTransfer(this.formData.selectTime[0]);
      //   data.endTime = this.dateTransfer(this.formData.selectTime[1]);
      // };
      // this.$axios
      //   .post("/oms-basic/abilityLog!exportLogMessage.json",this.$qs.stringify(data))
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
      //     // window.open('http://192.168.1.203:28082' + res.data.address)
      //   });
        // this.$axios.post('/oms-basic/abilityLog!exportLogMessage.json',this.$qs.stringify(data)).then( res => {
        //   let tForm=document.createElement('form');
        //   tForm.setAttribute('style','display:none');
        //   tForm.setAttribute('target','');
        //   tForm.setAttribute('method','get');
        //   tForm.setAttribute('action','http://192.168.1.203:28082'+res.data.address);
        //   let tInput=document.createElement('input');
        //   tInput.setAttribute('name','t');
        //   tInput.setAttribute('value','1');
        //   let tBody=document.getElementsByTagName('body')[0];
        //   tBody.appendChild(tForm);
        //   tForm.appendChild(tInput);
        //   console.log(res,tBody);
        //   tForm.submit()
        // })


    },
    // 请求日志调用概况接口
    queryLogCallOverview(obj) {
      console.log(obj,'obj')
      this.$axios
        .post(
          "/oms-basic/abilityLog!selectLogOverview.json",
          this.$qs.stringify(obj)
        )
        .then(res => {
          if (res.data.code === "10000") {
            //console.log("logCallOverview----",res.data.List);
            //this.barChartOptions.series=res.data.List;

            //还要进行数据排序 对应日期
            let _filter = obj => {
              // switch(code){
              //   case 0: series[0].push(count);break;
              //   case 8: series[1].push(count);break;
              //   case 16: series[2].push(count);break;
              // }
              series[0].push(obj.first);
              series[1].push(obj.second);
              series[2].push(obj.third);
            };
            let xAxis = [];
            let series = [[], [], []];
            res.data.list.forEach((item, index) => {
              if (index === 0) {
                xAxis.push(item.TimeDay);
              } else {
                if (xAxis.indexOf(item.TimeDay) === -1) {
                  xAxis.push(item.TimeDay);
                }
              } // 时间区间     时间区间内日志总数
              _filter(item);
            });
            // console.log("调用概况",xAxis,series)
            this.barChartOptions = { xAxis, series };
            this.$nextTick(() => this.generatorBarChart());
          }
        })
        .catch(err => console.log("logCallOverview_error", err));
    }
  }
};
</script>
<style>
.el-tooltip__popper {
  max-width: 210px !important;
}
</style>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
.home {
  height: 870px;
  background: #fff;
  position: relative;
  overflow: hidden;
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
      border-bottom: 2px solid red;
      border-right: 2px solid red;
    }
  }
}
</style>
