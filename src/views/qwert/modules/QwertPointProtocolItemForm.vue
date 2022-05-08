<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container :disabled="formDisabled">
      <a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
        <a-row>
          <a-col :span="24">
            <a-form-model-item label="项目编号" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="itemNo">
              <a-input v-model="model.itemNo" placeholder="请输入项目编号"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="项目名称" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="itemName">
              <a-input v-model="model.itemName" placeholder="请输入项目名称"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="协议分类" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="protocolId">
        <!--      <a-input v-model="model.protocolId" placeholder="请输入外键"  ></a-input> -->
              <j-tree-select
                ref="treeSelect"
                placeholder="请输入协议分类"
                v-model="model.protocolId"
                dict="qwert_point_protocol,protocol_name,id"
                pidField="pid"
                pidValue="0"
                hasChildField="has_child"
              >
              </j-tree-select>

            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="项目顺序" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="itemOrder">
              <a-input v-model="model.itemOrder" placeholder="请输入项目顺序"  ></a-input>
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
    name: 'QwertPointProtocolItemForm',
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
          add: "/point/qwertPointProtocolItem/add",
          edit: "/point/qwertPointProtocolItem/edit",
          queryById: "/point/qwertPointProtocolItem/queryById"
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
    },
    methods: {
      add () {
        this.edit(this.modelDefault);
      },
      edit (record) {
        this.model = Object.assign({}, record);
        this.visible = true;
      },
      submitForm () {
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