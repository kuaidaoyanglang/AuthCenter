<template>
  <el-card shadow="never">
    <div class="side-table-container">
      <div class="table-area full">
        <div class="action-filter">
          <div class="actions">
            <el-button type="primary" size="small" @click="handleCreate">{{ $t("AbpIdentityServer['NewClient']") }}</el-button>
          </div>
          <div style="margin-left:50px" class="filters">
            <el-input
              v-model="queryForm.keywords"
              size="small"
              style="margin-right:5px"
              :placeholder="$t('AbpIdentityServer[\'SearchHint\']')"
              prefix-icon="el-icon-search"
            />
            <el-button size="small" @click="handleSearch">{{ $t("AbpIdentityServer['Search']") }}</el-button>
          </div>
        </div>
        <el-table
          :key="tableKey"
          v-loading="listLoading"
          :data="list"
          border
          fit
          highlight-current-row
          size="small"
          style="width: 100%;"
          @selection-change="handleSelectionChange"
        >
          <el-table-column type="selection" width="40" align="center" />
          <el-table-column
            :label="$t('AbpIdentityServer[\'ClientId\']')"
            prop="clientId"
            align="center"
          >
            <template slot-scope="{ row }">
              <span>{{ row.clientId | empty }}</span>
            </template>
          </el-table-column>
          <el-table-column
            :label="$t('AbpIdentityServer[\'ClientName\']')"
            prop="clientName"
            align="center"
          >
            <template slot-scope="{ row }">
              <span>{{ row.clientName | empty }}</span>
            </template>
          </el-table-column>
          <el-table-column
            :label="$t('AbpIdentityServer[\'Operation\']')"
            prop="action"
            align="center"
            width="220"
          >
            <template slot-scope="{ row }">
              <el-button
                size="mini"
                type="primary"
                icon="el-icon-edit"
                @click="handleUpdate(row.id)"
              >
                {{ $t("AbpIdentityServer['Update']") }}
              </el-button>
              <el-button
                size="mini"
                type="danger"
                icon="el-icon-delete"
                @click="handleDelete(row.id)"
              >
                {{ $t("AbpIdentityServer['Delete']") }}
              </el-button>
            </template>
          </el-table-column>
        </el-table>
        <div class="bottom">
          <div>
            <!-- 已选{{ seletedDatas.length }}条 -->
          </div>
          <pagination
            v-show="total > 0"
            :total="total"
            :page.sync="queryForm.page"
            :limit.sync="queryForm.limit"
            @pagination="getList"
          />
        </div>
      </div>
    </div>
    <CreateClientDialog ref="createClientDialog" @success="handleCreateSuccess" />
  </el-card>
</template>

<script>
import {
  getClients, deleteClient
} from '@/api/identity-server/client'
import baseListQuery from '@/utils/abp'
import Pagination from '@/components/Pagination'
import CreateClientDialog from './components/CreateClientDialog'

export default {
  components: { Pagination, CreateClientDialog },
  data() {
    return {
      tableKey: 0,
      list: null,
      total: 0,
      listLoading: true,
      queryForm: Object.assign({
        keywords: undefined
      }, baseListQuery),
      seletedDatas: []
    }
  },
  created() {
    this.getList()
  },
  methods: {
    handleSelectionChange(rows) {
      this.seletedDatas = rows
    },
    handleSearch() {
      this.getList()
    },
    handleCreate() {
      this.$refs.createClientDialog.showDialog()
    },
    handleCreateSuccess() {
      this.getList()
    },
    handleDelete(id) {
      this.$confirm('确定要删除？', '确定', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        deleteClient(id).then(res => {
          this.$notify({
            title: '成功',
            message: '操作成功',
            type: 'success',
            duration: 2000
          })
          this.getList()
        })
      }).catch(() => {})
    },
    handleUpdate(id) {
      this.$router.push({
        name: 'Id4-ClientEditor',
        query: { id }
      })
    },
    getList() {
      this.listLoading = true
      getClients(this.queryForm).then(response => {
        this.list = response.items
        this.total = response.totalCount
        this.listLoading = false
      })
    }
  }
}
</script>

<style>

</style>
