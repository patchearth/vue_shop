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
        <el-button type="primary">添加分类</el-button>
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
      >
        <template slot="isOk" slot-scope="scope">
          <i
            class="el-icon-success"
            v-if="scope.row.cat_deleted == true"
            style="color:lightgreen"
          ></i>
          <i class="el-icon-error" v-else-if="scope.row.cat_deleted == false" style="color:red"></i>
        </template>
      </tree-table>
      <!-- 分页区域 -->
    </el-card>
  </div>
</template>
<script>
export default {
  data() {
    return {
      cateList: [],
      queryInfo: {
        type: 3,
        pagepagenum: 1,
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
      ],
    }
  },
  methods: {
    async getCateList() {
      const { data: res } = await this.$http.get('categories', {
        params: this.queryInfo,
      })
      if (res.meta.status != 200) return this.$message.error('获取商品分类失败')
      this.cateList = res.data
      console.log(res.data);
    },
  },
  created() {
    this.getCateList()
  },
}
</script>
<style lang="less" scoped>
</style>