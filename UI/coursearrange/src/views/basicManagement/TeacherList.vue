<template>
  <div>
    <!-- 功能 使用中 -->
    <div class="header-menu">
      <el-input placeholder="搜索教师" v-model="keyword" @clear="inputListener" clearable>
        <el-button slot="append" type="primary" icon="el-icon-search" @click="searchTeacher">搜索</el-button>
      </el-input>
      <el-button type="primary" @click="addTeacher()" class="addbtn">添加教师信息</el-button>
    </div>
    <!-- 讲师列表 -->
    <el-table :data="teacherData" size="mini" :stripe="true" :highlight-current-row="true">
      <el-table-column label="序号" type="selection"></el-table-column>
      <el-table-column prop="teacherNo" label="编号" ></el-table-column>
      <el-table-column prop="username" label="用户名" ></el-table-column>
      <el-table-column prop="realname" label="姓名"  ></el-table-column>
      <el-table-column prop="jobtitle" label="职称"  ></el-table-column>
      <el-table-column prop="age" label="年龄" ></el-table-column>
      <el-table-column prop="telephone" label="电话"  ></el-table-column>
      <el-table-column prop="email" label="邮件" ></el-table-column>
      <el-table-column prop="address" label="地址"></el-table-column>
      <el-table-column prop="operation" label="操作" width="150px">
        <template slot-scope="scope">
          <el-button type="danger" size="mini" @click="deleteById(scope.$index, scope.row)">删除</el-button>
          <el-button type="primary" size="mini" @click="editById(scope.$index, scope.row)">编辑</el-button>
        </template>
      </el-table-column>
    </el-table>

    <!-- 弹出表单编辑讲师 -->
    <el-dialog title="编辑讲师" :visible.sync="visibleForm">
      <el-form
        :model="editFormData"
        label-position="left"
        label-width="80px"
        :rules="addTeacherRules"
      >
        <el-form-item label="编号" prop="teacherNo">
          <el-input v-model="editFormData.teacherNo" autocomplete="off" disabled></el-input>
        </el-form-item>
        <el-form-item label="昵称" prop="username">
          <el-input v-model="editFormData.username" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="姓名" prop="realname">
          <el-input v-model="editFormData.realname" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="职称" prop="jobtitle">
          <!-- <el-input v-model="editFormData.jobtitle" autocomplete="off"></el-input> -->
          <el-select v-model="editFormData.jobtitle" placeholder="请选择职称" style="width: 100%; float: left;">
            <el-option
              v-for="item in jobtitles"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="所授科目" prop="teach">
          <!-- <el-input v-model="editFormData.teach" autocomplete="off"></el-input> -->
          <el-select v-model="editFormData.teach" placeholder="请选择所授科目" style="width: 100%; float: left;">
            <el-option
              v-for="item in teachs"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="手机" prop="telephone">
          <el-input v-model="editFormData.telephone" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="邮件" prop="email">
          <el-input v-model="editFormData.email" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="地址" prop="address">
          <el-input v-model="editFormData.address" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="年龄" prop="age">
          <el-input v-model="editFormData.age" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="visibleForm = false">取 消</el-button>
        <el-button type="primary" @click="commit()">提 交</el-button>
      </div>
    </el-dialog>

    <!-- 弹出表单添加讲师 -->
    <el-dialog title="添加讲师" :visible.sync="visibleAddForm">
      <el-form
        :model="addTeacherForm"
        label-position="left"
        label-width="80px"
        :rules="addTeacherRules"
      >
        <el-form-item label="编号" prop="teacherNo">
          <el-input v-model="addTeacherForm.teacherNo" autocomplete="off" disabled></el-input>
        </el-form-item>
        <el-form-item label="昵称" prop="username">
          <el-input v-model="addTeacherForm.username" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="姓名" prop="realname">
          <el-input v-model="addTeacherForm.realname" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="职称" prop="jobtitle">
          <!-- <el-input v-model="addTeacherForm.jobtitle" autocomplete="off"></el-input> -->
          <el-select v-model="addTeacherForm.jobtitle" placeholder="请选择职称" style="width: 100%; float: left;">
            <el-option
              v-for="item in jobtitles"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="所授科目" prop="teach">
          <!-- <el-input v-model="addTeacherForm.teach" autocomplete="off"></el-input> -->
          <el-select v-model="addTeacherForm.teach" placeholder="请选择所授科目" style="width: 100%; float: left;">
            <el-option
              v-for="item in teachs"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="手机" prop="telephone">
          <el-input v-model="addTeacherForm.telephone" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="邮件" prop="email">
          <el-input v-model="addTeacherForm.email" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="地址" prop="address">
          <el-input v-model="addTeacherForm.address" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="年龄" prop="age">
          <el-input v-model="addTeacherForm.age" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="visibleAddForm = false">取 消</el-button>
        <el-button type="primary" @click="addCommit()">提 交</el-button>
      </div>
    </el-dialog>

    <!-- 上一页，当前页，下一页 -->
    <div class="footer-button">
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page.sync="page"
        :page-size="pageSize"
        layout="total, prev, pager, next"
        :total="total"
      ></el-pagination>
    </div>
  </div>
</template>

<script>
export default {
  name: "TeacherList",
  data() {
    return {
      jobtitles: [{
          value: '三级教师',
          label: '三级教师'
        }, {
          value: '二级教师',
          label: '二级教师'
        }, {
          value: '一级教师',
          label: '一级教师'
        }, {
          value: '高级教师',
          label: '高级教师'
        }, {
          value: '正高级教师',
          label: '正高级教师'
        }],
      teachs: [],
      teacherData: [],
      keyword: "",
      page: 1,
      pageSize: 10,
      total: 0,
      visibleForm: false,
      visibleAddForm: false,
      editFormData: [],
      addTeacherRules: {
        username: [
          { required: true, message: "请输入昵称", trigger: "blur" },
          { min: 2, max: 12, message: "长度在 2 到 12 个字符", trigger: "blur" }
        ],
        realname: [
          { required: true, message: "请输入真实姓名", trigger: "blur" }
        ],
        jobtitle: [{ required: true, message: "请输入职称", trigger: "blur" }],
        teach: [{ required: true, message: "请输入教授科目", trigger: "blur" }],
        telephone: [
          { required: true, message: "请输入手机号码", trigger: "blur" }
        ],
        email: [{ required: true, message: "请输入邮件", trigger: "blur" }],
        address: [{ required: true, message: "请输入地址", trigger: "blur" }],
        age: [{ required: true, message: "请输入年龄", trigger: "blur" }]
      },
      addTeacherForm: {
        teacherNo: "",
        username: "",
        realname: "",
        jobtitle: "",
        email: "",
        teach: "",
        telephone: "",
        address: "",
        age: ""
      }
    };
  },
  mounted() {
    this.allTeacher();
  },

  methods: {
    addTeacher() {
      // 在弹出添加表单之前从后台获取讲师的编号
      this.$axios
        .get("http://localhost:8080/teacher/no")
        .then(res => {
          if (res.data.code == 0) {
            let number = parseInt(res.data.message) + 1;
            // 给讲师编号赋值
            this.addTeacherForm.teacherNo = number.toString();
          }
        })
        .catch(error => {});
      this.visibleAddForm = true;

      this.$axios
        .get("http://localhost:8080/courseinfo/selectCourse")
        .then(res => {
          if (res.data.code == 0) {
            res.data.data.forEach(el => {
              this.teachs.push({
              key: el.id,
              value: el.id,
              label: el.courseName
            })
            });
          }
        })
        .catch(error => {});
      this.visibleAddForm = true;


    },

    // 提交添加讲师的表单
    addCommit() {
      this.$axios
        .post("http://localhost:8080/teacher/add", this.addTeacherForm)
        .then(res => {
          if (res.data.code == 0) {
            this.allTeacher();
            this.visibleAddForm = false;
            this.$message({ message: "添加讲师成功", type: "success" });
          }
        })
        .catch(error => {
          this.$message.error("添加讲师失败");
        });
    },

    // 提交更新讲师信息
    commit() {
      // let modifyData = this.editFormData
      this.modifyTeacher(this.editFormData);
    },

    inputListener() {
      this.allTeacher();
    },

    handleSizeChange() {},

    handleCurrentChange(v) {
      this.page = v;
      if (this.keyword == "") {
        this.allTeacher();
      } else {
        this.searchTeacher();
      }
    },

    deleteById(index, row) {
      this.deleteTeacherById(row.id);
    },

    editById(index, row) {
      let modifyId = row.id;
      this.editFormData = row;
      this.visibleForm = true;
    },

    /**
     * 根据ID更新讲师
     */
    modifyTeacher(modifyData) {
      this.$axios
        .post("http://localhost:8080/teacher/modify", modifyData)
        .then(res => {
          this.$message({ message: "更新成功", type: "success" });
          this.allTeacher();
          this.visibleForm = false;
        })
        .catch(error => {
          this.$message.error("更新失败");
        });
    },

    /**
     * 关键词搜索讲师
     */
    searchTeacher() {
      this.page = 1;
      this.$axios
        .get(
          "http://localhost:8080/teacher/search/" +
            this.page +
            "/" +
            this.keyword
        )
        .then(res => {
          let ret = res.data.data;
          this.teacherData = ret.records;
          this.total = ret.total;
          this.$message({ message: "查询成功", type: "success" });
        })
        .catch(error => {
          this.$message.error("查询失败");
        });
    },

    /**
     * 根据ID删除讲师
     */
    deleteTeacherById(id) {
      this.$axios
        .delete("http://localhost:8080/teacher/delete/" + id)
        .then(res => {
          this.allTeacher();
          this.$message({ message: "删除成功", type: "success" });
        })
        .catch(error => {
          this.$message.error("删除失败");
        });
    },

    /**
     * 获取所有讲师，带分页
     */
    allTeacher() {
      this.$axios
        .get("http://localhost:8080/teacher/query/" + this.page)
        .then(res => {
          let ret = res.data.data;
          this.teacherData = ret.records;
          this.total = ret.total;
          // this.$message({message:'查询成功', type: 'success'})
        })
        .catch(error => {
          this.$message.error("查询讲师列表失败");
        });
    },
    handleUploadSuccess() {
      this.allTeacher();
    }
  }
};
</script>

<style lang="less" scoped>
.addbtn {
  margin-left: 15px;
}

.el-input-group {
  width: 40%;
}

.header-menu {
  margin-bottom: 5px;
  padding: 0;
  text-align: left;
  margin-bottom: 5px;
}

.footer-button {
  margin-top: 10px;
}
</style>