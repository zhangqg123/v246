<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
        </a-row>
      </a-form>
    </div>
    <!-- 查询区域-END -->

    <!-- 操作按钮区域 -->
    <div class="table-operator">
      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>
      <a-button type="primary" icon="download" @click="handleExportXls('qwert_point_target')">导出</a-button>
      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">
        <a-button type="primary" icon="import">导入</a-button>
      </a-upload>
      <!-- 高级查询区域 -->
      <j-super-query :fieldList="superFieldList" ref="superQueryModal" @handleSuperQuery="handleSuperQuery"></j-super-query>
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>
      </a-dropdown>
    </div>

    <!-- table区域-begin -->
    <div>
      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">
        <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择 <a style="font-weight: 600">{{ selectedRowKeys.length }}</a>项
        <a style="margin-left: 24px" @click="onClearSelected">清空</a>
      </div>

      <a-table
        ref="table"
        size="middle"
        :scroll="{x:true}"
        bordered
        rowKey="id"
        :columns="columns"
        :dataSource="dataSource"
        :pagination="ipagination"
        :loading="loading"
        :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
        class="j-table-force-nowrap"
        @change="handleTableChange">

        <template slot="htmlSlot" slot-scope="text">
          <div v-html="text"></div>
        </template>
        <template slot="imgSlot" slot-scope="text">
          <span v-if="!text" style="font-size: 12px;font-style: italic;">无图片</span>
          <img v-else :src="getImgView(text)" height="25px" alt="" style="max-width:80px;font-size: 12px;font-style: italic;"/>
        </template>
        <template slot="fileSlot" slot-scope="text">
          <span v-if="!text" style="font-size: 12px;font-style: italic;">无文件</span>
          <a-button
            v-else
            :ghost="true"
            type="primary"
            icon="download"
            size="small"
            @click="downloadFile(text)">
            下载
          </a-button>
        </template>

        <span slot="action" slot-scope="text, record">
          <a @click="handleEdit(record)">编辑</a>

          <a-divider type="vertical" />
          <a-dropdown>
            <a class="ant-dropdown-link">更多 <a-icon type="down" /></a>
            <a-menu slot="overlay">
              <a-menu-item>
                <a @click="handleDetail(record)">详情</a>
              </a-menu-item>
              <a-menu-item>
                <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
                  <a>删除</a>
                </a-popconfirm>
              </a-menu-item>
            </a-menu>
          </a-dropdown>
        </span>

      </a-table>
    </div>

    <qwert-point-target-modal ref="modalForm" @ok="modalFormOk"></qwert-point-target-modal>
  </a-card>
</template>

<script>

  import '@/assets/less/TableExpand.less'
  import { getAction } from '@/api/manage'
  import { mixinDevice } from '@/utils/mixin'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import QwertPointTargetModal from './modules/QwertPointTargetModal'

  export default {
    name: 'QwertPointTargetList',
    mixins:[JeecgListMixin, mixinDevice],
    components: {
      QwertPointTargetModal
    },
    data () {
      return {
        inDevNo: null,
        description: 'qwert_point_target管理页面',
        // 表头
        columns: [
          {
            title: '#',
            dataIndex: '',
            key:'rowIndex',
            width:60,
            align:"center",
            customRender:function (t,r,index) {
              return parseInt(index)+1;
            }
          },
          {
            title:'设备编号',
            align:"center",
            dataIndex: 'devNo_dictText'
          },
          {
            title:'设备类型',
            align:"center",
            dataIndex: 'devType'
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
            title:'显示样式',
            align:"center",
            dataIndex: 'displayMode'
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
          {
            title:'寄存器地址格式',
            align:"center",
            dataIndex: 'addressType'
          },
          {
            title:'数据类型',
            align:"center",
            dataIndex: 'dataType'
          },
          {
            title:'信息点类型',
            align:"center",
            dataIndex: 'infoType'
          },
          {
            title:'报警场景',
            align:"center",
            dataIndex: 'alarmScheme'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            fixed:"right",
            width:147,
            scopedSlots: { customRender: 'action' }
          }
        ],
        url: {
          list: "/point/qwertPointTarget/list",
          delete: "/point/qwertPointTarget/delete",
          deleteBatch: "/point/qwertPointTarget/deleteBatch",
          exportXlsUrl: "/point/qwertPointTarget/exportXls",
          importExcelUrl: "point/qwertPointTarget/importExcel",

        },
        dictOptions:{},
        superFieldList:[],
      }
    },
    created() {
    this.getSuperFieldList();
    },
    computed: {
      importExcelUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      },
    },
    methods: {
      handleAdd(){
        var oo={};
        oo.devNo=this.inDevNo;
        this.$refs.modalForm.add(oo);
        this.$refs.modalForm.title = "新增";
        this.$refs.modalForm.disableSubmit = false;
      },
      loadData(arg) {
        if(!this.url.list){
          this.$message.error("请设置url.list属性!")
          return
        }
        //加载数据 若传入参数1则加载第一页的内容
        if (arg === 1) {
          this.ipagination.current = 1;
        }
        var params = this.getQueryParams();//查询条件
        this.inDevNo=this.$route.params.id;
        if(this.inDevNo!=null){
          params.devNo=this.inDevNo;
        }
        var httpurl=null;
        if(params.orgUser!=null&&params.devType==null){
          httpurl=this.url.orgUserList;
        }else{
          httpurl=this.url.list;
        }
        this.loading = true;
        getAction(httpurl, params).then((res) => {
          if (res.success) {
            //update-begin---author:zhangyafei    Date:20201118  for：适配不分页的数据列表------------
            this.dataSource = res.result.records||res.result;
            if(res.result.total)
            {
              this.ipagination.total = res.result.total;
            }else{
              this.ipagination.total = 0;
            }
            //update-end---author:zhangyafei    Date:20201118  for：适配不分页的数据列表------------
          }
          if(res.code===510){
            this.$message.warning(res.message)
          }
          this.loading = false;
        })
      },
      initDictConfig(){
      },
      getSuperFieldList(){
        let fieldList=[];
        fieldList.push({type:'string',value:'devNo',text:'设备编号',dictCode:''})
        fieldList.push({type:'string',value:'devType',text:'设备类型',dictCode:''})
        fieldList.push({type:'string',value:'targetNo',text:'点编号',dictCode:''})
        fieldList.push({type:'string',value:'targetName',text:'点名称',dictCode:''})
        fieldList.push({type:'string',value:'displayMode',text:'显示样式',dictCode:''})
        fieldList.push({type:'string',value:'unit',text:'单位',dictCode:''})
        fieldList.push({type:'string',value:'ifGet',text:'是否采集',dictCode:''})
        fieldList.push({type:'string',value:'instruct',text:'主指令',dictCode:''})
        fieldList.push({type:'string',value:'address',text:'寄存器地址',dictCode:''})
        fieldList.push({type:'string',value:'addressType',text:'寄存器地址格式',dictCode:''})
        fieldList.push({type:'string',value:'dataType',text:'数据类型',dictCode:''})
        fieldList.push({type:'string',value:'infoType',text:'信息点类型',dictCode:''})
        fieldList.push({type:'string',value:'infoDatatype',text:'信息点数值类型',dictCode:''})
        fieldList.push({type:'string',value:'infoDataaccurate',text:'信息点数值精度',dictCode:''})
        fieldList.push({type:'string',value:'alarmPoint',text:'报警点',dictCode:''})
        fieldList.push({type:'string',value:'yinzi',text:'计算因子',dictCode:''})
        fieldList.push({type:'int',value:'alarmAccept',text:'收到报警次数',dictCode:''})
        fieldList.push({type:'int',value:'alarmRestore',text:'报警恢复次数',dictCode:''})
        fieldList.push({type:'int',value:'alarmRepeat',text:'报警重复次数',dictCode:''})
        fieldList.push({type:'string',value:'alarmScheme',text:'报警场景',dictCode:''})
        fieldList.push({type:'string',value:'alarmConfig',text:'报警配置',dictCode:''})
        fieldList.push({type:'string',value:'targetOrder',text:'指令顺序',dictCode:''})
        this.superFieldList = fieldList
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>