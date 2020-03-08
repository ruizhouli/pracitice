<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>员工管理</el-breadcrumb-item>
      <el-breadcrumb-item>员工列表</el-breadcrumb-item>
    </el-breadcrumb>

    <el-card class="box-card">
      <div class="search">
        <!-- 取消选择/删除 -->
        <div class="search-left">
          <el-row>
            <el-button type="primary" @click="toggleSelection()">取消选择</el-button>
            <el-button type="primary" icon="el-icon-delete" @click="open">删除</el-button>
          </el-row>
        </div>
        <div class="search-right">
          <!-- 下拉筛选 -->
          <el-select
              v-model="value"
              size="small"
              placeholder="全部"
              @change='changeSelect'
            >
            <el-option
              v-for="item in userSelect"
              :key="item.id"
              :label="item.name"
              :value="item.id"
            ></el-option>
          </el-select>
          <!-- 搜索 -->
          <el-input
              v-model="search"
              @change="searchFn"
              size="small"
              placeholder="请输入搜索内容"
              autofocus
          ></el-input>
        </div>
      </div>
      <!-- 当el-table元素中注入data对象数组后，在el-table-column中用prop属性来对应对象中的键名即可填入数据 -->
      <el-table
        ref="multipleTable"
        :data="tableData"
        stripe
        border
        @selection-change="handleSelectionChange"
        class="tableList"
      >
        <el-table-column type="selection" width="55"></el-table-column>
        <el-table-column prop="name" label="姓名" width="80">
          <!-- <template  slot-scope="scope">
                {{scope.row.name==='周啸天' ? '周晓天' : scope.row.name}}
          </template>-->
        </el-table-column>
        <el-table-column prop="sex" label="性别" width="50">
          <template slot-scope="scope">{{scope.row.sex *1 ? '女' : '男'}}</template>
        </el-table-column>
        <el-table-column prop="department" label="部门" width="100"></el-table-column>
        <el-table-column prop="job" label="职务" width="100"></el-table-column>
        <el-table-column prop="email" label="邮箱" width="180" show-overflow-tooltip></el-table-column>
        <el-table-column prop="phone" label="电话" width="120"></el-table-column>
        <el-table-column prop="desc" label="描述" width="160" show-overflow-tooltip></el-table-column>
        <el-table-column label="操作"></el-table-column>
      </el-table>
    </el-card>
  </div>
</template>

<script>
import {
  userListAPI,
  userSelectAPI,
  userDeleteAPI,
  allListAPI,
  userInfoAPI,
  changeUserAPI
} from "../api/api";
export default {
  data() {
    return {
      tableData: [],
      selectionChange: [],
      userSelect:[],
      searchId:1,
      search:'',
      options: [
        {
          value: "选项1",
          label: "全部"
        },
        {
          value: "选项2",
          label: "总裁办"
        },
        {
          value: "选项3",
          label: "销售部"
        },
        {
          value: "选项4",
          label: "后勤部"
        }
      ],
      value: ""
    };
  },
  created() {
    // 获取员工列表
    this.getList();
    // 下拉筛选
    this.userSelectFn();
  },
  methods: {
    //获取员工列表
    async getList(departmentId, search) {
      const data = await userListAPI(departmentId, search);
      if (data.code === 0) {
        this.tableData = data.data;
      } else {
        this.$message.error("获取失败");
      }
    },
    //员工列表选中项
    handleSelectionChange(val) {
      this.selectionChange = val;
    },
    //取消选择按钮
    toggleSelection(rows) {
      if (rows) {
        rows.forEach(row => {
          this.$refs.multipleTable.toggleRowSelection(row);
        });
      } else {
        this.$refs.multipleTable.clearSelection();
      }
    },
    //员工列表删除,因为后台接口只能请求一次删除一条信息,所以要使用Promise.all等待所有的信息删除完成才可以
    open() {
      if (this.selectionChange.length) {
        this.$confirm("此操作将永久删除该文件,是否继续?", "提示", {
          confirmButtonText: "确定",
          cancelButtonText: "取消",
          type: "warning"
        })
          .then(() => {
            let promiseAllAry = [];
            this.selectionChange.forEach(item => {
              promiseAllAry.push(userDeleteAPI(item.id));
            });
            Promise.all(promiseAllAry)
              .then(ary => {
                this.$message.success("删除成功!");
                this.getList();
              })
              .catch(() => {
                this.$message.info("删除失败");
              });
          })
          .catch(() => {
            this.$message.info("已取消删除");
          });
      } else {
        this.$message.info("请选择删除项!");
      }
    },

    //请求下拉筛选数据
    async userSelectFn(){
      const data = await userSelectAPI();
      if(data.code === 0){
        // data.data.unshift({id:0,name:'全部'});
        this.userSelect = data.data;
      }else{
        this.$message.error('查询失败');
      }
    },

    // select下拉筛选
    changeSelect(id){
      this.searchId = id;
      this.getList(this.searchId);
    },

    // input输入搜索
    searchFn(val){
      this.getList(this.searchId,this.search);
    }

  },//mothods括号




};
</script>

<style scoped>
.text {
  font-size: 14px;
}

.item {
  margin-bottom: 18px;
}

.clearfix:before,
.clearfix:after {
  display: table;
  content: "";
}
.clearfix:after {
  clear: both;
}

.box-card {
  margin-top: 20px;
}
.text {
  font-size: 14px;
}

.item {
  margin-bottom: 18px;
}

.clearfix:before,
.clearfix:after {
  display: table;
  content: "";
}
.clearfix:after {
  clear: both;
}

.box-card {
  width: 100%;
}
/* 需要加载vue-loader @12版本以上 */
.tableList >>> .cell {
  text-align: center;
}
.search {
  display: flex;
  justify-content: space-between;
  /* margin-bottom: 5px; */
}

.search-right {
  width: 60%;
  display: flex;
  justify-content: flex-end;
  align-items: flex-end;
  margin-bottom: 10px;
}
.el-button {
  margin-bottom: 10px;
}
.el-select {
  width: 20%;
}
.search .el-input {
  margin-left: 10px;
  width: 40%;
}
</style>
<style>
.el-main {
  line-height: 0;
  text-align: left;
}
/* .has-gutter .cell {
  text-align: center;
} */
.el-row {
  margin-top: 10px;
}
.el-button {
  padding: 5px 10px;
}
</style>