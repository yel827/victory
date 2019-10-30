<template>
  <div class="home">
    <div class="titleQ">日志详情</div>
    <div class="search">
      <el-form :inline="true" :model="formInline" class="demo-form-inline">
        

        

        
       
        
      </el-form>
    </div>
    
  </div>
</template>

 <script>
import echarts from "echarts";
export default {
  data() {
    return {
     
     
    
    };
  },
  methods: {
  

    getDa() {
      var vaiod = {vaiod:this.$route.params.logID}
      // this.$axios
      //   .post("/oms-basic/abilityLog!selectLog.json", {
      //     level:'移动'
      //   })
      //   .then(res => {
      //     this.tableData = res.data.data;
      //     this.total = res.data.count;
      //     console.log(res.data.data);
      //     console.log(res,'.data.data');
      //   });
     console.log(vaiod,'params')
    },
  
    
  },
  mounted() {
    this.getDa()
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
