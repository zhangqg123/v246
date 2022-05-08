<template>
  <page-layout :title="devName">
    <a-card :bordered="false" :loading="cardLoading">
      <detail-list title="环控点" >
        <detail-list-item :term="item.targetName" v-if="targets.length>0" v-for="(item,index) in targets" :key="index">{{ item.targetValue }}</detail-list-item>
      </detail-list>
      <a-divider style="margin-bottom: 32px"/>
      <detail-list title="环控点 222" >
        <detail-list-item :term="item.targetName" v-if="targets2.length>0" v-for="(item,index) in targets2" :key="index">{{ item.targetValue }}</detail-list-item>
      </detail-list>
      <a-divider style="margin-bottom: 32px"/>


    </a-card>
  </page-layout>
</template>

<script>
  import PageLayout from '@/components/page/PageLayout'
  import DetailList from '@/components/tools/DetailList'
  import { getAction } from '@/api/manage'
//  import { JeecgListMixin } from '@/mixins/JeecgListMixin'

  const DetailListItem = DetailList.Item

  export default {
    name: 'QpposListRightDev',
//    mixins: [JeecgListMixin],
    components: {
      PageLayout,
      DetailList,
      DetailListItem,
    },
    props: ['value'],
    data() {
      return {
        cardLoading: false,
        loading: false,
        devName: null,
        targets: [],
        targets2: [],
        ipagination:{
          current: 1,
          pageSize: 10,
          pageSizeOptions: ['10', '20', '30'],
          showTotal: (total, range) => {
            return range[0] + "-" + range[1] + " 共" + total + "条"
          },
          showQuickJumper: true,
          showSizeChanger: true,
          total: 0
        },
        url: {
          list: "/point/qwertPointDev/devTarget",
        },
      }
    },
    watch: {
      value: {
        immediate: true,
        handler(orgCode) {
          this.loadData(1, orgCode)
        }
      },
    },
    created() {
 //     this.queryPositionInfo()
      this.loadData();
    },
    methods: {
      loadData(pageNum, orgCode) {
        if(orgCode==null || orgCode==""){
          return;
        }
        this.cardLoading=true;
//        this.loading=true;
        var tmp=orgCode.split(":");
        var id=tmp[0];
        this.devName=tmp[1];
        var _this = this;
        if (pageNum === 1) {
            this.ipagination.current = 1
        }
        var params = {};//查询条件
        params.code=id;
        getAction(this.url.list, params).then((res) => {
            if (res.success) {
              var tmpResult=res.result;
        //      let tr=JSON.parse(tmpResult);
              _this.targets = tmpResult.filter(item=> item.displayMode !==0)
              _this.targets2 = tmpResult.filter(item=> item.displayMode ===0)
            }
        }).finally(() => {
//            _this.loading = false
            _this.cardLoading = false
        })
      },

      searchQuery() {
        this.loadData(1, this.value)
      },
      searchReset() {
        this.queryParam = {}
        this.loadData(1, this.value)
      },

      handleTableChange(pagination, filters, sorter) {
        if (Object.keys(sorter).length > 0) {
          this.isorter.column = sorter.field
          this.isorter.order = 'ascend' === sorter.order ? 'asc' : 'desc'
        }
        this.ipagination = pagination
        this.loadData(null, this.value)
      },

      // 查询职务信息
      queryPositionInfo() {
        getAction(this.url.listByPosition, { pageSize: 99999 }).then(res => {
          if (res.success) {
            let positionInfo = {}
            res.result.records.forEach(record => {
              positionInfo[record['code']] = record['name']
            })
            this.positionInfo = positionInfo
          }
        })
      }

    }
  }
</script>
<style>
  .j-address-list-right-card-box .ant-table-placeholder {
    min-height: 46px;
  }
</style>
<style scoped>
  .j-address-list-right-card-box {
    height: 100%;
    min-height: 300px;
  }
</style>