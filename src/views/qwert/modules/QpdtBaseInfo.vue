<template>
  <a-card :visible="visible">
    <a-form-model ref="form" :model="model" :rules="validatorRules">
      <a-form-model-item label="位置编号" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="posId">
        <a-input v-model="model.posId" placeholder="请输入位置编号" ></a-input>
      </a-form-model-item>
      <a-form-model-item label="位置名称" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="posName">
        <a-input v-model="model.posName" placeholder="请输入位置名称" ></a-input>
      </a-form-model-item>
      <a-form-model-item label="单位用户" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="orgUser">
        <a-input v-model="model.orgUser" placeholder="请输入单位用户" ></a-input>
      </a-form-model-item>
      <a-form-model-item label="上级id" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="pid">
        <j-tree-select
          ref="treeSelect"
          placeholder="请选择上级id"
          v-model="model.pid"
          dict="qwert_point_position,pos_name,id"
          pidField="pid"
          pidValue="0"
          hasChildField="has_child"
        >
        </j-tree-select>
      </a-form-model-item>

    </a-form-model>
    <div class="anty-form-btn">
      <a-button @click="emptyCurrForm" type="default" htmlType="button" icon="sync">重置</a-button>
      <a-button @click="submitCurrForm" type="primary" htmlType="button" icon="form">保存</a-button>
    </div>
  </a-card>
</template>
<script>
  import { queryIdTree } from '@/api/api'
  import { getAction } from '@/api/manage'
  import {httpAction} from '@/api/manage'

  export default {
    name: 'QpdtBaseInfo',
    components: {},
    props: ['value'],
    data() {
      return {
  //      currSelected: {},
        departTree: [],
        id: '',
        model: {},
        visible: false,
        disable: true,
        treeData: [],
        labelCol: {
          xs: {span: 24},
          sm: {span: 3}
        },
        wrapperCol: {
          xs: {span: 24},
          sm: {span: 16}
        },
        validatorRules: {
          posName: [{required: true, message: '请输入机构/部门名称!'}],
        },
        url: {
          posById: '/point/qwertPointPosition/queryById',
          edit: '/point/qwertPointPosition/edit'
        }
      }
    },
    created() {
//      this.loadTreeData();
    },
    watch: {
      value: {
        immediate: true,
        handler(data) {
          if(data!=null && data.id!=null){
//            this.model = {}
            this.loadData(data.id)
          }
        }
      },
    },
    methods: {
      submitCurrForm() {
        this.$refs.form.validate(valid => {
          if (valid) {
            if (!this.model.id) {
              this.$message.warning('请点击选择要修改部门!')
              return
            }

            httpAction(this.url.edit, this.model, 'put').then((res) => {
              if (res.success) {
                this.$message.success('保存成功!')
      //          this.loadTree()
              } else {
                this.$message.error(res.message)
              }
            })
          }
        })
      },
      emptyCurrForm() {
        this.$refs.form.resetFields();
        this.model={}
      },
      loadData(id) {
        this.loading = true
        const that= this
          //加载数据 若传入参数1则加载第一页的内容
        getAction(this.url.posById, {
          id
        }).then((res) => {
          if (res.success) {
            that.model = res.result
          }
        }).finally(() => {
          that.loading = false
        })
      },
      loadTreeData() {
        queryIdTree().then((res) => {
          if (res.success) {
            for (let i = 0; i < res.result.length; i++) {
              let temp = res.result[i];
              this.treeData.push(temp);
            }
          }

        })
      },
      open(record) {
        this.visible = true;
        this.$nextTick(() => {
          this.$refs.form.resetFields()
          this.model = Object.assign({}, record)
        })
      },
      clearForm() {
        this.$refs.form.resetFields();
        this.treeData = [];
      },
    }
  }
</script>
<style scoped>
  .anty-form-btn {
    width: 100%;
    text-align: center;
  }

  .anty-form-btn button {
    margin: 0 5px;
  }

</style>