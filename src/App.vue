<template>
  <div class="app">
    <!-- 头 -->
    <div class="header">
      <div class="title">
        <i class="fa fa-tv"></i> &nbsp;&nbsp;看点咨询精选
      </div>
      <div class="info">
        <img class="photo" :src="user.userFace" alt="">
        <div class="u">
          <el-dropdown @command='handleCommand' size='mini'>
            <span class="el-dropdown-link">
              {{user.username}}<i class="el-icon-arrow-down el-icon--right"></i>
            </span>
            <el-dropdown-menu slot="dropdown">
              <el-dropdown-item>个人中心</el-dropdown-item>
              <el-dropdown-item command='logout'>退出</el-dropdown-item>
            </el-dropdown-menu>
          </el-dropdown>
        </div>

      </div>
    </div>
    <!-- 体 -->
    <div class="center">
      <!-- 左侧导航 -->
      <div class="left-nav">
        <ul>
          <li :class="{current:currentRoute=='/'}">
            <i class="fa fa-tv"></i>
            <router-link to='/'>首页</router-link>
            <i class="fa fa-angle-right"></i>
          </li>
          <li :class="{current:currentRoute=='/category'}">
            <i class="fa fa-ticket"></i>
            <router-link to='/category'>栏目管理</router-link>
            <i class="fa fa-angle-right"></i>
          </li>
          <li :class="{current:currentRoute=='/article'}">
            <i class="fa fa-upload"></i>
            <router-link to='/article'>文章管理</router-link>
            <i class="fa fa-angle-right"></i>
          </li>
          <li :class="{current:currentRoute=='/comment'}">
            <i class="fa fa-tree"></i>
            <router-link to='/comment'>评论管理</router-link>
            <i class="fa fa-angle-right"></i>
          </li>
          <li :class="{current:currentRoute=='/content'}">
            <i class="fa fa-adjust"></i>
            <router-link to='/content'>文章内容</router-link>
            <i class="fa fa-angle-right"></i>
          </li>
          <li :class="{current:currentRoute.indexOf('/setting')>=0}">
            <i class="fa fa-cog"></i>
            <router-link to='/setting'>系统设置</router-link>
            <i class="fa fa-angle-right"></i>
          </li>
        </ul>

      </div>
      <!-- 右侧内容区 -->
      <div class="content">
        <div class="wrapper">
          <router-view></router-view>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
  import axios from '@/http/axios'
  export default {
    data(){
      return {
        currentRoute:'/',
        user:{}
      }
    },
    watch:{
      '$route':function(to,from){
        this.currentRoute = to.path;
      }
    },
    created(){
      this.currentRoute = this.$route.path;


      // 从localStorage中获取用户信息，用于在页面中显示
      let user = JSON.parse(localStorage.getItem('user'));
      if(user && user.id){
        this.user = user;
      } else {
        window.vm.currentComponent = 'Login';
      }
    },
    methods:{
      handleCommand(command){
        if(command === 'logout'){
          axios.get('/manager/user/logout').then(()=>{
            //跳转
            window.vm.currentComponent = 'Login';
            //清理localstorage中的user
            localStorage.removeItem('user');
          });
        }
      }
    }
  }
</script>
<style>
  html {
    font: normal normal 14px '微软雅黑','Microsoft YaHei';
    color: #666
  }
  input:-webkit-autofill {
    -webkit-box-shadow: 0 0 0px 1000px white inset !important;
  }
  body , ul ,ol,dl ,p, h1,h2,h3 {
    margin: 0;
    padding: 0;
    background-color:#fff;
  }
  ul , ol {
    list-style: none;
  }
  a {
    color: #666;
    text-decoration: none;
  }
  div {
    box-sizing: border-box;
  }
  .el-dialog__header {
    background: #ededed;
  }
  .el-dialog__body {
    padding: 2em 2em 0 2em;
  }
  .el-upload-list--picture-card ,
  .el-upload-list__item,
  .el-upload--picture-card {
    width: 68px;
    height: 68px;
    line-height: 68px;
  }

  .header {
    position: absolute;
    width: 100%;
    height: 60px;
    top: 0;
    background-color: teal;
    padding: 0 1em;
  }
  .header .title {
    color: #ffffff;
    line-height: 60px;
    font-size: 18px;
    float: left;
  }
  .header .photo {
    width: 50px;
    height: 50px;
    border-radius: 50%;
    margin-top: 5px;
    margin-right: 1em;
  }
  .header .info {
    float: right;
    cursor: pointer;
  }
  .header .info .u {
    float: right;
    line-height: 20px;
    margin-top: 20px;
  }
  .header .info .el-dropdown {
    color: #fff;
  }

  .center {
    position: absolute;
    top: 60px;
    bottom: 0;
    width: 100%;

  }
  .center > .left-nav {
    width: 180px;
    height: 100%;
    float: left;
  }
  .center > .left-nav > ul {

  }
  .center > .left-nav > ul > li{
    line-height: 2.6em;
    text-align: center;
    border-bottom: 1px solid #f0f0f0;
    position: relative;
  }
  .center > .left-nav > ul > li i.fa {
    position: absolute;
    top: 50%;
    margin-top: -.5em;
  }
  .center > .left-nav > ul > li i.fa:last-child {
    right: 1em;
  }
  .center > .left-nav > ul > li i.fa:first-child {
    left: 2em;
  }
  .center > .left-nav > ul > li.current {
    background-color: #f0f0f0;
  }
  .center > .content {
    margin-left: 180px;
    height: 100%;
    background-color: #f0f0f0;
    padding: 1em 1em 0 1em;
    overflow-y: auto;
  }
  .center > .content > .wrapper {
    width: 100%;
    height: 100%;
    background-color: #ffffff;
    border-radius: 5px;
    padding: .5em;
    overflow-y: auto;
  }




  .btns {
    margin-bottom: .5em;
  }
  i.fa {
    margin: 0 .3em;
    cursor: pointer;
  }
  i.fa.fa-trash {
    color: #F56C6C;
  }





</style>
