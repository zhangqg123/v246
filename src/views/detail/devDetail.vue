<template>
  <page-layout :title="devName">
    <a-card :bordered="false">
      <detail-list title="环控点" >
        <detail-list-item :term="item.targetName" v-for="(item,index) in targets" :key="index">{{ item.value }}</detail-list-item>
      </detail-list>
      <a-divider style="margin-bottom: 32px"/>
      <detail-list title="环控点 222" >
        <detail-list-item :term="item.targetName" v-for="(item,index) in targets2" :key="index">{{ item.value }}</detail-list-item>
      </detail-list>
      <a-divider style="margin-bottom: 32px"/>


    </a-card>
  </page-layout>
</template>

<script>
  import PageLayout from '@/components/page/PageLayout'
  import DetailList from '@/components/tools/DetailList'
  import { getAction } from '@/api/manage'

  const DetailListItem = DetailList.Item

  export default {
    name: 'devDetail',
    props: ['value'],
    components: {
      PageLayout,
      DetailList,
      DetailListItem,
//      STable,
    },
    data () {
      return {
        loading: false,
        devName: null,
        targets: [],
        targets2: [],
        url: {
          devRedis: "/jst/qwertFc/devRedis",
        },

      }
    },
    filters: {
      statusFilter(status) {
        const statusMap = {
          'processing': '进行中',
          'success': '完成',
          'failed': '失败'
        }
        return statusMap[status]
      }
    },
    created() {
//      var code=this.$route.params.code;
//      this.loadData(code);
    },
    watch: {
      value: {
        immediate: true,
        handler(orgCode) {
//          this.dataSource = []
          this.loadData(1, orgCode)
        }
      },
    },

    methods: {
      loadData(pageNo,arg) {
        if(arg==null){
          return;
        }
  //      var code=this.$route.params.code;
        var code=arg;
        if(!this.url.devRedis){
          this.$message.error("请设置url.list属性!")
          return
        }
        //加载数据 若传入参数1则加载第一页的内容
        if (arg === 1) {
          this.ipagination.current = 1;
        }
        var params = {};//查询条件
        params.code=code;
        this.loading = true;
        getAction(this.url.devRedis, params).then((res) => {
          if (res.success) {
            this.devName=res.message;
             var tmpResult=res.result;
             let tr=JSON.parse(tmpResult);
            this.targets = tr.filter(item=> item.displayMode !==0)
            this.targets2 = tr.filter(item=> item.displayMode ===0)
   //          this.registerTypeList=res.result;
          }
          if(res.code===510){
            this.$message.warning(res.message)
          }
          this.loading = false;
        })
      },
      initDictConfig(){
      },
    },
    computed: {
//      key() {
//        this.loadData(this.$route.params.code);
//        return this.$route.name !== undefined? this.$route.name +new Date(): this.$route +new Date()
//      },
      title () {
        return this.$route.meta.title
      }
    },
    watch: { //通过watch来监听路由变化
      "$route": function() {
        this.loadData(this.$route.params.code);
      }
    },

  }
</script>

<style lang="less" scoped>
  .title {
    color: rgba(0,0,0,.85);
    font-size: 16px;
    font-weight: 500;
    margin-bottom: 16px;
  }
</style>