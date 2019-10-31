<template>
  <div class="home">
    <div class="titleQ">授权管理</div>
    <div class="search">
      <el-form :inline="true" :model="formInline" class="demo-form-inline">
        <el-form-item label="授权码">
          <el-input v-model="tableDataValue" placeholder="授权码"  clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="doFilter">搜索</el-button>
        </el-form-item>
      </el-form>
    </div>
    <el-table
      class="tabP"
      @click="kala"
      :data="tableData"
      fit
      border
      stripe
      tooltip-effect="dark"
      empty-text="暂无数据"
    >
      <template>
        <!-- v-for="(item, index) in tableLabel" -->
        <el-table-column show-overflow-tooltip prop="code" label="授权码"></el-table-column>
        <el-table-column show-overflow-tooltip prop="tenantID" label="租户ID"></el-table-column>
        <el-table-column show-overflow-tooltip prop="tenantName" label="租户名称"></el-table-column>
        <el-table-column show-overflow-tooltip prop="abilityID" label="授权能力编号"></el-table-column>
        <el-table-column show-overflow-tooltip prop="abilityName" label="授权能力">
          <!-- <template slot-scope="scope">
            <div v-for="(item,index) in scope.row.abilityNames.split(',')" :key="index">{{item}}</div>
          </template>-->
        </el-table-column>
        <el-table-column show-overflow-tooltip prop="loadUrl" label="负载地址"></el-table-column>
        <el-table-column show-overflow-tooltip prop="maxNum" label="最大连接数"></el-table-column>
      </template>
      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-button type="text" @click="editgsForm(scope.$index,scope.row)">
            <i class="icon iconfont icon-bianji" style="font-size:18px; font-weight:bold;"></i>
          </el-button>
          <el-dialog
            class="headers"
            :title="title"
            :visible.sync="dialogEditgsVisible"
            width="30%"
            center
            @close="closeDialogVisible"
          >
            <el-form :model="editForm" :rules="rules" ref="editForm">
              <el-form-item label="能力编号" :label-width="formLabelWidth">
                <el-input readonly v-model="editForm.abilityID" autocomplete="off"></el-input>
              </el-form-item>
              <el-form-item label="租户名称" :label-width="formLabelWidth">
                <el-input readonly v-model="editForm.tenantName" autocomplete="off"></el-input>
              </el-form-item>
              <el-form-item label="授权码" :label-width="formLabelWidth">
                <el-input readonly v-model="editForm.code" autocomplete="off"></el-input>
              </el-form-item>
              <el-form-item label="授权能力" :label-width="formLabelWidth">
                <el-input readonly v-model="editForm.abilityName" autocomplete="off"></el-input>
              </el-form-item>
              <el-form-item label="负载地址" :label-width="formLabelWidth">
                <el-input v-model="editForm.loadUrl" autocomplete="off"></el-input>
              </el-form-item>
              <el-form-item label="最大连接" :label-width="formLabelWidth">
                <el-input v-model="editForm.maxNum" autocomplete="off"></el-input>
              </el-form-item>
            </el-form>

            <div slot="footer" class="dialog-footer">
              <el-button @click="dialogEditgsVisible = false">取 消</el-button>
              <el-button type="primary" @click="saveEditForm()">确 定</el-button>
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
      currentPage: 1, //初始页
      pagesize: 10, //每页的数据
      total: 0,
      tableDataValue: "",
      formInline: {
        user: "",
        name: "",
        region: ""
      },
      addForm: {
        name: "",
        sort: 99
      },
      pagination: {
        start: 1, //从第一页开始
        pageSize:10, //每一页展示6条数据
        total: 0
      }, // 分页配置
      editForm: {
        name: "",
        sort: 99,
        arrayAbility: [],
        temArr: []
      },
      _index: "",
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
      getData1:'',
      title: "",
      souma:'',
      title1: "",
      dialogAddgsVisible: false,
      dialogEditgsVisible: false,
      dialogEditgsVisible1: false,
      formLabelWidth: "120px",
      rules: {
        name: [
          { required: true, message: "请输入名称" },
          { min: 2, max: 10, message: "SSSSSSS", trigger: "blur" }
        ],
        sort: [{ type: "number", message: "11233552", trigger: "blur" }]
      },
      tableData: [],
      tableLabel: [
        { label: "授权码", prop: "code" },
        { label: "租户ID", prop: "tenantID" },
        { label: "租户名称", prop: "tenantName" },
        { label: "授权能力编号", prop: "abilityID" },
        { label: "授权能力", prop: "abilityName" },
        { label: "负载地址", prop: "loadUrl" },
        { label: "最大连接数", prop: "maxNum" }
      ]
    };
  },
  methods: {
    closeDialogVisible() {
      console.log("closeDialogVisible");
    },
    currentPage1() {
      console.log("currentPage1");
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
      this.listInt({
        start: currentPage,
        pageSize: this.pagination.pageSize,
        // ...this.formData
        code:this.getData1
      });
    },
    deleteRow1(index) {
      this.tableData.forEach((item, index) => {});

      this.dialogEditgsVisible1 = false;
    },

    /**
     *
     * @param
     */
    editgsForm(index, row) {
      this.dialogEditgsVisible = true;
      console.log(this.editForm.temArr,'0000');
      row.temArr = this.editForm.temArr;
      console.log(row, "row");

      (this.title = "编辑"), (this.title1 = "删除");
      this.editForm = row;
      console.log(this.editForm,"xxxxxxx")
      var arr = [];
      this.form.TA_Id = row.TA_Id; //arr赋值给editForm.arrayAbility
      this.editForm.arrayAbility = this.editForm.temArr;
      console.log(this.editForm.temArr);
    },

    saveEditForm() {
      var that = this;
      var params1 = {
        TA_Id: parseInt(this.form.TA_Id),
        loadUrl:this.editForm.loadUrl,
        maxNum:parseInt(this.editForm.maxNum),
      };

      this.$axios
        .post("/oms-basic/ability!editTenantAbility.json", this.$qs.stringify(params1))
        .then(res=>{
          if (res.data.code == 10000) {
            that.dialogEditgsVisible = false;
          }
        })
        .catch(function(error) {
          console.log(error);
        });
    },

    //
    sou_suo() {
      var that = this
       that.getData1=localStorage.getItem("key");
      console.log(that.getData1,"手动接收的数据")
      that.getData1=localStorage.getItem("key");
      console.log(that.getData1,"手动接收的数据")
      that.filtertableData = []; //过滤后的数据
      var IDcode = {
        code: that.getData1,
        start:1,
        pageSize:that.pagination.pageSize
      };
      that.listInt(IDcode);
     
    },
    //搜索
    doFilter() {
      var that = this
     
      that.filtertableData = []; //过滤后的数据
      that.tableDataValue
      console.log(that.tableDataValue,"sssssssssssssssss")
      var IDcode = {
        code: that.tableDataValue,
        start:1,
        pageSize:that.pagination.pageSize
      };
      that.listInt(IDcode);
     
    },
    listInt(arr){
      var that = this
      that.$axios.post("/oms-basic/ability!selectByCode.json",
        that.$qs.stringify(arr)
      ).then(res => {
      // this.tableData = res.data.data;
      console.log(res);
      that.tableData = [].concat(res.data.data);
      that.pagination.total = res.data.count;
    });
    },
  },
  mounted() {
    //调取能力值库 this.editForm.arrayAbility=res;
    //发送ajax请求获取数据
    
    this.listInt({
      start:this.pagination.start,
      pageSize:this.pagination.pageSize
    })
    if(localStorage.getItem("key")){
      this.sou_suo()
   }
    
  },
  beforeDestroy(){
    localStorage.removeItem("key");
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
</style>
