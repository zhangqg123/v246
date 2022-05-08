<template>
  <a-modal
    :title="title"
    :width="1200"
    :visible="visible"
    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="关闭">

    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="24">
          <a-col :md="6" :sm="8">
            <a-form-item label="指标名称">
              <j-input placeholder="请输入指标名称" v-model="queryParam.targetName"></j-input>
            </a-form-item>
          </a-col>

          <a-col :span="6" >
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchByquery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
              <a @click="handleToggleSearch" style="margin-left: 8px">
                {{ toggleSearchStatus ? '收起' : '展开' }}
                <a-icon :type="toggleSearchStatus ? 'up' : 'down'"/>
              </a>
            </span>
          </a-col>

        </a-row>
      </a-form>
    </div>

 <!--    update-begin author:kangxiaolin   date:20190921   for:系统发送通知 用户多选失败 #513  -->
    <a-table
      ref="table"
      rowKey="id"
      :columns="columns"
      :dataSource="dataSource"
      :pagination="ipagination"
      :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange,onSelect:onSelect}"
      @change="handleTableChange"
    >
<!--     update-end   author:kangxiaolin  date:20190921     for:系统发送通知 用户多选失败 #513 -->
    </a-table>
  </a-modal>
</template>

<script>
  import { filterObj } from '@/utils/util';
  import { getCatTargetList } from '@/api/api';
  import { httpAction } from '@/api/manage';
  import JInput from '@/components/jeecg/JInput.vue';

  export default {
    name: "SelectCatTargetListModal",
    components: {
      JInput
    },
    data () {
      return {
        title:"选择数据点",
        queryParam: {},
        tempInfo:null,
        columns: [
    /*    {
          title: '设备类型',
          align:"center",
          dataIndex: 'devType_dictText',
          fixed:'left',
          width:200
        }, */
        {
          title: '寄存器名称',
          align:"center",
          dataIndex: 'targetName'
        },
    /*    {
          title: '协议类型',
          align:"center",
          dataIndex: 'proType'
        }, */
        {
          title:'指令',
          align:"center",
          dataIndex: 'instruct'
        },
        {
          title: '寄存器地址',
          align:"center",
          dataIndex: 'address'
        },{
            title: '点类型',
            align:"center",
            dataIndex: 'infoType'
          },{
            title: '报警点',
            align:"center",
            dataIndex: 'alarmPoint'
          },{
            title: '计算因子',
            align:"center",
            dataIndex: 'yinzi'
          },{
          title: '数据类型',
          align:"center",
          dataIndex: 'dataType'
        },/*{
          title: '地址类型',
          align:"center",
          dataIndex: 'addressType',
          customRender:function (text) {
            if(text=="DEC"){
              return "十进制";
            }else if(text=="HEX"){
              return "十六进制";
            }else{
              return text;
            }
          }
        },*/{
          title: '返回数据',
          align:"center",
          dataIndex: 'dataReturn'
        }],
        url: {
          testUrl:"/point/qwertPointDev/conntest"
        },
        dataSource:[],
        ipagination:{
          current: 1,
          pageSize: 5,
          pageSizeOptions: ['5', '10', '20','700'],
          showTotal: (total, range) => {
            return range[0] + "-" + range[1] + " 共" + total + "条"
          },
          showQuickJumper: true,
          showSizeChanger: true,
          total: 0
        },
        isorter:{
          column: 'createTime',
          order: 'desc',
        },
        selectedRowKeys: [],
        selectionRows: [],
        visible:false,
        toggleSearchStatus:false,
      }
    },
    created() {
//      this.loadData();
    },
    methods: {
      add (record) {
        this.visible = true;
//        this.edit(type);
        this.queryParam.devCat=record.devCat;
        this.queryParam.devNo=record.id;
        this.loadData();

        this.tempInfo=record.conInfo;
      },
 /*     edit(type){
        if(!userIds){
          this.selectedRowKeys = []
        }else{
          this.selectedRowKeys = userIds.split(',');
        }
        if(!selectUser){
          this.selectionRows=[]
        }else{
          var that=this;
          that.selectionRows=[];
          selectUser.forEach(function(record,index){
            console.log(record)
            that.selectionRows.push({id: that.selectedRowKeys[index],realname:record})
          })
          // this.selectionRows = selectUser;
        }
      }, */
      loadData (arg){
        const that = this;
        if(arg===1){
          this.ipagination.current = 1;
        }
        let params = this.getQueryParams();//查询条件
        getCatTargetList(params).then((res)=>{
          if(res.success){
            that.dataSource = res.result.records;
            that.ipagination.total = res.result.total;
          }
        })
      },
      getQueryParams(){
        let param = Object.assign({}, this.queryParam,this.isorter);
        param.field = this.getQueryField();
        //--update-begin----author:scott---date:20190818------for:新建公告时指定特定用户翻页错误SelectUserListModal #379----
        // param.current = this.ipagination.current;
        // param.pageSize = this.ipagination.pageSize;
        param.pageNo = this.ipagination.current;
        param.pageSize = this.ipagination.pageSize;
        //--update-end----author:scott---date:20190818------for:新建公告时指定特定用户翻页错误SelectUserListModal #379---
        return filterObj(param);
      },
      getQueryField(){
        let str = "id,";
        for(let a = 0;a<this.columns.length;a++){
          str+=","+this.columns[a].dataIndex;
        }
        return str;
      },
      //--update-begin----author:kangxiaolin---date:20190921------for:系统发送通知 用户多选失败 #513----
      onSelectChange (selectedRowKeys) {
        this.selectedRowKeys = selectedRowKeys;
      },
      onClearSelected() {
        this.selectedRowKeys = [];
        this.selectionRows = [];
      },
      onSelect(record, selected){
        if(selected == true ){
          this.selectionRows.push(record);
        }else {
          this.selectionRows.forEach(function(item,index,arr){
            if(item.id == record.id) {
              arr.splice(index, 1);
            }
          })
        }
        //--update-end----author:kangxiaolin---date:20190921------for:系统发送通知 用户多选失败 #513----
      },

      searchReset(){
        let that = this;
        Object.keys(that.queryParam).forEach(function(key){
          that.queryParam[key] = '';
        });
        that.loadData(1);
      },
      handleTableChange(pagination, filters, sorter){
        //TODO 筛选
        if (Object.keys(sorter).length>0){
          this.isorter.column = sorter.field;
          this.isorter.order = "ascend"==sorter.order?"asc":"desc"
        }
        this.ipagination = pagination;
        this.loadData();
      },
      handleCancel () {
        this.selectionRows = [];
        this.selectedRowKeys = [];
        this.visible = false;
      },
      handleOk () {
        let revData={};
        revData.devNo=this.queryParam.devNo;
        if(this.selectionRows.length<1){
          revData.devCat=this.queryParam.devCat;
//          alert("请选择测试项目");
//          return;
        }
        const that = this;
        let httpurl = '';
        httpurl+=this.url.testUrl;
        if(this.selectionRows.length>0){
          revData.revList=JSON.stringify(this.selectionRows);
        }


        revData.connInfo=this.tempInfo;
        httpAction(httpurl,revData,'post').then((res)=>{
          if(res.success){
            if(res==null){
              that.$message.error("调试失败");
            }else {
              //         that.$message.success("调试成功");
              debugger;
              let resdata = res.result;
              if (resdata.length > 0) {
                that.$message.success("调试成功");
              }
              that.dataSource = that.dataSource.filter(item => item);
              if (resdata.length > 0) {
      //          if(resdata[0].indexOf("gf")!=-1){
                  for (var j = 0; j < resdata.length; j++) {
                    var r1=resdata[j];
                    if(r1.indexOf("{")!=-1){
                      r1=r1.substring(1,r1.length-1);
                    }
                    r1=r1.replace(/\s*/g,"");  //正则表达式无法用多行注释
                    var rt=r1.split(",");
                    for (var k=0;k<rt.length;k++){
                      var r2=rt[k].split('=');
                      for (var m = 0; m < that.dataSource.length; m++) {

                        if(r2[0]==that.dataSource[m].id){
                          var tmpaddress=that.dataSource[m].address;
                          let filterList=null;
                          if(tmpaddress.indexOf('.')!=-1&&tmpaddress.length>5){
                            var ta=tmpaddress.split('.');
                            tmpaddress=ta[0];
                            filterList = that.dataSource.filter(i => i.address.substring(0,4) === tmpaddress)
                            var tmpint=parseInt(r2[1]).toString(2);
                            var a0=(Array(16).join(0) + tmpint).slice(-16);
                            for(var n=0;n<filterList.length;n++){
                               var item=filterList[n];
                               var a2=item.address.split('.');
                               var a4=parseInt(a2[1]);
                               var a5=15-a4;
                               item.dataReturn=a0.substring(a5,a5+1);
                            }
                          }else{
                            that.dataSource[m].dataReturn=r2[1];
                            break;
                          }
                        }
                      }
                    }
                  }
                //}
              }
            }
          }else{
            that.$message.warning(res.message);
          }
        }).finally(() => {
          that.confirmLoading = false;
//          that.close();
        })


//          this.dataSource[0].dataReturn="111";

//        this.$emit("choseUser",this.selectionRows);
//        this.handleCancel();
      },
/*      conntest(record){
        const that = this;
        let httpurl = '';
        httpurl+=this.url.testUrl;
        let revdata = Object.assign({}, record);
        console.log("接收的数据",revdata);
        httpAction(httpurl,revdata,'post').then((res)=>{
          if(res.success){
            that.$message.success(res.message);
            that.$emit('ok');
          }else{
            that.$message.warning(res.message);
          }
        }).finally(() => {
          that.confirmLoading = false;
          that.close();
        })
      },*/
      searchByquery(){
        this.loadData(1);
      },
      handleToggleSearch(){
        this.toggleSearchStatus = !this.toggleSearchStatus;
      },
    }
  }
</script>
<style scoped>
  .ant-card-body .table-operator{
    margin-bottom: 18px;
  }

  .ant-table-tbody .ant-table-row td{
    padding-top:15px;
    padding-bottom:15px;
  }
  .anty-row-operator button{margin: 0 5px}
  .ant-btn-danger{background-color: #ffffff}

  .ant-modal-cust-warp{height: 100%}
  .ant-modal-cust-warp .ant-modal-body{height:calc(100% - 110px) !important;overflow-y: auto}
  .ant-modal-cust-warp .ant-modal-content{height:90% !important;overflow-y: hidden}

  .anty-img-wrap{height:25px;position: relative;}
  .anty-img-wrap > img{max-height:100%;}
</style>