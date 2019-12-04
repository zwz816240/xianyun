<template>
  <el-form ref="form" :model="form" :rules="rules" class="form">
    <el-form-item class="form-item" prop="username">
      <el-input v-model="form.username" placeholder="用户名/手机" />
    </el-form-item>

    <el-form-item class="form-item" prop="password">
      <el-input v-model="form.password" placeholder="密码" type="password" />
    </el-form-item>

    <p class="form-text">
      <nuxt-link to="#">
        忘记密码
      </nuxt-link>
    </p>

    <el-button @click="handleLoginSubmit" class="submit" type="primary">
      登录
    </el-button>
  </el-form>
</template>

<script>
export default {
  data () {
    return {
      form: {
        username: '13800138000',
        password: '123456'
      },
      rules: {
        username: [
          {
            required: true,
            message: '请输入用户名',
            trigger: 'blur'
          }
        ],
        password: [
          {
            required: true,
            message: '请输入密码',
            trigger: 'blur'
          }
        ]
      }
    }
  },
  methods: {
    // 提交登录
    handleLoginSubmit () {
      // 验证表单
      this.$refs.form.validate((valid) => {
        // 为true表示没有错误
        if (valid) {
          // 新增代码
          this.$store.dispatch('user/login', this.form).then((res) => {
            this.$message.success('登录成功，正在跳转')
            // 跳转到首页
            setTimeout(() => {
              this.$store.commit('user/setUserInfo', res)
              this.$router.replace('/')
            }, 1000)
          })
        }
      })
    }
  }
}
</script>

<style scoped lang="less">
.form {
  padding: 25px;
}

.form-item {
  margin-bottom: 20px;
}

.form-text {
  font-size: 12px;
  color: #409eff;
  text-align: right;
  line-height: 1;
}

.submit {
  width: 100%;
  margin-top: 10px;
}
</style>
