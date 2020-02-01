
<template>
  <div id="app">
      <TreeViewSearch @search="search"/>
    <ul>
      <TreeView
        v-for="(item, index) in rootTreeData"
        :key="index"
        :item="item"
        :value="value"
        :treeData="treeData"
        :isSearch="isSearch"
        :searchInputText="searchInputText"
        :openedNodes="openedNodes"
        @select-another-value="selectAnotherValue"
        @open="open"
        @close="close"
      />
    </ul>
  </div>
</template>

<script>
/* eslint-disable no-console */
import TreeView from './components/TreeView'
import TreeViewSearch from './components/TreeViewSearch'
import { TREE_DATA } from './components/constants'
export default {
  name: 'app',
  components: {
    TreeView,
    TreeViewSearch
  },
  data() {
    return {
      flatTreeData: [],
      value: null,
      isSearch: false,
      searchInputText: '',
      parentId: null,
      openedNodes: []
    }
  },
  methods: {
    selectAnotherValue(value) {
      this.value = value
    },
    open(id) {
      this.openedNodes.push(id)
    },
    close(id) {
      const deleteIndex = this.openedNodes.findIndex(itemId => itemId === id)
      this.openedNodes.splice(deleteIndex, 1)
    },
    openAllNodes() {
      // this.openedNodes = this.flatTreeData
      //   .filter(item =>
      //     !!this.flatTreeData.find(child => child.parentId === item.id)
      //   )
      //   .map(item => item.id)
      const ids = this.flatTreeData.reduce((res, item) => {
        if (item.name.toLowerCase().includes(this.searchInputText.toLowerCase()))
          res.push(...item.ancestorsIds)
        return res
      }, [])
      this.openedNodes = ids
    },
    closeAllNodes() {
      this.openedNodes = []
    },
    getAllChilds(id) {
      return this.flatTreeData.reduce((res, item) => {
        if(id === item.parentId){
          res.push(item)
          res = res.concat(this.getAllChilds(item.id))
        }
        return res
      }, [])
    },
    search(searchInputText) {
      searchInputText ? this.isSearch = true : this.isSearch = false
      this.searchInputText = searchInputText
      this.openAllNodes()
    },
    searchTreeNodes(treeData, searchText) {
      if(searchText === '')
        this.closeAllNodes()
      return treeData.reduce((result, item) => {
        if (item.name.toLowerCase().includes(searchText.toLowerCase())){
          result.push(item, ...item.ancestorsIds.map(ancestorId => treeData.find(({id}) => ancestorId === id)), ...this.getAllChilds(item.id));
        }
        return result;
      }, []);
    },
    normalizeData(treeData, parentId, ancestorsIds) {
      return treeData.reduce((flatData, item) => {
        flatData.push({
          id: item.id,
          name: item.name,
          desc: item.desc,
          value: item.value,
          parentId,
          ancestorsIds
        })
        if (item.phones)
          flatData = flatData.concat(this.normalizeData(item.phones, item.id, [...ancestorsIds, item.id]))
        return flatData
      }, [])
    },
  },
  beforeMount() {
      this.flatTreeData = this.normalizeData(TREE_DATA, null, [])
  },
  computed: {
    treeData: function () {
      return [...new Set(this.searchTreeNodes(this.flatTreeData, this.searchInputText))]
    },
    rootTreeData: function() {
      return this.treeData.filter(item => item.parentId === this.parentId)
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
