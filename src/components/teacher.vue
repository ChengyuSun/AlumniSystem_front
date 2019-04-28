<template>
  <div class="teacher" style="background-color: #ffeadc">
    <!--<my-top-menu></my-top-menu>-->

    <el-container>

      <el-main>
        <div>
          <div id="listText">
            <span style="font-weight: bold;font-size: 24px">校友信息</span>
            <router-link to="/createTeacher">
              <el-button type="success"
                         size="small" icon="el-icon-circle-plus-outline"
                         style="float: right;background-color: #0000cc">新建校友
              </el-button>
            </router-link>
          </div>

          <div class="line"></div>

          <!--表格数据-->
          <div>
            <el-table :data="tableData.slice((currentPage-1)*pageSize,currentPage*pageSize)"
                      max-height="420" height="420" border fit stripe
                      v-loading="tableLoading"
                      id="myTable">
              <el-table-column type="expand">
                <template slot-scope="props">
                  <el-form label-position="left" inline class="demo-table-expand">
                    <el-form-item label="生日：">
                      <span>{{ props.row.birthday }}</span>
                    </el-form-item>
                    <el-form-item label="入学年份：">
                      <span>{{ props.row.admission }}</span>
                    </el-form-item>
                    <el-form-item label="毕业年份：">
                      <span>{{ props.row.graduation }}</span>
                    </el-form-item>
                    <el-form-item label="工作城市：">
                      <span>{{ props.row.city }}</span>
                    </el-form-item>
                    <el-form-item label="工作地点：">
                      <span>{{ props.row.workplace }}</span>
                    </el-form-item>
                    <el-form-item label="职位：">
                      <span>{{ props.row.job }}</span>
                    </el-form-item>
                    <el-form-item label="电话号码：">
                      <span>{{ props.row.telephone }}</span>
                    </el-form-item>
                    <el-form-item label="微信号：">
                      <span>{{ props.row.wechat }}</span>
                    </el-form-item>
                  </el-form>
                </template>
              </el-table-column>
              <el-table-column prop="name" label="姓名" min-width="200px" align="center"></el-table-column>
              <el-table-column prop="sex" label="性别" min-width="200px" align="center"></el-table-column>
              <el-table-column prop="email" label="电子邮箱" min-width="200px" align="center"></el-table-column>

              <el-table-column width="220px" label="操作" align="center">
                <template slot-scope="scope">
                  <el-button type="primary" size="small" style="background-color: #0000cc"
                             @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
                  <el-button type="danger" size="small"
                             @click="confirmDelete(scope.$index, scope.row)">删除</el-button>
                </template>
              </el-table-column>
            </el-table>

            <div style="margin-top: 20px; ">
              <el-pagination
                :total="totalSize"
                @current-change="currentChange"
                :page-size="pageSize"
                layout="prev, pager, next, jumper">
              </el-pagination>
            </div>
          </div>

          <div id="search">
            <el-form :inline="true" :model="formInline" label-width="100px" style="height:45px;" >
              <el-form-item style="margin-left: 40px">
                <el-input  v-model="formInline.name" placeholder="姓名"></el-input>
              </el-form-item>
              <el-form-item style="width:100px;margin-left: 20px">
                <el-button type="primary" size="small" @click="searchByName(formInline.name)">查找</el-button>
              </el-form-item>
              <el-form-item style="width:100px;float: right">
                <button style="border-radius: 10px;border: transparent;background: red;width: 100px;height: 40px;color: white;font-size: 20px" @click="logoff(id)">退 出</button>
              </el-form-item>
            </el-form>
          </div>

        </div>

      </el-main>
    </el-container>


  </div>
</template>

<script>

  import app from "../App";
  import myTopMenu from "./topMenu"

  export default {
    name: "teacher",
    components: {
      myTopMenu
    },
    data() {
      return {
        openString: '1-1',
        openArray: ['1'],
        search: '',
        tableLoading: true,
        //表格数据
        tableData: [],

        //查找框内数据
        formInline: {
          name: ''
        },

        //当前用户
        onlineUserName: this.$route.params.name,

        //当前正在编辑的Index
        currentIndex: '',

        //分页数据
        pageSize: 6,
        totalSize: 0,
        currentPage: 1,//默认开始页面
      }
    },

    created() {
      this.loadAllTeacher();
    },

    methods: {

      pushData: function (response) {
        let _this = this;
        for (var i = 0; i < response.data.length; i++) {
          var res = response.data[i];
          var d = new Date(res.birthday);
          var date = d.getFullYear() + '-' + (d.getMonth() + 1) + '-' + d.getDate();
          var newItem = {
            id: res.id,
            name: res.name,
            sex: res.sex,
            birthday: date,
            admission: res.admission,
            graduation: res.graduation,
            city: res.city,
            workplace: res.workplace,
            job: res.job,
            telephone: res.telephone,
            email: res.email,
            wechat: res.wechat
          };
          _this.$data.tableData.push(newItem);
        }
      },

      //加载所有校友
      loadAllTeacher: function () {
        let _this = this;
        this.$axios({
          method: 'get',
          url: '/alumni/search?name=',
        }).then(function (response) {
          _this.$data.tableData = [];
          _this.pushData(response);
          _this.$data.totalSize = parseInt(response.data.length);
          _this.$data.tableLoading = false;
        }).catch(function (error) {
          console.log(error.response.data)
        })
      },

      //删除时弹框确定
      confirmDelete: function (index, row) {
        let _this = this;
        this.$confirm('此操作将永久删除该校友, 是否继续?', '删除校友', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          let newIndex = index + ((_this.$data.currentPage - 1) * (_this.$data.pageSize));//分页要换算当前Index
          console.log(newIndex);
          _this.handleDelete(newIndex, row);
        }).catch(() => {
          _this.$message({
            type: 'info',
            message: '已取消删除'
          });
        });
      },

      //删除一个校友账号
      handleDelete: function (index, row) {
        var userId = row.id.toString();
        let _this = this;
        _this.$axios({
          method: 'delete',
          url: '/alumni/' + userId,
          contentType: 'application/json;charset=UTF-8',
        }).then(function (response) {
          console.log(response);
          _this.$message({
            type: 'success',
            message: '删除成功!'
          });
          _this.tableData.splice(index, 1);
        }).catch(function (error) {
          console.log(error.response);
          _this.$message({
            type: 'error',
            message: '删除失败!'
          });
        })
      },

      //编辑一个教师
      handleEdit: function (index, row) {
        let _this = this;
        _this.$router.push({
          path: '/modifyTeacher',
          query: {
            teacher: {
              id: row.id,
              name: row.name,
              sex: row.sex,
              birthday: row.birthday,
              admission: row.admission,
              graduation: row.graduation,
              city: row.city,
              workplace: row.workplace,
              job: row.job,
              telephone: row.telephone,
              email: row.email,
              wechat: row.wechat,
            }
          }
        });
      },

      //根据名字搜索校友
      searchByName: function (name) {
        let _this = this;
        _this.$data.tableLoading = true;
        this.$axios({
          method: 'get',
          url: '/alumni/search?name=' + name,
        }).then(function (response) {
          console.log(response.data);
          _this.$data.tableData = [];
          _this.pushData(response);
          _this.$data.tableLoading = false;
          _this.$data.totalSize = parseInt(response.data.length);
        }).catch(function (error) {
          console.log(error.response.data)
        })
      },

      //换页触发的函数
      currentChange: function (currentPage) {
        this.currentPage = currentPage;
      },

      //退出
      logoff: function () {
        let _this = this;
        // _this.$data.loginLoading=true;
        let loginName = _this.$data.onlineUserName;
        // var loginPassword = _this.$data.loginPassword;
        _this.$axios({
          method: 'get',
          url: '/admin/logout/'+loginName,
        }).then(response => {
          _this.$message({
            message:'退出成功！',
            type:'success'
          })
          _this.$router.push('/');
        }).catch(error=>{
          console.log(error.data)
        })
      }

    }
  }
</script>

<style scoped>
  @import '../public/css/teacher.css';

  .demo-table-expand {
    font-size: 0;
  }
  .demo-table-expand label {
    width: 90px;
    color: #99a9bf;
  }
  .demo-table-expand .el-form-item {
    margin-right: 0;
    margin-bottom: 0;
    width: 50%;
  }
</style>
