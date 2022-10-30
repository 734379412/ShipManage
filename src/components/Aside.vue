<template>
  <el-menu :default-openeds="['1', '3']" style="min-height: 100%;overflow-x: hidden"
           background-color="rgb(48,65,86)"
           text-color="#fff"
           active-text-color="#ffd04b"
           :collapse-transition="false"
           :collapse="isCollapse"
           router
  >
    <div style="height: 60px;line-height: 60px;text-align: center ">
      <img src="../assets/logo.png"alt=""style="width: 20px;position: relative;top: 5px;margin-right: 5px">
      <b style="color: white" v-show="logoTextShow">港口客户网上办单系统</b>
    </div>
      <el-menu-item index="/">
        <template slot="title">
        <i class="el-icon-s-home"></i>
        <span slot="title">主页</span>
      </template>
      </el-menu-item>
    <el-submenu index="2">
      <template slot="title">
        <i class="el-icon-menu"></i>
        <span slot="title">系统管理</span>
      </template>
      <el-menu-item-group>
<!--        <el-menu-item index="/user">-->
<!--          <i class="el-icon-s-custom"></i>-->
<!--          <span slot="title">用户管理</span>-->
<!--        </el-menu-item>-->
        <el-menu-item index="/ship">
          <i class="el-icon-ship"></i>
          <span slot="title">船舶管理</span>
        </el-menu-item>
        <el-menu-item index="/attorney">
          <i class="el-icon-sugar"></i>
          <span slot="title">作业委托书管理</span>
        </el-menu-item>
        <el-menu-item index="/order">
          <i class="el-icon-s-flag"></i>
          <span slot="title">发货指令管理</span>
        </el-menu-item>
      </el-menu-item-group>
    </el-submenu>
  </el-menu>
</template>

<script>
export default {
  name: "Aside",
  props: {
    isCollapse: Boolean,
    logoTextShow: Boolean
  }
}
</script>

<style scoped>

</style>