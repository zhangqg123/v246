<template>
  <a-row type="flex" :gutter="16">
    <a-col :md="5" :sm="24">
      <qppos-list-left v-model="currentOrgCode"/>
    </a-col>
    <a-col :md="24-5" :sm="24">
      <a-card :bordered="false">
        <a-tabs defaultActiveKey="1" @change="callback">
          <a-tab-pane tab="基本信息" key="1" forceRender>
            <qppos-list-right v-model="currentOrgCode" ref="DeptUserInfo" ></qppos-list-right>
          </a-tab-pane>
          <a-tab-pane tab="用户信息" key="2">
            <qppos-list-right-dev v-model="currentOrgCode" ref="DeptUserInfo" ></qppos-list-right-dev>
          </a-tab-pane>
        </a-tabs>
      </a-card>
    <!--  <qppos-list-right v-model="currentOrgCode"/> -->
    </a-col>
  </a-row>
</template>

<script>
  import QpposListLeft from './modules/QpposListLeft'
  import QpposListRight from './modules/QpposListRight'
  import QpposListRightDev from './modules/QpposListRightDev'
  import DetailList from '@/components/tools/DetailList'
  import DevDetail from '../detail/devDetail'

  const DetailListItem = DetailList.Item

  export default {
    name: 'QpposList',
    components: {
      DevDetail,
      QpposListLeft,
      QpposListRight,
      QpposListRightDev,
    },
    data() {
      return {
        description: '协议页面',
        currentOrgCode: ''
      }
    },
    methods: {
      callback(){}
    },
  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>