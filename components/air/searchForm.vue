<template>
  <div class="search-form">
    <!-- 头部tab切换 -->
    <el-row type="flex" class="search-tab">
      <span @click="changTag(index)" v-for="(item, index) in tabs" :key="index" :class="{active: index === currentTab}">
        <i :class="item.icon" />
        {{ item.name }}
      </span>
    </el-row>

    <el-form ref="form" :model="form" class="search-form-content" label-width="80px">
      <el-form-item label="出发城市">
        <el-autocomplete
          :fetch-suggestions="getDepartList"
          :trigger-on-focus="false"
          :highlight-first-item="true"
          v-model="form.departCity"
          @select="selectdepartCity"
          placeholder="请搜索出发城市"
          class="el-autocomplete"
        />
      </el-form-item>

      <el-form-item label="到达城市">
        <el-autocomplete
          :fetch-suggestions="getDestCity"
          :trigger-on-focus="false"
          :highlight-first-item="true"
          @select="selectdestCity"
          v-model="form.destCity"
          placeholder="请搜索到达城市"
          class="el-autocomplete"
        />
      </el-form-item>

      <el-form-item label="出发时间">
        <!-- change 用户确认选择日期时触发 -->
        <el-date-picker
          v-model="form.departDate"
          @change="changeDate"
          type="date"
          placeholder="请选择日期"
          style="width: 100%;"
        />
      </el-form-item>

      <el-form-item label>
        <el-button @click="handleSubmit" style="width:100%;" type="primary" icon="el-icon-search">
          搜索
        </el-button>
      </el-form-item>
      <div class="reverse">
        <span @click="reverseCity">换</span>
      </div>
    </el-form>
  </div>
</template>

<script>
import moment from 'moment'
export default {
  data () {
    return {
      tabs: [
        { icon: 'iconfont icondancheng', name: '单程' },
        { icon: 'iconfont iconshuangxiang', name: '往返' }
      ],
      currentTab: 0,
      form: {
        departCity: '', // 出发城市
        departCode: '', // 出发城市代码
        destCity: '', // 到达城市
        destCode: '', // 到达城市代码
        departDate: '' // 日期字符串
      }
    }
  },
  methods: {
    handleSubmit () {
      this.$router.push({
        path: '/air/flights',
        query: this.form
      })
    },
    // 获取出发地数据
    async getDepartList (value, showList) {
      const cityList = await this.searchCity(value)
      if (!cityList || cityList.length === 0) {
        this.$message.error('暂不支持该城市')
      }
      this.departCode = cityList[0].sort
      showList(cityList)
    },
    // 获取到达地数据
    async getDestCity (value, showList) {
      const cityList = await this.searchCity(value)
      this.destCode = cityList[0].sort
      showList(cityList)
    },
    // 获取出发地代码Code
    selectdepartCity (item) {
      this.form.departCode = item.sort
    },
    // 获取到达地代码Code
    selectdestCity (item) {
      this.form.destCode = item.sort
    },
    // 修改时间格式
    changeDate (value) {
      this.form.departDate = moment(this.form.departDate).format('YYYY-MM-DD')
    },
    // 封装获取数据的函数
    searchCity (value) {
      return this.$axios({
        url: '/airs/city',
        params: {
          name: value
        }
      }).then((res) => {
        const { data } = res.data
        const citys = data.map((element) => {
          return {
            ...element,
            value: element.name.replace(/市$/, '')
          }
        })
        // const cityList = citys.filter((element) => {
        //   return element.sort
        // })
        // 进阶
        const cityList = citys.filter(element => element.sort)

        return cityList
      })
    },
    // 调换出发地和到达地
    reverseCity () {
      const { departCity, departCode, destCity, destCode } = this.form
      this.form.departCity = destCity
      this.form.destCity = departCity
      this.form.departCode = destCode
      this.form.destCode = departCode
    },
    changTag (index) {
      if (index === 1) {
        this.$confirm('目前暂不支持往返，请使用单程选票！', '提示', {
          confirmButtonText: '确定',
          showCancelButton: false,
          type: 'warning'
        })
      }
    }
  }
}
</script>

<style scoped lang="less">
.search-form {
  border: 1px #ddd solid;
  border-top: none;
  width: 360px;
  height: 350px;
  box-sizing: border-box;
}

.search-tab {
  span {
    display: block;
    flex: 1;
    text-align: center;
    height: 48px;
    line-height: 42px;
    box-sizing: border-box;
    border-top: 3px #eee solid;
    background: #eee;
  }

  .active {
    border-top-color: orange;
    background: #fff;
  }

  i {
    margin-right: 5px;
    font-size: 18px;

    &:first-child {
      font-size: 16px;
    }
  }
}

.search-form-content {
  padding: 15px 50px 15px 15px;
  position: relative;

  .el-autocomplete {
    width: 100%;
  }
}

.reverse {
  position: absolute;
  top: 35px;
  right: 15px;

  &:after,
  &:before {
    display: block;
    content: "";
    position: absolute;
    left: -35px;
    width: 25px;
    height: 1px;
    background: #ccc;
  }

  &:after {
    top: 0;
  }

  &:before {
    top: 60px;
  }

  span {
    display: block;
    position: absolute;
    top: 20px;
    right: 0;
    font-size: 12px;
    background: #999;
    color: #fff;
    width: 20px;
    height: 20px;
    line-height: 18px;
    text-align: center;
    border-radius: 2px;
    cursor: pointer;

    &:after,
    &:before {
      display: block;
      content: "";
      position: absolute;
      left: 10px;
      width: 1px;
      height: 20px;
      background: #ccc;
    }

    &:after {
      top: -20px;
    }

    &:before {
      top: 20px;
    }
  }
}
</style>
