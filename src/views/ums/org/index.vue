<template>
  <div>
    <div class="app-container">
      <el-form label-position="right" label-width="80px" :model="dataForm" class="base-form">
        <el-row :gutter="20">
          <el-col :xs="24" :sm="8" :md="6">
            <el-form-item label="输入搜索">
              <el-input v-model="dataForm.name" clearable placeholder="账号/姓名"/>
            </el-form-item>
          </el-col>
          <el-col :xs="24" :sm="16" :md="18" class="text-right">
            <el-button>重置</el-button>
            <el-button type="primary">查询结果</el-button>
          </el-col>
        </el-row>
      </el-form>
    </div>
    <div class="app-container">
      <div class="btn-tools">
        <el-button type="primary" icon="el-icon-plus">新增</el-button>
        <el-button type="danger" icon="el-icon-download">导出</el-button>
      </div>
      <el-table
          v-loading="loading"
          :data="tableData"
          stripe
          style="width: 100%"
      >
        <el-table-column type="selection" width="55"/>
        <el-table-column prop="code" label="唯一识别码"/>
        <el-table-column prop="name" label="角色名称"/>
        <el-table-column prop="create_time" label="创建时间" min-width="140"/>
        <el-table-column label="状态" min-width="100">
          <template slot-scope="scope">
            <el-tag>{{scope.row.status ? '启用' : '禁用'}}</el-tag>
          </template>
        </el-table-column>
        <el-table-column label="操作" width="180">
          <template slot-scope="scope">
            <el-button type="text">分配菜单</el-button>
            <el-divider direction="vertical"/>
            <el-button type="text">编辑</el-button>
            <el-divider direction="vertical"/>
            <el-popconfirm title="是否要进行该删除操作?" icon="el-icon-info" iconColor="red">
              <el-button slot="reference" type="text">删除</el-button>
            </el-popconfirm>
          </template>
        </el-table-column>
      </el-table>
      <div class="page-container">
        <div class="fl">
          <el-select v-model="select" clearable placeholder="批量操作">
            <el-option
                v-for="item in options"
                :key="item.value"
                :label="item.label"
                :value="item.value">
            </el-option>
          </el-select>
          <el-button type="primary">确定</el-button>
        </div>
        <el-pagination
            class="fr"
            background
            :small="small"
            :current-page="p"
            :page-size="ps"
            :page-sizes="[15, 20, 30, 50]"
            :layout="layout"
            :total="total"
            @size-change="onSizeChange"
            @current-change="onCurrentChange"
        />
      </div>
    </div>
  </div>
</template>

<script>
  // api
  import {roleList} from '@/api/ums'
  // vuex
  import {mapGetters} from 'vuex'

  export default {
    name: 'Role',
    data() {
      return {
        loading: false,
        tableData: [],
        total: 0,
        p: 1,
        ps: 15,
        dataForm: {
          name: ''
        },
        select: '',
        options: [
          {value: '选项1', label: '批量删除'},
        ],
      }
    },
    computed: {
      small() {
        return ['screen-md', 'screen-xs', 'screen-sm'].includes(this.query)
      },
      layout() {
        return ['screen-md', 'screen-xs', 'screen-sm'].includes(this.query) ? 'total, prev, pager, next' : 'total, sizes, prev, pager, next, jumper'
      },
      ...mapGetters([
        'query'
      ])
    },
    created() {
      this._roleList()
    },
    methods: {
      async _roleList() {
        this.loading = true
        const res = await roleList({p: this.p, ps: this.ps})
        this.tableData = res.data.items
        this.total = res.data.total
        this.loading = false
      },
      onSizeChange(val) {
        this.ps = val
        this._roleList()
      },
      onCurrentChange(val) {
        this.p = val
        this._roleList()
      },
    },
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss">

</style>