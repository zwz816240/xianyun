<template>
  <el-form ref="form" :model="form" :rules="rules" class="form">
    <el-form-item class="form-item" prop="username">
      <el-input v-model="form.username" placeholder="用户名手机" />
    </el-form-item>

    <el-form-item class="form-item" prop="captcha">
      <el-input v-model="form.captcha" placeholder="验证码">
        <template slot="append">
          <el-button @click="handleSendCaptcha">
            发送验证码
          </el-button>
        </template>
      </el-input>
    </el-form-item>

    <el-form-item class="form-item" prop="nickname">
      <el-input v-model="form.nickname" placeholder="你的名字" />
    </el-form-item>

    <el-form-item class="form-item" prop="password">
      <el-input v-model="form.password" placeholder="密码" type="password" />
    </el-form-item>

    <el-form-item class="form-item" prop="checkPassword">
      <el-input v-model="form.checkPassword" placeholder="确认密码" type="password" />
    </el-form-item>

    <el-button @click="handleRegSubmit" class="submit" type="primary">
      注册
    </el-button>
  </el-form>
</template>

<script>
export default {
  data () {
    // 确认密码
    const validatePass = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('请再次输入密码'))
      } else if (value !== this.form.password) {
        callback(new Error('两次输入密码不一致!'))
      } else {
        callback()
      }
    }
    return {
      // 表单数据
      form: {
        username: '', // 登录用户名/手机
        password: '', // 登录密码
        checkPassword: '', // 确认密码
        nickname: '', // 昵称
        captcha: ''// 手机验证码
      },
      // 表单规则
      rules: {
        username: [{
          required: true,
          message: '请输入用户名',
          trigger: 'blur'
        }],
        password: [{
          required: true,
          message: '请输入密码',
          trigger: 'blur'
        }],
        checkPassword: [{
          validator: validatePass,
          trigger: 'blur'
        }],
        nickname: [{
          required: true,
          message: '请输入昵称',
          trigger: 'blur'
        }],
        captcha: [{
          required: true,
          message: '请输入验证码',
          trigger: 'blur'
        }] }
    }
  },
  methods: {
    // 发送验证码
    handleSendCaptcha () {
      if (!this.form.username) {
        this.$confirm('手机号码不能为空', '提示', {
          confirmButtonText: '确定',
          showCancelButton: false,
          type: 'warning'
        })
        return
      }

      if (!/^1\d{10}$/.test(this.form.username)) {
        this.$confirm('手机号码格式错误', '提示', {
          confirmButtonText: '确定',
          showCancelButton: false,
          type: 'warning'
        })
        return
      }

      this.$axios({
        url: `captchas`,
        method: 'POST',
        data: {
          tel: this.form.username
        }
      }).then((res) => {
        this.$confirm(`模拟手机验证码为：${res.data.code}`, '提示', {
          confirmButtonText: '确定',
          showCancelButton: false,
          type: 'warning'
        })
      })
    },

    // 注册
    handleRegSubmit () {
      this.$refs.form.validate((valid) => {
        if (valid) {
          // 注册提交
          const { checkPassword, ...props } = this.form
          this.$store.dispatch('user/register', props).then((res) => {
            this.$message.success('注册成功，正在跳转')
            setTimeout(() => {
              this.form = {}
              this.$emit('changcurrentTab')
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
