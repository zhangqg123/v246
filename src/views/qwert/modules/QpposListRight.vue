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
  import { filterObj } from '@/utils/util';
  import { getAction } from '@/api/manage'
//  import { JeecgListMixin } from '@/mixins/JeecgListMixin'

  export default {
    name: 'QpposListRight',
//    mixins: [JeecgListMixin],
    components: {},
    props: ['value'],
    data() {
      return {
        queryParam: {},
        description: '协议项目',
        cardLoading: true,
        positionInfo: {},
        dataSource: [],
        /* 分页参数 */
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
        /* 筛选参数 */
        filters: {},
        /* table加载状态 */
        loading:false,
        /* table选中keys*/
        selectedRowKeys: [],
        /* table选中records*/
        selectionRows: [],
        /* 查询折叠 */
        toggleSearchStatus:false,
        /* 高级查询条件生效状态 */
        superQueryFlag:false,
        /* 高级查询条件 */
        superQueryParams: '',
        /** 高级查询拼接方式 */
        superQueryMatchType: 'and',
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
        handler(orgCode) {
  //        this.dataSource = []
          this.loadData(1, orgCode)
        }
      },
    },
    created() {
//      this.loadData();
    },
    methods: {

      loadData(pageNum, orgCode) {
        this.loading = true
        if (pageNum === 1) {
            this.ipagination.current = 1
        }
        var id=null;
        if(orgCode!=null){
          var tmp=orgCode.split(":");
          id=tmp[0];
        }

        // update-begin- --- author:wangshuai ------ date:20200102 ---- for:传过来的部门编码为空全查
        if (id==null||id=="") {
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
      getQueryParams() {
        //获取查询条件
        let sqp = {}
        if(this.superQueryParams){
          sqp['superQueryParams']=encodeURI(this.superQueryParams)
          sqp['superQueryMatchType'] = this.superQueryMatchType
        }
        var param = Object.assign(sqp, this.queryParam, this.isorter ,this.filters);
        param.field = this.getQueryField();
        param.pageNo = this.ipagination.current;
        param.pageSize = this.ipagination.pageSize;
        return filterObj(param);
      },
      getQueryField() {
        //TODO 字段权限控制
        var str = "id,";
        this.columns.forEach(function (value) {
          str += "," + value.dataIndex;
        });
        return str;
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