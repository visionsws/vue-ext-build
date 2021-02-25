<template>
  <el-container class="home-container">
    <!--头部区-->
    <el-header>
      <div>
        <img src="../assets/avatar_pic2.jpeg">
        <span>外部构建项目平台</span>
      </div>
      <el-button type="info" @click="logout">退出</el-button>
    </el-header>
    <!--页面主体区-->
    <el-container>
      <!--侧边栏-->
      <el-aside :width="isCollapse? '64px' : '200px'">
        <div class="toggle-button" @click="toggleCollapse">|||</div>
        <el-menu
          :default-active="activePath"
          background-color="#333744"
          text-color="#fff"
          active-text-color="#409EFF"
          unique-opened
          :collapse="isCollapse"
          :collapse-transition="false"
          router>
          <!--一级菜单按钮-->
          <el-submenu :index="'idx'+item.menuId" v-for="item in menuList" :key="item.menuId">
            <!--一级菜单的模板区域-->
            <template slot="title">
              <i :class="iconsObj[item.menuId]"></i>
              <span>{{item.menuName}}</span>
            </template>
            <!--二级菜单-->
            <el-menu-item :index="'/'+subItem.menuPath" v-for="subItem in item.children" :key="subItem.menuId"
            @click="saveNavState('/'+subItem.menuPath)">
              <template slot="title">
                <i class="el-icon-menu"></i>
                <span>{{subItem.menuName}}</span>
              </template>
            </el-menu-item>
          </el-submenu>
        </el-menu>
      </el-aside>
      <!--右侧内容主体-->
      <el-main>
        <!--路由占位符-->
        <router-view></router-view>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
  export default {
    name: 'Home',
    data() {
      return {
        menuList: [],
        iconsObj: {
          1: 'iconfont iconuser-filling',
          2: 'iconfont iconcangchucangku',
          3: 'iconfont iconcunchu',
          4: 'iconfont icondingdan'
        },
        // 是否折叠
        isCollapse: false,
        // 被激活的链接地址
        activePath: ''
      }
    },
    created () {
      this.getMenuList()
      this.activePath = window.sessionStorage.getItem('activePath')
    },
    methods: {
      logout() {
        window.sessionStorage.clear()
        this.$router.push('/login')
      },
      async getMenuList() {
        const { data: res } = await this.$http.post('/menu/getMenuList')
        if (res.code !== 200) {
          return this.$message.error('获取失败')
        }
        this.menuList = res.content
        console.log(res)
      },
      toggleCollapse() {
        this.isCollapse = !this.isCollapse
      },
      // 保存链接的激活状态
      saveNavState(activePath) {
        window.sessionStorage.setItem('activePath', activePath)
        this.activePath = activePath
      }
    }

  }
</script>

<style lang="less" scoped>
  .home-container{
    height: 100%;
  }
  .el-header{
    // 背景颜色
    background-color: #242a2e;
    // header采用Flex布局，称为Flex容器。它的所有子元素(一个div和一个button)自动成为容器成员，称为Flex项目。
    display: flex;
    // Flex容器下的项目两端对齐，项目之间的间隔都相等。如有3个flex项目，除首尾两个项目在左右两端，中间项目和首尾两个项目距离相当
    justify-content: space-between;
    // 交叉轴的中点对齐
    align-items: center;
    padding-left: 5px;
    color: #fff;
    font-size: 20px;
    img{
      height: 50px;
      width: 50px;
    }
    // >是直接子元素，不包括孙子辈的
    > div{
      display: flex;
      align-items: center;
      span{
        margin-left: 15px;
      }
    }
  }
  .el-aside{
    background-color: #333744;
    .el-menu{
      border-right: none;
    }
  }
  .el-main{
    background-color: #eaedf1;
  }
  .toggle-button{
    background-color: #2b4b6b;
    // 字体大小
    font-size: 15px;
    // 行高
    line-height: 24px;
    // 字体颜色
    color: #fff;
    // 字体居中
    text-align: center;
    // 字体的间距
    letter-spacing: 0.2em;
    // 鼠标放上去出现小手图标
    cursor: pointer;
  }

</style>
