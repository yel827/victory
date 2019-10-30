<template>
  <div class="home">
    <div class="titleQ">租户管理</div>
    <!-- 头部结构 -->
    <div class="search">
      <el-form
        :inline="true"
        :model="ruleForm"
        :rules="rules"
        ref="ruleForm"
        label-width="100px"
        class="demo-ruleForm"
      >
        <el-form-item label="租户名称">
          <el-input v-model="tableDataName" placeholder="租户名称"  clearable></el-input>
        </el-form-item>
        <el-form-item label="授权码">
          <el-input v-model="tableDataValue" placeholder="授权码"  clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="doFilter">搜索</el-button>
          <el-button @click="dialogVisible = true">+ 添加租户</el-button>
          <!--  -->
          <el-dialog title="提示" :visible.sync="dialogVisible" width="30%">
            <el-form-item label="租户名称" prop="name">
              <el-input v-model="ruleForm.name" clearable></el-input>
            </el-form-item>
            <el-form-item label="租户能力" prop="region" style="margin-top:20px;">
               <el-select v-model="ruleForm.region" collapse-tags multiple clearable>
                <el-option
                  v-for="(item,index) in selec"
                  :key="item.ID"
                  :label="item.abilityName"
                  :value="item.ID"

                ></el-option>
              </el-select>
            </el-form-item>
            <div slot="footer" class="dialog-footer">
              <el-button @click="dialogVisible = false">取 消</el-button>
              <el-button type="primary" @click="addData('ruleForm')">确 定</el-button>
            </div>
          </el-dialog>
          <!--  -->
        </el-form-item>
      </el-form>
    </div>
    <!-- 表格布局结构   -->
    <el-table
      style="width: 100%;"
      class="tabP"
      :data="tableData.slice((currentPage-1)*pagesize,currentPage*pagesize)"
      fit
      border
      stripe
      tooltip-effect="dark"
      empty-text="暂无数据"
    >
      <template>
        <!-- v-for="(item, index) in tableLabel" -->
        <el-table-column show-overflow-tooltip prop="tenantID" label="租户ID"></el-table-column>
        <el-table-column show-overflow-tooltip prop="tenantName" label="租户名称"></el-table-column>
        <el-table-column show-overflow-tooltip prop="code" label="授权码"></el-table-column>
        <el-table-column show-overflow-tooltip prop="abilityIDs" label="授权能力识别码"></el-table-column>
        <el-table-column show-overflow-tooltip prop="abilityNames" label="授权能力">
          <!-- <template slot-scope="scope">
            <div v-for="(item,index) in scope.row.abilityNames.split(',')" :key="index">{{item}}</div>
          </template>-->
        </el-table-column>
        <el-table-column show-overflow-tooltip prop="createTime" label="创建时间"></el-table-column>
      </template>
      <el-table-column label="操作">
        <!-- 操作column -->
        <template slot-scope="scope">
          <!-- 编辑图标 -->
          <el-button type="text" @click="editgsForm(scope.$index, scope.row)">
            <i class="icon iconfont icon-bianji" style="font-size:18px; font-weight:bold;"></i>
          </el-button>
          <!-- 编辑弹窗 dialog-->
          <el-dialog
            class="headers"
            :title="title"
            :visible.sync="dialogEditgsVisible"
            width="30%"
            center
            @close="closeDialogVisible"
          >
            <el-form :model="editForm" :rules="rules" ref="editForm">
              <el-form-item label="租户ID">
                <el-input readonly v-model="editForm.tenantID" autocomplete="off"></el-input>
              </el-form-item>
              <el-form-item label="租户名称">
                <el-input v-model="editForm.tenantName" autocomplete="off"></el-input>
              </el-form-item>
              <el-form-item label="授权码">
                <el-input readonly v-model="editForm.code" autocomplete="off"></el-input>
              </el-form-item>
              <el-form-item label="授权能力">
                <el-select v-model="form.abilityId" placeholder="请选择" multiple>
                  <el-option
                    v-for="(o,j) in editForm.arrayAbility"
                    :key="j"
                    :label="o.abilityName"
                    :value="o.ID"
                  ></el-option>
                </el-select>
              </el-form-item>
            </el-form>

            <div slot="footer" class="dialog-footer">
              <el-button @click="dialogEditgsVisible = false">取 消</el-button>
              <el-button type="primary" @click="saveEditForm()">确 定</el-button>
            </div>
          </el-dialog>
          <!-- 设置 -->
          <el-button type="text" @click="edit(scope.row)">
            <i
              class="icon iconfont icon-icon-test"
              style="font-size:18px; font-weight:bold;margin-left:10px;"
            ></i>
          </el-button>
          <!-- 删除 -->
          <!-- 删除图标 -->
          <el-button type="text" @click="open(scope.row.tenantID)">
            <i class="icon iconfont icon-shanchu" style="font-size:18px; font-weight:bold ;"></i>
          </el-button>
          <!-- 删除弹窗 -->
          <el-dialog
            :title="title1"
            :visible.sync="dialogEditgsVisible1"
            width="30%"
            center
            @close="closeDialogVisible"
          >
            <span class="rmdata">确定要删除这条数据吗？</span>
            <div slot="footer" class="dialog-footer">
              <el-button @click="dialogEditgsVisible1 = false">取 消</el-button>
              <el-button type="primary" @click.native.prevent="deleteRow1(scope.row, tableData)">确 定</el-button>
            </div>
          </el-dialog>
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
  </div>
</template>

 <script>
export default {
  data() {
    return {
      value_one: "",
      dialogTableVisible: false,
      dialogFormVisible: false,
      value: "",
      search: "",
      dialogVisible: false,
      dialogVisibleb: false,
      visible: false,
      productName: "",
      productDescription: "",
      productNameb: "",
      productDescriptionb: "",
      _index: "",
      ruleForm: {
        name: "",
        region: "",
        date1: "",
        date2: "",
        delivery: false,
        type: [],
        resource: "",
        desc: ""
      },
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
      selec: [],
      formLabelWidth: "120px",
      //----------------------------------------
      currentPage: 1, //初始页
      pagesize: 10, //每页的数据
      userList: [],
      formInline: {
        user: "",
        name: "",
        region: ""
      },
      pagination: {
        start: 1, //从第一页开始
        pageSize: 10, //每一页展示6条数据
        total: 0
      }, // 分页配置
      //点击编辑时的data
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
      dialogAddgsVisible: false,
      dialogEditgsVisible: false,
      dialogEditgsVisible1: false,

      rules: {
        name: [
          { required: true, message: "请输入名称" },
          { min: 2, max: 10, message: "名称必须是两个到十个字符", trigger: "blur" }
        ],
        region: [
            { required: true, message: '请选择能力', trigger: 'change' }
            
          ],
        sort: [{ type: "number", message: "11233552", trigger: "blur" }]
      },
      //搜索功能需要的data
      tableDataName: "", //租户ID
      tableDataValue: "", //授权码
      // tableDataEnd: [],
      totalItems: 0,
      filterTableData: [],
      flag: false,
      //列表数据
      total: 0,
      tableData: [],
      tableLabel: [
        { label: "租户ID", prop: "tenantID" },
        { label: "租户名称", prop: "tenantName" },
        { label: "授权码", prop: "code" },
        { label: "授权能力编号", prop: "abilityIDs" },
        { label: "授权能力", prop: "abilityNames" },
        { label: "创建时间", prop: "createTime" }
      ]
    };
  },
  methods: {
    //能力下拉
    getster() {
      this.$axios
        .post("/oms-basic/ability!selectAbilityList.json")
        .then(res => {
          console.log(res.data, "能力下拉");
          this.selec = res.data.data;
          console.log(this.selec, "libiao");
        });
    },
    //添加
    addData(formName) {
      //显示弹窗
      if (this.ruleForm.region == "" || this.ruleForm.name == "") {
        return;
      }
      var that = this;
      that.dialogVisible = false;
      var arr_shu = [];
      arr_shu.push(this.ruleForm.region);
      var paramso = arr_shu.join(",");
      var params = new URLSearchParams();
      params.append("tenantName", that.ruleForm.name);
      params.append("abilityIDs", paramso);
      //2. that.$qs.stringify(params1)

      var params1 = {
        tenantName: that.ruleForm.name,
        abilityIDs: paramso
      };

      that.$axios
        .post("/oms-basic/tenant!addTenant.json", that.$qs.stringify(params1))
        //成功
        .then(res => {
          //返回的数据
          console.log("res777", res);
          //自己定义的空数组tableData
          // this.tableData.splice(0, this.tableData.length)
          var arrs = { tenantName: that.ruleForm.name, abilityIDs: paramso };
          that.tableData.splice(that.tableData.length, 0, arrs);
          //发送ajax请求获取数据
          if(res.data.code === "11111"){
            alert(res.data.msg)
          }else if(res.data.code === "10000"){
            alert(res.data.msg)
          }

          this.$axios
            .post("/oms-basic/tenant!selectTenantBy.json", {
              // tenantName: "110",
              // abilityIDs: "112"
            })
            .then(res => {
              // this.tableData = res.data.data;
              this.tableData = [].concat(res.data.data);
              that.ruleForm.name = '';
              this.ruleForm.region = ""
              // console.log(this.tableData, "this.tableData");
            });
            this.$refs[formName].validate((valid) => {
              if (valid) {
               
              }
        });
      
        })
        //失败
        .catch(error => {
          console.log(error);
        });
    },

    handleSubmit() {
      console.log(this.$data.form.name, this.$data.form.region, "=========");
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
            tenantID: index
          };
          console.log(par);
          that.$axios
            .post(
              "/oms-basic/tenant!deleteTenant.json",
              that.$qs.stringify(par)
            )
            .then(res => {
              console.log(res);
              this.$axios
                .post("/oms-basic/tenant!selectTenantBy.json", {
                  // tenantName: "110",
                  // abilityIDs: "112"
                })
                .then(res => {
                  // this.tableData = res.data.data;
                  this.tableData = [].concat(res.data.data);
                  // console.log(this.tableData, "this.tableData");
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
    closeDialogVisible() {
      console.log("closeDialogVisible");
    },
    currentPage1() {
      console.log("currentPage1");
    },
    //搜索
    doFilter() {
      var that = this;
      this.filtertableData = []; //过滤后的数据
      var IDcode = {
        tenantName: this.tableDataName,
        code: this.tableDataValue
      };
      this.$axios
        .post(
          "/oms-basic/tenant!selectTenantBy.json",
          this.$qs.stringify(IDcode)
        )
        .then(res => {
          console.log(res.data.data);
          var subjectY = res.data.data;
          this.tableData = subjectY;
          that.pagination.total = res.data.count;
        })
        .catch(error => {
          console.log(error);
        });
      //页面数据改变重新统计数据数量和当前页
      this.currentPage = 1;
      this.totalItems = this.filtertableData.length;
      //渲染表格,根据值
      this.currentChangePage(this.filtertableData);
      //页面初始化数据需要判断是否检索过
      this.flag = true;
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
      // this.pagination.start = currentPage;
      this.listInit({
        start: currentPage,
        pageSize: this.pagination.pageSize,
        tenantName: this.tableDataName
        // ...this.formData
      });
      // console.log(this.pagination.start)  //点击第几页
    },
    deleteRow1(index) {
      this.tableData.forEach((item, index) => {});

      this.dialogEditgsVisible1 = false;
    },
    edit(index) {
      this.$router.push("/Administration");
      localStorage.setItem("key",index.code);
    },
    /**
     *
     * @param
     */
    editgsForm(index, row) {
      this.form.abilityId = []
      this.dialogEditgsVisible = true;
      console.log(this.editForm.temArr);
      row.temArr = this.editForm.temArr;
      console.log(row, "row");
      (this.title = "编辑"), (this.title1 = "删除");
      this.editForm = row;
      var arr1 = [],
        arr2 = [];
      console.log(row.abilityIDs, "row.abilityIDs"); //111,116,115,114,122,113,112,119 row.abilityIDs
      //arr1 = row.abilityIDs.split(","); //arr1是什么, row.abilityIDs是什么???
      console.log(arr1, "arr1"); //["111", "116", "115", "114", "122", "113", "112", "119"]
      arr2 = row.abilityNames.split(",");
      console.log(arr2, "arr2");
      var arr = [];
      const abiArr = row.abilityIDs.split(",") //arr赋值给editForm.arrayAbility
      for(let i in abiArr){
        abiArr[i] = parseInt(abiArr[i])
      }
      this.form.abilityId = abiArr
      this.editForm.arrayAbility = this.editForm.temArr;
    },

    saveEditForm() {
      var that = this;
      var param = new URLSearchParams();
      param.append("tenantID", this.editForm.tenantID);
      param.append("tenantName", this.editForm.tenantName);
      let abilityId = ''
      const abilityIdArr = [...new Set(this.form.abilityId)]
      if (abilityIdArr.length > 0) {
        abilityId = abilityIdArr.join(",")
      } else {
        abilityId = abilityIdArr[0]
      }
      param.append("abilityIDs", abilityId)
      this.$axios
        .post("/oms-basic/tenant!editTenant.json", param)
        .then(function(response) {
          console.log(response);
          
          if (response.data.code == 10000) {
            that.dialogEditgsVisible = false;
            that.form.abilityId = ''
            that.listInit()
          }
          
        })
        .catch(function(error) {
          console.log(error);
        });

      this.$axios
        .post("/oms-basic/tenant!selectTenantBy.json", {
          // tenantName: "110",
          // abilityIDs: "112"
        })
        .then(res => {
          // this.tableData = res.data.data;
          this.tableData = [].concat(res.data.data);
          this.total = res.data.count;
          // console.log(this.tableData, "this.tableData");
        });
    },
    getData() {},
    getAbilitys() {
      let that = this;
      that.$axios
        .post("/oms-basic/ability!selectAbilityList.json")
        .then(res => {
          that.editForm.temArr = res.data.data;
          console.log(that.editForm.temArr);
        })
        .catch(() => {
          console.log("error");
        });
    },
    listInit(arr) {
      this.$axios
        .post("/oms-basic/tenant!selectTenantBy.json", this.$qs.stringify(arr))
        .then(res => {
          this.tableData = res.data.data;
          this.pagination.total = res.data.count;
        });
    }
  },
  mounted() {
    //调取能力值库 this.editForm.arrayAbility=res;
    this.getAbilitys();
    this.handleSubmit();
    this.getster();
    //发送ajax请求获取数据
    //租户列表table接口
    this.listInit();
  },
  created() {
    this.getData();
  }
};
</script>
<style>
.el-tooltip__popper {
  max-width: 200px !important;
}
</style>
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
.rmdata {
  display: inline-block;
  width: 100%;
  font-size: 16px;
  text-align: center;
}
</style>
