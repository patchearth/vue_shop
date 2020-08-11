<template>
  <div>
    <!-- 面包屑导航 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>商品管理</el-breadcrumb-item>
      <el-breadcrumb-item>商品分类</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片试图 -->
    <el-card>
      <el-row>
        <el-button type="primary" @click="showAddDialog">添加分类</el-button>
      </el-row>
      <!-- 表格区域 -->
      <tree-table
        :data="cateList"
        :columns="columns"
        :selection-type="false"
        :show-row-hover="false"
        border
        :expand-type="false"
        show-index
        index-text="#"
        class="tree"
      >
        <template slot="isOk" slot-scope="scope">
          <i class="el-icon-success" v-if="scope.row.cat_deleted == true" style="color:lightgreen"></i>
          <i class="el-icon-error" v-else-if="scope.row.cat_deleted == false" style="color:red"></i>
        </template>
        <template slot="order" slot-scope="scope">
          <el-tag type size="mini" v-if="scope.row.cat_level==0">一级</el-tag>
          <el-tag type="success" size="mini" v-else-if="scope.row.cat_level==1">二级</el-tag>
          <el-tag type="warning" size="mini" v-else>三级</el-tag>
        </template>
        <template slot="opt" slot-scope="scope">
          <el-button type="primary" icon="el-icon-edit" size="mini">编辑</el-button>
          <el-button type="danger" icon="el-icon-delete" size="mini">删除</el-button>
        </template>
      </tree-table>
      <!-- 分页区域 -->
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="queryInfo.pagenum"
        :page-sizes="[3, 5, 10, 30]"
        :page-size="queryInfo.pagesize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="total / 1"
      ></el-pagination>
    </el-card>
    <!-- 添加分类弹出层 -->
    <el-dialog title="提示" :visible.sync="addCateDialogVisible" width="50%">
      <el-form :model="addForm" :rules="addrules" ref="addFormRef" label-width="100px">
        <el-form-item label="分类名称:" prop="name">
          <el-input v-model="addForm.cat_name"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="addCateDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="addCateDialogVisible = false">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>
<script>
export default {
  data() {
    return {
      total: '',
      cateList: [],
      queryInfo: {
        type: 3,
        pagenum: 1,
        pagesize: 5,
      },
      columns: [
        {
          label: '分类名称',
          prop: 'cat_name',
        },
        {
          label: '是否有效',
          type: 'template',
          template: 'isOk',
        },
        {
          label: '排序',
          type: 'template',
          template: 'order',
        },
        {
          label: '操作',
          type: 'template',
          template: 'opt',
        },
      ],
      addForm: {
        cat_pid: '0',
        cat_name: '',
        cat_level: '0',
      },
      addrules: {
        name: [{ required: true, message: '请输入分类名称', trigger: 'blur' }],
      },
      addCateDialogVisible: false,
    }
  },
  methods: {
    async getCateList() {
      const { data: res } = await this.$http.get('categories', {
        params: this.queryInfo,
      })
      if (res.meta.status != 200) return this.$message.error('获取商品分类失败')
      this.cateList = res.data.result
      this.total = res.data.total
    },
    handleSizeChange(newSize) {
      this.queryInfo.pagesize = newSize
      this.getCateList()
    },
    handleCurrentChange(newNum) {
      this.queryInfo.pagenum = newNum
      this.getCateList()
    },
    showAddDialog() {
      this.addCateDialogVisible = true
    },
    async getParentCateList() {
      const {data:res} = await this.$http.get('')
    },
  },
  created() {
    this.getCateList()
  },
}
</script>
<style lang="less" scoped>
.tree {
  margin-top: 15px;
}
</style>