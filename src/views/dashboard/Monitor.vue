<template>
  <div>
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="48">
          <a-col :md="6" :sm="24">
            <a-form-item label="商户编号">
              <a-input v-model="queryParam.id" placeholder/>
            </a-form-item>
          </a-col>

          <a-col :md="6" :sm="24">
            <a-form-item label="商户名称">
              <a-input v-model="queryParam.id" placeholder/>
            </a-form-item>
          </a-col>

          <a-col :md="6" :sm="24">
            <a-form-item label="商品名称">
              <a-input-number v-model="queryParam.callNo" style="width: 100%"/>
            </a-form-item>
          </a-col>

          <a-col :md="6" :sm="24">
            <a-form-item label="用户设备号">
              <a-input-number v-model="queryParam.callNo" style="width: 100%"/>
            </a-form-item>
          </a-col>

          <a-col :md="6" :sm="24">
            <a-form-item label="精确查询">
              <a-select v-model="queryParam.status" placeholder="收银台流水号" default-value="0" style="width: 50%">
                <a-select-option value="0">收银台流水号</a-select-option>
                <a-select-option value="1">商户订单号</a-select-option>
                <a-select-option value="2">现金订单号</a-select-option>
              </a-select>
              <a-input v-model="queryParam.id" placeholder style="width: 50%"/>
            </a-form-item>
          </a-col>
          <a-col :md="12" :sm="24">
            <a-form-item label="订单时间">
              <a-range-picker showTime v-model="queryParam.date"></a-range-picker>
            </a-form-item>
          </a-col>

          <a-col :md="!advanced && 6 || 24" :sm="24">
            <a-button type="primary" @click="$refs.table.refresh(true)">查询</a-button>
            <a-button style="margin-left: 8px" @click="() => queryParam = {}">重置</a-button>
          </a-col>
        </a-row>
      </a-form>
    </div>

    <div class="page-header-index-wide">
      <a-row :gutter="24">
        <a-col :sm="24" :md="12" :xl="6" :style="{ marginBottom: '24px' }">
          <chart-card title="订单总额" total="￥1126.5">
            <template slot="footer">
              <span>234</span>笔
            </template>
          </chart-card>
        </a-col>

        <a-col :sm="24" :md="12" :xl="6" :style="{ marginBottom: '24px' }">
          <chart-card title="成交订单总额" total="￥116.5">
            <template slot="footer">
              <span>34</span>笔
            </template>
          </chart-card>
        </a-col>
      </a-row>
    </div>

    <div>
      <a-tabs defaultActiveKey="1">
        <a-tab-pane tab="全部" key="1">
          <a-table
            :columns="columns"
            :rowKey="record => record.login.uuid"
            :dataSource="data"
            :pagination="pagination"
            :loading="loading"
            @change="handleTableChange"
          >
            <template slot="name" slot-scope="name">
              {{ name.first }} {{ name.last }}
            </template>
          </a-table>
        </a-tab-pane>
        <a-tab-pane tab="待付款" key="2" forceRender>
        </a-tab-pane>
        <a-tab-pane tab="部分支付" key="3">
        </a-tab-pane>
        <a-tab-pane tab="成功" key="4">
        </a-tab-pane>
        <a-tab-pane tab="退款" key="5">
        </a-tab-pane>
      </a-tabs>
    </div>

  </div>
</template>

<script>
import ChartCard from '@/components/chart/ChartCard'
import ACol from 'ant-design-vue/es/grid/Col'
import Axios from 'axios'

const columns = [{
  title: 'Name',
  dataIndex: 'name',
  sorter: true,
  width: '20%',
  scopedSlots: { customRender: 'name' }
}, {
  title: 'Gender',
  dataIndex: 'gender',
  filters: [
    { text: 'Male', value: 'male' },
    { text: 'Female', value: 'female' }
  ],
  width: '20%'
}, {
  title: 'Email',
  dataIndex: 'email'
}]

export default {
  name: 'Analysis',
  components: {
    ACol,
    ChartCard
  },
  data () {
    return {
      mdl: {},
      // 高级搜索 展开/关闭
      advanced: false,
      // 查询参数
      queryParam: {},
      data: [],
      pagination: {},
      loading: false,
      columns
    }
  },
  computed: {
  },
  created () {
  },
  methods: {
    handleTableChange (pagination, filters, sorter) {
      console.log(pagination)
      const pager = { ...this.pagination }
      pager.current = pagination.current
      this.pagination = pager
      this.fetch({
        results: pagination.pageSize,
        page: pagination.current,
        sortField: sorter.field,
        sortOrder: sorter.order,
        ...filters
      })
    },
    fetch (params = {}) {
      this.loading = true
      Axios.get('https://randomuser.me/api', {
        params: {
          results: 10
          // ...params,
        }
      }).then((data) => {
        console.log(data.data.results)
        const pagination = { ...this.pagination }
        // Read total count from server
        // pagination.total = data.totalCount;
        pagination.total = 200
        this.loading = false
        this.data = data.data.results
        this.pagination = pagination
      })
    }
  },
  mounted () {
    this.fetch()
  }
}
</script>

<style lang="less">
.extra-wrapper {
  line-height: 55px;
  padding-right: 24px;

  .extra-item {
    display: inline-block;
    margin-right: 24px;

    a {
      margin-left: 24px;
    }
  }
}
</style>
