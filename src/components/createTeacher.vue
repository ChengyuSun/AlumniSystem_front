<template>
  <div class="createTeacher">


    <el-container>
      <el-main>
        <label style="font-size: 20px">新建校友</label>
        <div>
          <div id="information">
            <div style="margin-top: 10px">
              <span class="formSpan">校友姓名：</span>
              <el-input v-model="form.name" class="formInput" auto-complete="off"></el-input>
            </div>
            <div style="margin-top: 20px">
              <span class="formSpan">性别：</span>
              <el-select v-model="form.sex" placeholder="请选择">
                <el-option
                        v-for="item in sexOptions"
                        :key="item"
                        :label="item"
                        :value="item">
                </el-option>
              </el-select>
            </div>
            <div style="margin-top: 20px">
              <span class="formSpan">生日：</span>
              <el-date-picker
                      v-model="form.birthday"
                      type="date"
                      value-format="yyyy-MM-dd"
                      placeholder="选择日期">
              </el-date-picker>
            </div>
            <div style="margin-top: 20px">
              <span class="formSpan">入学时间：</span>
              <el-date-picker
                      v-model="form.admission"
                      type="year"
                      value-format="yyyy"
                      placeholder="选择年">
              </el-date-picker>
            </div>
            <div style="margin-top: 20px">
              <span class="formSpan">毕业时间：</span>
              <el-date-picker
                      v-model="form.graduation"
                      type="year"
                      value-format="yyyy"
                      placeholder="选择年">
              </el-date-picker>
            </div>
            <div style="margin-top: 20px">
              <span class="formSpan">工作城市：</span>
              <el-input v-model="form.city" class="formInput" style="width:220px" auto-complete="off"></el-input>
              <span style="width: 60px;margin-left: 50px">单位：</span>
              <el-input v-model="form.workplace" class="formInput" style="width:220px" auto-complete="off"></el-input>
              <span style="width: 60px;margin-left: 50px">职位：</span>
              <el-input v-model="form.job" class="formInput" style="width:220px" auto-complete="off"></el-input>
            </div>
            <div style="margin-top: 20px">
              <span class="formSpan">电话号码：</span>
              <el-input v-model="form.telephone" class="formInput" auto-complete="off"></el-input>
            </div>
            <div style="margin-top: 20px">
              <span class="formSpan">电子邮箱：</span>
              <el-input v-model="form.email" class="formInput" auto-complete="off"></el-input>
            </div>
            <div style="margin-top: 20px">
              <span class="formSpan">微信：</span>
              <el-input v-model="form.wechat" class="formInput" auto-complete="off"></el-input>
            </div>
          </div>
          <div style="width: 88%;min-width: 700px">
            <div style="width: 300px;margin:40px auto 0 auto;">
              <el-button type="success" @click="checkValue" style="margin-right: 15px">
                创建  <i class="el-icon-circle-check-outline"></i>
              </el-button>
              <router-link to="/teacher">
                <el-button>
                  取消 <i class="el-icon-circle-close-outline"></i>
                </el-button>
              </router-link>
            </div>
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
    name: "createTeacher",
    components:{
      myTopMenu
    },
    data() {
      return {
        openString:'1-1',
        openArray:['1'],
        sexOptions:['男','女'],
        form:{
            name:"",
            sex:"",
            birthday:"",
            admission:"",
            graduation:"",
            city:"",
            workplace:"",
            job:"",
            telephone:"",
            email:"",
            wechat:"",
        }

      }
    },

    methods: {

      checkValue(){
        let _this=this;
        var form = _this.$data.form;
          if(form.name.toString()===""){
              _this.$alert('姓名不能为空!', '注意', {
                  confirmButtonText: '确定',
                  callback: action => {}
              });
              return;
          }
          if(form.sex.toString()===""){
              _this.$alert('请选择性别!', '注意', {
                  confirmButtonText: '确定',
                  callback: action => {}
              });
              return;
          }
          if(form.birthday.toString()===""){
              _this.$alert('请选择生日!', '注意', {
                  confirmButtonText: '确定',
                  callback: action => {}
              });
              return;
          }
          var reg=new RegExp(/^([a-zA-Z0-9._-])+@([a-zA-Z0-9_-])+(\.[a-zA-Z0-9_-])+/);
          if(!reg.test(form.email)){
              _this.alertMes("请输入正确的邮箱格式！");
              return false;
          }
        _this.$confirm('确定要创建 '+form.name+' 的账号吗?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'info'
        }).then(() => {
          _this.confirmCreate();
        }).catch(() => {
          _this.$message({
            type: 'info',
            message: '已取消创建'
          });
        });

      },

      confirmCreate:function () {
        let _this=this;
        var temp = _this.$data.form;
        temp.admission = parseInt(temp.admission);
        temp.graduation = parseInt(temp.graduation);
          _this.$axios({
          method:'post',
          url:'/alumni',
          header:{
            contentType:'application/json'
          },
          data:temp
        }).then(function (response) {
          _this.$alert('您成功创建 '+temp.name+' 的账号', '操作成功', {
            confirmButtonText: '确定',
            callback: action => {}
          });
        }).catch(function (error) {
          console.log(error.response);
          _this.$message({
            type: 'error',
            message: '创建失败,已存在该校友'
          });
        })
      }
    }
  }
</script>

<style scoped>
  @import '../public/css/createTeacher.css';
</style>
