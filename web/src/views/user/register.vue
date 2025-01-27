<template>
  <div class="register-container">
    <el-form
      ref="registerForm"
      :model="registerForm"
      :rules="registerRules"
      class="register-form"
      label-width="150px"
    >
      <div class="register-error">{{ this.error }}</div>
      <el-form-item label="邮箱" prop="email">
        <el-input v-model="registerForm.email"></el-input>
      </el-form-item>
      <el-form-item label="密码" prop="password">
        <el-input v-model="registerForm.password" type="password"></el-input>
      </el-form-item>
      <el-form-item label="确认密码" prop="comparePassword">
        <el-input v-model="registerForm.comparePassword" type="password"></el-input>
      </el-form-item>
      <div class="register-button">
        <el-button :loading="loading" native-type="submit" type="primary" @click.prevent="register">注册</el-button>
      </div>
      <div class="register-info">如果已注册账号请点击
        <router-link :to="{name: 'login'}">登录</router-link>
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
      registerForm: {
        email: '',
        password: '',
        comparePassword: ''
      },
      registerRules: {
        email: { type: 'email', required: true, message: '请输入有效的邮箱地址', trigger: 'blur' },
        password: { type: 'string', required: true, message: '密码不能为空', trigger: 'blur' },
        comparePassword: {
          type: 'string',
          required: true,
          trigger: 'blur',
          validator: (rule, value, callback) => {
            if (value === '') {
              callback(new Error('请再次输入密码'))
            } else if (value !== this.registerForm.password) {
              callback(new Error('两次输入的密码不一致'))
            } else {
              callback()
            }
          }
        }
      }
    }
  },
  methods: {
    register () {
      this.$refs['registerForm'].validate(async (valid) => {
        if (valid) {
          this.loading = true
          this.error = ''
          try {
            // 发送表单请求，用户注册
            const response = await UserService.register(
              {
                email: this.registerForm.email,
                password: this.registerForm.password,
                ratings: ''
              }
            )
            if (response.data.code !== 200) {
              this.error = response.data.error
            } else {
              // 将用户信息和token保存到 vuex
              this.$store.dispatch('setToken', response.data.token)
              this.$store.dispatch('setUser', response.data.user)
              this.$router.push('/')
            }
            this.loading = false
          } catch (error) {
            if (error.response.data.error) {
              this.error = error.response.data.error
            } else {
              this.error = '注册失败，请稍后重试'
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
.register-container {
  position: absolute;
  top: 0;
  bottom: 0;
  width: 100%;
  background: #2d3a4b;

  .register-form {
    position: relative;
    width: 430px;
    margin: 120px auto 0;
    background: #fff;
    padding: 20px 0;
    padding-right: 30px;
    border-radius: 15px;

    .register-button {
      display: flex;
      justify-content: center;
    }

    .register-info {
      text-align: right;
      font-size: 0.9rem;
      margin-top: 10px;
      color: #909399;
    }

    .register-error {
      color: #f56c6c;
      text-align: center;
      padding: 0 0 5px 5px;
    }
  }
}
</style>
