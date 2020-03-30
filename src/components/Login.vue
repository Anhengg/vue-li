<template>
    <div class="login_container">
        <div class="login_box">
            <div class="avatar_box">
            <img src="../assets/logo.png" >
            </div>
            <el-form :model="loginForm" label-width="0px" class="login_form" :rules="rules" ref="loginFormRef">
           <el-form-item prop="username" >
           <el-input prefix-icon="iconfont icon-user" v-model="loginForm.username" ></el-input>
           </el-form-item>
             <el-form-item prop="password">
           <el-input prefix-icon="iconfont icon-3702mima" v-model="loginForm.password" type="password"></el-input>
           </el-form-item>

             <el-form-item class="btns">
                 <el-button type="primary" @click="validataForm">登录</el-button>
            <el-button type="info" @click="resetLoginForm">重置</el-button>
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
      rules: {
        username: [
          { required: true, message: '请输入用户名', trigger: 'blur' },
          { min: 3, max: 7, message: '长度在 2 到 7 个字符', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          { min: 3, max: 7, message: '长度在 2 到 7 个字符', trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    resetLoginForm () {
      this.$refs.loginFormRef.resetFields()
    },
    validataForm () {
      this.$refs.loginFormRef.validate(async valida => {
        if (!valida) return false
        const { data: result } = await this.$http.post('login', this.loginForm)
        if (result.meta.status !== 200) return this.$message.error('登陆失败')
        this.$message.success('登陆成功')
        window.sessionStorage.setItem('token', result.data.token)
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
    width: 450px;
    height: 300px;
    background-color: #fff;
    border-radius: 3px;
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%,-50%);

    .avatar_box{
height: 130px;
width: 130px;
position: absolute;
left: 50%;
transform: translate(-50%, -50%);
background-color: #fff;
img{
    height: 100%;
    width: 100%;
    border-radius: 50%;
    background-color: #eee;
}
padding: 10px;
border: 1px solid #eee;
border-radius: 50%;
box-shadow: 0 0 10px #ddd;
    }
}

.login_form{
    position: absolute;
    bottom: 0;
    width: 100%;
    padding: 0 20px;
    box-sizing: border-box;
}

.btns{
    display: flex;
    justify-content: flex-end;
}
</style>
