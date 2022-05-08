<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container :disabled="formDisabled">
      <a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
        <a-row>
          <a-col :span="8">
            <a-form-model-item label="设备名称" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="devName">
              <a-input v-model="model.devName" placeholder="请输入设备名称"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="设备分类" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="devCat">
        <!--      <a-input v-model="model.devCat" placeholder="请输入设备分类"  ></a-input> -->
              <j-tree-select
                ref="treeSelect"
                placeholder="请输入设备分类"
                v-model="model.devCat"
                dict="qwert_point_cat,cat_name,id"
                pidField="pid"
                pidValue="0"
                hasChildField="has_child"
              >
              </j-tree-select>
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="设备编号" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="devNo">
              <a-input v-model="model.devNo" placeholder="请输入设备编号"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="设备位置" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="devPos">
       <!--       <a-input v-model="model.devPos" placeholder="请输入设备位置"  ></a-input>
              <j-dict-select-tag v-model="model.devPos" placeholder="请选择位置"
                                 dictCode="qwert_point_position,pos_name,pos_id"/> -->
              <j-tree-select
                ref="treeSelect"
                placeholder="请输入协议分类"
                v-model="model.devPos"
                dict="qwert_point_position,pos_name,id"
                pidField="pid"
                pidValue="0"
                hasChildField="has_child"
              >
              </j-tree-select>

            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="orgUser" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="orgUser">
              <a-input v-model="model.orgUser" placeholder="请输入orgUser"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="模型编号" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="modNo">
              <a-input v-model="model.modNo" placeholder="请输入模型编号"  ></a-input>
            </a-form-model-item>
          </a-col>
    <!--      <a-col :span="8">
            <a-form-model-item label="位置" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="position">
              <a-input v-model="model.position" placeholder="请输入位置"  ></a-input>
            </a-form-model-item>
          </a-col> -->
    <!--      <a-col :span="8">
            <a-form-model-item label="状态" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="status">
              <a-input-number v-model="model.status" placeholder="请输入状态" style="width: 100%" />
            </a-form-model-item>
          </a-col> -->
        </a-row>
        <div class="title">连接</div>
        <a-divider style="margin: 16px 0 16px 0" />
        <a-row>

          <a-col :span="8">
            <a-form-model-item label="连接类型" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="connType">
              <a-input v-model="model.connType" placeholder="请输入连接类型" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="协议类型" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="type">
              <j-tree-select
                ref="treeSelect"
                placeholder="请输入协议分类"
                v-model="model.type"
                dict="qwert_point_protocol,protocol_name,id"
                pidField="pid"
                pidValue="0"
                hasChildField="has_child"
                @change="handleChangeProtocolType"
              >
              </j-tree-select>

            </a-form-model-item>
          </a-col>
          <a-col :span="8" v-if="showConn" v-for="(item,index) in dataModel" :key="index">
            <a-form-model-item :label="item.value1" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="value2">
              <a-input v-model="item.value2" placeholder="请输入"  ></a-input>
            </a-form-model-item>
          </a-col>
    <!--      <a-col :span="8">
            <a-form-model-item label="休眠" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="sleeptime">
              <a-input v-model="model.sleeptime" placeholder="请输入休眠"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="重试次数" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="retry">
              <a-input v-model="model.retry" placeholder="请输入重试次数"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="超时" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="timeOut">
              <a-input v-model="model.timeOut" placeholder="请输入超时"  ></a-input>
            </a-form-model-item>
          </a-col> -->
          <a-col :span="8">
            <a-form-model-item label="连接信息" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="conInfo">
              <a-input v-model="model.conInfo" placeholder="请输入连接信息"  ></a-input>
            </a-form-model-item>
          </a-col>
        </a-row>
        <div class="title">其他</div>
        <a-divider style="margin: 16px 0 16px 0" />
        <a-row>
          <a-col :span="8">
            <a-form-model-item label="属性信息" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="proInfo">
              <a-input v-model="model.proInfo" placeholder="请输入属性信息"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="扩展信息" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="extInfo">
              <a-input v-model="model.extInfo" placeholder="请输入扩展信息"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="备注" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="remark">
              <a-input v-model="model.remark" placeholder="请输入备注"  ></a-input>
            </a-form-model-item>
          </a-col>
        </a-row>
      </a-form-model>
    </j-form-container>
  </a-spin>
</template>

<script>

  import { httpAction, getAction } from '@/api/manage'
  import { validateDuplicateValue } from '@/utils/util'

  export default {
    name: 'QwertPointDevForm',
    components: {
    },
    props: {
      //表单禁用
      disabled: {
        type: Boolean,
        default: false,
        required: false
      }
    },
    data () {
      return {
        contype: null,
        showConn: false,
        dataModel: [],
        protocolData:[],
        model:{
         },
        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },
        confirmLoading: false,
        validatorRules: {
        },
        url: {
          add: "/point/qwertPointDev/add",
          edit: "/point/qwertPointDev/edit",
          queryById: "/point/qwertPointDev/queryById",
          protocolList:"/point/qwertPointProtocolItem/queryprotocolList"
        }
      }
    },
    computed: {
      formDisabled(){
        return this.disabled
      },
    },
    created () {
       //备份model原始值
      this.modelDefault = JSON.parse(JSON.stringify(this.model));
      this.queryProtocolData();
    },
    methods: {
      handleChangeProtocolType(value){
//        console.log("value::"+value);
//        this.contype = value;
        let showData=this.protocolData.filter(item => item.protocolId == value);
        this.showConnItem(showData);
      },
      queryProtocolData() {
        this.loading = true
        let params={}
        getAction(this.url.protocolList, {
          params
        }).then((res) => {
          if (res.success) {
            let retData = res.result
            this.protocolData=retData;
            if(this.model.type!=null){
              let showData=this.protocolData.filter(item => item.protocolId == this.model.type);
              this.showConnItem(showData);
            }
          }
        }).finally(() => {
            this.loading = false
        })
      },
      showConnItem (showData) {
        showData.sort(function(a,b){
          return a.itemOrder - b.itemOrder
        })
        var tempInfo=null;
        if(this.model.conInfo!=null){
          tempInfo=JSON.parse(this.model.conInfo);
        }
        this.showConn=true;
        this.dataModel=[];
        for (var i = 0; i < showData.length; i ++) {
          var itemValue='';
          if(tempInfo!=null){
            for (var key in tempInfo){
//            console.log(key);
              //add your statement to get key value
              if(key==showData[i].itemNo){
                itemValue=tempInfo[key];
              }
            }
          }
          var item = {value1: showData[i].itemName,value2: itemValue,value3: showData[i].itemNo};
          this.dataModel.push(item);
        }
      },
      add (obj) {
        if(obj!=null){
        //  this.modelDefault.devPos=obj.devPos;
          this.modelDefault.devCat=obj.devCat;
        }

        this.edit(this.modelDefault);
      },
      edit (record) {
        this.model = Object.assign({}, record);
        this.visible = true;
      },
      submitForm () {
     //   console.log("dataModel::"+this.dataModel);
     //   console.log("model::"+this.model);
        var tmpConInfo={};
        for (var i = 0; i < this.dataModel.length; i ++) {
          tmpConInfo[this.dataModel[i].value3]=this.dataModel[i].value2;
        }
   //     tmpConInfo["proType"]=this.model.type;
        let tempInfo=JSON.stringify(tmpConInfo);
        if(tmpConInfo!=null){
          this.model.conInfo=tempInfo;
        }
        const that = this;
        // 触发表单验证
        this.$refs.form.validate(valid => {
          if (valid) {
            that.confirmLoading = true;
            let httpurl = '';
            let method = '';
            if(!this.model.id){
              httpurl+=this.url.add;
              method = 'post';
            }else{
              httpurl+=this.url.edit;
               method = 'put';
            }
            httpAction(httpurl,this.model,method).then((res)=>{
              if(res.success){
                that.$message.success(res.message);
                that.$emit('ok');
              }else{
                that.$message.warning(res.message);
              }
            }).finally(() => {
              that.confirmLoading = false;
            })
          }

        })
      },
    }
  }
</script>

<style lang="less">
    .title {
      color: rgba(0,0,0,1);
      font-size: 16px;
      font-weight: 500;
      margin-bottom: 16px;
    }

  </style>