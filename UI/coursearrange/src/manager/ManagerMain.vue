<template>
  <!-- 后台管理系统主界面 -->
  <div class="wrapper">
    <el-container>
      <el-header>
        <!-- 头 -->
        <span style="text-align: left; font-size: 24px; float: left">高 校 智 能 排 课 管 理 系 统</span>
        <!-- 系统标题 -->
        <div style="text-align: right; font-size: 15px; float: right">
          <el-dropdown @command="handleCommand">
          <i class="el-icon-setting" style="margin-right: 13px"></i>
          <el-dropdown-menu slot="dropdown">
            <el-dropdown-item command="center">个人中心</el-dropdown-item>
            <el-dropdown-item command="updatePassword">修改密码</el-dropdown-item>
            <el-dropdown-item command="exit">退出</el-dropdown-item>
          </el-dropdown-menu>
        </el-dropdown>您好，<span>{{name}}</span>
        </div>
      </el-header>
      <el-container>
        <el-aside width="200px">
          <!-- 侧边 -->
          <!-- 默认展开的索引default-active -->
          <el-menu :default-active="default_active" @select="handleSelect" unique-opened>
            <el-menu-item index="0">
              <template slot="title">
                <router-link to="/systemdata" class="links">
                  <i class="el-icon-setting"></i>系统数据
                </router-link>
              </template>
            </el-menu-item>

            <el-submenu index="1">
              <template slot="title">
                <i class="el-icon-s-data"></i>排课管理
              </template>
              <el-menu-item index="1-1" v-if="!isTeacher">
                <router-link to="/classtasklist" class="links">课程计划</router-link>
              </el-menu-item>
              <el-menu-item index="1-2">
                <router-link to="/coursetable" class="links">查看课表</router-link>
              </el-menu-item>
            </el-submenu>

            <el-submenu index="2">
              <template slot="title">
                <i class="el-icon-reading"></i>基本管理
              </template>
                <el-menu-item index="2-1" v-if="!isTeacher">
                  <router-link to="/teacherlist" class="links">教师管理</router-link>
                </el-menu-item>
                <el-menu-item index="2-2">
                  <router-link to="/studentlist" class="links">学生管理</router-link>
                </el-menu-item>
                <el-menu-item index="2-3">
                  <router-link to="/classmanager" class="links">班级管理</router-link>
                </el-menu-item>
                <el-menu-item index="2-4">
                  <router-link to="/courseinfolist" class="links">课程管理</router-link>
                </el-menu-item>
            </el-submenu>
            <el-submenu index="3" v-if="!isTeacher">
              <template slot="title">
                <i class="el-icon-office-building"></i>教学设施
              </template>
              <el-menu-item index="3-1">
                <router-link class="links" to="/teachbuildinglist">教学楼管理</router-link>
              </el-menu-item>
              <el-menu-item index="3-2">
                <router-link to="/classroomlist" class="links">教室列表</router-link>
              </el-menu-item>
            </el-submenu>
          </el-menu>
        </el-aside>

        <el-main>
          <!-- Main区域，数据显示 -->
          <router-view></router-view>
        </el-main>

      </el-container>
      <!-- 显示系统时间 -->
      <el-footer>{{time}}</el-footer>
    </el-container>
  </div>
</template>

<script>
export default {
  name: "ManagerMain",
  data() {
    return {
      time: "",
      default_active: "0",
      name: '用户名',
    };
  },
  computed: {
    isTeacher:()=>{
      return window.localStorage.getItem('teacher') != null;
    }
  },
  mounted() {
    setInterval(() => {
      this.getTime();
    }, 1000);
    
    let admin = window.localStorage.getItem('admin')
    if(admin != null){
      this.name = (JSON.parse(admin)).realname
    } else {
      let teacher = window.localStorage.getItem('teacher')
      if (teacher != null) {
        this.name = (JSON.parse(teacher)).realname
      }
    }
  },

  methods: {
    // 下拉菜单功能，退出、个人中心
    handleCommand(command) {
      // alert(command)
      if (command == 'exit') {
        localStorage.removeItem('token')
        localStorage.removeItem('admin')
        localStorage.removeItem('teacher')
        // 判断，返回指定页面
        this.$router.push('/')
      } else if (command == 'center') {
        // 跳转到个人中心
      } else if (command == 'updatePassword') {
        // 修改密码页面
        this.$router.push('/updatepass')
      }
      
    },

    // 获取系统时间
    getTime() {
      this.time = new Date().toLocaleString();
    },

    // 展开一个菜单
    handleSelect(val) {
      this.default_active = val;
    }
  }
};
</script>

<style lang="less" scoped>
.wrapper {
  height: 100%;
  width: 100%;
  .a {
    text-decoration: none !important;
    color: #303133 !important;
  }
  .links {
    text-decoration: none !important;
    color: #303133 !important;
  }
}

.el-container {
  height: 100%;
  padding: 0;
  margin: 0;
  width: 100%;
}

.menuItem {
  background-color: #146cef;
}

// .el-header {
//   background-color: #5199f2;
//   color: #333;
//   text-align: center;
//   line-height: 60px;
// }

.el-header,.el-footer {
  background-color: #7f98b7;
  color: #333;
  text-align: center;
  line-height: 60px;
}

.el-aside {
  background-color: #d3dce6;
  color: #333;
  text-align: center;
  line-height: 200px;
}

.el-main {
  background-color: #e9eef3;
  color: #333;
  text-align: center;
  padding: 10px;
}

body > .el-container {
  margin-bottom: 40px;
}

.el-container:nth-child(5) .el-aside,
.el-container:nth-child(6) .el-aside {
  line-height: 260px;
}

.el-container:nth-child(7) .el-aside {
  line-height: 320px;
}
</style>