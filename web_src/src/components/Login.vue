<template>
  <div class="login" id="login">
    <div class="logo">
      <img src="../assets/logo-dwi.png" alt="" />
      <i></i>
      <span>视频流媒体平台</span>
    </div>
    <!-- <div class="limiter">
    <div class="container-login100">
      <div class="wrap-login100">
					<span class="login100-form-title p-b-26">江苏长三角智慧水务研究院视频平台</span>

          <div class="wrap-input100 validate-input" data-validate = "Valid email is: a@b.c">
            <input :class="'input100 ' + (username==''?'':'has-val')" type="text" v-model="username" name="username">
            <span class="focus-input100" data-placeholder="用户名"></span>
          </div>

          <div class="wrap-input100 validate-input" data-validate="Enter password">
						<span class="btn-show-pass">
							<i :class="'fa ' + (!showPassword?'fa-eye':'fa-eye-slash')" @click="showPassword = !showPassword"></i>
						</span>
            <input :class="'input100 ' + (password==''?'':'has-val')" :type="(!showPassword?'password':'text')" v-model="password" name="password">
            <span class="focus-input100" data-placeholder="密码"></span>
          </div>

          <div class="container-login100-form-btn">
            <div class="wrap-login100-form-btn" :class="{'login-loading': isLoging}" v-loading="isLoging" element-loading-background="rgb(0 0 0 / 0%);" element-loading-custom-class="login-loading-class">
              <div class="login100-form-bgbtn"></div>
              <button class="login100-form-btn" @click="login">登录</button>
            </div>
          </div>
      </div>
    </div>
  </div> -->

    <div class="login-form">
      <span class="hello">Hello~</span>
      <span class="welcome"> 欢迎您登录</span>
      <el-form
        class="login-box"
        ref="loginFormRef"
        :model="loginData"
        :rules="loginRules"
      >
        <el-form-item prop="username">
          <el-input
            class="flex-1"
            ref="username"
            size="large"
            :prefix-icon="User"
            v-model="username"
            placeholder="用户名"
            name="username"
          />
        </el-form-item>
        <el-tooltip disabled="false" placement="right" >
          <el-form-item prop="password">
            <el-input
              class="flex-1"
              v-model="password"
              placeholder="密码"
              :type="passwordVisible === false ? 'password' : 'input'"
              size="large"
              name="password"
              @keyup.enter="login"
            />
          </el-form-item>
        </el-tooltip>
        <!-- <el-form-item class="forget-box">
					<el-checkbox label="记住密码" name="type" />
				<span class="forget">忘记密码</span> 
					<el-text class="forget" tag="ins">忘记密码</el-text>
				</el-form-item> -->
        <el-button
          size="default"
          :loading="loading"
          type="primary"
          class="login-button"
          @click.prevent="login"
          >登录
        </el-button>
      </el-form>
      <span class="support">由江苏长三角智慧水务提供技术支持</span>
    </div>
  </div>
</template>

<script>
import crypto from "crypto";
import userService from "./service/UserService";
export default {
  name: "Login",
  data() {
    return {
      isLoging: false,
      showPassword: false,
      loginLoading: false,
      username: "",
      password: "",
    };
  },
  created() {
    var that = this;
    document.onkeydown = function (e) {
      var key = window.event.keyCode;
      if (key == 13) {
        that.login();
      }
    };
  },
  methods: {
    //登录逻辑
    login() {
      if (this.username != "" && this.password != "") {
        this.toLogin();
      }
    },

    //登录请求
    toLogin() {
      //需要想后端发送的登录参数
      let loginParam = {
        username: this.username,
        password: crypto
          .createHash("md5")
          .update(this.password, "utf8")
          .digest("hex"),
      };
      var that = this;
      //设置在登录状态
      this.isLoging = true;
      let timeoutTask = setTimeout(() => {
        that.$message.error("登录超时");
        that.isLoging = false;
      }, 1000);

      this.$axios({
        method: "get",
        url: "/api/user/login",
        params: loginParam,
      })
        .then(function (res) {
          window.clearTimeout(timeoutTask);
          console.log(res);
          console.log("登录成功");
          if (res.data.code === 0) {
            userService.setUser(res.data.data);
            //登录成功后
            that.cancelEnterkeyDefaultAction();
            that.$router.push("/");
          } else {
            that.isLoging = false;
            that.$message({
              showClose: true,
              message: "登录失败，用户名或密码错误",
              type: "error",
            });
          }
        })
        .catch(function (error) {
          console.log(error);
          window.clearTimeout(timeoutTask);
          that.$message.error(error.response.data.msg);
          that.isLoging = false;
        });
    },
    cancelEnterkeyDefaultAction: function () {
      document.onkeydown = function (e) {
        var key = window.event.keyCode;
        if (key == 13) {
          return false;
        }
      };
    },
  },
};
</script>

<style>
.login {
  width: 100vw;
  min-height: 100vh;
  overflow: hidden;
  background: url("../assets/bg-logo.png") no-repeat 100%100%/ 100%100%;
}
.login .logo {
  margin: 70px 86px;
}
.login .logo img {
  width: 184.8px;
  height: 67.2px;
}
.login .logo i {
  display: inline-block;
  width: 1px;
  height: 44px;
  background-color: #036eb8;
  margin: 0 20px;
}
.login .logo span {
  font-family: PangMenZhengDao;
  font-size: 60px;
  font-weight: normal;
  line-height: 35px;
  letter-spacing: 0.04em;
  color: #036eb8;
  -webkit-text-stroke: #036eb8 0.4px;
  /* 浏览器可能不支持 */
}
.login .login-form {
  position: relative;
  position: fixed;
  top: 240px;
  right: 252px;
  width: 460px;
  height: 600px;
  border-radius: 24px;
  background: rgba(255, 255, 255, 0.7);
  border: 2px dashed #ffffff;
  backdrop-filter: blur(6px);
}
.login .login-form span1 {
  position: absolute;
  left: 40px;
  display: inline-block;
  height: 112px;
  font-family: AlibabaPuHuiTi;
  font-size: 44px;
  font-weight: 900;
  line-height: 56px;
  letter-spacing: 0px;
  color: #1e67d4;
}

.login .login-form .hello {
  top: 56px;
  width: 110px;
  font-family: AlibabaPuHuiTi;
  font-weight: 400;
  font-size: 36px;
  position: absolute;
  left: 40px;
  display: inline-block;
  height: 112px;
  font-family: AlibabaPuHuiTi;
  font-size: 44px;
  font-weight: 900;
  line-height: 56px;
  letter-spacing: 0px;
  color: #1e67d4;
}
.login .login-form .welcome {
  top: 110px;
  width: 230px;
  font-family: AlibabaPuHuiTi;
  font-weight: 900;
  font-size: 44px;
  position: absolute;
  left: 40px;
  display: inline-block;
  height: 112px;
  font-family: AlibabaPuHuiTi;
  font-size: 44px;
  font-weight: 900;
  line-height: 56px;
  letter-spacing: 0px;
  color: #1e67d4;
}
.login .login-form .captcha {
  position: absolute;
  top: 0;
  right: 0;
}
.login .login-form .captcha img {
  width: 120px;
  height: 48px;
  cursor: pointer;
}
.login .login-form .login-box {
  position: absolute;
  top: 224px;
  left: 40px;
  width: 380px;
}
.login
  .login-form
  .login-box
  :deep(.el-form-item)
  .el-input
  .el-input__wrapper
  .el-input__inner {
  color: #383838;
}
.login .login-form .login-box :deep(.el-form-item) .el-input__wrapper {
  height: 56px;
  border-radius: 12px;
  background: #e2eeff;
  align-items: center;
  font-family: 思源黑体;
  font-size: 16px;
  font-weight: normal;
  line-height: 24px;
  letter-spacing: 2px;
  color: #383838;
}
.login
  .login-form
  .login-box
  :deep(.el-form-item)
  .el-input__wrapper
  .el-icon
  svg {
  width: 16px;
  height: 16px;
}
.login .login-form .login-box .login-button {
  width: 380px;
  height: 56px;
  border-radius: 12px;
  background: #2681ff;
}
.login .login-form .login-box .forget-box :deep(.el-form-item__content) {
  display: flex;
  width: 100%;
  justify-content: space-between !important;
}
.login .login-form .login-box .forget-box .forget {
  height: 24px;
  opacity: 0.8;
  font-family: AlibabaPuHuiTi;
  font-size: 14px;
  font-weight: normal;
  line-height: 24px;
  letter-spacing: 0px;
  text-decoration: underline;
  color: #2681ff;
}
.login .login-form .support {
  position: absolute;
  left: 120px;
  top: 544px;
  width: 224px;
  height: 24px;
  font-family: AlibabaPuHuiTi;
  font-size: 14px;
  font-weight: normal;
  line-height: 24px;
  text-align: center;
  letter-spacing: 0px;
  color: #748599;
}
</style>
