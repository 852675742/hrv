<template>
  <el-form :rules="rules" class="login-container" label-position="left"
           label-width="0px" v-loading="loading">
    <h3 class="login_title">系统登录</h3>
    <el-form-item prop="account">
      <el-input type="text" v-model="loginForm.username" auto-complete="off" placeholder="账号"></el-input>
    </el-form-item>
    <el-form-item prop="checkPass">
      <el-input type="password" v-model="loginForm.password" auto-complete="off" placeholder="密码"></el-input>
    </el-form-item>
    <!--
    <el-checkbox class="login_remember" v-model="checked" label-position="left">记住密码</el-checkbox>
    -->
    <div ref="captcha" style="width:310px;"></div>
    <el-form-item style="width: 100%">
      <el-button type="primary" @click.native="submitClick" style="width: 100%">登录</el-button>
    </el-form-item>
  </el-form>
</template>
<script>
  import  '../lib/jigsaw.js';
  export default{
    data(){
      return {
        rules: {
          account: [{required: true, message: '请输入用户名', trigger: 'blur'}],
          checkPass: [{required: true, message: '请输入密码', trigger: 'blur'}]
        },
        checked: true,
        verification : 0,
        loginForm: {
          username: 'admin',
          password: '123'
        },
        loading: false
      }
    },
    methods: {
      submitClick: function () {
        console.log("获取回调1:",this.verification);
        /*
        if(1 != this.verification) {
          this.$message({
            type: 'warn',
            message: '拼图验证失败'
          });
          return;
        }
        */
        var _this = this;
        this.loading = true;
        this.postRequest('/login', {
          username: this.loginForm.username,
          password: this.loginForm.password
        }).then(resp=> {
          _this.loading = false;
          if (resp && resp.status == 200) {
            var data = resp.data;
            _this.$store.commit('login', data.msg);
            var path = _this.$route.query.redirect;
            _this.$router.replace({path: path == '/' || path == undefined ? '/home' : path});
          }
        });
      },
    },
    mounted(){
      jigsaw.init({
        el:this.$refs.captcha,
        onSuccess: function (val) {
          console.log("拼图回调值",val);
          this.verification = 1;
          console.log("拼图回调值1",this.verification);
        },
        onFail: function (val) {
          this.verification = 0;
        },
        onRefresh: function (val) {
          this.verification = 0;
        }
     });
    },
    watch:{
      verification (val,oldVal) {
        console.log("xxxyyzzz",val);
      }
    },
  }
</script>
<style>
  .login-container {
    border-radius: 15px;
    background-clip: padding-box;
    margin: 180px auto;
    width: 350px;
    padding: 35px 35px 15px 35px;
    background: #fff;
    border: 1px solid #eaeaea;
    box-shadow: 0 0 25px #cac6c6;
  }

  .login_title {
    margin: 0px auto 40px auto;
    text-align: center;
    color: #505458;
  }

  .login_remember {
    margin: 0px 0px 35px 0px;
    text-align: left;
  }
</style>
