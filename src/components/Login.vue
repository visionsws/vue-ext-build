<template>
  <div class="login_container">
    <div class="login_box">
      <div class="avatar_box">
        <img src="../assets/avatar_pic2.jpeg">
      </div>
      <el-form class="login_form" ref="loginFormRef" :model="loginForm" :rules="loginFormRules">
        <el-form-item prop="username">
          <el-input v-model="loginForm.username" prefix-icon="iconfont iconuser-filling"></el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input v-model="loginForm.password" type="password" prefix-icon="iconfont iconPassword" ></el-input>
        </el-form-item>
        <el-form-item class="btns">
          <el-button type="primary" @click="login">登录</el-button>
          <el-button type="info" @click="loginFormReset" >重置</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
  export default {
    data () {
      return {
        loginForm: {
          username: 'admin',
          password: '123456'
        },
        // 表单规则验证
        loginFormRules: {
          username: [
            { required: true, message: '请输入登录名称', trigger: 'blur' },
            { min: 3, max: 10, message: '长度在 3 到 10 个字符', trigger: 'blur' }
          ],
          password: [
            { required: true, message: '请输入密码名称', trigger: 'blur' },
            { min: 6, max: 15, message: '长度在 6 到 15 个字符', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      // 重置表单
      loginFormReset () {
        this.$refs.loginFormRef.resetFields()
      },
      login() {
        this.$refs.loginFormRef.validate(async valid => {
          if (!valid) return
          const { data: res } = await this.$http.post('/user/login', this.loginForm)
          if (res.code !== 200) {
            return this.$message.error('登录失败')
          }
          this.$message.success('登录成功')
          console.log(res)
          window.sessionStorage.setItem('token', res.content.userId)
          this.$router.push('/home')
        })
      }
    }

  }
</script>

<style lang="less" scoped>
  .login_container{
    background-color: #2b4b6b;
    height: 100%;
  }
  .login_box{
    height: 300px;
    width: 450px;
    background-color: aliceblue;
    border-radius: 10px;
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%,-50%);
  }
  .avatar_box{
    height: 100px;
    width: 100px;
    border: 1px solid #ddd;
    border-radius: 50%;
    padding: 10px;
    box-shadow: 0 0 10px #aaa;
    position: absolute;
    left: 50%;
    transform: translate(-50%,-50%);
    background-color: aliceblue;
    img{
      height: 100%;
      width: 100%;
      border-radius: 50%;
      background-color: #eee;
    }
  }
  .login_form{
    position: absolute;
    bottom: 0;
    width: 100%;
    padding: 20px;
    box-sizing: border-box;
  }
  .btns{
    display: flex;
    justify-content: flex-end;
  }

</style>
