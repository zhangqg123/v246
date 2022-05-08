<template>
  <j-modal
    :title="title"
    :width="width"
    :visible="visible"
    switchFullscreen
    @ok="handleOk"
    :okButtonProps="{ class:{'jee-hidden': disableSubmit} }"
    @cancel="handleCancel"
    cancelText="关闭">
    <qwert-point-dev-form ref="realForm" @ok="submitCallback" :disabled="disableSubmit"></qwert-point-dev-form>
  </j-modal>
</template>

<script>

  import QwertPointDevForm from './QwertPointDevForm'
  export default {
    name: 'QwertPointDevModal',
    components: {
      QwertPointDevForm
    },
    data () {
      return {
        title:'',
        width:1024,
        visible: false,
        disableSubmit: false
      }
    },
    methods: {
      add (obj) {
        this.visible=true
        this.$nextTick(()=>{
          this.$refs.realForm.add(obj);
        })
      },
      edit (record) {
        this.visible=true
        this.$nextTick(()=>{
          this.$refs.realForm.edit(record);
        })
      },
      close () {
        this.$emit('close');
        this.visible = false;
      },
      handleOk () {
        this.$refs.realForm.submitForm();
      },
      submitCallback(){
        this.$emit('ok');
        this.visible = false;
      },
      handleCancel () {
        this.close()
      }
    }
  }
</script>