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

    <div class="page-checkstand-table">
      <a-tabs defaultActiveKey="1">
        <a-tab-pane tab="全部" key="1">
          <a-table
            :columns="columns"
            :dataSource="data"
            :pagination="pagination"
            :loading="loading"
            size="middle"
            :scroll="{ x: 2350 }"
          >
            <a-button slot="action" type="primary" size="small">详情</a-button>

          </a-table>
        </a-tab-pane>
        <a-tab-pane tab="待付款" key="2" forceRender>
        </a-tab-pane>
        <a-tab-pane tab="部分支付" key="3">
        </a-tab-pane>
        <a-tab-pane tab="成功" key="4">
        </a-tab-pane>
        <a-tab-pane tab="退款">
        </a-tab-pane>
      </a-tabs>
    </div>

    <a-card style="margin-top: 24px" :bordered="false" title="交易详情">

      <div class="page-checkstand-result">
        <a-icon type="check-circle" theme="twoTone" style="fontSize:24px"/>
        <a href="#" slot="title" class="page-checkstand-title">交易成功</a>
      </div>

      <detail-list>
        <detail-list-item term="单收银台交易流水">2344357923678275982385</detail-list-item>
        <detail-list-item term="商户订单号">1398435792234275982385</detail-list-item>
      </detail-list>

      <a-table :columns="columns2" :dataSource="data" :loading="loading" size="middle"></a-table>

      <a-steps style="margin: 24px 0; width:50%;">
        <a-step status="finish" title="交易创建" description="2019-01-01 00：08" >
          <a-icon type="credit-card" slot="icon" />
        </a-step>
        <a-step status="finish" title="结束时间" description="2019-01-01 00：08" >
          <a-icon type="clock-circle" slot="icon" />
        </a-step>
      </a-steps>

      <detail-list>
        <detail-list-item term="商品描述">爱奇艺会员周卡*2 </detail-list-item>
        <detail-list-item term="交易买家">181****6546</detail-list-item>
        <detail-list-item term="省账户编号">123*****234</detail-list-item>
        <detail-list-item term="非电信账户编号">702*****234</detail-list-item>
        <detail-list-item term="小票信息">2018-08-08</detail-list-item>
      </detail-list>

    </a-card>

  </div>
</template>

<script>
import ChartCard from '@/components/chart/ChartCard'
import ACol from 'ant-design-vue/es/grid/Col'
import DetailList from '@/components/tools/DetailList'
import { mixinDevice } from '@/utils/mixin.js'
import Axios from 'axios'

const DetailListItem = DetailList.Item
const columns = [
  { title: '创建时间', dataIndex: 'dob.date', key: 'dob.date', width: 250, fixed: 'left' },
  { title: '商品名称', dataIndex: 'id.name', key: 'id.name', width: 100, fixed: 'left' },
  { title: '商户编号', dataIndex: 'id.value', key: 'id.value', width: 150 },
  { title: '商户名称', dataIndex: 'name.first', key: 'name.first', width: 100 },
  { title: '商户订单号', dataIndex: 'cell', key: 'cell', width: 150 },
  { title: '收银台交易流水', dataIndex: 'id.value', key: 'id.value', width: 150 },
  { title: '客户设备号', dataIndex: 'phone', key: '5', width: 150 },
  { title: '订单金额（元）', dataIndex: 'dob.age', key: '6', width: 150 },
  { title: '积分金额（元）', dataIndex: 'dob.ag', key: '7', width: 150 },
  { title: '抵用券金额（元）', dataIndex: 'dob.ag', key: '8', width: 150 },
  { title: '现金金额（元）', dataIndex: 'dob.ag', key: '9', width: 150 },
  { title: '服务费（元）', dataIndex: 'dob.ag', key: '10', width: 100 },
  { title: '现金支付方式', dataIndex: 'nat', key: '11', width: 100 },
  { title: '调用方式', dataIndex: 'nat', key: '12', width: 150 },
  { title: '现金订单号', dataIndex: 'cell', key: '13', width: 150 },
  { title: '状态', dataIndex: 'gender', key: '14', width: 100 },
  { title: '操作', key: 'gender', fixed: 'right', width: 100, scopedSlots: { customRender: 'action' } }
]

const columns2 = [
  { title: '商品名称', dataIndex: 'id.name' },
  { title: '订单金额（元）', dataIndex: 'dob.age' },
  { title: '积分金额（元）', dataIndex: 'dob.ag' },
  { title: '抵用券金额（元）', dataIndex: 'dob.ag' },
  { title: '现金金额（元）', dataIndex: 'dob.ag' },
  { title: '现金支付方式', dataIndex: 'nat' },
  { title: '现金订单号', dataIndex: 'cell' }
]

export default {
  name: 'Analysis',
  components: {
    ACol,
    ChartCard,
    DetailList,
    DetailListItem
  },
  data () {
    return {
      data: [],
      mdl: {},
      queryParam: {},
      pagination: {},
      loading: false,
      advanced: false,
      columns,
      columns2
    }
  },
  computed: {
  },
  created () {

  },
  methods: {
    fetch (params = {}) {
      this.loading = true
      Axios.get('https://randomuser.me/api', {
        params: {
          results: 3
          // ...params,
        }
      }).then((data) => {
        console.log(data.data.results.i)
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
@import '~ant-design-vue/lib/style/themes/default.less';
.page-checkstand-table {
  background: #fff;
}
.page-checkstand-result{
  margin: 24px 0;
}
.page-checkstand-title{
  margin-left: 10px;
  font-size: 18px;
}
</style>
