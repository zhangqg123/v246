<template>
  <div style="height: 100%">
      <a-card :loading="cardLoading" :bordered="false" style="height: 100%;">
        <a-spin :spinning="loading">
    <!--      <a-input-search @search="handleSearch" style="width:100%;margin-top: 10px" placeholder="输入机构名称查询..." enterButton /> -->
          <template>
            <a-dropdown :trigger="[this.dropTrigger]" @visibleChange="dropStatus">
                   <span style="user-select: none">

          <a-tree
            showLine
            checkStrictly
            :expandedKeys.sync="expandedKeys"
            :selectedKeys="selectedKeys"
            :dropdownStyle="{maxHeight:'200px',overflow:'auto'}"
            :treeData="treeDataSource"
            @select="handleTreeSelect"
            @rightClick="rightHandle"
          />
                    </span>
              <!--新增右键点击事件,和增加添加和删除功能-->
              <a-menu slot="overlay">
                <a-menu-item @click="handleAdd(1)" key="1">添加区域</a-menu-item>
                <a-menu-item @click="handleAdd(2)" key="2">添加节点</a-menu-item>
                <a-menu-item @click="handleDelete" key="3">删除</a-menu-item>
                <a-menu-item @click="closeDrop" key="4">取消</a-menu-item>
              </a-menu>
            </a-dropdown>
          </template>

        </a-spin>
      </a-card>
    <qwert-point-position-modal ref="qwertPointPositionModal" @ok="queryTreeData"></qwert-point-position-modal>
    <qwert-point-dev-modal ref="qwertPointDevModal" @ok="queryTreeData"></qwert-point-dev-modal>

  </div>

</template>

<script>
  import { queryPositionTreeList } from '@/api/api'
  import QwertPointPositionModal from './QwertPointPositionModal'
  import QwertPointDevModal from './QwertPointDevModal'
  import {deleteAction} from '@/api/manage'

  export default {
    name: 'QpdtListLeft',
    props: ['value'],
    components: {
      QwertPointPositionModal,
      QwertPointDevModal,
    },
    data() {
      return {
        typeValue: '',
        isLeaf: false,
        rightClickSelectedKey: '',
        dropTrigger: '',
        cardLoading: true,
        loading: false,
        treeDataSource: [],
        selectedKeys: [],
        expandedKeys: [],
        url: {
          delete: '/point/qwertPointPosition/delete',
          delete2: '/point/qwertPointDev/delete',
        }
      }
    },
    created() {
      this.queryTreeData()
    },
    methods: {
      handleDelete() {
        if(!this.isLeaf){
          alert("子节点有数据，无法删除!!!")
          return
        }

        var deleteUrl = this.url.delete
        if(this.typeValue=="dev"){
          deleteUrl = this.url.delete2
        }
        var that = this
        this.$confirm({
          title: '确认删除',
          content: '确定要删除节点数据吗?',
          onOk: function () {
            deleteAction(deleteUrl, {id: that.rightClickSelectedKey}).then((resp) => {
              if (resp.success) {
                //删除成功后，去除已选中中的数据
        //        that.checkedKeys.splice(that.checkedKeys.findIndex(key => key === that.rightClickSelectedKey), 1);
                that.$message.success('删除成功!')
                that.queryTreeData()
                //删除后同步清空右侧基本信息内容
                let orgCode=that.model.orgCode;
                if(orgCode && orgCode === that.rightClickSelectedOrgCode){
                  that.onClearSelected()
                }
              } else {
                that.$message.warning('删除失败!')
              }
            })
          }
        })
      },
      handleAdd(num) {
        if(num==1){
          this.$refs.qwertPointPositionModal.add(this.rightClickSelectedKey)
          this.$refs.qwertPointPositionModal.title = '新增'
        }
        if(num==2){
          this.$refs.qwertPointDevModal.add(this.rightClickSelectedKey)
          this.$refs.qwertPointDevModal.title = '新增'
        }
      },
      rightHandle(node) {
        this.dropTrigger = 'contextmenu'
        console.log(node.node.isLeaf)
        this.isLeaf = node.node.isLeaf
        this.typeValue = node.node.value
        this.rightClickSelectedKey = node.node.eventKey
        this.rightClickSelectedOrgCode = node.node.dataRef.orgCode
      },
      dropStatus(visible) {
        if (visible == false) {
          this.dropTrigger = ''
        }
      },
      // 右键下拉关闭下拉框
      closeDrop() {
        this.dropTrigger = ''
      },

      queryTreeData(keyword) {
        this.commonRequestThen(queryPositionTreeList({
          departName: keyword ? keyword : undefined
        }))
      },
/*
      handleSearch(value) {
        if (value) {
          this.commonRequestThen(searchByKeywords({ keyWord: value }))
        } else {
          this.queryTreeData()
        }
      },
*/
      handleTreeSelect(selectedKeys, event) {
        if (selectedKeys.length > 0 && this.selectedKeys[0] !== selectedKeys[0]) {
          this.selectedKeys = [selectedKeys[0]]
          let orgCode = event.node.dataRef.id
//          this.$emit('input', event.node.dataRef)
          this.emitInput(orgCode)
        }
      },

      emitInput(orgCode) {
        this.$emit('input', orgCode)
      },

      commonRequestThen(promise) {
        this.loading = true
        promise.then(res => {
          if (res.success) {
            this.treeDataSource = res.result
            // update-begin- --- author:wangshuai ------ date:20200102 ---- for:去除默认选中第一条数据、默认展开所有第一级
            // 默认选中第一条数据、默认展开所有第一级
            // if (res.result.length > 0) {
            //   this.expandedKeys = []
            //   res.result.forEach((item, index) => {
            //     if (index === 0) {
            //       this.selectedKeys = [item.id]
            //       this.emitInput(item.orgCode)
            //     }
            //     this.expandedKeys.push(item.id)
            //   })
            // }
           // update-end- --- author:wangshuai ------ date:20200102 ---- for:去除默认选中第一条数据、默认展开所有第一级
          } else {
            this.$message.warn(res.message)
            console.error('组织机构查询失败:', res)
          }
        }).finally(() => {
          this.loading = false
          this.cardLoading = false
        })
      },

    }
  }
</script>

<style scoped>

</style>