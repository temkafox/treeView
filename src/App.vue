<template>
  <div id="app">
    <ul>
      <TreeView v-for="(item) in treeData" :key="item.id" :treeData="item" :value="value" @select-another-value="selectAnotherValue"/>
    </ul>
  </div>
</template>

<script>
import TreeView from './components/TreeView'
import { TREE_DATA } from './components/constants'
export default {
  name: 'app',
  components: {
    TreeView
  },
  data() {
    return {
      treeData: TREE_DATA,
      value: null
    }
  },
  methods: {
    selectAnotherValue(value) {
      this.value = value
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
