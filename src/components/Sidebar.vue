<template>
  <div class="sidebar">
    <el-row class="tac">
      <el-col class="col" :span="12">
        <el-menu
          default-active="2"
          class="el-menu-vertical-demo"
          background-color="#545c64"
          text-color="#fff"
          active-text-color="#ffd04b"
        >
          <Avatar />

          <router-link to="/note" title="笔记">
            <el-menu-item index="1">
              <i class="iconfont icon-notebook"></i>
            </el-menu-item>
          </router-link>

          <router-link to="/notebooks" title="笔记本">
            <el-menu-item index="2">
              <i class="iconfont icon-note"></i>
            </el-menu-item>
          </router-link>

          <router-link to="/trash" title="回收站">
            <el-menu-item index="3">
              <i class="iconfont icon-trash"></i>
            </el-menu-item>
          </router-link>

          <el-menu-item class="logout" @click="logout" @clickonLogout index="4">
            <i
              class="iconfont icon-logout"

              title="退出登录"
            ></i>
          </el-menu-item>
        </el-menu>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import Avatar from "./Avatar.vue";
import Auth from "../api/auth";

export default {
  name: "Sidebar",
  components: {Avatar},
  methods: {
    logout() {
      Auth.logout()
        .then(data => {
          this.$router.push({path: 'login'})

          this.$message({
            message: '已退出登录',
            type: 'warning'
          });

        })
    }
  }
}


</script>

<style lang="less" scoped>
.sidebar {
  height: 100vh;
  width: 70px;

  .tac {
    width: 100%;
    height: 100%;

    .col {
      width: 100%;
      height: 100%;
      position: relative;

      .el-menu-vertical-demo {
        height: 100%;
        text-align: center;

        .iconfont {
          font-size: 22px;
        }

        .logout {
          position: absolute;
          bottom: 10px;
          width: 100%;
        }
      }
    }
  }
}
</style>

