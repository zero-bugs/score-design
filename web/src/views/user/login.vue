<template>
  <div class="login-container">
    <el-form
      ref="loginForm"
      :model="loginForm"
      :rules="loginRules"
      class="login-form"
      label-width="150px"
    >
      <div class="login-error">{{ this.error }}</div>
      <el-form-item label="邮箱" prop="email">
        <el-input v-model="loginForm.email"></el-input>
      </el-form-item>
      <el-form-item label="密码" prop="password">
        <el-input v-model="loginForm.password" type="password"></el-input>
      </el-form-item>
      <div class="login-button">
        <el-button :loading="loading" native-type="submit" type="primary" @click="login">登录</el-button>
      </div>
      <div class="login-info">如果没有册账号请点击
        <router-link :to="{name: 'register'}">注册</router-link>
      </div>
    </el-form>
  </div>
</template>

<script>
import UserService from '../../services/UserService'

export default {
  data () {
    return {
      loading: false,
      error: '',
      loginForm: {
        email: '',
        password: ''
      },
      loginRules: {
        email: { type: 'email', required: true, message: '请输入有效的邮箱地址', trigger: 'blur' },
        password: { type: 'string', required: true, message: '密码不能为空', trigger: 'blur' }
      }
    }
  },
  methods: {
    login () {
      this.$refs['loginForm'].validate(async (valid) => {
        if (valid) {
          this.loading = true
          this.error = ''
          try {
            const response = await UserService.login(
              {
                email: this.loginForm.email,
                password: this.loginForm.password
              }
            )
            if (response.data.code !== 200) {
              this.error = response.data.error
            } else {
              this.$store.dispatch('setToken', response.data.token)
              this.$store.dispatch('setUser', response.data.user)
              if (this.$route.query.redirect) {
                this.$router.push(this.$route.query.redirect)
              } else {
                this.$router.push('/')
              }
            }
            this.loading = false
          } catch (error) {
            if (error.response.data.error) {
              this.error = error.response.data.error
            } else {
              this.error = '登录失败，请重新登陆'
            }
            this.loading = false
          }
        }
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.login-container {
  position: absolute;
  top: 0;
  bottom: 0;
  width: 100%;
  background: #2d3a4b;

  .login-form {
    position: relative;
    width: 430px;
    margin: 120px auto 0;
    background: #fff;
    padding: 20px 0;
    padding-right: 30px;
    border-radius: 15px;

    .login-button {
      display: flex;
      justify-content: center;
    }

    .login-info {
      text-align: right;
      font-size: 0.9rem;
      margin-top: 10px;
      color: #909399;
    }

    .login-error {
      color: #F56C6C;
      text-align: center;
      padding: 0 0 5px 5px;
    }
  }
}
</style>
