<template>
    <div>
           <el-breadcrumb separator="/">
  <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
  <el-breadcrumb-item>商品管理</el-breadcrumb-item>
  <el-breadcrumb-item>商品分类</el-breadcrumb-item>
</el-breadcrumb>
<el-card>
    <el-row>
        <el-col>
            <el-button type="primary" @click="showAddCateDialog">添加分类</el-button>
        </el-col>
    </el-row>
    <tree-table class="treetable" :data="catelist" :columns="columns" :selection-type="false"
    :expand-type="false" show-index index-text="#" border
    :show-row-hover="false">
    <!--是否有效-->
    <template v-slot:isOk="scope">
        <i class="el-icon-success"
         v-if="scope.row.cat_deleted === false "
          style="color: lightgreen;"> </i>
         <i class="el-icon-error"
         v-else style="color: red;"> </i>
         <!-- 排序 -->
         </template>
         <template v-slot:order="scope">
      <el-tag size="mini" v-if="scope.row.cat_level===0">一级</el-tag>
      <el-tag type="success" size="mini" v-else-if="scope.row.cat_level===1">二级</el-tag>
      <el-tag type="warning" size="mini" v-else>三级</el-tag>
         </template>
         <!--操作-->
         <template slot="opt">
             <el-button type="primary" size="mini" icon="el-icon-edit">编辑</el-button>
             <el-button type="danger" size="mini" icon="el-icon-delete">删除</el-button>
         </template>
    </tree-table>
       <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="queryInfo.pagenum"
      :page-sizes="[3, 5, 10, 15]"
      :page-size="queryInfo.pagesize"
      layout="total, sizes, prev, pager, next, jumper"
      :total="total">
    </el-pagination>
</el-card>
<!-- 添加分类对话框 -->
<el-dialog
  title="添加分类"
  :visible.sync="addCateDialogVisible"
  width="50%"
  @close="addCateDialogClosed"
  >
  <!-- 添加分类的表单 -->
  <el-form ref="addCateFormRef" :model="addCateForm"
   label-width="100px" :rules="addCateFormRules">
  <el-form-item label="分类名称：" prop="cat_name">
    <el-input v-model="addCateForm.cat_name"></el-input>
  </el-form-item>
  <el-form-item label="父级分类：">
       <el-cascader
    v-model="selectedKeys"
    :options="parentCateList" :props="{ expandTrigger: 'hover' ,label: 'cat_name',
     value:'cat_id' ,children: 'children'}"
    @change="parentCateChange" clearable checkStrictly></el-cascader>
  </el-form-item>
  </el-form>
  <span slot="footer" class="dialog-footer">
    <el-button @click="addCateDialogVisible = false">取 消</el-button>
    <el-button type="primary" @click="addCate">确 定</el-button>
  </span>
</el-dialog>
    </div>
</template>
<script>
export default {
  data () {
    return {
      queryInfo: {
        type: 3,
        pagenum: 1,
        pagesize: 5
      },
      catelist: [],
      total: 0,
      columns: [
        {
          label: '分类名称',
          prop: 'cat_name'
        },
        {
          label: '是否有效',
          type: 'template',
          template: 'isOk'
        },
        {
          label: '排序',
          type: 'template',
          template: 'order'
        },
        {
          label: '操作',
          type: 'template',
          template: 'opt'
        }
      ],
      /* 控制添加分类对话框的显示与隐藏 */
      addCateDialogVisible: false,
      /* 添加表单分类对象 */
      addCateForm: {
        cat_name: '',
        cat_pid: 0,
        cat_level: 0
      },
      addCateFormRules: {
        cat_name: [
          { required: true, message: '请输入分类名称', trigger: 'blur' }
        ]
      },
      parentCateList: [],
      cascaderProps: {
        value: 'cat_id',
        label: 'cat_name',
        children: 'children'
      },
      selectedKeys: []
    }
  },
  methods: {
    async getCateList () {
      const { data: res } = await this.$http.get('categories', { params: this.queryInfo })
      if (res.meta.status !== 200) {
        return this.$message.error('获取商品失败')
      }
      this.catelist = res.data.result
      this.total = res.data.total
    },
    /* 监听pagesize改变 */
    handleSizeChange (newSize) {
      this.queryInfo.pagesize = newSize
      this.getCateList()
    },
    /* 监听pagenum的改变 */
    handleCurrentChange (newPage) {
      this.queryInfo.pagenum = newPage
      this.getCateList()
    },
    showAddCateDialog () {
      this.getParentCateList()
      this.addCateDialogVisible = true
    },
    async getParentCateList () {
      const { data: res } = await this.$http.get('categories', { params: { type: 2 } })
      if (res.meta.status !== 200) {
        return this.$message.error('获取父级分类数据失败')
      }
      this.parentCateList = res.data
    },
    parentCateChange () {
      console.log(this.selectedKeys)
      if (this.selectedKeys.length > 0) {
        this.addCateForm.cat_pid = this.selectedKeys[this.selectedKeys.length - 1]
        this.addCateForm.cat_level = this.selectedKeys.length
        return true
      } else {
        this.addCateForm.cat_pid = 0
        this.addCateForm.cat_level = 0
      }
    },
    addCate () {
      this.$refs.addCateFormRef.validate(async valid => {
        if (!valid) return true
        const { data: res } = await this.$http.post('categories', this.addCateForm)
        if (res.meta.status !== 201) {
          return this.$message.error('添加失败')
        }
        this.$message.success('添加成功')
        this.getCateList()
        this.addCateDialogVisible = false
      })
    },
    addCateDialogClosed () {
      this.$refs.addCateFormRef.resetFields()
      this.selectedKeys = []
      this.addCateForm.cat_pid = 0
      this.addCateForm.cat_level = 0
    }
  },
  created () {
    this.getCateList()
  }
}
</script>
<style lang="less" scoped>
.treetable {
    margin-top: 15px;
}
</style>
