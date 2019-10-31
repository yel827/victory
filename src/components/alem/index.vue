<template>
  <div class="mHome">
    <div class="titleQ">
      <el-tabs v-model="activeName" @tab-click="handleClick">
        <!-- 选项卡一告警列表的布局 -->
        <el-tab-pane label="告警列表" name="first">
          <div class="search">
            <el-form :inline="true" :model="formData" class="demo-form-inline">
              <!-- 告警源 -->
              <!-- <span class="demonstration">告警源</span> -->
              <el-form-item label="告警源" class="right">
                <el-select v-model="formData.source" clearable>
                  <el-option
                    v-for="item in optionss"
                    :key="item.label"
                    :label="item.label"
                    :value="item.label"
                  ></el-option>
                </el-select>
              </el-form-item>
              <!-- 告警类型 -->
              <span class="demonstration">告警类型</span>
              <el-select v-model="formData.warnStatus" placeholder="请选择" class="right" clearable>
                <el-option
                  v-for="item in options"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>

              <el-form-item label="监控项" class="right">
                <el-input v-model="formData.monitorItem" placeholder="监控项" clearable></el-input>
              </el-form-item>

              <!-- 时间选择 -->
              <!-- <span class="demonstration">时间选择</span> -->
              <span class="ssss">
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
                ></el-date-picker>
              </span>
              <!-- 搜索 导出当前按钮 -->
              <el-form-item>
                <el-button type="primary" @click="onSubmit_" class="right">搜索</el-button>
                <el-button type="primary" class="right" @click="exportCurrent">发 送</el-button>
              </el-form-item>
            </el-form>
          </div>
          <el-table
            :data="tableData"
            style="width: 100%;"
            class="tabP"
            @selection-change="changFun"
          >
            <el-table-column type="selection" width="65" @selection-change="changFun"></el-table-column>
            <el-table-column prop="id" label="告警ID" width="180"></el-table-column>
            <el-table-column prop="source" label="告警源" width="180"></el-table-column>
            <el-table-column prop="monitorItem" label="监控项"></el-table-column>
            <el-table-column prop="createTime" label="告警时间"></el-table-column>
            <el-table-column prop="warnStatus" label="告警状态"></el-table-column>
            <el-table-column prop="msg" label="告警信息"></el-table-column>

            <el-table-column label="操作">
              <template slot-scope="scope">
                <!-- 操作 -->
                <div>
                  <i
                    class="el-icon-success"
                    :class='scope.row.strategy == 2  ? "undealed" : (scope.row.strategy == 0 ? "dealed" : "undealed")'
                    @click="on_(scope.row,scope.$index)"
                    style="font-size:18px;"
                  >
                    <span style="font-size:14px;">已处理</span>
                  </i>
                  <i
                    class="el-icon-success"
                    :class='scope.row.strategy == 2  ? "undealed" : (scope.row.strategy == 1 ? "dealed" : "undealed")'
                    @click="off_(scope.row,scope.$index)"
                    style="font-size:18px;"
                  >
                    <span style="font-size:14px;">已忽略</span>
                  </i>
                </div>
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
          <el-dialog class="tanCheng" title="联系人" :visible.sync="dialogDetailsVisible" width="50%">
            <el-table :data="tableData_once" style="width: 100%" @selection-change="changFun_sou">
              <el-table-column type="selection" width="65" @selection-change="changFun_sou"></el-table-column>
              <el-table-column prop="name" label="联系人姓名" width="180"></el-table-column>
              <el-table-column prop="phone" label="联系人电话" width="180"></el-table-column>
              <el-table-column prop="email" label="联系人邮箱"></el-table-column>
            </el-table>
            <div slot="footer" class="dialog-footer" style="text-align=:center;">
              <el-button @click="dialogDetailsVisible = false">取 消</el-button>
              <el-button type="primary" @click="submit_once">确 定</el-button>
            </div>
          </el-dialog>
        </el-tab-pane>
        <!-- 选项卡二布局结构 -->
        <el-tab-pane label="告警配置管理" name="second">
          <div class="search">
            <el-form :inline="true" :model="formInline" class="demo-form-inline">
              <el-form-item label="联系人姓名" class="right plain_ome">
                <el-input v-model="tableDataValue" placeholder="联系人姓名" clearable></el-input>
              </el-form-item>
              <el-form-item>
                <el-button type="primary" @click="doFilter" class="right">搜索</el-button>
                <el-button type="primary" plain @click="dialogVisible = true" class="right">创建联系人</el-button>
                <!-- 0 -->
                <el-dialog title="提示"  :visible.sync="dialogVisible" class="onace" width="30%">
                  <el-row>
                    联系人名称
                    <el-input v-model="productName" style="width: 70%;margin: 10px 0 10px 0"></el-input>
                  </el-row>
                  <el-row>
                    联系人邮箱
                    <el-input v-model="value" style="width: 70%;margin: 10px 0 10px 0"></el-input>
                  </el-row>
                  <el-row>
                    联系人电话
                    <el-input v-model="values" style="width: 70%;margin: 10px 0 10px 0"></el-input>
                  </el-row>
                  <div slot="footer" class="dialog-footer">
                    <el-button @click="dialogVisible = false">取 消</el-button>
                    <el-button type="primary" @click="addData">确 定</el-button>
                  </div>
                </el-dialog>
                <!-- 0 -->
              </el-form-item>
            </el-form>
          </div>
          <el-table
            :data="tableDatas.slice((currentPage-1)*pagesize,currentPage*pagesize)"
            style="width: 100%;"
            class="tabP"
          >
            <el-table-column prop="id" label="联系人ID" width="180"></el-table-column>
            <el-table-column prop="name" label="联系人姓名" width="180"></el-table-column>
            <el-table-column prop="phone" label="联系人电话"></el-table-column>
            <el-table-column prop="email" label="联系人邮箱"></el-table-column>
            <el-table-column prop="msg" label="告警策略"></el-table-column>
            <el-table-column prop="createTime" label="创建时间"></el-table-column>
            <el-table-column prop="updateTime" label="更新时间"></el-table-column>
            <el-table-column label="操作">
              <template slot-scope="scope">
                <!-- 编辑 -->
                <el-button type="text" @click="editgsForm(scope.$index, scope.row)">
                  <i class="icon iconfont icon-bianji" style="font-size:18px; font-weight:bold;"></i>
                </el-button>
                <!-- 编辑弹窗 dialog-->
                <el-dialog
                  class="headers onace"
                  :title="title"
                  :visible.sync="dialogEditgsVisible"
                  width="30%"
                  center
                >
                  <el-form :model="editForm" :rules="rules" ref="editForm">
                    <el-form-item label="联系人姓名">
                      <el-input readonly v-model="editForm.name" autocomplete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="联系手机号">
                      <el-input v-model="editForm.phone" autocomplete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="联系人邮箱">
                      <el-input v-model="editForm.email" autocomplete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="告警策略">
                      <el-input v-model="editForm.msg" autocomplete="off"></el-input>
                    </el-form-item>
                  </el-form>

                  <div slot="footer" class="dialog-footer">
                    <el-button @click="quXiao()">取 消</el-button>
                    <el-button type="primary" @click="saveEditForm()">确 定</el-button>
                  </div>
                </el-dialog>
                <!-- 删除 -->
                <el-button type="text" @click="open(scope.row.id)">
                  <i
                    class="icon iconfont icon-shanchu"
                    style="font-size:18px; color:orange; font-weight:bold;"
                  ></i>
                </el-button>
              </template>
            </el-table-column>
          </el-table>
          <div class="block">
            <el-pagination
              @current-change="handleCurrentChange2"
              :current-page.sync="pagination2.start"
              :page-size="pagination2.pageSize"
              layout="total, prev, pager, next"
              :total="pagination2.total"
            ></el-pagination>
          </div>
        </el-tab-pane>
      </el-tabs>
    </div>
  </div>
</template>

 <script>
import echarts from "echarts";
export default {
  data() {
    return {
      formData: {
        warnStatus: "",
        source: "",
        name: "",
        monitorItem: "",
        selectTime: []
      },
      tableData_once: [],
      // gaos:'',
      // gaos2:'',
      // gaos3:'',
      radio: "",
      activeName: "first",
      currentPage: 1, //初始页
      pagesize: 10, //每页的数据
      //进度条
      percentage: 20,
      percentage1: 35,
      customColor: "#409eff",
      customColors: [
        { color: "#f56c6c", percentage: 65 },
        { color: "#e6a23c", percentage: 70 },
        { color: "#5cb87a", percentage: 75 },
        { color: "#1989fa", percentage: 80 },
        { color: "#6f7ad3", percentage: 100 }
      ],
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
      value2: "",
      value3: "",
      form: {
        name: "",
        abilityId: [],
        date1: "",
        date2: "",
        delivery: false,
        type: [],
        resource: "",
        desc: ""
      },
      formInline: {
        user: "",
        name: "",
        region: ""
      },
      addForm: {
        name: "",
        sort: 99
      },
      editForm: {
        name: "",
        sort: 99,
        arrayAbility: [],
        temArr: []
      },
      title: "",
      title1: "",
      // 分页配置
      pagination: {
        start: 1, //从第一页开始
        pageSize: 10, //每一页展示6条数据
        total: 0
      },
      pagination2: {
        start: 1, //从第一页开始
        pageSize: 10, //每一页展示6条数据
        total: 0
      },
      dialogAddgsVisible: false,
      dialogEditgsVisible: false,
      dialogEditgsVisible1: false,
      dialogDetailsVisible: false,
      dialogVisible: false,
      productName: "",
      tableDataValue: "",
      options: [
        {
          value: 0, 
          label: "已处理" 
        },
        {
          value: 1,
          label: "已忽略"
        },
        {
          value: 2,
          label: "待处理"
        }
      ],
      optionss: [
        {
          value: "0",
          label: "主机监控"
        },
        {
          value: "1",
          label: "组件监控"
        },
        {
          value: "2",
          label: "能力监控"
        }
      ],
      
      value: "",
      values: "",

      value2: "",
      value3: "",

      rules: {
        name: [
          { required: true, message: "请输入名称" },
          { min: 2, max: 10, message: "SSSSSSS", trigger: "blur" }
        ],
        sort: [{ type: "number", message: "11233552", trigger: "blur" }]
      },
      tableData: [],
      tableDatas: [],
      data_id: [],
      data_sun:[]
    };
  },
  methods: {
    // 初始页currentPage、初始每页数据数pagesize和数据data
    handleCurrentChange: function(currentPage) {
      let params = {
        start: currentPage,
        pageSize: this.pagination.pageSize,
        source: this.formData.source,
        warnStatus: this.formData.warnStatus,
        monitorItem: this.formData.monitorItem
      };
      if (this.formData.selectTime.length) {
        params.startTime = this.dateTransfer_(this.formData.selectTime[0]);
        params.endTime = this.dateTransfer_(this.formData.selectTime[1]);
      }
      this.listInt(params);
    },
    //----------------------------------------------------------------------
    handleCurrentChange2: function(currentPage) {
      // this.pagination.start = currentPage;
      this.getData({
        start: currentPage,
        pageSize: this.pagination2.pageSize,
        // level: "移动",
        // ...this.formData  //延展操作符
        name: this.tableDataValue
      });
    },
    //数据展示(告警列表)
    listInt(arr) {
      this.$axios
        .post("/oms-basic/warnInfoRelate!list.json", this.$qs.stringify(arr))
        .then(res => {
          this.tableData = res.data.list;
          console.log(this.tableData, "this.tableData");
          for (var i = 0; i < this.tableData.length; i++) {
            if (this.tableData[i].warnStatus == "2") {
              this.tableData[i].warnStatus = "待处理"; //把返回的状态值2转换成待处理
            } else if (this.tableData[i].warnStatus == "1") {
              this.tableData[i].warnStatus = "已忽略"; //把返回的状态值1转换成已忽略
            } else if (this.tableData[i].warnStatus == "0") {
              this.tableData[i].warnStatus = "已处理"; //把返回的状态值0转换成已处理
            }
          }

          // this.tableData.map(item=>{
          //   let strategy = '';
          //   switch(item.warnStatus){
          //     case 0: strategy = '已处理';break;
          //     case 1: strategy = '已忽略';break;
          //     case 2: strategy = '待处理';break;
          //   }
          //   item.warnStatus = status;
          //   return item;
          // });
          this.pagination.total = res.data.count;
        });
    },
    //数据展示(联系人列表)
    getData(obj) {
      this.$axios
        .post("/oms-basic/emergencyContact!list.json", this.$qs.stringify(obj))
        .then(res => {
          this.tableDatas = res.data.list;
          this.pagination2.total = res.data.count;
        });
    },
    // 告警搜索
    dateTransfer_(date) {
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
    onSubmit_() {
      // if (!(this.formData.warnStatus || this.formData.source || this.formData.name || this.formData.selectTime)) {
      //   return;
      // }
      let data = {
        start: 1,
        pageSize: 10
      };
      // this.pagination = {
      //   start: 1,
      //   pageSize: 10,
      //   total: 0
      // };
      // console.log(this.formData.warnStatus)

      if (this.formData.warnStatus) {
        data.warnStatus = this.options.filter(
          item => item.value === this.formData.warnStatus
        )[0].value;
      }
      if (this.formData.source) {
        data.source = this.formData.source;
      }
      if (this.formData.monitorItem) {
        data.monitorItem = this.formData.monitorItem;
      }
      if (
        this.formData.selectTime != "" &&
        this.formData.selectTime != undefined
      ) {
        data.startTime = this.dateTransfer_(this.formData.selectTime[0]);
        data.endTime = this.dateTransfer_(this.formData.selectTime[1]);
      }
      this.resetPagination();
      data.start = 0;
      data.pageSize = 10;
      this.listInt(data);
    },
    viewdetail(index, row) {
      this.dialogDetailsVisible = true;
      this.detailForm = row;
    },
    resetPagination() {
      this.pagination = {
        start: 0,
        pageSize: 10,
        total: 0
      };
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
    //联系人编辑（需要刷新）
    quXiao(){
      this.dialogEditgsVisible = false
      this.getData()
    },
    //联系人搜索
    doFilter() {
      this.filtertableData = []; //过滤后的数据
      let IDcode = {
        name: this.tableDataValue,
        start: 1,
        pageSize: this.pagination2.pageSize
      };
      this.pagination2 = {
        start: 1,
        pageSize: 10,
        total: 0
      };
      this.getData(IDcode);
    },
    //进度条
    customColorMethod(percentage) {
      if (percentage < 30) {
        return "#909399";
      } else if (percentage < 70) {
        return "#e6a23c";
      } else {
        return "#67c23a";
      }
    },
    increase() {
      this.percentage += 10;
      if (this.percentage > 100) {
        this.percentage = 100;
      }
    },
    decrease() {
      this.percentage -= 10;
      if (this.percentage < 0) {
        this.percentage = 0;
      }
    },
    //
    handleClick(tab, event) {
      console.log(tab, "tab---tab");
      // if(tab.name === 'first'){

      // }else if(tab.name === 'second'){

      // }
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

    deleteRow1(index) {
      this.tableData.forEach((item, index) => {});

      this.dialogEditgsVisible1 = false;
    },
    onSubmit() {
      if (
        !(
          this.formInline.level ||
          this.formInline.name ||
          this.formInline.same ||
          this.value2 ||
          this.value ||
          this.values
        )
      ) {
        return;
      }
      var formData = {};
      if (this.formInline.level) {
        formData.level = this.formInline.level;
      }
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
        formData.level = this.value;
      }
      console.log(formData, "传递的值");
      this.$axios
        .post(
          "/oms-basic/warnInfoRelate!list.json",
          this.$qs.stringify(formData)
        )
        .then(res => {
          this.tableData = res.data.list;

          console.log(res, "search");
        })
        .catch(err => {});
      //
    },
    viewdetail(index, row) {
      console.log(index, "index");
      this.dialogDetailsVisible = true;
      this.detailForm = row;
    },
    /**
     *
     * @param
     */
    editgsForm(index, row) {
      let updatas = {
        name: this.editForm.name,
        email: this.editForm.email
      };
      this.dialogEditgsVisible = true;
      console.log(this.editForm.temArr);
      row.temArr = this.editForm.temArr;
      console.log(row, "row");
      this.title = "编辑";
      this.editForm = row;
      var arr1 = [],
        arr2 = [];
      console.log(row.abilityIDs, "row.abilityIDs"); //111,116,115,114,122,113,112,119 row.abilityIDs
      //arr1 = row.abilityIDs.split(","); //arr1是什么, row.abilityIDs是什么???
      console.log(arr1, "arr1"); //["111", "116", "115", "114", "122", "113", "112", "119"]
      arr2 = row.abilityNames.split(",");
      console.log(arr2, "arr2");
      var arr = [];
      this.form.abilityId = row.abilityIDs; //arr赋值给editForm.arrayAbility
      this.editForm.arrayAbility = this.editForm.temArr;
      console.log(this.editForm.temArr);
    },
    saveEditForm() {
      var that = this;
      let param = new URLSearchParams();
      param.append("id", this.editForm.id);
      param.append("name", this.editForm.name);
      param.append("email", this.editForm.email);
      param.append("phone", this.editForm.phone);
      param.append("status", this.editForm.status);
      param.append("wechatName", this.editForm.wechatName);
      param.append("msg", this.editForm.msg);
      console.log(this.form.abilityId);
      let parm = {
        id: this.editForm.id,
        name: this.editForm.name,
        email: this.editForm.email,
        phone: this.editForm.phone,
        status: this.editForm.status,
        wechatName: this.editForm.wechatName,
        msg: this.editForm.msg
      };
      console.log(parm);
      this.$axios
        .post("/oms-basic/emergencyContact!save.json", param)
        .then(res => {
          console.log(res, "res");
          console.log(res, "创建联系新人");
          if (res.data.code == 10000) {
            that.dialogEditgsVisible = false;
            this.getData()
          }
        })
        .catch(error => {
          console.log("error");
        });
      this.$axios.post("/oms-basic/emergencyContact!list.json").then(res => {
        console.log(res.data.list, "联系人列表");
        this.tableDatas = res.data.list;
      });
    },
    //点击删除是触发函数
    open(index) {
      this.$confirm("此操作将删除该数据, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
        center: true
      })
        .then(() => {
          var that = this;
          console.log(index, "下标");
          var par = {
            id: index
          };
          console.log(par);
          that.$axios
            .post(
              "/oms-basic/emergencyContact!delete.json",
              that.$qs.stringify(par)
            )
            .then(res => {
              console.log(res);
              this.$axios
                .post("/oms-basic/emergencyContact!list.json")
                .then(res => {
                  console.log(res.data.list, "联系人列表");
                  this.tableDatas = res.data.list;
                });
            });
        })
        .catch(() => {
          console.log("11111111");
          this.$message({
            type: "info",
            message: "已取消删除"
          });
        });
    },

    //////
    addData() {
      //显示弹窗
      var that = this;
      that.dialogVisible = false;
      //调取添加接口
      // let header = {
      //   headers: {
      //     "Content-Type": "application/x-www-form-urlencoded"
      //   }
      // };
      //1.
      var params = new URLSearchParams();
      params.append("tenantName", that.productName);
      params.append("abilityIDs", that.value);
      //2. that.$qs.stringify(params1)

      var params1 = {
        name: that.productName,
        email: that.value,
        phone: that.values
      };
      that.$axios
        .post(
          "/oms-basic/emergencyContact!save.json",
          that.$qs.stringify(params1)
        )
        //成功
        .then(res => {
          //返回的数据
          console.log("res777", res);
          //自己定义的空数组tableData
          // this.tableData.splice(0, this.tableData.length)
          var arrs = { tenantName: that.productName, abilityIDs: that.value };
          that.tableData.splice(that.tableData.length, 0, arrs);
          //发送ajax请求获取数据
          this.pagination2 = {
            start: 1,
            pageSize: 10,
            total: 0
          };
          this.getData(this.pagination2);
        })
        //失败
        .catch(error => {
          console.log(error);
        });
    }, //忽略的接口
    on_(obj, i) {
      
      var intMyData1 = {
        id: obj.id,
        strategy:0
      };
      this.$axios
        .post(
          "/oms-basic/warnInfoRelate!updateStatusById.json",
          this.$qs.stringify(intMyData1)
        )
        .then(res => {
          this.tableData[i].strategy = !0;
          this.listInt()
        });
    }, //已处理的接口
    off_(obj, i) {
      
      console.log(obj);
      var intMyData2 = {
        id: obj.id,
        strategy:1
      };
      console.log(obj.strategy, "obj.strategy");
      console.log(intMyData2, "sss");
      this.$axios
        .post(
          "/oms-basic/warnInfoRelate!updateStatusById.json",
          this.$qs.stringify(intMyData2)
        )
        .then(res => {    
          this.tableData[i].strategy = !1;
          this.listInt()
        });
    },
    changFun(val) {
      console.log(this, "this-val");
      console.log(val, "val");
      this.multipleSelection = val;
      for (var i = 0; i < this.multipleSelection.length; i++) {
        console.log(this.multipleSelection[i].id);
        this.data_id.push(this.multipleSelection[i].id);
      }
      console.log(this.data_id, "当行数据");
    },
    changFun_sou(val){
       this.multipleSelections = val;
       for (var i = 0; i < this.multipleSelections.length; i++) {
        console.log(this.multipleSelections[i].id);
        this.data_sun.push(this.multipleSelections[i].id);
      }
      console.log(this.data_sun,"联系人选中的数据")
    },
    //发送
    submit_once() {
      var that = this;
      that.dialogDetailsVisible = false;
      console.log(this.data_id, "multipleSelection");
      console.log(this.data_sun, "once");
      let data = [];
      for (var i = 0; i < this.data_sun.length; i++) {
        data.push(this.data_sun[i]);
      }
      console.log(data,"shuhsuhsuhushsu")
      var myMain = {
        warnIds: this.data_id.join(","),
        emergencyContactIds: data.join(",")
      };
      console.log(data);
      console.log(myMain);
      this.$axios
        .post(
          "/oms-basic/warnInfoRelate!sendWareInfoToEmergencyContact.json",
          this.$qs.stringify(myMain)
        )
        .then(res => {
          console.log(res, "信息发送给联系人");
        });
    },
    exportCurrent() {
      this.dialogDetailsVisible = true;
    },
    getOnce(obj) {
      this.$axios
        .post("/oms-basic/emergencyContact!list.json", this.$qs.stringify(obj))
        .then(res => {
          this.tableData_once = res.data.list;
          this.total = res.data.count;
        });
    }
  },
  mounted() {
    this.getData({
      start: this.pagination2.start,
      pageSize: this.pagination2.pageSize
    });
    this.listInt({
      start: this.pagination.start,
      pageSize: this.pagination.pageSize
    });
    this.getOnce();
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
.mHome {
  height: 870px;
  background: #fff;
  overflow: hidden;
  position: relative;

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
.home {
  height: 870px;
  background: #fff;
  overflow: hidden;
  position: relative;
}

.search {
  padding: 12px 0 0 12px;
}
.tetMy /deep/ .el-input__inner {
  width: 170px;
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
.active {
  background: #3584f3;
  color: #fff;
}
.block {
  position: fixed;
  left: 37%;
  top: 915px;
}

.onace {
  
  /deep/.el-form-item__content {
    display: flex;
    flex-direction: row;
    /deep/.el-input__inner {
      width: 440px;
      float: right;
    }
  }
}

.icon_tu {
  display: inline-block;
  position: fixed;
  right: 4%;
}
.information {
  div:nth-child(1) {
    width: 100%;
    height: 100px;
    border-bottom: 1px solid #ccc;
    padding: 0 20px 20px 20px;
    box-sizing: border-box;
    background: #fff;
    p {
      line-height: 30px;
      span {
        margin-left: 22px;
      }
    }
  }
  div:nth-child(2) {
    width: 100%;
    height: 240px;
    border-bottom: 1px solid #ccc;
    padding: 0 20px 20px 20px;
    box-sizing: border-box;
    background: #fff;
  }
  div:nth-child(3) {
    width: 100%;
    height: 240px;
    border-bottom: 1px solid #ccc;
    padding: 0 20px 20px 20px;
    box-sizing: border-box;
    background: #fff;
  }
}
/deep/.el-input__inner {
  width: 120px;
}
.myCPU {
  display: flex;
  flex-direction: row;
  div:nth-child(1) {
    width: 240px;
    height: 100%;
    border: none;
    .list {
      width: 100%;
      padding-left: 60px;
      box-sizing: border-box;
      li {
        line-height: 15px;
        font-size: 10px;
      }
    }
  }
  div:nth-child(2) {
    flex: 1;
    background: #fff;
  }
}
/deep/ .svgTick {
  margin-left: 40px;
  margin-top: -16px;
}
/deep/ .el-dialog__header {
  background: #3584f3;
  color: #fff;
  /deep/ .el-dialog__title {
    color: #fff;
  }
  /deep/ .el-dialog__close.el-icon.el-icon-close::before {
    color: #fff;
  }
}
.MyData {
  width: 350px;
  /deep/ .el-range-input {
    width: 150px;
  }
}
.plain_ome {
  width: 300px;
  /deep/.el-input__inner {
    width: 200px;
  }
  /deep/ .el-range-input {
    width: 200px;
  }
}
.add {
  width: 500px;

  /deep/.el-input__inner {
    width: 400px;
  }
  /deep/ .el-range-input {
    width: 400px;
  }
}
.ssss {
  /deep/.el-input__inner {
    width: 400px;
  }
}
.dealed {
  color: #3584f3;
}
.undealed {
  color: #ccc;
}
</style>
