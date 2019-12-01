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
        username: '',
        password: ''
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
    handleLoginSubmit () {
      // 验证表单
      this.$refs.form.validate(async (valid) => {
        // 验证表单通过时发送请求
        if (valid) {
          const res = await this.$axios({
            url: '/accounts/login',
            method: 'POST',
            data: this.form
          })
          // eslint-disable-next-line no-console
          console.log(res.data)
        } else {
          // 验证不通过
          // 给提示
          this.$message.error('用户数据输入不合法')
          // 中止本次请求
          return false
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
