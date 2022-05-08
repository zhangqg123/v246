<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container :disabled="formDisabled">
      <a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
        <a-row>
          <a-col :span="8">
            <a-form-model-item label="设备编号" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="devNo">
          <!--    <a-input v-model="model.devNo" placeholder="请输入设备编号"  ></a-input> -->
              <j-dict-select-tag v-model="model.devNo" placeholder="请输入设备编号" :dictCode="dictCodeDev" />
            </a-form-model-item>
          </a-col>
      <!--    <a-col :span="8">
            <a-form-model-item label="设备类型" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="devType">
              <j-tree-select
                ref="treeSelect"
                placeholder="请输入设备分类"
                v-model="model.devCat"
                dict="qwert_point_cat,cat_name,id"
                pidField="pid"
                pidValue="0"
                hasChildField="has_child"
                @change="handleChangeCat"
              >
              </j-tree-select>
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="区域编号" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="positionId">
              <j-tree-select
                ref="treeSelect"
                placeholder="请输入区域"
                v-model="model.positionId"
                dict="qwert_point_position,pos_name,id"
                pidField="pid"
                pidValue="0"
                hasChildField="has_child"
                @change="handleChangePosition"
              >
              </j-tree-select>
            </a-form-model-item>
          </a-col> -->
          <a-col :span="8">
            <a-form-model-item label="点编号" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="targetNo">
              <a-input v-model="model.targetNo" placeholder="请输入点编号"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="点名称" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="targetName">
              <a-input v-model="model.targetName" placeholder="请输入点名称"  ></a-input>
            </a-form-model-item>
          </a-col>
      <!--    <a-col :span="8">
            <a-form-model-item label="单位" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="unit">
              <a-input v-model="model.unit" placeholder="请输入单位"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="是否采集" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="ifGet">
              <a-input v-model="model.ifGet" placeholder="请输入是否采集"  ></a-input>
            </a-form-model-item>
          </a-col> -->
        </a-row>
        <div class="title">指令</div>
        <a-divider style="margin: 16px 0 16px 0" />
        <a-row>
          <a-col :span="8">
            <a-form-model-item label="主指令" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="instruct">
              <a-input v-model="model.instruct" placeholder="请输入主指令"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="寄存器地址" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="address">
              <a-input v-model="model.address" placeholder="请输入寄存器地址"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="地址格式" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="addressType">
              <a-input v-model="model.addressType" placeholder="请输入寄存器地址格式"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="数据类型" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="dataType">
      <!--        <a-input v-model="model.dataType" placeholder="请输入数据类型"  ></a-input> -->
              <j-dict-select-tag v-model="model.dataType" placeholder="请选择位置"
                                 dictCode="qwert_point_data_type,data_type_name,data_type_value"/>
            </a-form-model-item>
          </a-col>
        </a-row>
        <div class="title">点类型</div>
        <a-divider style="margin: 16px 0 16px 0" />
        <a-row>
          <a-col :span="8">
            <a-form-model-item label="报警点" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="alarmPoint">
              <!--      <a-input v-model="model.alarmPoint" placeholder="请输入报警点"  ></a-input> -->
              <a-select v-model="model.alarmPoint" placeholder="请输入报警点" @change="handleChangeAlarmPoint">
                <a-select-option value="0">否</a-select-option>
                <a-select-option value="1">是</a-select-option>
              </a-select>
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="点类型" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="infoType">
              <!--        <a-input v-model="model.infoType" placeholder="请输入信息点类型"  ></a-input> -->
              <j-dict-select-tag v-model="model.infoType" placeholder="请选择位置" @change="handleChangeTargetType"
                                 dictCode="qwert_point_target_type,target_type_name,target_type_no"/>
            </a-form-model-item>
          </a-col>
        </a-row>
        <div class="title" >报警</div>
        <a-divider style="margin: 16px 0 16px 0" />
        <a-row>
          <a-col :span="8" v-if="showTargetTypeItem" v-for="(item,index) in dataModel" :key="index">
            <a-form-model-item :label="item.value1" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="value2">
              <a-input v-model="item.value2" placeholder="请输入"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="计算因子" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="yinzi">
              <a-input v-model="model.yinzi" placeholder="请输入计算因子"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="报警配置" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="alarmConfig">
              <a-input v-model="model.alarmConfig" placeholder="请输入报警配置"  ></a-input>
            </a-form-model-item>
          </a-col>
        </a-row>
        <div class="title">其他</div>
        <a-divider style="margin: 16px 0 16px 0" />
        <a-row>
          <a-col :span="8">
            <a-form-model-item label="显示样式" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="displayMode">
              <a-input v-model="model.displayMode" placeholder="请输入显示样式"  ></a-input>
            </a-form-model-item>
          </a-col>
      <!--    <a-col :span="8">
            <a-form-model-item label="收到报警次数" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="alarmAccept">
              <a-input-number v-model="model.alarmAccept" placeholder="请输入收到报警次数" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="报警恢复次数" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="alarmRestore">
              <a-input-number v-model="model.alarmRestore" placeholder="请输入报警恢复次数" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="报警重复次数" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="alarmRepeat">
              <a-input-number v-model="model.alarmRepeat" placeholder="请输入报警重复次数" style="width: 100%" />
            </a-form-model-item>
          </a-col> -->
          <a-col :span="8">
            <a-form-model-item label="报警场景" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="alarmScheme">
              <a-input v-model="model.alarmScheme" placeholder="请输入报警场景"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="8">
            <a-form-model-item label="指令顺序" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="targetOrder">
              <a-input v-model="model.targetOrder" placeholder="请输入指令顺序"  ></a-input>
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
    name: 'QwertPointTargetForm',
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
        dictCodeDev: null,
        targetTypeData:[],
        showTargetTypeItem: false,
        dataModel: [],
        model:{
         },
        labelCol: {
          xs: { span: 24 },
          sm: { span: 8 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 13 },
        },
        confirmLoading: false,
        validatorRules: {
        },
        url: {
          add: "/point/qwertPointTarget/add",
          edit: "/point/qwertPointTarget/edit",
          queryById: "/point/qwertPointTarget/queryById",
          targetTypeList:"/point/qwertPointTargetTypeItem/queryTargetTypeList"
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
//      this.showTargetTypeItem=false;
      this.queryTargetTypeData();
    },
    methods: {
      handleChangePosition(value){
//        console.log("value::"+value);
        this.dictCodeDev="qwert_point_dev,dev_name,id,dev_pos='"+value+"'&&dev_cat ='"+this.model.devCat+"'";
      },
      handleChangeCat(value){
//        console.log("value::"+value);
        this.dictCodeDev="qwert_point_dev,dev_name,id,dev_pos='"+this.model.positionId+"'&&dev_cat ='"+value+"'";
      },
      handleChangeAlarmPoint(value){
        if(value=="1"){
          this.showTargetTypeItem=true;
        }else{
          this.showTargetTypeItem=false;
        }
      },
      handleChangeTargetType(value){
//        console.log("value::"+value);
//        this.contype = value;
        if(this.showTargetTypeItem){
          let showData=this.targetTypeData.filter(item => item.targetTypeNo == value);
          this.showTypeItem(showData);
        }
      },
      queryTargetTypeData(s) {
        this.loading = true
        const that =this
        let params={}
        getAction(this.url.targetTypeList, {
          params
        }).then((res) => {
          if (res.success) {
            let retData = res.result
            that.targetTypeData=retData;
            if(that.model.infoType!=null){
              let showData=that.targetTypeData.filter(item => item.targetTypeNo == that.model.infoType);
              if(that.showTargetTypeItem){
                that.showTypeItem(showData);
              }
            }
          }
        }).finally(() => {
          this.loading = false
        })
      },
      showTypeItem (showData) {
        var tmpAlarmConfig=null;
        if(this.model.alarmConfig!=null){
          tmpAlarmConfig=JSON.parse(this.model.alarmConfig);
        }
        this.showTargetTypeItem=true;
        this.dataModel=[];

        for (var i = 0; i < showData.length; i ++) {
          var itemValue='';
          if(tmpAlarmConfig!=null){
            for (var key in tmpAlarmConfig){
//            console.log(key);
              //add your statement to get key value
              if(key==showData[i].targetTypeItemNo){
                itemValue=tmpAlarmConfig[key];
              }
            }
          }
          var item = {value1: showData[i].targetTypeItemName,value2: itemValue,value3: showData[i].targetTypeItemNo};
          this.dataModel.push(item);
        }
      },

      add (oo) {
        if(oo!=null){
          //  this.modelDefault.devPos=obj.devPos;
          this.modelDefault.devNo=oo.devNo;
        }
        this.edit(this.modelDefault);
      },
      edit (record) {
        this.model = Object.assign({}, record);
        if(this.model.alarmPoint=="1"){
          this.showTargetTypeItem=true;
        }else{
          this.showTargetTypeItem=false;
        }

    //    "jst_zc_position,pos_name,pos_id,org_user= '"+value+"'";
//        this.dictCodeDev="qwert_point_dev,dev_name,id,dev_pos='"+this.model.positionId+"'&&dev_cat ='"+this.model.devCat+"'";
//        this.dictCodeDev="qwert_point_dev,dev_name,id,dev_no='"+this.model.devNo+"'";
        this.dictCodeDev="qwert_point_dev,dev_name,id";
  //      this.$set(this.dictCode2, "val", tmp);
        this.visible = true;
      },
      submitForm () {
        console.log("dataModel::"+this.dataModel);
        console.log("model::"+this.model);
        var tmpAlarmConfig={};
        for (var i = 0; i < this.dataModel.length; i ++) {
          tmpAlarmConfig[this.dataModel[i].value3]=this.dataModel[i].value2;
        }
        let tac=JSON.stringify(tmpAlarmConfig);
        if(tmpAlarmConfig!=null){
          this.model.alarmConfig=tac;
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