<template>
  <div class="acl-roles">
    <div class="acl-roles-header">
      <a-button @click="handleCreate" type="primary">{{ $t('acl.addVisualRole') }}</a-button>
      <a-input-search
        class="ops-input"
        allowClear
        :style="{ display: 'inline', marginLeft: '10px', width: '200px' }"
        :placeholder="`${$t('search')} | ${$t('acl.role')}`"
        v-model="searchName"
        @search="
          () => {
            this.tablePage.currentPage = 1
            this.loadData()
          }
        "
      ></a-input-search>
      <a-checkbox :checked="is_all" @click="handleClickBoxChange">{{ $t('acl.allRole') }}</a-checkbox>
    </div>
    <a-spin :spinning="loading">
      <ops-table
        stripe
        class="ops-stripe-table"
        size="small"
        resizable
        :data="tableData"
        :height="`${windowHeight - 200}px`"
        highlight-hover-row
        :filter-config="{ remote: true }"
        @filter-change="filterTableMethod"
      >
        <vxe-table-column
          field="name"
          :title="$t('acl.role')"
          :min-width="150"
          align="left"
          fixed="left"
          sortable
          show-overflow
        >
        </vxe-table-column>

        <!-- 2 -->
        <vxe-table-column field="is_app_admin" :title="$t('admin')" :min-width="100" align="center">
          <template #default="{ row }">
            <a-icon type="check" v-if="row.is_app_admin" />
          </template>
        </vxe-table-column>

        <vxe-table-column field="id" :title="$t('acl.inheritedFrom')" :min-width="150">
          <template #default="{ row }">
            <a-tag color="cyan" v-for="role in id2parents[row.id]" :key="role.id">{{ role.name }}</a-tag>
          </template>
        </vxe-table-column>

        <vxe-table-column
          field="uid"
          :title="$t('acl.visualRole')"
          :width="120"
          align="center"
          :filters="visualRoleFilters"
          :filterMultiple="false"
          :filter-method="
            ({ value, row }) => {
              return value === !row.uid
            }
          "
        >
          <template #default="{ row }">
            {{ row.uid ? $t('no') : $t('yes') }}
          </template>
        </vxe-table-column>

        <vxe-table-column field="action" :title="$t('operation')" :width="120" fixed="right">
          <template #default="{ row }">
            <a-space>
              <a-tooltip :title="$t('acl.resourceList')">
                <a
                  v-if="$route.name !== 'acl_roles'"
                  @click="handleDisplayUserResource(row)"
                ><a-icon
                  type="file-search"
                /></a>
              </a-tooltip>
              <a-tooltip
                :title="$t('acl.userList')"
                v-if="!row.uid"
              ><a @click="handleDisplayUserUnderRole(row)"><a-icon type="team"/></a
              ></a-tooltip>
              <a @click="handleEdit(row)"><a-icon type="edit"/></a>
              <a-popconfirm
                :title="$t('confirmDelete')"
                @confirm="handleDelete(row)"
                @cancel="cancel"
                :okText="$t('yes')"
                :cancelText="$t('no')"
              >
                <a style="color: red"><a-icon type="delete"/></a>
              </a-popconfirm>
            </a-space>
          </template>
        </vxe-table-column>
      </ops-table>
      <a-pagination
        size="small"
        show-size-changer
        show-quick-jumper
        :current="tablePage.currentPage"
        :total="tablePage.total"
        :show-total="
          (total, range) =>
            $t('pagination.total', {
              range0: range[0],
              range1: range[1],
              total,
            })
        "
        :page-size="tablePage.pageSize"
        :default-current="1"
        :page-size-options="pageSizeOptions"
        @change="pageOrSizeChange"
        @showSizeChange="pageOrSizeChange"
        :style="{ marginTop: '10px', textAlign: 'right' }"
      />
    </a-spin>

    <roleForm ref="roleForm" :allRoles="allRoles" :id2parents="id2parents" :handleOk="handleOk"></roleForm>
    <resource-user-form ref="ruf"></resource-user-form>
    <users-under-role-form ref="usersUnderRoleForm" :allRoles="allRoles"></users-under-role-form>
  </div>
</template>

<script>
import { mapState } from 'vuex'
import roleForm from './module/roleForm'
import resourceUserForm from './module/resourceUserForm'
import usersUnderRoleForm from './module/usersUnderRoleForm'
import { deleteRoleById, searchRole } from '@/modules/acl/api/role'

export default {
  name: 'Roles',
  components: {
    roleForm,
    resourceUserForm,
    usersUnderRoleForm,
  },
  data() {
    return {
      loading: false,
      tablePage: {
        total: 0,
        currentPage: 1,
        pageSize: 50,
      },
      is_all: this.$route.name === 'acl_roles',
      tableData: [],
      allRoles: [],
      id2parents: {},
      pageSizeOptions: ['20', '50', '100', '200'],
      searchName: '',
      filterTableValue: { user_role: 1, user_only: 0 },
      visualRoleFilters: [
        { label: this.$t('yes'), value: 1 },
        { label: this.$t('no'), value: 0 }
      ]
    }
  },
  computed: {
    ...mapState({
      windowHeight: (state) => state.windowHeight,
    }),
  },
  watch: {
    '$route.name': function(newName, oldName) {
      this.tablePage = {
        total: 0,
        currentPage: 1,
        pageSize: 50,
      }
      this.tableData = []
      this.allRoles = []
      this.loadData()
      this.initData()
    },
    searchName: {
      immediate: true,
      handler(newVal) {
        if (!newVal) {
          this.tablePage.currentPage = 1
          this.loadData()
        }
      },
    },
  },
  mounted() {
    this.initData()
  },
  inject: ['reload'],
  methods: {
    handleClickBoxChange() {
      this.is_all = !this.is_all
      this.$nextTick(() => {
        this.tablePage.currentPage = 1
        this.loadData()
      })
    },
    initData() {
      console.log(111)
      searchRole({
        app_id: this.$route.name.split('_')[0],
        page_size: 9999,
      }).then((res) => {
        this.allRoles = res.roles
      })
    },
    loadData() {
      console.log(222)
      this.loading = true
      const { currentPage, pageSize } = this.tablePage
      searchRole({
        ...this.filterTableValue,
        app_id: this.$route.name.split('_')[0],
        page_size: pageSize,
        page: currentPage,
        is_all: this.is_all,
        q: this.searchName,
      })
        .then((res) => {
          this.tableData = res.roles
          this.id2parents = res.id2parents
          this.tablePage.total = res.numfound
          this.loading = false
        })
        .catch(() => {
          this.loading = false
        })
    },
    handleDisplayUserResource(record) {
      this.$refs.ruf.loadUserResource(record)
    },
    handleDisplayUserUnderRole(record) {
      this.$refs.usersUnderRoleForm.handleProcessRole(record.id)
    },
    handleEdit(record) {
      this.$refs.roleForm.handleEdit(record)
    },
    handleDelete(record) {
      this.deleteRole(record.id)
    },
    handleOk() {
      this.loadData()
      this.initData()
    },

    handleCreate() {
      this.$refs.roleForm.handleCreate()
    },

    deleteRole(id) {
      deleteRoleById(id, { app_id: this.$route.name.split('_')[0] }).then((res) => {
        this.$message.success(this.$t('deleteSuccess'))
        this.handleOk()
      })
    },
    cancel(e) {
      return false
    },
    pageOrSizeChange(currentPage, pageSize) {
      this.tablePage.currentPage = currentPage
      this.tablePage.pageSize = pageSize
      this.loadData()
    },
    filterTableMethod({ column, property, values, datas, filterList, $event }) {
      if (property === 'uid') {
        if (values[0] === 1) {
          this.filterTableValue = { user_role: 0 }
        } else if (values[0] === 0) {
          this.filterTableValue = {
            user_only: 1,
          }
        } else {
          this.filterTableValue = {
            user_role: 1,
            user_only: 0,
          }
        }
      }
      this.tablePage.currentPage = 1
      this.loadData()
    },
  },
}
</script>

<style lang="less">

.acl-roles {
  border-radius: @border-radius-box;
  background-color: #fff;
  height: calc(100vh - 64px);
  margin-bottom: -24px;
  padding: 20px;
  .acl-roles-header {
    width: 100%;
    display: inline-flex;
    margin-bottom: 20px;
    align-items: center;
    .ant-checkbox-wrapper {
      margin-left: auto;
    }
  }
  .vxe-table--filter-body {
    min-height: 60px;
  }
}
</style>
