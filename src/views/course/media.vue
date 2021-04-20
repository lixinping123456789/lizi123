<template>
  <div>
    <div class="header">
      <el-button
        type="primary"
        icon="el-icon-edit"
        class="left"
        @click="dialogFormVisible = true"
      >
        新增图文
      </el-button>
      <el-select
        v-model="item.status"
        placeholder="商品状态"
        clearable
        style="width: 90px"
        class="filter-item"
      >
        <el-option label="已下架" :value="0" />
        <el-option label="已上架" :value="1" />
      </el-select>

      <el-input placeholder="Title" style="width: 200px" class="filter-item2" />
      <el-button type="primary" icon="el-icon-search"> 搜索 </el-button>
    </div>

    <el-table
      :key="tableKey"
      :data="list"
      border
      fit
      highlight-current-row
      style="width: 100%"
    >
      <el-table-column label="ID" prop="id" align="center" width="80">
        <template slot-scope="{ row }">
          <span>{{ row.id }}</span>
        </template>
      </el-table-column>
      <el-table-column label="图文内容" min-width="150px" class="container">
        <template slot-scope="{ row }">
          <img :src="row.cover" class="Img">
          <span class="content-one" @click="handleUpdate(row)">
            {{ row.title }}
          </span>
          <span style="color: red" class="content-price">
            ￥{{ row.price }}
          </span>
        </template>
      </el-table-column>
      <el-table-column label="订阅量" width="110px" align="center">
        <template slot-scope="{ row }">
          <span style="color: red">{{ row.sub_count }}</span>
        </template>
      </el-table-column>
      <el-table-column label="状态" class-name="status-col" width="100">
        <template slot-scope="{ row }">
          <el-tag :type="row.status | statusFilter">
            {{ row.status ? "已上架" : "已下架" }}
          </el-tag>
        </template>
      </el-table-column>
      <el-table-column label="创建时间" width="200px" align="center">
        <template slot-scope="{ row }">
          <span>{{ row.created_time }}</span>
        </template>
      </el-table-column>
      <el-table-column
        label="操作"
        align="center"
        width="230"
        class-name="small-padding fixed-width"
      >
        <template slot-scope="{ row, $index }">
          <el-button type="primary" size="mini"> 编辑 </el-button>
          <el-button v-if="row.status != '1'" size="mini" type="success">
            上架
          </el-button>
          <el-button v-if="row.status != '0'" size="mini"> 下架 </el-button>
          <el-button v-if="row.status != 'deleted'" size="mini" type="danger">
            删除
          </el-button>
        </template>
      </el-table-column>
    </el-table>
    <pagination
      v-show="total > 0"
      :total="total"
      :page.sync="listQuery.page"
      :limit.sync="listQuery.limit"
      @pagination="getList"
    />

    <el-dialog title="收货地址" :visible.sync="dialogFormVisible" fullscreen>
      <el-form :model="form">
        <el-form-item label="活动名称" :label-width="formLabelWidth">
          <el-input v-model="form.name" autocomplete="off" />
        </el-form-item>
        <el-form-item label="活动区域" :label-width="formLabelWidth">
          <el-select v-model="form.region" placeholder="请选择活动区域">
            <el-option label="区域一" value="shanghai" />
            <el-option label="区域二" value="beijing" />
          </el-select>
        </el-form-item>
      </el-form>

      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button
          type="primary"
          @click="dialogFormVisible = false"
        >确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>
<script>
import { fetchList, createMedia, updateMedia, deleteMedia } from '@/api/media'
import Pagination from '@/components/Pagination'
import Tinymce from '@/components/Tinymce'
export default {
  name: 'Media',
  components: { Pagination, Tinymce },
  filters: {
    statusFilter(status) {
      const statusMap = {
        1: 'success',
        0: 'info',
        deleted: 'danger'
      }
      return statusMap[status]
    }
  },
  data() {
    return {
      tableKey: 0,
      list: [],
      total: 0,
      listQuery: {
        page: 1,
        limit: 20
      },
      item: {
        status: 0
      },
      form: {
        name: '',
        region: '',
        date1: '',
        date2: '',
        delivery: false,
        type: [],
        resource: '',
        desc: ''
      },
      dialogTableVisible: false,
      dialogFormVisible: false
    }
  },
  created() {
    this.getList()
  },

  methods: {
    getList() {
      fetchList(this.listQuery).then((response) => {
        console.log(response)
        this.list = response.data.items
        this.total = response.data.total
      })
    },
    // 新增内容
    handleCreate() {
      this.resetTemp()
      this.dialogStatus = 'create'
      this.dialogFormVisible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].clearValidate()
      })
    }
  }
}
</script>
<style scoped>
.header {
  height: 45px;
  margin: 10px 0;
}
.left {
  margin: 0 600px 0 20px;
}
.filter-item {
  margin-right: 20px;
}
.filter-item2 {
  margin-right: 10px;
}
.Img {
  width: 100px;
  height: 50px;
  display: block;
  float: left;
}
.content-one,
content-price {
  display: block;
  margin: 5px;
}
</style>
