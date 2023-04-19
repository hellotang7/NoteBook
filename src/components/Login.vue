<template>
  <div id="login">
    <div class="mask">
      <div class="wrapper">
        <div class="container">
          <div class="main"></div>
          <div class="form">

            <h3 @click="showRegister">创建账户(点击创建账号)</h3>
            <transition name="slide">
              <div v-bind:class="{show:isShowRegister}" class="register">
                <input type="text" v-model="register.username" placeholder="用户名">
                <input type="password" v-model="register.password" placeholder="密码">
                <p :class="{error:register.isError}">{{ register.notice }}</p>
                <div @click="onRegister" class="button">创建账号</div>
              </div>
            </transition>

            <h3 @click="showLogin">登录</h3>
            <transition name="slide">
              <div v-bind:class="{show:isShowLogin}" class="login">
                <input type="text" v-model="login.username" placeholder="请输入用户名">
                <input type="password" v-model="login.password" placeholder="请输入密码">
                <p :class="{error:login.isError}">{{ login.notice }}</p>
                <div  @click="onLogin" @keyup.enter="onLogin" class="button">登录</div>
              </div>
            </transition>

          </div>
        </div>
      </div>
    </div>
  </div>


</template>
<script>
import {mapGetters,  mapActions} from "vuex";



export default {
  data() {
    return {
      isShowLogin: true,
      isShowRegister: false,
      login: {
        username: '111',
        password: '111111',
        notice: '请输入用户名和密码',
        isError: false
      },
      register: {
        username: '',
        password: '',
        notice: '创建用户后请记住用户名和密码',
        isError: false
      }
    }
  },
  methods: {
    ...mapActions(
      {
        loginUser: 'login',
        registerUser: 'register',
      }
    ),

    showRegister() {
      this.isShowRegister = true
      this.isShowLogin = false
    },
    showLogin() {
      this.isShowLogin = true
      this.isShowRegister = false
    },
    onRegister() {
      let result1 = this.validUsername(this.register.username)
      if (!result1.isValid) {
        this.register.isError = true
        this.register.notice = result1.notice
        return
      }
      let result2 = this.validPassword(this.register.password)
      if (!result2.isValid) {
        this.register.isError = true
        this.register.notice = result2.notice
        return
      }

      this.registerUser({
        username: this.register.username,
        password: this.register.password
      }).then(() => {
        this.register.isError = false
        this.register.ontice = ''
        this.$router.push({path:'notebooks'})
        this.$message.success('创建账户成功！');

      }).catch(data=>{
        this.register.isError = true
        this.register.notice = data.msg
      })
    },

    onLogin() {
      let result1 = this.validUsername(this.login.username)
      if (!result1.isValid) {
        this.login.isError = true
        this.login.notice = result1.notice
        return
      }
      let result2 = this.validPassword(this.login.password)
      if (!result2.isValid) {
        this.login.isError = true
        this.login.notice = result2.notice
        return
      }

      this.loginUser({
        username: this.login.username,
        password: this.login.password
      }).then(() => {
        this.login.isError = false
        this.login.ontice = ''
        this.$router.push({path:'notebooks'})
        this.$message.success('登录成功！');

      }).catch(data=>{
        this.login.isError = true
        this.login.notice = data.msg
      })


    },
    validUsername(username) {
      return {
        isValid: /^[a-zA-Z_0-9_\u4e00\u9fa5]{3,15}$/.test(username),
        notice: '用户名必须是3~15个字符，限于字母数字下划线中文'
      }
    },
    validPassword(password) {
      return {
        isValid: /^.{6,16}$/.test(password),
        notice: '密码长度为6~16个字符'
      }
    },

    keydown(e){
      if(e.keyCode === 13){
        this.onLogin();
        this.onRegister()
      }
    },

  },
  mounted() {
    window.addEventListener("keydown",this.keydown)
  },
  destroyed() {
    window.removeEventListener("keydown",this.keydown,false)
  }

}
</script>


<style lang="less">

.mask {
  position: fixed;
  z-index: 100;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, .7);
  display: table;
  transition: opacity 0.3s ease;
}

.wrapper {
  display: table-cell;
  vertical-align: middle;
}

.container {
  width: 700px;
  height: 450px;
  margin: 0 auto;
  background-color: #ffffff;
  border-radius: 4px;
  box-shadow: 0 2px 8px reba(0, 0, 0, .33);
  transition: all .3s ease;
  font-family: Helvetica, Arial, sans-serif;
  display: flex;

  .main {
    flex: 1;
    background:url(https://never-forget-note-1315213295.cos-website.ap-chengdu.myqcloud.com/static/img/books.c3f6a14.jpg
  );
    background-size: contain;
  }

  .form {
    width: 270px;
    border-left: 1px solid #ccc;

    h3 {
      padding: 10px 20px;
      font-weight: normal;
      font-size: 16px;
      border-top: 1px solid #eee;
      cursor: pointer;

      &:nth-of-type(2) {
        border-bottom: 1px solid #eee;
      }
    }

    .button {
      background-color: #777;
      height: 36px;
      line-height: 36px;
      text-align: center;
      font-weight: bold;
      color: #fff;
      border-radius: 4px;
      margin-top: 18px;
      cursor: pointer;
    }
    .button:hover{
      background-color: #999;
      color: #666;
    }

    .login, .register {
      padding: 0px 20px;

      height: 0px;
      overflow: hidden;
      transition: height .4s;

      &.show {
        height: 193px;
      }

      input {
        display: block;
        width: 100%;
        height: 35px;
        line-height: 35px;
        padding: 0 6px;
        border-radius: 4px;
        border: 1px solid #ccc;
        outline: none; //消除默认输入框高亮
        font-size: 14px;
        margin-top: 10px;
      }

      input:focus {
        border: 2px solid #9dcaf8; //输入框添加搞了样式
      }

      p {
        font-size: 12px;
        margin-top: 10px;
        color: #444;
      }

      .error {
        color: red;
      }
    }

    .login {
      border-top: 0;
    }
  }
}
@media (max-width: 500px) {
  .container {
    width: 270px;
    height: 276px;
    margin: 0 auto;
    background-color: #ffffff;
    border-radius: 4px;
    box-shadow: 0 2px 8px reba(0, 0, 0, .33);
    transition: all .3s ease;
    font-family: Helvetica, Arial, sans-serif;
    display: flex;
  }
  .main{
    display: none;
  }
}

</style>
