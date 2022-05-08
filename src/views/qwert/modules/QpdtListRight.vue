<template>
  <a-card class="j-address-list-right-card-box" :loading="cardLoading" :bordered="false">
    <div class="table-page-search-wrapper">
      <a-form-model layout="inline" :model="queryParam">
        <a-row :gutter="10">
        </a-row>
      </a-form-model>
    </div>

    <a-table
      ref="table"
      size="middle"
      bordered
      rowKey="userId"
      :pagination="ipagination"
      :columns="columns"
      :dataSource="dataSource"
      :loading="loading"
      @change="handleTableChange">
    </a-table>

  </a-card>
</template>

<script>
  import { getAction } from '@/api/manage'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'

  export default {
    name: 'QpdtListRight',
    mixins: [JeecgListMixin],
    components: {},
    props: ['value'],
    data() {
      return {
        description: '协议项目',
        cardLoading: true,
        positionInfo: {},
        columns: [
          {
            title: '#',
            key: 'rowIndex',
            dataIndex: '',
            width: 60,
            align: 'center',
            customRender: (t, r, i) => parseInt(i) + 1
          },
          {
            title:'点编号',
            align:"center",
            sorter: true,
            dataIndex: 'targetNo'
          },
          {
            title:'点名称',
            align:"center",
            dataIndex: 'targetName'
          },
          {
            title:'主指令',
            align:"center",
            dataIndex: 'instruct'
          },
          {
            title:'寄存器地址',
            align:"center",
            dataIndex: 'address'
          },
        ],
        url: {
//          list: '/sys/user/queryByOrgCodeForAddressList',
          list: "/point/qwertPointTarget/list",
          listByPosition: '/sys/position/list'
        }
      }
    },
    watch: {
      value: {
        immediate: true,
        handler(data) {
          if(data!=null&&data.id!=null){
            this.dataSource = []
            this.loadData(1, data.id)
          }
        }
      },
    },
    created() {
 //     this.queryPositionInfo()
    },
    methods: {

      loadData(pageNum, id) {
        this.loading = true
        if (pageNum === 1) {
            this.ipagination.current = 1
        }
        // update-begin- --- author:wangshuai ------ date:20200102 ---- for:传过来的部门编码为空全查
        if (!id) {
            getAction(this.url.list, {
                ...this.getQueryParams()
            }).then((res) => {
                if (res.success) {
                    this.dataSource = res.result.records
                    this.ipagination.total = res.result.total
                }
            }).finally(() => {
                this.loading = false
                this.cardLoading = false
            })
          // update-end- --- author:wangshuai ------ date:20200102 ---- for:传过来的部门编码为空全查
        }else{
          var devNo=id;
          //加载数据 若传入参数1则加载第一页的内容
          getAction(this.url.list, {
            devNo,
            ...this.getQueryParams()
          }).then((res) => {
            if (res.success) {
              this.dataSource = res.result.records
              this.ipagination.total = res.result.total
            }
          }).finally(() => {
          this.loading = false
          this.cardLoading = false
        })
        }
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