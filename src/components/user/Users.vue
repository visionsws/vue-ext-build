<template>
    <div>
      <el-breadcrumb separator-class="el-icon-arrow-right">
        <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
        <el-breadcrumb-item>用户管理</el-breadcrumb-item>
        <el-breadcrumb-item>用户列表</el-breadcrumb-item>
      </el-breadcrumb>
      <el-card>
        <el-row :gutter="20">
          <el-col :span="7">
            <el-input placeholder="请输入内容" v-model="quaryInfo.query.area_name" clearable
            @clear="getReportSale">
              <el-button slot="append" icon="el-icon-search" @click="getReportSale"></el-button>
            </el-input>
          </el-col>
          <el-col :span="4">
            <el-button type="primary" @click="addUserVisible = true">添加用户</el-button>
          </el-col>
        </el-row>
        <!--用户列表区域-->
        <el-table :data="userList" border stripe>
          <el-table-column type="index" label="序列"></el-table-column>
          <el-table-column label="区域名称" prop="areaName"></el-table-column>
          <el-table-column label="销售名称" prop="saleName"></el-table-column>
          <el-table-column label="产品类型" prop="productType"></el-table-column>
          <el-table-column label="产品名称" prop="productName"></el-table-column>
          <el-table-column label="销量" prop="saleNum"></el-table-column>
          <el-table-column label="状态">
            <template v-slot="scope">
              <el-tag :type="scope.row.saleNum > 200 ? 'success' : 'danger'"
              @click="changeSaleNum(scope.row)">{{scope.row.saleNum}}</el-tag>
            </template>
          </el-table-column>
          <el-table-column label="操作" width="180px">
            <template >
              <el-button type="primary" icon="el-icon-edit" size="mini"></el-button>
              <el-button type="danger" icon="el-icon-delete" size="mini"></el-button>
              <el-tooltip effect="dark" content="分配任务" placement="top-end" :enterable="false">
                <el-button type="warning" icon="el-icon-setting" size="mini"></el-button>
              </el-tooltip>
            </template>
          </el-table-column>
        </el-table>
        <!--分页功能区域-->
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="quaryInfo.currentPage"
          :page-sizes="[1, 2, 5, 10]"
          :page-size="quaryInfo.pageSize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total">
        </el-pagination>
      </el-card>
      <!--添加用户对话框-->
      <el-dialog
        title="添加用户"
        :visible.sync="addUserVisible"
        width="50%"
        @close="addUserClosed">
        <!--内容主体区域-->
        <el-form :model="addForm" :rules="addFormRules" ref="addFormRef" label-width="70px">
          <el-form-item label="用户名" prop="username">
            <el-input v-model="addForm.username"></el-input>
          </el-form-item>
          <el-form-item label="密码" prop="password">
            <el-input v-model="addForm.password"></el-input>
          </el-form-item>
          <el-form-item label="邮箱" prop="email">
            <el-input v-model="addForm.email"></el-input>
          </el-form-item>
          <el-form-item label="手机" prop="mobile">
            <el-input v-model="addForm.mobile"></el-input>
          </el-form-item>
        </el-form>
        <!--底部区域-->
        <span slot="footer" class="dialog-footer">
          <el-button @click="addUserVisible = false">取 消</el-button>
          <el-button type="primary" @click="addUser">确 定</el-button>
        </span>
      </el-dialog>
    </div>
</template>

<script>
  export default {
    name: 'Users',
    data() {
      var checkMobile = (rule, value, cb) => {
        // 验证手机号的正则表达式
        const regMobile = /^(0|86|17951)?(13[0-9]|15[012356789]|17[678]|18[0-9]|14[57])[0-9]{8}$/
        if (regMobile.test(value)) {
          return cb()
        }
        cb(new Error('请输入合法的手机号'))
      }
      return {
        quaryInfo: {
          currentPage: 1,
          pageSize: 10,
          query: {
            area_name: ''
          }
        },
        total: 0,
        userList: [],
        addUserVisible: false,
        // 添加用户的表单数据
        addForm: {
          username: '',
          password: '',
          email: '',
          mobile: ''
        },
        // 添加表单的验证规则对象
        addFormRules: {
          username: [
            { required: true, message: '请输入用户名称', trigger: 'blur' },
            { min: 3, max: 10, message: '用户名长度在 3 到 10 个字符', trigger: 'blur' }
          ],
          password: [
            { required: true, message: '请输入密码', trigger: 'blur' },
            { min: 6, max: 10, message: '密码长度在 3 到 10 个字符', trigger: 'blur' }
          ],
          email: [
            { required: true, message: '请输入邮箱地址', trigger: 'blur' },
            { type: 'email', message: '请输入正确的邮箱地址', trigger: 'blur' }
          ],
          mobile: [
            { required: true, message: '请输入手机号码', trigger: 'blur' },
            { validator: checkMobile, trigger: ['blur', 'change'] }
            // { pattern: /^[0-9]*$/, message: '手机号码必须为数字值', trigger: 'blur' }
            // { type: 'number', message: '手机号码必须为数字值', trigger: ['blur', 'change'] }
          ]
        }
      }
    },
    created () {
      this.getReportSale()
    },
    methods: {
      async getReportSale() {
        const { data: res } = await this.$http.post('/sale/getReportSaleList', this.quaryInfo)
        if (res.code !== 200) {
          return this.$message.error('获取数据失败!')
        }
        this.userList = res.content.records
        this.total = res.content.total
      },
      handleSizeChange(val) {
        console.log(`每页 ${val} 条`)
        this.quaryInfo.pageSize = val
        this.getReportSale()
      },
      handleCurrentChange(val) {
        console.log(`当前页: ${val}`)
        this.quaryInfo.currentPage = val
        this.getReportSale()
      },
      async changeSaleNum(rowInfo) {
        const { data: res } = await this.$http.post(`/sale/change/${rowInfo.id}/${rowInfo.saleNum}`)
        if (res.code !== 200) {
          return this.$message.error('获取数据失败!')
        }
        console.log(res)
      },
      // 监听添加用户对话框的关闭事件
      addUserClosed() {
        this.$refs.addFormRef.resetFields()
      },
      // 点击确定时,添加新用户
      addUser() {
        this.$refs.addFormRef.validate(async (valid) => {
          if (!valid) {
            console.log('error submit!!')
            return false
          } else {
            // 这里添加保存用户的方法
            const { data: res } = await this.$http.post('/user/saveUser', this.addForm)
            if (res.code !== 200) {
              this.$message.error('保存失败！')
            }
            this.$message.success('保存成功！')
            this.addUserVisible = false
            this.getReportSale()
          }
        })
      }
    }
  }
</script>

<style lang="less" scoped>

</style>
