<template>
  <a-row type="flex" :gutter="16">
    <a-col :md="5" :sm="24">
      <qpdt-list-left v-model="currentCode"/>
    </a-col>
    <a-col :md="24-5" :sm="24">
      <a-card :bordered="false">
    <!--    <a-tabs defaultActiveKey="2" @change="callback"> -->
        <a-tabs defaultActiveKey="2">
          <a-tab-pane tab="基本信息" key="1" forceRender>
            <qpdt-base-info v-model="currentCode" ref="QpdtBaseInfo"></qpdt-base-info>
          </a-tab-pane>
          <a-tab-pane tab="用户信息" key="2">
        <!--    <qpdt-list-right v-model="currentCode" ref="QpdtListRight" @clearSelectedDepartKeys="clearSelectedDepartKeys"></qpdt-list-right> -->
            <qpdt-list-right v-model="currentCode" ref="QpdtListRight" ></qpdt-list-right>
          </a-tab-pane>
          <a-tab-pane tab="部门角色" key="3" forceRender>
            <qpdt-list-right ref="DeptRoleInfo" />
          </a-tab-pane>
        </a-tabs>
      </a-card>
    <!--  <qppos-list-right v-model="currentCode"/> -->
    </a-col>
  </a-row>
</template>

<script>
  import QpdtListLeft from './modules/QpdtListLeft'
  import QpdtListRight from './modules/QpdtListRight'
  import QpdtBaseInfo from './modules/QpdtBaseInfo'

  export default {
    name: 'QwertPosDevTargetList',
    components: {
      QpdtListLeft,
      QpdtListRight,
      QpdtBaseInfo
    },
    data() {
      return {
        description: '协议页面',
        currentCode: ''
      }
    },

    methods: {
      onSelect(selectedKeys, e) {
        if (this.selectedKeys[0] !== selectedKeys[0]) {
          this.selectedKeys = [selectedKeys[0]];
        }
        let record = e.node.dataRef;
        this.checkedKeys.push(record.id);
        this.$refs.DeptBaseInfo.open(record);
        this.$refs.DeptUserInfo.onClearSelected();
        this.$refs.DeptUserInfo.open(record);
        this.$refs.DeptRoleInfo.onClearSelected();
        this.$refs.DeptRoleInfo.open(record);
      },

    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>