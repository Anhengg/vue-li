<template>
  <el-container class="home-container">
     <el-header>
       <div>
         <img src="../assets/heima.png">
         <span>電商后台管理系統</span>
       </div>
        <el-button type="info" @click="logout">退出</el-button>
      </el-header>
    <el-container>

      <el-aside :width="isCollapse ? '64px':'200px'">
        <div class="toggle-button" @click="toggle"> |||</div>
         <el-menu
      background-color="#333744"
      text-color="#fff"
      active-text-color="#409eff" :unique-opened="true" :collapse="isCollapse" :collapse-transition="false" :router="true">
      <el-submenu :index="item.id +''" v-for="item in menulist" :key="item.id">
        <template slot="title">
          <i :class="iconlist[item.id]"></i>
          <span>{{item.authName}}</span>
        </template>
        <el-menu-item :index="'/'+subitem.path +''" v-for="subitem in item.children" :key="subitem.id"> <template slot="title">
          <i class="el-icon-menu"></i>
          <span>{{subitem.authName}}</span>
        </template></el-menu-item>
        </el-submenu>
    </el-menu>
      </el-aside>
      <el-main><router-view></router-view></el-main>

    </el-container>
  </el-container>
</template>
<script>
export default {
  data () {
    return {
      menulist: [],
      iconlist: {
        125: 'iconfont icon-user',
        103: 'iconfont icon-tijikongjian',
        101: 'iconfont icon-shangpin',
        102: 'iconfont icon-danju',
        145: 'iconfont icon-baobiao'
      },
      isCollapse: false
    }
  },
  created () {
    this.getMenuList()
  },
  methods: {
    logout () {
      window.sessionStorage.clear()
      this.$router.push('/login')
    },
    async getMenuList () {
      const { data: res } = await this.$http.get('menus')
      if (res.meta.status !== 200) return this.$message.error(res.meta.msg)
      this.menulist = res.data
      console.log(res)
    },
    toggle () {
      this.isCollapse = !this.isCollapse
    }
  }
}
</script>
<style lang="less" scoped>
.el-header {
 background-color: #373d41;
 display: flex;
 justify-content: space-between;
 align-items: center;
 padding-left: 0;
 color: #fff;
 font-size: 20px;
 > div {
   display: flex;
   align-items: center;
   > span {
     margin-left: 15px;
   }
 }
}
.el-aside{
  background-color: #333744;
  .el-menu {
    border-right:none ;
  }
}
.el-main{
  background-color: #eaedf1;
}
.home-container{
  height: 100%;
}
.iconfont{
  margin-right: 10px;
}
.toggle-button{
  background-color: #4A5064;
  font-size: 10px;
  line-height: 24px;
  color: #fff;
  text-align: center;
  letter-spacing: 0.2em;
  cursor: pointer;
}
</style>
