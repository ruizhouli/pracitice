<template>
  <div class="login_box">
    <main class="mainBox">
      <h1 class="title">CRM客户管理系统</h1>
      <p class="tip">为保护企业的数据安全，请妥善保管密码</p>

      <section class="loginBox">
        <el-form ref="form" :model="form" :rules="rules">
          <!-- 用户名 -->
          <el-form-item prop="user">
            <div class="form">
              <el-input
                placeholder="请输入用户名/手机号/邮箱"
                prefix-icon="icon-yonghuming iconfont"
                v-model="form.user"
              ></el-input>
            </div>
          </el-form-item>
          <!-- 密码 -->
          <el-form-item prop="pass">
            <div class="form open">
              <el-input
                placeholder="请输入登录密码"
                prefix-icon="iconfont icon-mima"
                v-model="form.pass"
                show-password
              ></el-input>
            </div>
          </el-form-item>

          <el-form-item>
            <el-button style="width:100%" type="primary" @click="submit">登录</el-button>
          </el-form-item>
        </el-form>
      </section>
    </main>
    <footer class="footerBox">
      <span>山西能源学院</span>
      <span>京ICP备09041920号</span>
      <span>京公网安备110108400531号</span>
    </footer>
  </div>
</template>

<script>
import "../assets/css/iconfont.css";
import "../assets/css/login.css";
import { loginAPI } from "../api/api";
import md5 from "js-md5"; //导入js-md5加密
export default {
  data() {
    return {
      form: {
        user: "侯丹",
        pass: "1234567890"
      },
      rules: {
        user: [
          { required: true, message: "请输入用户名", trigger: "blur" },
          { min: 2, max: 8, message: "长度在2到8个字符", trigger: "blur" }
        ],
        pass: [
          { required: true, message: "请输入密码", trigger: "blur" },
          { min: 6, max: 18, message: "长度在6到18个字符", trigger: "blur" }
        ]
      }
    }
  },
  methods: {
    submit() {
      this.$refs.form.validate(async (valid) => {
        //可以登录
        if (valid) {
          const password = md5(this.form.pass);
          const data = await loginAPI({ account: this.form.user, password });
          console.log(data);
          if (data.code === 0) {
            this.$message.success("登录成功");
            this.$router.push("/home");
          } else {
            this.$message.error("登录失败");
          }
        } else {
          this.$message.error("错误!");
        }
      });
    }
  }
};
</script>
<style>
.loginBox .form i.icon-mima {
  top: 30px;
  left: 2px;
}
.loginBox .form i {
  left: 2px;
}
/* .el-form-item{
  margin-bottom: 10px;
} */
.loginBox .open i {
  left: -30px;
}
</style>
