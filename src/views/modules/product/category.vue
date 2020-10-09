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
             default-expand-all
             :expand-on-click-node="false"
             :filter-node-method="filterNode"
             node-key="catId"
             show-checkbox
             ref="tree">
      <span class="custom-tree-node" slot-scope="{ node, data }">
        <span>{{ data.name }}</span>

        <span>
          <el-button
            type="text"
            size="mini"
            v-if="node.level<=2"
            @click="() => append(data)">
            新增
          </el-button>

          <el-button
            v-if="node.childNodes.length==0"
            type="text"
            size="mini"
            @click="() => remove(node, data)">
            删除
          </el-button>

            <el-button
              type="text"
              size="mini"
              @click="() => update(node, data)">
            修改
          </el-button>
        </span>
      </span>
    </el-tree>

    <el-dialog
      title="新增品类"
      :visible.sync="dialogVisible"
      width="30%"
    >
      <el-form :model="form">
        <el-form-item label="类别名称" :label-width="formLabelWidth">
          <el-input v-model="form.name" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="是否显示" :label-width="formLabelWidth">
          <el-select v-model="form.showStatus" placeholder="请选择是否显示">
            <el-option label="显示" value="1"></el-option>
            <el-option label="不显示" value="0"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label=排序 :label-width="formLabelWidth">
          <el-input-number v-model="form.sort" min="0" autocomplete="off"></el-input-number>
        </el-form-item>
      </el-form>
      <el-button @click="dialogVisible = false">取 消</el-button>
      <el-button type="primary" @click="submit">确 定</el-button>
    </el-dialog>

  </div>
</template>

<script>
  export default {
    watch: {
      filterText (val) {
        this.$refs.tree.filter(val)
      }
    },
    data () {
      return {
        formLabelWidth: '120px',
        dialogVisible: false,
        filterText: '',
        menuTree: [],
        defaultProps: {
          children: 'children',
          label: 'name'
        },
        form: {
          name: '',
          parentCid: '',
          catLevel: '',
          showStatus: '',
          sort: 0,
          productUnit: '',
          productCount: 0,
        },
        dialogType:'',
      }
    },
    created () {
      this.getMenu()
    },
    methods: {
      submit () {
        if (this.dialogType === 'add') {
          this.addCategory()
        } else if (this.dialogType === 'update') {
          this.updateCategory()
        }
      },
      updateCategory () {
        this.$http({
          url: this.$http.adornUrl('/product/pmscategory/update'),
          method: 'post',
          data: this.form
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.$message({
              message: '菜单修改成功',
              type: 'success',
              duration: 1500,
            })
            this.dialogVisible = false
            this.getMenu()
            this.form = {}
          }
        })
      },
      addCategory () {
        this.dialogType == 'add';
        this.$http({
          url: this.$http.adornUrl('/product/pmscategory/save'),
          method: 'post',
          data: this.form
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.$message({
              message: '菜单添加成功',
              type: 'success',
              duration: 1500,
            })
            this.dialogVisible = false
            this.getMenu()
            this.form = {}
          }
        })
      },
      append (data) {
        this.dialogVisible = true
        this.form.parentCid = data.catId
        this.form.catLevel = data.catLevel * 1 + 1
      },

      remove (node, data) {
        console.log('node:', node)
        const catIds = [data.catId]
        this.$confirm('是否删除【' + data.name + '】菜单？', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/product/pmscategory/delete'),
            method: 'post',
            data: catIds
          }).then(({data}) => {
            if (data && data.code === 0) {
              this.$message({
                message: '菜单删除成功',
                type: 'success',
                duration: 1500,
              })
              this.getMenu()
            } else {
              this.$message.error(data.msg)
            }
          })
        }).catch(() => {
          this.$message({
            message: '已取消删除',
            type: 'info',
          })
        })
      },

      update (data) {
        console.log('updatedata:', data)
        this.dialogVisible = true
        this.form.name = data.name
        this.form.catId = data.catId

      },
      filterNode (value, menuTree) {
        if (!value) return true
        return menuTree.name.indexOf(value) !== -1
      },
      getMenu () {
        this.$http({
          url: this.$http.adornUrl('/product/pmscategory/list/tree'),
          method: 'get',
          params: this.filterText
        }).then(({data}) => {
          debugger
          this.menuTree = data.listTree
        })
      },
    }
  }
</script>
