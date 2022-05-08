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
      <a-button type="primary" icon="download" @click="handleExportXls('zsj_zb_a')">导出</a-button>
      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">
        <a-button type="primary" icon="import">导入</a-button>
      </a-upload>
      <a-button @click="handleData" type="primary" icon="plus">数据</a-button>

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

    <zsj-zb-a-modal ref="modalForm" @ok="modalFormOk"></zsj-zb-a-modal>
  </a-card>
</template>

<script>

  import '@/assets/less/TableExpand.less'
  import { mixinDevice } from '@/utils/mixin'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import ZsjZbAModal from './modules/ZsjZbAModal'
  import { httpAction } from '@/api/manage'

  export default {
    name: 'ZsjZbAList',
    mixins:[JeecgListMixin, mixinDevice],
    components: {
      ZsjZbAModal
    },
    data () {
      return {
        description: 'zsj_zb_a管理页面',
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
            title:'项目名称',
            align:"center",
            dataIndex: 'projectname'
          },
          {
            title:'中标日期',
            align:"center",
            dataIndex: 'zhongbiaodate',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
          },
          {
            title:'标书编号',
            align:"center",
            dataIndex: 'biaoduanno'
          },
          {
            title:'项目编号',
            align:"center",
            dataIndex: 'projectno'
          },
          {
            title:'进入时间',
            align:"center",
            dataIndex: 'entrytime',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
          },
          {
            title:'开标日期',
            align:"center",
            dataIndex: 'kaibiaodate',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
          },
          {
            title:'标段名称',
            align:"center",
            dataIndex: 'biaoduanname'
          },
          {
            title:'招标日期',
            align:"center",
            dataIndex: 'zhaobiaodate',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
          },
          {
            title:'合同日期',
            align:"center",
            dataIndex: 'hetongdate',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
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
          list: "/zsj/zsjZbA/list",
          delete: "/zsj/zsjZbA/delete",
          deleteBatch: "/zsj/zsjZbA/deleteBatch",
          exportXlsUrl: "/zsj/zsjZbA/exportXls",
          importExcelUrl: "zsj/zsjZbA/importExcel",
          createDataUrl: "/zsj/zsjZbA/createData"
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
      handleData(){
        this.confirmLoading=true;
        const that = this;
        let httpurl = '';
        httpurl+=this.url.createDataUrl;
        httpAction(httpurl,null,'post').then((res)=>{
          if(res.success){
            that.$message.success(res.message);
          }else{
            that.$message.warning(res.message);
          }
        }).finally(() => {
          that.confirmLoading = false;
        })
      },

      initDictConfig(){
      },
      getSuperFieldList(){
        let fieldList=[];
        fieldList.push({type:'string',value:'projectname',text:'项目名称'})
        fieldList.push({type:'date',value:'zhongbiaodate',text:'中标日期'})
        fieldList.push({type:'string',value:'biaoduanno',text:'标书编号'})
        fieldList.push({type:'string',value:'projectno',text:'项目编号'})
        fieldList.push({type:'date',value:'entrytime',text:'进入时间'})
        fieldList.push({type:'date',value:'kaibiaodate',text:'开标日期'})
        fieldList.push({type:'string',value:'biaoduanname',text:'标段名称'})
        fieldList.push({type:'date',value:'zhaobiaodate',text:'招标日期'})
        fieldList.push({type:'date',value:'hetongdate',text:'合同日期'})
        this.superFieldList = fieldList
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>