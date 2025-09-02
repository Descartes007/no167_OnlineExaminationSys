<!--
 * 顶部信息栏
 *
 * @Author: ShanZhu
 * @Date: 2023-11-23
-->
<template>
  <header id="topbar">

    <el-dialog
      :append-to-body="true"
      style="z-index:2028px;"
      title="修改密码"
      :visible.sync="dialogVisible"
      width="30%"
    >
      <el-form status-icon ref="ruleForm2" label-width="100px" class="demo-ruleForm">
        <el-form-item label="旧密码" prop="pass">
          <el-input type="password" v-model="oldPsw" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="新密码" prop="pass">
          <el-input type="password" v-model="newPsw" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="确认新密码" prop="checkPass">
          <el-input type="password" v-model="confirmNewPsw" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="resetPsw"
          >确 定</el-button
        >
      </span>
    </el-dialog>

    <el-row>
      <el-col :span="3" class="topbar-left">
        <span class="title" @click="index()" style="font-size: 1.2vw;">在线考试系统管理台</span>
      </el-col>
      <el-col :span="21" class="topbar-right">
        <i class="el-icon-menu" @click="toggle()"></i>
        <div class="user">
          <span>{{ user.userName }}</span>
          <img
            src="@/assets/img/userimg.png"
            class="user-img"
            ref="img"
            @click="showSetting()"
          />
          <transition name="fade">
            <div class="out" ref="out" v-show="login_flag">
              <ul>
                <li v-if="role==0"><a @click="dialogVisible = true" href="javascript:;">修改密码</a></li>
                <li class="exit" @click="exit()">
                  <a href="javascript:;">退出登录</a>
                </li>
              </ul>
            </div>
          </transition>
        </div>
      </el-col>
    </el-row>

  </header>
</template>

<script>
import { mapState, mapMutations } from "vuex";
export default {
  data() {
    return {
      login_flag: false,
      user: {
        //用户信息
        userName: null,
        userId: null,
      },
      dialogVisible: false,
      oldPsw: "",
      newPsw: "",
      confirmNewPsw: "",
      role:0,
    };
  },
  created() {
    this.getUserInfo();

    this.role = this.$cookies.get("role")
  },
  computed: mapState(["flag", "menu"]),
  methods: {
    // 管理员重置密码
    resetPsw() {
      if(this.oldPsw == "") {
        this.$message('请输入旧密码');
        return;
      }
      if(this.newPsw == "") {
        this.$message('请输入新密码');
        return;
      }
      if(this.confirmNewPsw != this.newPsw) {
        this.$message('两次新密码不一致');
        return;
      }
      this.$axios(`/api/admin/resetPsw/${this.user.userId}/${this.oldPsw}/${this.newPsw}`).then(res => {
        let status = res.data.code
        if(status == 200) {
          if(res.data.data != true) {
            this.$message(res.data.data);
          } else {
            // 修改成功
            this.$message("修改成功");
            this.dialogVisible = false;
            this.oldPsw = "";
            this.newPsw = "";
            this.confirmNewPsw = "";
          }
        }
      })
    },
    //显示、隐藏退出按钮
    showSetting() {
      this.login_flag = !this.login_flag;
    },
    //左侧栏放大缩小
    ...mapMutations(["toggle"]),
    getUserInfo() {
      //获取用户信息
      let userName = this.$cookies.get("cname");
      let userId = this.$cookies.get("cid");
      this.user.userName = userName;
      this.user.userId = userId;
    },
    index() {
      this.$router.push({ path: "/index" });
    },
    exit() {
      let role = this.$cookies.get("role");
      this.$router.push({ path: "/" }); //跳转到登录页面
      this.$cookies.remove("cname"); //清除cookie
      this.$cookies.remove("cid");
      this.$cookies.remove("role");
      this.$cookies.remove("rb_token") //清除cookie
      this.$cookies.remove("rb_role")
      if (role == 0) {
        this.menu.pop();
      }
    },
  },
};
</script>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
#topbar {
  position: relative;
  z-index: 10;
  background-color: #3186cb;
  height: 80px;
  line-height: 80px;
  color: #fff;
  box-shadow: 5px 0px 10px rgba(0, 0, 0, 0.5);
}
#topbar .topbar-left {
  height: 80px;
  display: flex;
  justify-content: center;
  background: rgba(0, 0, 0, 0.05);
  overflow: hidden;
}
.topbar-left .icon-kaoshi {
  font-size: 60px;
}
.topbar-left .title {
  font-size: 20px;
  cursor: pointer;
}
.topbar-right {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.topbar-right .user-img {
  width: 50px;
  height: 50px;
  border-radius: 50%;
}
.topbar-right .el-icon-menu {
  font-size: 30px;
  margin-left: 20px;
}
.topbar-right .user {
  position: relative;
  margin-right: 40px;
  display: flex;
}
.topbar-right .user .user-img {
  margin-top: 15px;
  margin-left: 10px;
  cursor: pointer;
}
.user .out {
  font-size: 14px;
  position: absolute;
  top: 80px;
  right: 0px;
  background-color: #fff;
  box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
  padding: 12px;
}
.user .out ul {
  list-style: none;
}
.user .out ul > li {
  height: 26px;
  line-height: 26px;
}
.out a {
  text-decoration: none;
  color: #000;
}
.out .exit {
  margin-top: 4px;
  padding-top: 4px;
  border-top: 1px solid #ccc;
}
</style>
