<template>
<div class="common-pop-header">
  <header class="header">
    <nav class="wrapper">
        <router-link to="/" exact class="logo">
          <img src="../assets/images/logo.png" alt="logo" >
        </router-link>
        <ul class="nav-list m-t-14">
          <li><router-link to="/" class="border-line" :class="{'active': url !== '/' }">首页</router-link></li>
          <li><router-link to="/questions/1" :class="{'active': isNav(/(questions|examiner|itemlist)/) }">我的题库</router-link></li>
          <li><router-link to="/exam/latest" :class="{'active': isNav(/(exam\/latest|examination|mytest)/) }">我的考试</router-link></li>
          <li><router-link to="/myerror/1" :class="{'active': isNav(/(myerror|errorlist)/) }">我的错题</router-link></li>
          <li><router-link to="/judgelist/1" :class="{'active': isNav(/(judge|judgelist|fenxi)/) }">我要当考官</router-link></li>
          <li><router-link to="/triallist/1" :class="{'active': isNav(/(trial|triallist)/) }">我要当判官</router-link></li>
          <li><router-link to="/myRecord" :class="{'active': isNav(/(myRecord)/)}">我的记录</router-link></li>
        </ul>
        <div class="nav-right m-t-14">

          <div class="user-info" v-if="isShowPop">
            <!-- <input type="text" placeholder="请输入您要查找的内容"> -->
            <!-- <span class="search-button"></span>    -->
            <span class="uesr-name">您好：{{ user }}</span>
            <span class="head-img" @click="isSelect =!isSelect">
              <ul v-if="isSelect" class="head-select" :class="{'active': !isNav(/(questions|examiner|itemlist|exam\/latest|examination|mytest|myerror|errorlist|judge|judgelist|trial|triallist|\/)/) }">
                <li ><router-link to="/questions/1" :class="{'active': isNav(/(questions|examiner|itemlist)/) }">我的题库</router-link></li>
                <li><router-link to="/exam/latest" :class="{'active': isNav(/(exam\/latest|examination|mytest)/) }">我的考试</router-link></li>
                <li><router-link to="/myerror/1" :class="{'active': isNav(/(myerror|errorlist)/) }">我的错题</router-link></li>
                <li><router-link to="/judgelist/1" :class="{'active': isNav(/(judge|judgelist|fenxi)/) }">我要当考官</router-link></li>
                <li><router-link to="/triallist/1"  :class="{'active': isNav(/(trial|triallist)/) }">我要当判官</router-link></li>
                <li style="border-bottom: 1px solid #878787;"><router-link to="/myRecord">我的记录</router-link></li>
                <li><a href="javascript:;" @click="loginOut" >退出帐号</a></li>
              </ul>
            </span>
          </div>
          <div class="sign-box" v-else>
            <div class="sign-button sign-in" @click="popLogin">登录</div>
            <!-- <a class="sign-button sign-up" href="javascript:;" title="SIGN UP"></a> -->
          </div>
        </div>
      </nav>
  </header>
  <div class="dialog" v-show="isLogin">
    <div class="dialog-bg" @click="closeLogin"></div>
    <div class="dialog-banner dialog-sign">
      <div class="sign-right">
        <div class="user-reset-pwd" v-if="isResetPwd">
          <p>修改密码</p>
          <div class="form-box"><i class="icon-user"></i><input type="text" placeholder="请输入学号" v-model="username"></div>
          <div class="form-box"><i class="icon-lock"></i><input type="password" placeholder="请输入您的新密码" v-model="newPassword"></div>
          <span class="reset-pwd" @click="backLogin">返回登录</span>

          <a href="javascript:" class="sign-button" @click="resetPwd">提交</a>
        </div>
        <div class="user-login" v-else>
          <p>欢迎登录</p>
          <div class="form-box"><i class="icon-user"></i><input type="text" placeholder="请输入学号" v-model="username"></div>
          <div class="form-box"><i class="icon-lock"></i><input type="password" placeholder="请输入密码" v-model="password"></div>
          <el-checkbox v-model="checked" true>记住密码</el-checkbox><span class="reset-pwd" @click="backLogin">修改密码</span>
          <a href="#" class="sign-button" @click="userLogin">登录</a>
        </div>

      </div>
      <span class="back-button" @click="closeLogin"><i class="icon-back"></i>返回首页</span>

    </div>
  </div>

</div>

</template>
<script>
import { mapState } from "vuex";
export default {
  name: "header",
  data() {
    return {
      isSelect: false,
      url: this.$route.path,
      checked: true,
      username: "",
      password: "",
      newPassword: "",
      isResetPwd: false,
      user: ''
    };
  },
  computed: {
    ...mapState({
      isShowPop: state => state.account.isShowPop,
      isLogin: state => state.isLogin,
      userInfo: state => state.account.userInfo,
    })
  },
  created() {},
  methods: {
    backLogin() {
      this.isResetPwd = !this.isResetPwd
    },
    resetPwd() {
      this.$store
        .dispatch("ACCOUNT_UPDATE", {
          userName: this.username,
          password: this.newPassword
        })
        .then(res => {
          if (res.success) {
            this.$message({
              message: "恭喜你，修改成功！",
              type: "success"
            });
            sessionStorage.setItem('username', this.username)
            sessionStorage.setItem('password', this.newPassword)
          } else {
            this.$message({
              message: res.message,
              type: "error"
            });
          }
        });
    },
    userLogin() {
      this.$store
        .dispatch("ACCOUNT_LOGIN", {
          "username": this.username,
          "password": this.password
        })
        .then(res => {
          if (res.success) {
            
            this.$message({
              message: "恭喜你，登录成功！",
              type: "success"
            });
            this.$store.commit("INDEX_SET", {
              target: "isLogin",
              data: false
            });
            this.$store.commit("ACCOUNT_SET", {
              target: "isShowPop",
              data: true
            });
            this.user = this.username
            sessionStorage.setItem('username', this.username)
            sessionStorage.setItem('password', this.password)
          } else {
            this.$message({
              message: res.message,
              type: "error"
            });
          }
        });
    },
    closeLogin() {
      this.$store.commit("INDEX_SET", {
        target: "isLogin",
        data: false
      });
    },
    popLogin() {
      this.$store.commit("INDEX_SET", {
        target: "isLogin",
        data: true
      });
    },
    isNav(reg) {
      if (Object.prototype.toString.call(reg) === "[object RegExp]") {
        return reg.test(this.$router.currentRoute.path);
      } else if (Object.prototype.toString.call(reg) === "[object String]") {
        return new RegExp(reg).test(this.$router.currentRoute.path);
      } else {
        return false;
      }
    },
    loginOut() {
      this.$store.dispatch("ACCOUNT_LOGOUT", {}).then(res => {
        if (res.success) {
          this.$message({
            message: "成功退出！",
            type: "success"
          });
          sessionStorage.removeItem('username')
          sessionStorage.removeItem('password')
        } else {
          this.$message({
            message: res.message,
            type: "error"
          });
        }
      });
      this.$router.push("/");

      this.$store.commit("ACCOUNT_SET", {
        target: "isShowPop",
        data: false
      });
    }
  },
  mounted() {
    this.user = sessionStorage.getItem('username')
  }
};
</script>
<style lang="less">
  .header {
    width: 100%;
    background-color: #7b27fb;
    nav {
      .logo {
        display: none;
        float: left;
        width: 220px;
        img{
          width: 220px;
        }
      }
      .sign-button {
        display: inline-block;
        width: 110px;
        height: 36px;
        line-height: 36px;
        text-align: center;
        border: 1px solid #fff;
        color: #fff;
        border-radius: 23px;
        cursor: pointer;
        // background: url(../assets/images/sign-up-in.png) no-repeat;
        // &.sign-in {
        //   background-position: 0 0;
        //   margin-right: 36px;
        // }
        // &.sign-up {
        //   background-position: -144px 0;
        // }
      }
    }
    .nav-list {
      float: left;
      list-style: none;
      li {
        float: left;
        height: 38px;
        // padding-right: 60px;
        a {
          display: none;
          // width: 108px;
          padding: 0 10px;
          margin-right: 10px;
          height: 30px;
          line-height: 30px;
          color: #fff;
          font-size: 16px;
          text-align: center;
          border: 1px solid #fff;
          border-radius: 15px;
          &.border-line {
            border: 1px solid #7b27fb;
          }
          &.active {
            display: block;
          }
        }
      }
    }
    .nav-right {
      float: right;
      margin-right: 20px;
    }
    .user-info {
      // display: none;
      span {
        display: inline-block;
        vertical-align: middle;
      }
      .uesr-name{
        color: #fff;
        margin-right: 10px;
      }
      .search-button {
        width: 21px;
        height: 30px;
        background: url(../assets/images/search.png) center no-repeat;
        margin-right: 30px;
        cursor: pointer;
      }
      .head-img {
        width: 30px;
        height: 30px;
        background: url(../assets/images/head.png) center no-repeat;
        cursor: pointer;
        position: relative;
        z-index: 2;
      }
      .head-select {
        position: absolute;
        top: 100%;
        left: -60px;
        padding: 10px 20px;
        width: 94px;
        margin-top: 12px;
        background: #fff;
        border-radius: 14px;
        box-shadow: 1px 3px 10px #eaeaea;
        li {
          width: 100%;
          float: left;
          a {
            width: 100%;
            display: block;
            color: #333;
            font-size: 14px;
            line-height: 24px;
            &:hover {
              color: #7b27fb;
            }
            &.active{
              // display: block;
            }
          }
        }
        &::before {
          content: "";
          clear: both;
          display: block;
          width: 0;
          height: 0;
          border-left: 6px solid transparent;
          border-right: 6px solid transparent;
          position: absolute;
          top: -12px;
          border-bottom: 12px solid #fff;
          left: 70px;
        }
        &.active{
          display: none;
        }
      }
      input {
        border: none;
        font-size: 16px;
        color: #333;
        height: 30px;
        margin-right: 20px;
        padding: 0 10px;
        border-radius: 4px;
        vertical-align: middle;
      }
    }
  }
  .dialog {
    position: fixed;
    width: 100%;
    height: 100%;
    top: 0;
    left: 0;
    z-index: 1002;
    .dialog-bg {
      position: absolute;
      width: 100%;
      height: 100%;
      top: 0;
      left: 0;
      background: #808080;
      opacity: 0.5;
    }
    .dialog-body {
      position: absolute;
      top: 50%;
      left: 50%;
      width: 1130px;
      padding: 0 76px 76px;
      transform: translate(-50%, -50%);
      background: #fff;
    }
    .dialog-info {
      height: 390px;
      padding: 55px;
      background: #fff url("../assets/images/banner-bg.png") 50px 50px no-repeat;
      background-size: 499px 384px;
      .sign-right {
        width: 610px;
        float: right;
        p {
          font-size: 16px;
          color: #333;
          line-height: 40px;
        }
        p:first-child {
          color: #720fb1;
          border-left: 6px solid #720fb1;
          padding-left: 20px;
          line-height: normal;
        }
        p:nth-child(2) {
          margin-bottom: 10px;
        }
      }
    }
    .dialog-banner {
      position: absolute;
      top: 50%;
      left: 50%;
      width: 1120px;
      transform: translate(-50%, -50%);

      border-radius: 14px;
    }
    .dialog-sign {
      height: 476px;
      padding: 130px 80px;
      background: #fff url("../assets/images/banner-bg.png") 80px 130px
        no-repeat;
      .sign-right {
        width: 474px;
        float: right;
        p {
          font-size: 36px;
          color: #4e00cc;
          text-align: center;
          margin: 42px 0;
        }
        .form-box {
          height: 40px;
          padding: 20px 0;
          border-bottom: 1px solid #ccc;
          i {
            display: inline-block;
            width: 18px;
            height: 20px;
            margin: 0 16px;
            vertical-align: sub;
            &.icon-user {
              background: url("../assets/images/icon-user.png") no-repeat;
            }
            &.icon-lock {
              background: url("../assets/images/icon-lock.png") no-repeat;
            }
          }
          input {
            width: 424px;
            height: 40px;
            line-height: 40px;
            font-size: 18px;
            color: #ccc;
          }
        }
        .el-checkbox {
          margin-top: 26px;
          margin-left: 16px;
          .el-checkbox__label {
            font-size: 16px;
          }

          .el-checkbox__input.is-checked + .el-checkbox__label {
            color: #606266;
          }
        }
        .reset-pwd {
          margin-top: 26px;
          line-height: 19px;
          font-size: 16px;
          float: right;
          color: #a36bfd;
          cursor: pointer;
        }
        .sign-button {
          display: block;
          width: 200px;
          height: 50px;
          margin: 70px auto 0;
          text-align: center;
          line-height: 50px;
          color: #fff;
          font-size: 18px;
          background: #4e00cc;
          border-radius: 25px;
          box-shadow: 0 0 #4d11ad;
        }
      }
      .back-button {
        position: absolute;
        bottom: 30px;
        left: 80px;
        color: #4e00cc;
        cursor: pointer;
        .icon-back {
          display: inline-block;
          width: 20px;
          height: 21px;
          background: url("../assets/images/icon-back.png") no-repeat;
          vertical-align: sub;
          padding-right: 8px;
        }
      }
    }
    .dialog-content {
      margin-right: -30px;
      h3 {
        height: 66px;
        line-height: 66px;
        border-bottom: 1px solid #dadada;
        font-size: 28px;
        color: #000;
        position: relative;
        &::after {
          content: "";
          display: block;
          clear: both;
          width: 112px;
          height: 4px;
          background-color: #000;
          position: absolute;
          left: 0;
          bottom: 0;
        }
      }
      a {
        display: inline-block;
        width: 256px;
        height: 120px;
        line-height: 120px;
        text-align: center;
        border: 2px #ccc solid;
        margin-right: 30px;
        margin-bottom: 52px;
        font-size: 26px;
        color: #b2b2b2;
        text-decoration: none;
        position: relative;
        &:hover {
          border-color: #f95c54;
          color: #010101;
          font-size: 28px;
          i {
            width: 30px;
            height: 30px;
            background: url("../assets/images/icon-check.png") -32px 0 no-repeat;
            position: absolute;
            right: -1px;
            bottom: -1px;
            display: block;
          }
        }
        &.active i {
          width: 30px;
          height: 30px;
          position: absolute;
          right: -1px;
          bottom: -1px;
          display: block;
        }
      }
    }
  }
</style>
