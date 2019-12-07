<template>
  <section class="contianer">
    <el-row type="flex" justify="space-between">
      <!-- 顶部过滤列表 -->
      <div class="flights-content">
        <!-- 过滤条件 -->
        <div />

        <!-- 航班头部布局 -->
        <FlightsListHead />

        <!-- 航班信息 -->
        <div>
          <FlightsItem v-for="(item,index) in dataList" :key="index" :flight="item" />
          <el-pagination
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :page-sizes="[2,4,6,8]"
            :page-size="pageSize"
            :total="flightsData.flights.length"
            v-if="dataList.length>0"
            layout="total, sizes, prev, pager, next, jumper"
          />
          <div v-else-if="!loading">
            本页暂无数据
          </div>
        </div>
      </div>

      <!-- 侧边栏 -->
      <div class="aside">
        <!-- 侧边栏组件 -->
      </div>
    </el-row>
  </section>
</template>

<script>

import FlightsListHead from '@/components/air/flightsListHead.vue'
import FlightsItem from '@/components/air/flightsItem.vue'

export default {
  components: {
    FlightsListHead,
    FlightsItem
  },
  data () {
    return {
      loading: true,
      flightsData: {
        flights: []
      }, // 航班总数据
      // dataList: [], // 航班列表数据，用于循环flightsItem组件，单独出来是因为要分页
      pageIndex: 1,
      pageSize: 2
    }
  },
  computed: {
    dataList () {
      const start = (this.pageIndex - 1) * this.pageSize
      const end = start + this.pageSize
      // 获取所有航班列表数据
      return this.flightsData.flights.slice(start, end)
    }
  },
  mounted () {
    this.$axios({
      url: '/airs',
      params: this.$route.query
    }).then((res) => {
      this.flightsData = res.data
      this.loadPage()
      this.loading = false
    })
  },
  methods: {
    loadPage () {
      const start = (this.pageIndex - 1) * this.pageSize
      const end = start + this.pageSize
      // 获取所有航班列表数据
      this.dataList = this.flightsData.flights.slice(start, end)
    },
    // 改变每页显示数据条数是触发的函数
    handleSizeChange (val) {
      this.pageSize = val
      this.loadPage()
    },
    // 改变页码是触发的函数
    handleCurrentChange (val) {
      // console.log(`当前页: ${val}`)
      this.pageIndex = val
      this.loadPage()
    }
  }
}
</script>

<style scoped lang="less">
    .contianer{
        width:1000px;
        margin:20px auto;
    }

    .flights-content{
        width:745px;
        font-size:14px;
    }

    .aside{
        width:240px;
    }
</style>
