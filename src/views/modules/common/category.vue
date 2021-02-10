<template>
  <div>
    <el-input
      placeholder="输入关键字进行过滤"
      v-model="filterText"
      clearable>
    </el-input>
    <el-tree class="filter-tree"
             :data="menuTree"
             :props="defaultProps"
             :expand-on-click-node="false"
             :filter-node-method="filterNode"
             node-key="catId"
             ref="tree"
             @node-click="handleNodeClick">
    </el-tree>
  </div>
</template>

<script>
  export default {
    name: 'category',
    data() {
      return {
        maxLevel: 1,
        formLabelWidth: '120px',
        dialogVisible: false,
        filterText: '',
        menuTree: [],
        title: '',
        defaultProps: {
          children: 'children',
          label: 'name'
        },
        form: {
          name: '',
          icon: '',
          parentCid: '',
          catLevel: '',
          showStatus: 1,
          sort: 0,
          productUnit: '',
          productCount: 0,
        },
        dialogType: '',
      };
    },
    watch: {
      filterText(val) {
        this.$refs.tree.filter(val);
      }
    },
    created() {
      this.getMenu();
    },
    methods: {
      getMenu() {
        this.$http({
          url: this.$http.adornUrl('/product/pmscategory/list/tree'),
          method: 'get',
          params: this.filterText
        }).then(({data}) => {
          this.menuTree = data.listTree;
        });
      },
      filterNode(value, menuTree) {
        if (!value) return true;
        return menuTree.name.indexOf(value) !== -1;
      },
      handleNodeClick(data,node,object) {
        console.log("子节点被点击:",data,node,object);
        this.$emit("click-tree-node",data)
      }
    }

  };
</script>

<style scoped>

</style>
