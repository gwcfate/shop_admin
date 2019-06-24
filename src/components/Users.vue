<template>
  <div class="users">
    <!-- 面包屑 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/users' }">用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 搜索 -->
    <div>
      <el-input placeholder="请输入内容" v-model="query" class="input-with-select">
        <el-button slot="append" icon="el-icon-search" @click="search"></el-button>
      </el-input>
      <el-button type="success" plain @click="showAddDialog">用户添加</el-button>
    </div>
    <!-- 用户列表 -->
    <el-table :data="userList" style="width: 100%">
      <el-table-column prop="username" label="姓名" width="180"></el-table-column>
      <el-table-column prop="email" label="邮箱" width="180"></el-table-column>
      <el-table-column prop="mobile" label="电话" width="180"></el-table-column>
      <el-table-column prop="mg_state" label="状态" width="180">
        <template slot-scope="scope">
          <el-switch
            v-model="scope.row.mg_state"
            active-color="#13ce66"
            inactive-color="#ff4949"
            @change="changeState(scope.row)"
          ></el-switch>
        </template>
      </el-table-column>
      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-button
            type="primary"
            icon="el-icon-edit"
            plain
            size="small"
            @click="showEditDialog(scope.row)"
          ></el-button>
          <el-button
            type="danger"
            icon="el-icon-delete"
            plain
            size="small"
            @click="delUser(scope.row.id)"
          ></el-button>
          <el-button type="success" icon="el-icon-check" plain size="small">分配角色</el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 分页功能 -->
    <el-pagination
      layout="total, sizes, prev, pager, next, jumper"
      :total="total"
      :page-sizes="[2, 4, 6, 8]"
      :page-size="pageSize"
      :current-page="currentPage"
      background
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
    ></el-pagination>
    <!-- 添加用户的对话框 -->
    <el-dialog title="添加用户" :visible.sync="addDialogVisible" width="30%">
      <el-form ref="addForm" :model="addForm" :rules="rules" label-width="80px">
        <el-form-item label="用户名" prop="username">
          <el-input placeholder="请添加用户名" v-model="addForm.username"></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="password">
          <el-input placeholder="请输入密码" v-model="addForm.password"></el-input>
        </el-form-item>
        <el-form-item label="邮箱" prop="email">
          <el-input placeholder="请输入邮箱" v-model="addForm.email"></el-input>
        </el-form-item>
        <el-form-item label="电话" prop="mobile">
          <el-input placeholder="请输入电话" v-model="addForm.mobile"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="addDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="addUser">确 定</el-button>
      </span>
    </el-dialog>
    <!-- //修改对话框 -->
    <el-dialog title="修改用户" :visible.sync="editDialogVisible" width="30%">
      <el-form ref="editForm" :model="editForm" :rules="rules" label-width="80px">
        <el-form-item label="用户名">
          <el-tag type="info">{{editForm.username}}</el-tag>
        </el-form-item>
        <el-form-item label="邮箱" prop="email">
          <el-input placeholder="请输入邮箱" v-model="editForm.email"></el-input>
        </el-form-item>
        <el-form-item label="电话" prop="mobile">
          <el-input placeholder="请输入电话" v-model="editForm.mobile"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="editDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="editUser">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      query: "",
      currentPage: 1,
      pageSize: 2,
      total: 0,
      userList: [],
      //控制添加用户矿显示
      addDialogVisible: false,
      //控制修改用户框显示
      editDialogVisible: false,
      // 收集添加用户信息的
      addForm: {
        username: "",
        password: "",
        email: "",
        mobile: ""
      },
      //修改用户数据
      editForm: {
        id: "",
        username: "",
        email: "",
        mobile: ""
      },
      rules: {
        username: [
          { required: true, message: "请输入用户名", trigger: "blur" },
          { min: 3, max: 9, message: "长度在 3 到 9 个字符", trigger: "blur" }
        ],
        password: [
          { required: true, message: "请输密码", trigger: "blur" },
          { min: 6, max: 12, message: "长度在 6 到 12 个字符", trigger: "blur" }
        ],
        email: [
          { type: "email", message: "输入正确格式邮箱", trigger: "blur" }
        ],
        mobile: [
          {
            pattern: /^1\d{10}$/,
            message: "输入正确shoujihao",
            trigger: "blur"
          }
        ]
      }
    };
  },
  methods: {
    // 获取用户列表信息
    getUserList() {
      axios({
        method: "get",
        url: "users",
        params: {
          query: this.query,
          pagenum: this.currentPage,
          pagesize: this.pageSize
        }
      }).then(res => {
        console.log(res);
        if (res.meta.status === 200) {
          this.userList = res.data.users;
          this.total = res.data.total;
        }
      });
    },
    // 分页方法
    handleCurrentChange(val) {
      this.currentPage = val;
      // 重新发送ajax请求
      this.getUserList();
    },
    handleSizeChange(val) {
      this.pageSize = val;
      this.currentPage = 1;
      this.getUserList();
    },
    // 搜索功能
    search() {
      this.currentPage = 1;
      this.getUserList();
    },
    // 删除功能
    delUser(id) {
      this.$confirm("确定要删除么？", "温馨提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          axios({
            method: "delete",
            url: `users/${id}`
          }).then(res => {
            if (res.meta.status === 200) {
              if (this.userList.length === 1 && this.currentPage > 1) {
                this.currentPage--;
              }
              this.getUserList();
              this.$message.success("删除成功");
            }
          });
        })
        .catch(() => {
          this.$message.info("取消删除");
        });
    },
    // 修改用户状态
    changeState(user) {
      axios({
        method: "put",
        url: `users/${user.id}/state/${user.mg_state}`
      }).then(res => {
        if (res.meta.status === 200) {
          this.$message.success("修改成功");
        } else {
          this.$message.error("修改失败");
        }
      });
    },
    //显示添加的用户框
    showAddDialog() {
      this.addDialogVisible = true;
    },
    //添加用户
    addUser() {
      this.$refs.addForm.validate(valid => {
        if (!valid) return false;
        //表单校验成功
        this.axios({
          url: "users",
          method: "post",
          data: this.addForm
        }).then(res => {
          console.log(res);
          if (res.meta.status === 201) {
            //重新渲染最后一页
            this.total++;
            this.currentPage = Math.ceil(this.total / this.pageSize);
            //重新渲染
            this.getUserList();
            // 隐藏模态框
            this.addDialogVisible = false;
            //清空表单
            this.$refs.addForm.resetFields();
          }
        });
      });
    },
    //显示修改用户对话框
    showEditDialog(user) {
      this.editDialogVisible = true;
      //让数据回显
      this.editForm.id = user.id;
      this.editForm.username = user.username;
      this.editForm.email = user.email;
      this.editForm.mobile = user.mobile;
    },
    //修改用户
    editUser() {
      this.$refs.editForm.validate(valid => {
        if (!valid) return false;
        this.axios({
          url: `users/${this.editForm.id}`,
          method: "put",
          data: this.editForm
        }).then(res => {
          console.log(res);
          let {
            meta: { status }
          } = res;
          if (status === 200) {
            this.getUserList();
            this.$refs.editForm.resetFields();
            this.editDialogVisible = false;
          }
        });
      });
    }
  },
  created() {
    //页面一加载发送ajax请求
    this.getUserList();
  }
};
</script>

<style lang="less" scoped>
.el-breadcrumb {
  height: 30px;
  line-height: 30px;
}
.el-input {
  width: 400px;
}
</style>
