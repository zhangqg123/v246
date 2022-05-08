<template>
  <a-row type="flex" :gutter="16">
    <a-col :md="5" :sm="24">
      <qpp-list-left v-model="currentProtocolId"/>
    </a-col>
    <a-col :md="24-5" :sm="24">
      <qpp-list-right v-model="currentProtocolId"/>
    </a-col>
  </a-row>
</template>

<script>
  import QppListLeft from './modules/QppListLeft'
  import QppListRight from './modules/QppListRight'

  export default {
    name: 'QppList',
    components: { QppListLeft, QppListRight },
    data() {
      return {
        description: '协议页面',
        currentProtocolId: ''
      }
    },

    methods: {}
  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>