<template>
  <div class="login-wrap">
    <el-form
      class="login-form"
      label-position="top"
      label-width="80px"
      :model="formdata"
    >
      <h2>用户登录</h2>
      <el-form-item label="用户名">
        <el-input v-model="formdata.username"></el-input>
      </el-form-item>
      <el-form-item label="密码">
        <el-input type="password" v-model="formdata.password"></el-input>
      </el-form-item>
      <el-form-item label="验证🐎">
        <div style="display: flex">
          <el-input v-model="formdata.vcode"></el-input>
          <img :src="vcodeUrl" @click="reload()" alt="" style="margin-right:10px;margin-left: 30px">
        </div>
      </el-form-item>
      <el-button class="login-btn" type="primary" @click="handleLodin()">登录</el-button>
      <p style="font-size:10px;font-weight: lighter" @click="turnPage('/register')">没有账号？点击这里注册</p>
    </el-form>
  </div>
</template>
<script>
export default {
  name: "Login",
  data() {
    return {
      vcodeUrl: 'http://localhost:8081/vcode',
      count: 0,
      formdata: {
        username: "",
        password: "",
        vcode: '',
      },
    };
  },
  created() {
    this.reload();
  },
  methods:{
    reload() {
      let date = new Date().getSeconds();
      window.localStorage.setItem("date", date);
      this.vcodeUrl =  'http://localhost:8081/vcode/' + date + '?' + this.count
      this.count++;
    },
    turnPage(page) {
      this.$router.push(page);
    },
    handleLodin(){
      if(this.formdata.username !== '' && this.formdata.password !== ''){
        this.$axios.post('http://localhost:8081/users/login', {
            username:this.formdata.username,
            password:this.formdata.password,
            vcode: this.formdata.vcode,
            date: window.localStorage.getItem('date')
        }).then((response) => {
          console.log(response)
          if (response.data.state === '200') {
            window.localStorage.setItem("token", response.data.token);
            window.localStorage.setItem("uid", response.data.uid);
            window.localStorage.setItem("username", response.data.username);
            window.localStorage.setItem("role", response.data.role);
            this.$router.push({path:'/first'})
            this.$message({
              showClose: true,
              message: '登录成功',
              type: 'success'
            });
          } else {
            this.$message({
              showClose: true,
              message: response.data.message,
              type: 'error'
            });
          }
        })
      }else {
        this.$message({
          showClose: true,
          message: '用户名和密码不能为空',
          type: 'error'
        });
      }
    }
  }
};

</script>
<style scoped>
.login-wrap {
  height: 100vh;
  background-color: #99a9bf;
  display: flex;
  justify-content: center;
  align-items: center;
}
.login-form {
  width: 400px;
  background: #fff;
  border-radius: 5px;
  padding: 30px;
}
.login-btn {
  width: 100%;
}
</style>
