<template>
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
<!--          <a-menu slot="overlay">
            <a-menu-item @click="handleAdd(3)" key="1">添加</a-menu-item>
            <a-menu-item @click="handleDelete" key="2">删除</a-menu-item>
            <a-menu-item @click="closeDrop" key="3">取消</a-menu-item>
          </a-menu> -->
        </a-dropdown>
      </template>

    </a-spin>
  </a-card>
</template>

<script>
  import { queryPositionTreeList } from '@/api/api'

  export default {
    name: 'QpposListLeft',
    props: ['value'],
    data() {
      return {
        dropTrigger: '',
        cardLoading: true,
        loading: false,
        treeDataSource: [],
        selectedKeys: [],
        expandedKeys: []
      }
    },
    created() {
      this.queryTreeData()
    },
    methods: {
      rightHandle(node) {
        this.dropTrigger = 'contextmenu'
        console.log(node.node.eventKey)
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

          let devNo = event.node.dataRef.id
          let devName = event.node.dataRef.name
          this.emitInput(devNo+":"+devName)
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