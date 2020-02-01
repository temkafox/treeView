<template>
  <div>
    <input
      type="text"
      v-model="searchInputText"
      placeholder="Search"
      @keyup="search()"
    >
  </div>
</template>

<script>
export default {
  name: "TreeViewSearch",
  data() {
    return {
      searchInputText: ""
    };
  },
  methods: {
    search() {
      this.$emit("search", this.searchInputText)
    }
  }
}
</script>